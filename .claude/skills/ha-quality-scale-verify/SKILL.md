---
name: ha-quality-scale-verify
description: Verifies that the ha-ems custom integration follows a specific Home Assistant quality scale rule, checking whether it implements the required patterns, configurations, or code structures. Use when asked to check a rule (e.g. "check the config-flow rule") or to verify ha-ems reaches a quality tier (Bronze, Silver, Gold, Platinum).
---

# Verify Quality Scale Rule

Adapted from [home-assistant/core](https://github.com/home-assistant/core/tree/4eb235287822d33574cb138104a2754b8f3ad2e5/.claude/skills/ha-quality-scale-verify) (Apache-2.0) for a custom integration distributed through HACS.

Verify one rule at a time; to check a full tier, verify each of that tier's rules (parallel subagents when possible).

Reading the code is not enough. Many rules have an objective threshold or a validator that CI enforces — run the tool and read its result instead of inferring from the source. Report the measured number or the tool's verdict; when a rule is a judgment from reading code, say so; when you could not check it, mark it **unverified** rather than implying a pass.

## 1. Understand the rule
Fetch the rule doc from `https://raw.githubusercontent.com/home-assistant/developers.home-assistant/refs/heads/master/docs/core/integration-quality-scale/rules/{rule_name}.md` and read the exact requirement — the wording matters (e.g. test-coverage is "Above 95% test coverage for all integration modules": per module, strictly above 95%). Note the tier the rule belongs to, its required patterns, and its exemption criteria.

## 2. Inspect the integration
Verify the code as it currently is (local working tree, or a checked-out PR head), not `main`. Read `custom_components/<domain>/manifest.json`, `custom_components/<domain>/quality_scale.yaml` (each rule's `done`/`todo`/`exempt`), and the modules and `tests/` relevant to the rule.

User docs live in `README.md` (what HACS shows) and `docs/`.

## 3. Which rules to verify
- **Tier bump** (the target tier in `quality_scale.yaml` changes): verify every rule from Bronze up to the target tier, from scratch — do not trust the existing `done`/`exempt` marks.
- **Rule status changes, same tier**: verify every rule whose status the diff changes, in any direction. A revert to `todo` at or below the target tier breaks the tier claim.
- **Code change only** (no `quality_scale.yaml` change): verify the changed code still satisfies the rules in force at the current tier. Map the diff to the rules it implicates and check those — the bar is no regression.
- **A single requested rule**: verify just that rule.

## 4. How to prove each rule
Run checks in the project's dev environment, with the versions it pins (`requirements*.txt` or `pyproject.toml`). A missing or stale dependency (ModuleNotFoundError, mypy flagging library symbols) is a build-environment gap to fix, not a rule violation.

- **hassfest** — validates `manifest.json`, translations, services and config-flow wiring. CI runs `home-assistant/actions/hassfest`; locally run the same image: `docker run --rm -v "$PWD:/github/workspace" ghcr.io/home-assistant/hassfest`. A clean run does not prove a `todo`/`exempt` rule — check those directly.
- **HACS validation** — CI runs `hacs/action` with `category: integration`. It checks `hacs.json`, the manifest keys HACS needs, the repo description and topics, and brands.
- **strict-typing** (platinum) — `mypy --strict custom_components/<domain>`; report the error count.
- **test-coverage** (silver+) — `pytest --cov=custom_components.<domain> --cov-report=term-missing`. The rule is strictly above 95% **per module**; report every module not above 95% by exact covered/total (a rounded "95%" can be 94.7%), with missing lines.
- **config-flow-test-coverage** (bronze) — `config_flow.py` fully covered, and every supported flow tested through error recovery to its correct terminal result: `CREATE_ENTRY` for user/discovery/import setup, `ABORT` + `reauth_successful`/`reconfigure_successful` for reauth/reconfigure, plus proof that a duplicate or otherwise-disallowed second entry is rejected — require the test to assert that the flow aborts, but accept the integration's reason (e.g. `already_configured`, `single_instance_allowed`). Options flows count and live in `config_flow.py`. 100% line coverage without these scenarios still fails.
- **docs-*** — confirm the required sections exist in `README.md` or `docs/`. A missing section is a finding.
- **brands** (bronze) — verify the icon and logo exist for `<domain>` in `home-assistant/brands` under `custom_integrations/`, or in the integration's own `brand/` folder; else mark unverified.
- **Everything else** — verify by reading the code against the rule doc.

Rules are cumulative and enforced at or above their own tier (e.g. `test-coverage` is Silver — it does not fail a Bronze claim). A `done` rule whose check fails is a finding; so is an `exempt` reason that masks an implementation gap rather than genuine non-applicability.

## 5. Report findings
Report the rules with issues and the rules you could not check. For each issue: the rule and the problem (non-compliance, over-claimed `done`, or invalid `exempt`), the evidence (measured result / tool output / file location), and the fix. For each unchecked rule: mark it unverified and say what is needed. State which checks you ran. If every rule you could check passes and nothing is left unverified, say so in one line.
