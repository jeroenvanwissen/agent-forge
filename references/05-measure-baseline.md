# Measure Baseline

Run all available quality tools and record the results. These baselines make improvements measurable.

## Measurements

1. **Linter** (if configured) — count total warnings and errors by category
2. **Formatter** (if configured) — check how many files would change
3. **Type checker** (if configured) — count type errors
4. **Test coverage** (if tooling exists) — record overall % and per-module breakdown
5. **Complexity hotspots** — find the longest functions, deepest nesting, largest files
6. **Performance baselines** (if benchmarks exist) — run existing benchmarks, record results
7. **Dependency health** — run vulnerability scanner (`npm audit`, `pip audit`, etc.), note outdated deps and license concerns

## Recording Format

Record baselines in the Quality Baseline table in BACKLOG.md (see `08-backlog-format`):

| Metric | Current | Target |
|--------|---------|--------|
| Test coverage | {{X%}} | No decrease |
| Linter errors | {{N}} | 0 |
| Linter warnings | {{N}} | {{target}} |
| Type errors | {{N or N/A}} | 0 |
| Longest function | {{N lines}} | ≤ {{limit}} |
| Largest file | {{N lines}} | ≤ {{limit}} |
| Dependency vulns | {{N or N/A}} | 0 |
| Bundle size | {{N or N/A}} | {{target}} |

If no tooling is configured for a measurement, note it as a finding and consider generating a setup task.

## When Tools Fail

- **Build is broken:** Record "build broken" as a Critical finding. Skip coverage and some lint checks. The first task should fix the build.
- **Linter not configured:** Record as Medium finding. Use language defaults for complexity estimates.
- **Tests won't run:** Record as High finding. Skip coverage measurement. Note which tests fail and why.
- **Can't determine coverage:** Record "N/A" and note why. Don't invent numbers.
