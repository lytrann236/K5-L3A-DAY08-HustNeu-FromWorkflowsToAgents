# Báo cáo cá nhân — 2A202603507_ĐỗHoàngMinh

## 1. Thông tin thành viên

- Họ tên: Đỗ Hoàng Minh
- MSSV: 2A202603507
- Email: 26ai.hoangd@vinuni.edu.vn
- GitHub: Mazzonthego

## 2. Phần việc phụ trách

- Shape C — **Orchestrator + handoff**, file `src/lab8/shapes/orchestrator.py`.
- Dựng handoff bằng `lab8.handoff.make_handoff`, chỉ cấp các tool đọc:
  `lookup_order`, `lookup_policy`, `compute_compensation`.
- Chạy investigator bằng `lab8.runtime.run_readonly_investigators`.
- Lấy quote từ investigator (không lấy từ nội dung ticket) rồi gọi
  `lab8.durability.issue_refund` với **quote đầy đủ**.
- Truyền `crash_at` vào `issue_refund`, không bắt `InjectedCrash`.
- Ghi trace chỉ gồm handoff đã sanitize và dữ liệu đã qua allowlist; không ghi
  `parent_secret`, header, ticket, nguyên claim row hoặc nguyên order row.
- Viết test riêng `src/tests/test_orchestrator_extra.py`.
- Chạy các kiểm tra CP2–CP5: 62 test orchestrator, test handoff secret, crash
  suite, benchmark và `check_submission`.

## 3. Đóng góp cụ thể

| Commit | Nội dung |
|---|---|
| `4de8972` | Viết `src/lab8/shapes/orchestrator.py`: handoff chỉ đọc, investigator, quote, `issue_refund`, trace sanitize |
| `9e77971` | Thêm `src/tests/test_orchestrator_extra.py` và bằng chứng `src/reports/benchmark.json`, `src/reports/crash.json` |
| `c3cb9b9` | Merge nhánh `feat/orchestrator` vào `main` (phần code orchestrator) |
| `94b0247` | Merge `feat/orchestrator` vào `main` |

## 4. Bằng chứng

### 4.1. Kết quả test

| Lệnh | Kết quả |
|---|---|
| `python -m pytest -q -m student -o addopts= -k orchestrator` | `62 passed, 249 deselected` |
| `python -m pytest -q tests/test_handoff_secret.py tests/test_orchestrator_extra.py` | `8 passed` |
| `python -m pytest -q` | `63 passed, 248 deselected` |
| `python -m pytest -q -o addopts=` | `311 passed` |
| `python -m lab8.crash_test --count 20 --out reports/crash.json` | `crash_safety 20/20` |

### 4.2. Chạy tay ba claim đại diện

| Claim | Status | Proposal | Refund thực tế | Audit |
|---|---|---:|---:|---:|
| `C-001` | `resolved` | 35.000 | 35.000 | 1 |
| `C-013` | `denied` | 35.000 | 0 | 0 |
| `C-014` | `resolved` | 150.000 | 120.000 | 1 |

Run id tương ứng:

```text
R-orchestrator-C-001-s11
R-orchestrator-C-013-s11
R-orchestrator-C-014-s11
```

### 4.3. Benchmark

Kết quả dòng `orchestrator` trong `src/reports/benchmark.json`:

| Pass | Pass^5 | Variance | Min | Max | Cost/success | Steps |
|---:|---:|---:|---:|---:|---:|---:|
| 1.000 | 1.000 | 0.000000 | 1.000 | 1.000 | 47.30 | 5.36 |

So sánh nhanh:

- `workflow`: pass 1.000, cost/success 28.79, steps 4.43.
- `single`: pass 1.000, cost/success 47.31, steps 9.07.
- `orchestrator`: pass 1.000, cost/success 47.30, steps 5.36.
- `swarm`: pass 0.986, pass^5 0.929, variance 0.000816.

### 4.4. `check_submission`

```text
== shapes ==
[ OK ] workflow: 12/12
[ OK ] single: 12/12
[ OK ] orchestrator: 12/12

== reports ==
[ OK ] benchmark.json: 4 shape x 14 claim x 5 seed
[ OK ] crash.json: 20/20

== docs ==
8 FAIL
```

8 FAIL nằm ở `report/team/decision_adr.md` và `report/team/research_card.md` vì
phần nội dung nhóm chưa điền. Đây không phải lỗi code, test hay bằng chứng.

### 4.5. Link commit

- [`4de8972` — implement orchestrator](https://github.com/lytrann236/K5-L3A-DAY08-HustNeu-FromWorkflowsToAgents/commit/4de8972)
- [`9e77971` — test + CP4–CP5 evidence](https://github.com/lytrann236/K5-L3A-DAY08-HustNeu-FromWorkflowsToAgents/commit/9e77971)
- [`94b0247` — merge vào main](https://github.com/lytrann236/K5-L3A-DAY08-HustNeu-FromWorkflowsToAgents/commit/94b0247)



## 5. Công cụ AI đã dùng

- **Claude Code**:
  - Đọc và tóm tắt `docs/business_rules.md`, `docs/contracts.md`,
    `lab8/handoff.py`, `lab8/runtime.py`, `lab8/durability.py`.
  - Gợi ý khung `orchestrator.py`, cách dựng handoff, cách ghi trace an toàn.
  - Chạy `pytest`, `lab8.crash_test`, `lab8.benchmark`, `lab8.check_submission`
    và phân tích output lỗi.
  - Chạy lại toàn bộ test trên máy, không dùng số liệu AI tự nghĩ ra.
  - Mở file trace JSON và kiểm tra không có `CANARY_SECRET`, `parent_secret`,
    `raw_headers`, ticket hoặc order row.
  - Chỉ ghi run id, commit hash và số liệu benchmark lấy từ output thực tế của
    `lab8.benchmark` / `lab8.crash_test`.

## 6. Tự đánh giá

**Điểm mạnh:**

- Hoàn thành shape C đúng contract: handoff chỉ đọc, investigator tách quyền ghi,
  host là nơi duy nhất ghi refund.
- Trace sạch, không lộ secret/PII; đã kiểm bằng test riêng và bằng mắt.
- Xử lý đúng T11/T12 (idempotency, crash/resume), T13/T14 (trần theo đơn,
  quote đầy đủ) và T07 (approval hỏng vẫn có audit `policy_denied`).
- Kết quả đo được: 62/62 test orchestrator, 311 test toàn bộ, crash 20/20,
  benchmark pass 1.000 và pass^5 1.000.

**Hạn chế:**
- Orchestrator tốn nhiều tool call hơn workflow (cost/success 47.30 so với 28.79),
  có thể tối ưu bằng cách bỏ bớt tool không cần thiết sau khi đo lại.
- Phần báo cáo nhóm cần các thành viên khác phối hợp để thống nhất shape
  chọn và số liệu cuối.
