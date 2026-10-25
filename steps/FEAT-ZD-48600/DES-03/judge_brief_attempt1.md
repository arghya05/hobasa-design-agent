# Evaluate step DES-03: Understand HLD or LLD+ Doubts Clarifications for FEAT-ZD-48600

- Workbook action: Not defined in workbook; complete the readiness action
- Workbook outcome: Assumptions, Questions or Clarifications would be looped till the developer has achieved complete clarity

## Outputs to evaluate
- `hobasa-design-agent/outputs/FEAT-ZD-48600/feat.clarifications.md`

## Inputs the agent was given
- feat.design: `hobasa-design-agent/inputs/FEAT-ZD-48600/DES-03/feat.design.md`
- feat.stories: `hobasa-design-agent/inputs/FEAT-ZD-48600/DES-03/feat.stories.md`
- feat.trd: `hobasa-design-agent/inputs/FEAT-ZD-48600/DES-03/feat.trd.md`

## Other GitHub files the agent read (valid sources when cited as file:<repository>/<path>)
- `hobasa-design-agent/outputs/FEAT-ZD-48600/feat.clarifications.md`
- `hobasa-design-agent/outputs/FEAT-ZD-48600/feat.screens.md`
- `hobasa-design-agent/steps/FEAT-ZD-48600/DES-02/gate_decisions_1.json`
- `ap-aging-sample/.git/config`
- `ap-aging-sample/CLAUDE.md`
- `ap-aging-sample/README.md`
- `ap-aging-sample/ci.py`
- `ap-aging-sample/src/ap_aging/__init__.py`
- `ap-aging-sample/src/ap_aging/aging.py`
- `ap-aging-sample/tests/test_aging.py`
- `ap-forecast-sample/CLAUDE.md`
- `ap-forecast-sample/ci.py`
- `ap-forecast-sample/src/ap_forecast/__init__.py`
- `ap-forecast-sample/src/ap_forecast/explain.py`
- `ap-forecast-sample/src/ap_forecast/predict.py`
- `ap-forecast-sample/tests/test_forecast.py`
- `ap-pipeline-sample/CLAUDE.md`
- `ap-pipeline-sample/ci.py`
- `ap-pipeline-sample/src/ap_pipeline/__init__.py`
- `ap-pipeline-sample/src/ap_pipeline/transform.py`
- `ap-pipeline-sample/tests/test_pipeline.py`
- `ap-portal-sample/CLAUDE.md`
- `ap-portal-sample/ci.mjs`
- `ap-portal-sample/package.json`
- `ap-portal-sample/src/ap_portal/index.js`
- `ap-portal-sample/tests/portal.test.js`
- `hobasa-agentic-sdlc/features/FEAT-ZD-48600/COMMUNICATION.md`

Shared memory and conventions: `memory/MEMORY.md` and `CLAUDE.md` in the SDLC repository.

## The step brief the agent was given (a valid source, cited as record:brief)

# Step DES-03: Understand HLD or LLD+ Doubts Clarifications (attempt 1) for FEAT-ZD-48600

- Workbook trigger: Task creation
- Workbook action: Not defined in workbook; complete the readiness action
- Workbook outcome: Assumptions, Questions or Clarifications would be looped till the developer has achieved complete clarity
- Owner of this step: Architect
- Gate (who approves this output): no gate: automated checks, the independent judge and sampled human evaluation

## Inputs (GitHub checkout; read them with Read, or context_get for a receipt)
- feat.design (HLD, LLD and contracts): `hobasa-design-agent/inputs/FEAT-ZD-48600/DES-03/feat.design.md` (from hobasa-design-agent/outputs/FEAT-ZD-48600/feat.design.md at commit 722a98f)
- feat.stories (Epics, stories and tasks with numbered AC, frame links, assumptions and open items (latest revision)): `hobasa-design-agent/inputs/FEAT-ZD-48600/DES-03/feat.stories.md` (from hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.stories.md at commit 0590ea4)
- feat.trd (Approved TRD): `hobasa-design-agent/inputs/FEAT-ZD-48600/DES-03/feat.trd.md` (from hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.trd.md at commit 3637e51)

## Outputs (write each file in full; the spine commits and pushes after evaluation and approval)
- feat.clarifications (Questions, assumptions and answers): write `hobasa-design-agent/outputs/FEAT-ZD-48600/feat.clarifications.md`


## How to work
Decide the best way to do this step from the inputs and the workbook action, plan it (TodoWrite for multi-part work), then do it. Helper subagents you can delegate to: explorer, verifier (use the verifier before you finish when the output makes many factual statements). Use the shared memory (memory_search) for decisions and lessons. Connectors approved for you: api:pypi (Python package metadata: latest version, licence, release dates (GET /pypi/<package>/json)), api:npm (npm package metadata: versions, licence (GET /<package>)). Use one only when your inputs do not hold what you need; request a missing one with capability_request. You may also read any other file in the GitHub checkouts your step needs (for example other requirements when grouping, or code); cite it as file:<repository>/<path>.

## Tool view (cite as tool:repo)
--- ap_aging/src/ap_aging/__init__.py ---
"""AP aging service: buckets open invoices by days past due."""
from .aging import aging_buckets

__version__ = "1.4.0"
__all__ = ["aging_buckets"]

--- ap_aging/src/ap_aging/aging.py ---
"""Accounts-payable aging: group open invoices into days-past-due buckets."""
from datetime import date
from decimal import Decimal
from typing import Dict, Iterable

BUCKETS = ("current", "1-30", "31-60", "61-90", "90+")


def days_past_due(due_date: date, as_of: date) -> int:
    """Calendar days after the due date (0 when not yet due)."""
    return max((as_of - due_date).days, 0)


def bucket_for(days: int) -> str:
    if days == 0:
        return "current"
    if days < 30:
        return "1-30"
    if days <= 60:
        return "31-60"
    if days <= 90:
        return "61-90"
    return "90+"


def aging_buckets(invoices: Iterable[Dict], as_of: date) -> Dict[str, Decimal]:
    """Total open amount per bucket. Each invoice: {'id', 'due_date': date, 'amount': Decimal, 'paid': bool}."""
    totals = {b: Decimal("0") for b in BUCKETS}
    for inv in invoices:
        if inv.get("paid"):
            continue
        totals[bucket_for(days_past_due(inv["due_date"], as_of))] += Decimal(inv["amount"])
    return totals

--- ap_aging/tests/__init__.py ---

--- ap_aging/tests/test_aging.py ---
import unittest
from datetime import date
from decimal import Decimal

from ap_aging.aging import aging_buckets, bucket_for, days_past_due

AS_OF = date(2026, 10, 25)


class AgingTest(unittest.TestCase):
    def test_days_past_due(self):
        self.assertEqual(days_past_due(date(2026, 10, 20), AS_OF), 5)
        self.assertEqual(days_past_due(date(2026, 11, 1), AS_OF), 0)

    def test_bucket_edges(self):
        self.assertEqual(bucket_for(0), "current")
        self.assertEqual(bucket_for(31), "31-60")
        self.assertEqual(bucket_for(90), "61-90")
        self.assertEqual(bucket_for(91), "90+")

    def test_aging_buckets_skips_paid(self):
        invoices = [{"id": "A", "due_date": date(2026, 9, 1), "amount": Decimal("100.00"), "paid": False},
                    {"id": "B", "due_date": date(2026, 9, 1), "amount": Decimal("50.00"), "paid": True},
                    {"id": "C", "due_date": date(2026, 11, 30), "amount": Decimal("10.00"), "paid": False}]
        totals = aging_buckets(invoices, AS_OF)
        self.assertEqual(totals["31-60"], Decimal("100.00"))
        self.assertEqual(totals["current"], Decimal("10.00"))


if __name__ == "__main__":
    unittest.main()

--- ap_forecast/src/ap_forecast/__init__.py ---
"""AP forecast: predicted payment delay and an LLM explanation request."""
from .predict import predict_delay_days
from .explain import build_explanation_request

__version__ = "0.9.0"
__all__ = ["predict_delay_days", "build_explanation_request"]

--- ap_forecast/src/ap_forecast/explain.py ---
"""Build the LLM request that explains a prediction to the AP user. The provider and model come from models.json."""
import json
from pathlib import Path

CONFIG = Path(__file__).with_name("models.json")


def load_config():
    return json.loads(CONFIG.read_text())


def build_explanation_request(vendor: str, predicted_days: int, config=None) -> dict:
    cfg = config or load_config()
    prompt = f"Explain in one sentence why vendor {vendor} is predicted to be paid {predicted_days} days late."
    return {"provider": cfg["provider"], "model": cfg["model"], "max_tokens": cfg["max_tokens"],
            "messages": [{"role": "user", "content": prompt}]}

--- ap_forecast/src/ap_forecast/models.json ---
{
  "provider": "acme-llm",
  "model": "acme-text-2",
  "max_tokens": 300
}

--- ap_forecast/src/ap_forecast/predict.py ---
"""Predict a vendor's payment delay in days from its history (simple, explainable baseline model)."""
from statistics import mean
from typing import Iterable


def predict_delay_days(history: Iterable[int]) -> int:
    """Rounded mean of past delays in days; 0 when there is no history; negative delays (early payments) count as 0."""
    values = [max(int(d), 0) for d in history]
    return round(mean(values)) if values else 0

--- ap_forecast/tests/__init__.py ---

--- ap_forecast/tests/test_forecast.py ---
import unittest

from ap_forecast import build_explanation_request, predict_delay_days


class ForecastTest(unittest.TestCase):
    def test_predict(self):
        self.assertEqual(predict_delay_days([10, 20, 30]), 20)
        self.assertEqual(predict_delay_days([]), 0)
        self.assertEqual(predict_delay_days([-5, 5]), 2)

    def test_request_uses_config(self):
        req = build_explanation_request("V-1", 12)
        self.assertEqual(req["model"], "acme-text-2")
        self.assertIn("V-1", req["messages"][0]["content"])

    def test_request_with_explicit_config(self):
        req = build_explanation_request("V-2", 3, {"provider": "p", "model": "m", "max_tokens": 10})
        self.assertEqual((req["provider"], req["max_tokens"]), ("p", 10))


if __name__ == "__main__":
    unittest.main()

--- ap_pipeline/src/ap_pipeline/__init__.py ---
"""AP pipeline: ingest the vendor invoice feed, map it to the Data Model, report data quality."""
__version__ = "0.4.0"

--- ap_pipeline/src/ap_pipeline/dq.py ---
"""Data-quality report for one pipeline run."""


def quality_report(records, rejected) -> dict:
    total = len(records) + len(rejected)
    return {"rows": total, "loaded": len(records), "rejected": len(rejected), "reject_reasons": sorted({r["reason"] for r in rejected})}

--- ap_pipeline/src/ap_pipeline/ingest.py ---
"""Ingestion: parse the vendor invoice feed (CSV with header)."""
import csv
import io
from typing import Dict, List


def parse_feed(csv_text: str) -> List[Dict[str, str]]:
    return [dict(r) for r in csv.DictReader(io.StringIO(csv_text))]

--- ap_pipeline/src/ap_pipeline/pipeline.py ---
"""One run: ingest -> transform -> data-quality report."""
from .dq import quality_report
from .ingest import parse_feed
from .transform import to_model


def run(csv_text: str):
    records, rejected = to_model(parse_feed(csv_text))
    return records, quality_report(records, rejected)

--- ap_pipeline/src/ap_pipeline/schedule.json ---
{
  "frequency": "daily",
  "time": "02:00",
  "timezone": "Asia/Kolkata"
}

--- ap_pipeline/src/ap_pipeline/transform.py ---
"""Transformation: map feed rows to the Invoice Data Model (id, vendor, due_date, amount, paid)."""
from datetime import date
from decimal import Decimal, InvalidOperation
from typing import Dict, List, Tuple


def to_model(rows: List[Dict[str, str]]) -> Tuple[List[Dict], List[Dict]]:
    """Return (records, rejected); a row is rejected when a required field is missing or invalid."""
    records, rejected = [], []
    for r in rows:
        try:
            records.append({"id": r["invoice_id"].strip(), "vendor": r["vendor"].strip(), "due_date": date.fromisoformat(r["due_date"]),
                            "amount": Decimal(r["amount"]), "paid": r.get("paid", "false").strip().lower() == "true"})
        except (KeyError, ValueError, InvalidOperation, AttributeError) as e:
            rejected.append({"row": r, "reason": type(e).__name__})
    return records, rejected

--- ap_pipeline/tests/__init__.py ---

--- ap_pipeline/tests/test_pipeline.py ---
import unittest
from decimal import Decimal

from ap_pipeline.pipeline import run

FEED = "invoice_id,vendor,due_date,amount,paid\nINV-1,V-100,2026-09-01,120.00,false\nINV-3,V-100,bad,10.00,false\n"


class PipelineTest(unittest.TestCase):
    def test_run_maps_and_rejects(self):
        records, report = run(FEED)
        self.assertEqual(records[0]["amount"], Decimal("120.00"))
        self.assertEqual(report, {"rows": 2, "loaded": 1, "rejected": 1, "reject_reasons": ["ValueError"]})

    def test_paid_flag(self):
        records, _ = run("invoice_id,vendor,due_date,amount,paid\nI,V,2026-01-01,1,TRUE\n")
        self.assertTrue(records[0]["paid"])

    def test_missing_column_rejected(self):
        records, report = run("invoice_id,vendor,due_date\nI,V,2026-01-01\n")
        self.assertEqual(report["rejected"], 1)


if __name__ == "__main__":
    unittest.main()

--- ap_portal/src/ap_portal/index.js ---
'use strict';
// AP portal view helpers: render the aging summary returned by the ap_aging service as HTML.
const VERSION = '2.3.0';
const LABELS = { current: 'Current', '1-30': '1-30 days', '31-60': '31-60 days', '61-90': '61-90 days', '90+': 'Over 90 days' };

function formatAmount(value) {
  return Number(value || 0).toFixed(2);
}

function renderAgingSummary(buckets) {
  const rows = Object.keys(LABELS).map((k) => `<tr><td>${LABELS[k]}</td><td class="amount">${formatAmount(buckets[k])}</td></tr>`);
  return `<table class="aging-summary">${rows.join('')}</table>`;
}

module.exports = { VERSION, LABELS, formatAmount, renderAgingSummary };

--- ap_portal/tests/portal.test.js ---
'use strict';
const test = require('node:test');
const assert = require('node:assert');
const portal = require('ap_portal');

test('formats amounts with two decimals', () => {
  assert.strictEqual(portal.formatAmount(12), '12.00');
  assert.strictEqual(portal.formatAmount(undefined), '0.00');
});

test('renders every bucket in order', () => {
  const html = portal.renderAgingSummary({ current: 10, '90+': 5 });
  assert.ok(html.startsWith('<table class="aging-summary">'));
  assert.ok(html.indexOf('Current') < html.indexOf('Over 90 days'));
  assert.ok(html.includes('<td class="amount">5.00</td>'));
});


Finish with the structured result: approach, tools_used, claims (each citing an input item name, a tool view, api:<name>, mcp:<server>, file:<repository>/<path> for any other GitHub file you read, record:intake, record:owner_answer, record:memory or record:brief for this brief; a claim drawing on several sources lists them separated by ';'; every number must appear in what you cite), open questions (BLOCKING: only when you cannot finish), summary.