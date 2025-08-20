# Security Advisory Metrics

End-to-end project to track and visualize Security Advisory lifecycle & risk posture:
- Metric catalog (SLA compliance, time-to-remediate, open vs closed, by severity/business unit)
- Reusable SQL and Python for data prep
- Power BI report for exec & operations views

## 🎯 Objectives
- Standardize advisory metrics across teams
- Improve visibility of overdue/high-risk advisories
- Support governance reporting with repeatable datasets

## 📊 Metric Catalog (v1)
| Metric | Definition | Grain | Notes |
|---|---|---|---|
| Open Advisories | Count where `status in ('Open','In Progress','Blocked')` | Daily snapshot | Excludes closed/cancelled |
| Closure Rate | `closed_this_period / total_this_period` | Weekly/Monthly | 28-day rolling |
| SLA Breach Rate | `% advisories past due date` | Daily | Uses `due_date` |
| Median TTR | Median days `closed_date - created_date` | Monthly | By severity |
| High/Critical Mix | `% of High/Critical out of total open` | Daily | Risk exposure |
| Blocked Aging | Median days since first 'Blocked' status | Weekly | Bottlenecks |

## 🧱 Data Model
**Tables**
- `advisories` (id, title, severity, status, owner, business_unit, created_date, due_date, closed_date, updated)
- `status_history` (advisory_id, status, changed_at)
- `owners` (owner_id, name, email, team)

**Keys**
- `advisories.id = status_history.advisory_id`
- `advisories.owner -> owners.owner_id`

## 🛠️ Getting Started
1. Clone the repo and set up a Python env (optional if you only use Power BI):
   ```bash
   python -m venv .venv && source .venv/bin/activate    # Windows: .venv\Scripts\Activate
   pip install -r src/requirements.txt
