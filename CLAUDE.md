# ha-ems

Home Assistant energy management: smart EV charging driven by solar surplus, energy tariffs and the home battery. Domain terms live in [GLOSSARY.md](GLOSSARY.md); use them in code, docs and issues. Decisions live in [docs/adr/](docs/adr/).

## Workflow

1. Every change starts from a GitHub issue, filed under `kristofdegrave`.
2. Branch `<issue-number>-<slug>` off `main`.
3. Commits, pushes and the PR run as `kristofdegrave-bot`. The repo-local git config carries the bot identity; open the PR with the bot token scoped to that one call:
   `GH_TOKEN=$(gh auth token --user kristofdegrave-bot) gh pr create ...`
   Plain `gh` stays `kristofdegrave` and is the user's side (approve, labels, merge).
4. PR body follows the `pr` skill and ends with `Closes #<n>`.
5. Before marking ready, run the mattpocock `code-review` and `ha-review` against `main`. Post the verified findings as one PR review from `kristofdegrave-bot`, event `COMMENT`, one inline comment per file and line:
   `GH_TOKEN=$(gh auth token --user kristofdegrave-bot) gh api repos/kristofdegrave/ha-ems/pulls/<n>/reviews --input review.json`
   with `review.json` in the scratchpad holding `{"event": "COMMENT", "body": "<summary>", "comments": [{"path": "...", "line": 12, "side": "RIGHT", "body": "..."}]}`. Inline comments may only target lines inside the diff hunks; one outside returns 422 for the whole review, so those findings go in `body`. Approve, request changes and merge stay with the user.

## Standards

Both pre-PR reviews check changes against this section.

- ha-ems is a HACS custom integration in Python. Integration code and tests follow the `ha-integration-knowledge` skill, and the quality scale rules are checked with `ha-quality-scale-verify`.
- Home Assistant YAML config (examples, test configuration) follows the `home-assistant-best-practices` skill: reference entities by `entity_id`, prefer native triggers, conditions and helpers over Jinja templates.
- Every new domain term lands in GLOSSARY.md in the same PR that introduces it.
- A choice between real alternatives (architecture, integration, control strategy) gets an ADR in `docs/adr/`, numbered `NNNN-<slug>.md`.
- Components follow the IDesign layers and call rules ([ADR 0001](docs/adr/0001-idesign-method.md)).
- ha-ems sends no notifications and holds no device code. It exposes facts, owns inputs and writes setpoints with read-back ([ADR 0002](docs/adr/0002-system-boundary.md)).
- The process rules in [ADR 0001](docs/adr/0001-idesign-method.md#process) apply: fixed document set, findings stay code issues, done = seen running, lean writing.

## Skills

The mattpocock plugin is enabled in `.claude/settings.json`. Reach for:

- `grilling`: stress-test a design before building it.
- `domain-modeling`: editing GLOSSARY.md or writing an ADR.
- `tdd`: building features or fixes test-first.
- `diagnosing-bugs`: something broken, throwing or slow.
- `code-review` and `pr`: before opening and when describing a PR.

Project skills in `.claude/skills/`, adapted from home-assistant/core:

- `ha-integration-knowledge`: writing or reviewing anything under `custom_components/` or `tests/`.
- `ha-quality-scale-verify`: checking a quality scale rule or tier.
- `ha-review`: the Home Assistant half of the pre-PR review.

## Agent skills

### Issue tracker

Issues live in GitHub Issues for kristofdegrave/ha-ems, via the `gh` CLI. See `docs/agents/issue-tracker.md`.

### Triage labels

The five default triage labels (`needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`). See `docs/agents/triage-labels.md`.

### Domain docs

Single-context: one `GLOSSARY.md` and `docs/adr/` at the repo root. See `docs/agents/domain.md`.
