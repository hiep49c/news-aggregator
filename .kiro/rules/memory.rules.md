---
inclusion: auto
---

# Memory Rules — Persistence, Integrity & Recovery Policy

Áp dụng cho mọi file trong:
`{module}/memory/`

Mục tiêu:
- không mất state
- không corrupt dữ liệu
- không duplicate processing
- không inconsistent memory
- không phình vô hạn
- đảm bảo recoverable và idempotent

Memory system phải hoạt động như:
- resilient persistence layer
- stateful ETL pipeline
- production-grade intelligence memory

KHÔNG hoạt động như:
- append-only log ngẫu nhiên
- best-effort cache
- uncontrolled history dump

---

# Core Principles

## Reliability First

Ưu tiên:
- data integrity
- consistency
- recoverability
- idempotency
- deterministic behavior

KHÔNG:
- overwrite mù
- partial commit
- silent corruption
- duplicate persistence

---

# File Schema

## `last_scan.json`

### Required fields

- `last_scan_timestamp`
  - ISO 8601 + timezone
  - ví dụ:
    `2026-05-13T15:03:36+07:00`

- `last_scan_date`
  - format:
    `YYYY-MM-DD`

- `run_count`
  - integer
  - số lần scan trong ngày

- `stories_found`
  - integer

---

### Optional fields

- `top_story`
- `summary`
  - ≤ 500 chars
- `next_scan_priority`
- `entities_tracked`
- `conflicts_tracked`
- module-specific metadata

---

## `processed_links.json`

```json
{
  "processed_urls": []
}
```

---

## URL Requirements

URL phải là:
- full canonical URL
- có protocol
- có hostname
- normalized

KHÔNG lưu:
- tracking params
- session token
- auth token
- private params

---

## Archive Files

### `processed_links_YYYY-MM.json`

- same schema
- read-only sau khi archive
- dùng cho dedup/history check

---

# Canonical URL Normalization

Trước khi persist URL:
PHẢI normalize.

---

## Normalize Rules

PHẢI:
- lowercase hostname
- remove fragments (`#...`)
- normalize trailing slash
- remove tracking params
- normalize mobile URL
- normalize AMP URL

---

## Remove Tracking Params

Ví dụ:
- `utm_source`
- `utm_medium`
- `fbclid`
- `gclid`
- `ref`

→ strip nếu không ảnh hưởng content identity.

---

## Canonical Identity

Dedup dựa trên:
1. canonical URL
2. semantic similarity
3. title similarity
4. publication timestamp

KHÔNG chỉ dựa vào raw URL.

---

# Atomic Write (BẮT BUỘC)

Mọi write PHẢI theo pattern:

1. write → `{file}.tmp`
2. validate
3. atomic rename → `{file}`

KHÔNG bao giờ write trực tiếp vào:
- `last_scan.json`
- `processed_links.json`

---

## Validation Before Commit

PHẢI validate:
- parse JSON thành công
- required fields đầy đủ
- schema hợp lệ
- timestamp hợp lệ

Nếu validate fail:
- xoá `.tmp`
- giữ file cũ
- log error

---

# Transaction Semantics

Report generation + memory update
PHẢI xem như một logical transaction.

---

## Commit Order

1. LOAD_STATE
2. FETCH
3. FILTER
4. DEDUP
5. ANALYZE
6. GENERATE_REPORT
7. VALIDATE_OUTPUT
8. WRITE_REPORT
9. VERIFY_REPORT_EXISTS
10. UPDATE_MEMORY
11. FINALIZE

---

## Critical Rule

KHÔNG update memory trước khi:
- report write thành công
- output validate thành công

---

## Partial Failure Handling

Ví dụ:
- markdown OK
- HTML fail

→ KHÔNG update memory.

State chỉ được update khi:
- toàn bộ required output write thành công.

---

# Idempotency (BẮT BUỘC)

Mọi operation PHẢI idempotent.

Rerun cùng input:
- KHÔNG duplicate report
- KHÔNG duplicate URL
- KHÔNG inflate counters
- KHÔNG append lặp

---

## Re-run Handling

Nếu report cùng ngày đã tồn tại:
- merge delta mới
- hoặc controlled overwrite

KHÔNG append toàn bộ report lại.

---

## URL Processing

Một URL chỉ được:
- classify
- analyze
- persist

một lần duy nhất,
trừ khi:
- article updated materially
- correction published
- significant delta detected

---

# Deduplication

## URL Dedup
- canonical URL trùng → skip

## Semantic Dedup
- nhiều bài cùng fact set → merge

Ưu tiên:
- bài gốc
- source tier cao hơn

KHÔNG xem:
- URL khác
= thông tin khác.

---

# Rotation & Retention

## `processed_links.json`

Retention:
- 60 ngày gần nhất
HOẶC
- 5,000 URL
(whichever larger)

---

## Rotation Rules

Khi:
- cuối tháng
HOẶC
- file > 5,000 URLs

THÌ:
- archive URLs cũ
- tạo:
  `processed_links_YYYY-MM.json`

---

## Active Window

`processed_links.json`
chỉ giữ:
- tháng hiện tại
- tháng trước đó

---

## Archive Usage

Archive dùng cho:
- dedup
- continuity check
- historical comparison

KHÔNG load toàn bộ archive vào RAM.

---

# Memory Size Protection

KHÔNG:
- parse toàn bộ history
- load archive vô hạn
- giữ full history trong memory

Ưu tiên:
- rolling window
- lazy loading
- bounded history

---

# Recovery Rules

## File Missing

### `last_scan.json` missing

Treat as:
- cold start

Behavior:
- scan window = 24h
- run_count = 1

PHẢI warning:

```text
⚠️ Cold start — không có lịch sử quét trước đó
```

---

### `processed_links.json` missing

Behavior:
- không skip URL nào
- cho phép duplicate trong lần đầu

PHẢI warning rõ.

---

# Corruption Handling

## Parse Fail

Nếu file corrupt:

KHÔNG:
- auto-fix
- overwrite
- silent recovery

PHẢI:
1. rename:
   `{file}.corrupt-{timestamp}`
2. treat as missing
3. warning trong report

---

# Timestamp Safety

Nếu:
- timestamp mới < timestamp cũ

THÌ:
- abort write
- log error
- yêu cầu manual confirmation

KHÔNG overwrite state non-monotonic.

---

# Crash Recovery

Nếu detect:
- `.tmp`
- `.partial`
- unfinished marker
- stale lock

→ treat as interrupted run.

---

## Recovery Rules

PHẢI:
- validate partial file
- rollback nếu invalid
- recover nếu safe

KHÔNG assume:
- tmp file valid
- interrupted write complete

---

# Concurrency Protection

Chỉ một process được phép:
- update memory
- update dashboard
- finalize state

tại cùng một thời điểm.

---

# Locking

Khi run bắt đầu:
- tạo `.lock`

Khi finalize:
- remove `.lock`

Nếu lock timeout:
- treat as stale lock
- warning user
- cho phép manual recovery

---

# Persistent State Rules

Trước khi chạy:
PHẢI đọc:
- `last_scan.json`
- `processed_links.json`

Chỉ update:
- SAU KHI report finalized thành công.

---

# Output Validation

Trước khi commit:

PHẢI validate:
- markdown parse được
- HTML renderable
- required sections tồn tại
- timestamps hợp lệ
- URLs hợp lệ
- report không empty
- story count consistent

---

# Consistency Checks

Ví dụ:
- `stories_found`
PHẢI khớp với:
- actual analyzed stories

Mismatch lớn:
→ abort commit.

---

# Source of Truth Hierarchy

Priority:

1. report files
2. `last_scan.json`
3. `processed_links.json`

Nếu conflict:
- report file là authoritative source.

KHÔNG overwrite report tốt
dựa trên corrupted memory state.

---

# Delta Integrity

Khi update report cũ:
PHẢI:
- preserve historical analysis
- append delta rõ ràng
- track conclusion changes

Nếu conclusion thay đổi:
PHẢI ghi:
- điều gì thay đổi
- evidence mới
- confidence thay đổi ra sao

KHÔNG silently rewrite history.

---

# Observability

Mỗi run PHẢI log:

- start time
- end time
- duration
- fetched stories
- deduped stories
- analyzed stories
- skipped stories
- warnings
- failed sources
- corruption events
- recovery actions

---

# Error Visibility

KHÔNG swallow error silently.

Mọi:
- timeout
- corruption
- parse fail
- validation fail
- partial failure
- lock conflict
- inconsistent state

→ PHẢI visible trong:
- logs
- warnings
- report metadata

---

# Safe Degradation

Nếu:
- source unavailable
- partial fetch fail
- dashboard fail
- memory fail

THÌ:
- generate partial report nếu safe
- preserve previous valid state
- degrade gracefully

KHÔNG crash toàn pipeline nếu không cần thiết.

---

# Privacy & Leakage

KHÔNG lưu:
- API keys
- auth token
- session token
- paywall bypass token
- private URLs

Nếu URL chứa sensitive params:
→ strip trước khi persist.

---

# Critical Rule

Memory system phải:
- deterministic
- recoverable
- idempotent
- corruption-resistant

Ưu tiên:
- integrity
- consistency
- continuity

hơn:
- speed
- convenience
- aggressive overwrite

Nếu không chắc state còn valid:
- preserve old state
- abort write
- warning rõ ràng.