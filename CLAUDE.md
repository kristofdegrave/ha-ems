# ha-ems

Home Assistant energy management: smart EV charging driven by solar surplus, energy tariffs and the home battery. Domain terms live in [GLOSSARY.md](GLOSSARY.md); use them in code, docs and issues. Decisions live in [docs/adr/](docs/adr/).

## Workflow

1. Every change starts from a GitHub issue, filed under `kristofdegrave`.
2. Branch `<issue-number>-<slug>` off `main`.
3. Commits, pushes and the PR run as `kristofdegrave-bot`. The repo-local git config carries the bot identity; open the PR with the bot token scoped to that one call:
   `GH_TOKEN=$(gh auth token --user kristofdegrave-bot) gh pr create ...`
   Plain `gh` stays `kristofdegrave` and is the reviewer side (reviews, labels, merge).
4. PR body follows the `pr` skill and ends with `Closes #<n>`.
5. Before marking ready, run `code-review` against `main`. The user merges.

## Standards

`code-review` checks changes against this section.

- Home Assistant config follows the `home-assistant-best-practices` skill: reference entities by `entity_id`, prefer native triggers, conditions and helpers over Jinja templates.
- Every new domain term lands in GLOSSARY.md in the same PR that introduces it.
- A choice between real alternatives (architecture, integration, control strategy) gets an ADR in `docs/adr/`, numbered `NNNN-<slug>.md`.

## Skills

The mattpocock plugin is enabled in `.claude/settings.json`. Reach for:

- `grilling`: stress-test a design before building it.
- `domain-modeling`: editing GLOSSARY.md or writing an ADR.
- `tdd`: building features or fixes test-first.
- `diagnosing-bugs`: something broken, throwing or slow.
- `code-review` and `pr`: before opening and when describing a PR.

## Agent skills

### Issue tracker

Issues live in GitHub Issues for kristofdegrave/ha-ems, via the `gh` CLI. See `docs/agents/issue-tracker.md`.

### Triage labels

The five default triage labels (`needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`). See `docs/agents/triage-labels.md`.

### Domain docs

Single-context: one `GLOSSARY.md` and `docs/adr/` at the repo root. See `docs/agents/domain.md`.
