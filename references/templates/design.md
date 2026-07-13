# SPEC_ID Design - Feature or Bugfix Title

## Current State

Summarize the current model logic, data flow, and known issue or gap.

## Proposed dbt Changes

| Resource | Change | Reason |
| --- | --- | --- |
|  |  |  |

## DAG and Lineage

List affected upstream and downstream nodes.

```text
source/model -> changed_model -> downstream_model
```

## Grain, Keys, and Contracts

- Model grain:
- Primary key:
- Required fields:
- Nullability assumptions:
- Relationship assumptions:

## Transformation Logic

Describe the core SQL logic in plain language before implementation. Include joins, filters, calculations, currency/date logic, and edge cases.

## Materialization and Incremental Strategy

- Materialization:
- Incremental key, if any:
- Backfill requirement:
- Full-refresh risk:

## Testing Strategy

### Data Tests

Add or update YAML tests for model/source integrity.

- [ ] Primary key `unique`
- [ ] Primary key `not_null`
- [ ] Important dimensions `accepted_values`
- [ ] Foreign keys or source relationships `relationships`

### Singular Data Tests

Use `tests/*.sql` when a business assertion should return zero failing rows.

- [ ] 

### Unit Tests

Add dbt unit tests only when the changed SQL has complex or high-risk logic.

- [ ] Complex `case when`
- [ ] Date math
- [ ] Regex
- [ ] Window functions
- [ ] Prior bug or high-risk refactor
- [ ] Edge case not present in real data

Unit test YAML must live under `models/` or another configured `model-paths` directory, not under `tests/`.

### Reconciliation Checks

Define comparison checks against previous logic, source-of-truth queries, or sampled records.

- Row count:
- Key totals:
- Grouped totals:
- Sample business keys:

## Validation Commands

```powershell
dbt compile --select <changed_model+>
dbt build --select <changed_model+>
dbt test --select <changed_model+>
```

## Risks and Rollback

- Risk:
- Rollback:
