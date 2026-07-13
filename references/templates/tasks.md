# SPEC_ID Tasks - Feature or Bugfix Title

## Execution Rules

- Keep tasks scoped to this spec.
- Before edits, inspect current dbt models, YAML, macros, and existing tests.
- After each completed repository modification, run `git status` and commit only files related to that completed task.
- Include `SPEC_ID` and, when useful, the task number in commit messages.
- If the implementation changes requirements or design decisions, update and commit the spec before continuing.

## Tasks

### 0. Spec Setup

- [ ] Copy `_template` into `spec/SPEC_ID_short_name/`
- [ ] Complete `requirements.md`
- [ ] Complete `design.md`
- [ ] Complete this `tasks.md`
- [ ] Commit the spec files

### 1. Baseline Inspection

- [ ] Identify impacted models, sources, macros, and downstream dependencies
- [ ] Read existing YAML tests and singular tests
- [ ] Capture baseline row counts or key totals if needed

### 2. Test and Validation Setup

- [ ] Add or update data tests in model/source YAML
- [ ] Add singular data tests under `tests/` if business-rule failures should return zero rows
- [ ] Add unit tests under `models/` if complex SQL logic needs static input/output examples
- [ ] Add reconciliation query or notes if parity validation is needed
- [ ] Commit test and validation setup

### 3. dbt Implementation

- [ ] Update model SQL
- [ ] Update model/source YAML documentation and tests
- [ ] Update macros or seeds only if required by the design
- [ ] Commit implementation changes

### 4. Verification

- [ ] Run `dbt compile --select <changed_model+>`
- [ ] Run `dbt build --select <changed_model+>`
- [ ] Run targeted `dbt test`
- [ ] Run reconciliation checks, if required
- [ ] Record command results and any exceptions below

## Validation Notes

| Command or check | Result | Notes |
| --- | --- | --- |
|  |  |  |
