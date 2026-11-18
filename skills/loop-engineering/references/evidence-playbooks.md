# Evidence Playbooks — what "done" means per task type

Employee 1 captures this evidence. Employee 2 audits whether it proves the claim. Employee 3 re-runs the critical line itself. No playbook row satisfied = not done.

---

## Code / feature / bug fix
- **Done when:** tests pass, the feature runs on real input, and the original failure no longer reproduces.
- **Evidence:** test command + full result line (`N passed`), coverage on new logic ≥ 80%, before/after of the reproducing case, build/type-check clean.
- **E3 re-verifies:** re-runs the test suite and the repro case from a clean state.
- **Extra for bug fixes:** a regression test that FAILS on the old code and PASSES on the new one. State that you confirmed it fails first.

## Script / CLI tool
- **Done when:** it runs end to end on the Boss's machine with real arguments.
- **Evidence:** actual invocation + stdout + exit code, plus one bad-input run showing a clean error message (not a stack trace dump).
- **Checks:** `--help` exists, exit codes are meaningful (0 success / non-zero failure), no hardcoded absolute paths, safe to re-run.

## Automation / workflow (n8n, Make, Zapier, cron, webhook)
- **Done when:** a real execution completed successfully in the platform's own log.
- **Evidence:** execution ID + status + the resulting record (row written, message sent, file created), plus a screenshot or the log excerpt.
- **Checks:** idempotent on re-run (no duplicate rows/messages), error branch exists and was triggered once on purpose, credentials referenced not embedded, schedule/timezone confirmed, what happens if the trigger fires twice in the same minute.
- **Trap:** "the workflow saved" ≠ "the workflow ran". Only an execution counts.

## API integration
- **Done when:** a real call returned real data and the failure path was exercised.
- **Evidence:** request + redacted response, status codes for success AND failure, rate-limit/pagination behavior shown on a multi-page result.
- **Checks:** timeout set, retry/backoff capped, token refresh path, secrets from env, response schema validated before use, nothing sensitive logged.

## Data work (sheets, DB, migration, ETL)
- **Done when:** counts and values reconcile against the source.
- **Evidence:** row counts before/after, a sample of transformed records compared to source, the reconciliation of any total (sum in = sum out).
- **Checks:** ran on a copy/test set first, rollback path stated, duplicates and nulls handled explicitly, encoding and date formats confirmed, no partial-write state possible.

## Infra / config / DNS / deployment
- **Done when:** verified live from outside the machine that changed it.
- **Evidence:** the live check output (`curl -I`, `dig`, the loaded page, the service status), timestamped after the change propagated.
- **Checks:** previous config captured for rollback, blast radius named, no service left in a half-applied state, secrets not in the committed config.

## Debugging / investigation
- **Done when:** the root cause is identified and demonstrated, not guessed.
- **Evidence:** the reproducing case, the observation that isolates the cause (log line, value, diff), and proof that changing that one thing changes the outcome.
- **Ban:** "it might be X" as a conclusion. Either prove it or report it as an open hypothesis with the next test named.

## Docs / research
- **Done when:** every factual claim traces to a source that was actually read.
- **Evidence:** links/paths per claim; the distinction between verified fact, inference, and assumption is explicit in the text.
- **Checks:** every code sample in the doc was run, every path/command referenced exists, dates are absolute not relative.

## Anything visual or animated (UI, page, dashboard, deck, render)
- **Done when:** it was rendered, looked at, and *watched moving* at every breakpoint and format that matters.
- **Evidence:** screenshots/preview renders (desktop + mobile, light + dark if both exist), the **motion token table actually used**, frames or a recording of the key animation, and the `prefers-reduced-motion` variant shown landing on a complete visible state.
- **Checks:** alignment and spacing on a consistent grid; no clipping/overflow at either breakpoint; one hero moment with real staggered choreography; compositor-safe properties only; 60fps verified not assumed; all states designed (hover / focus-visible / active / disabled / loading / empty / success / error); contrast legible; zero typos; nothing reads as an unmodified template default.
- **Trap:** "the CSS is written" is not evidence. Only a render you have looked at counts — and for motion, only frames or a recording.
