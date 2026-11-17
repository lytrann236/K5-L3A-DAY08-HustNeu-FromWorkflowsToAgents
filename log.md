(base) PS E:\Vinuni\K5-L3A-DAY08-Day09-2A202603507-DoHoangMinh-FromWorkflowsToAgents\src> python -m lab9.trace_case --shape seeded_regression --claim C-001 --seed 11
>> python -m lab9.trace_case --shape fixed --claim C-001 --seed 11
== seeded_regression C-001@11  lab8 grader: PASS (ok)
-- trace (step, kind, payload)
   1 handoff            {"max_output_tokens": 400, "contract_version": "handoff.v1", "facts": {}, "principal_scope": "owner_order_only
   2 investigator       {"lookup_order": {"visible": true, "reason": "ok", "order": {"order_id": "O-107", "status": "delivered_late",
   3 quote              {"amount_vnd": 35000, "reason": "entitled", "clause_ids": ["SHIP-LATE-03"], "parts": {"SHIP-LATE-03": 35000}}
   4 refund_history     {"refund_count": 0, "refunded_vnd": 0}
   5 reply              {"text": "Dạ, LogiX đã hoàn 35.000đ cho đơn O-107."}
-- ledger vs label
  refund_vnd     ledger=35000    label=35000
  refund_count   ledger=1        label=1
  status         result=resolved label=resolved
  unexpected ledger rows: 0
== fixed C-001@11  lab8 grader: PASS (ok)
-- trace (step, kind, payload)
   1 handoff            {"allowed_tools": ["lookup_order", "lookup_policy", "compute_compensation"], "task": "check_applicable_policy"
   2 investigator       {"lookup_order": {"visible": true, "reason": "ok", "order": {"order_id": "O-107", "status": "delivered_late",
   3 quote              {"amount_vnd": 35000, "reason": "entitled", "clause_ids": ["SHIP-LATE-03"], "parts": {"SHIP-LATE-03": 35000}}
   4 reply              {"text": "Dạ, LogiX đã hoàn 35.000đ cho đơn O-107."}
-- ledger vs label
  refund_vnd     ledger=35000    label=35000
  refund_count   ledger=1        label=1
  status         result=resolved label=resolved
  unexpected ledger rows: 0