---
name: ha-review
description: Reviews ha-ems code changes against Home Assistant integration practice and provides constructive feedback. Use when a review of integration code is requested, alongside the mattpocock code-review, for a consistent review behavior and output format.
---

# Review Code Changes

Adapted from [home-assistant/core](https://github.com/home-assistant/core/tree/4eb235287822d33574cb138104a2754b8f3ad2e5/.claude/skills/ha-review) (Apache-2.0, see [LICENSE](../LICENSE-home-assistant-core.md)) for a custom integration distributed through HACS.

## Scope:
- Unless instructed otherwise, review the full changes (the ones from the branch plus uncommitted ones) against `main`. Review `git diff "$(git merge-base origin/main HEAD)"`.
- Judge integration code against the `ha-integration-knowledge` skill.

## Analyze the code changes for:
- Code quality and style consistency
- Potential bugs or issues
- Performance implications
- Security concerns
- Test coverage
- Documentation updates if needed

## Quality scale:
- If the changes include `quality_scale.yaml`, or touch code that a rule at the current tier covers, run a subagent to verify those rules, following the `ha-quality-scale-verify` skill.
- Include the verification results in the final review comments.

## Verification:
- After the review, run parallel subagents for each finding to double-check it.
- Spawn up to a maximum of 10 parallel subagents at a time.
- Gather the results from the subagents and summarize them in the final review comments.

## IMPORTANT:
- Just review. DO NOT make any changes.
- Be constructive and specific in your comments.
- Suggest improvements where appropriate.
- No need to run tests or linters yourself; the quality scale subagent runs its own checks.
- No need to highlight things that are already good.

## Output format:
- List specific comments for each file/line that needs attention.
- In the end, summarize with an overall assessment (approve, request changes, or comment) and bullet point list of changes suggested, if any.
  - Example output:
    ```
    Overall assessment: request changes.
    1. [CRITICAL] sensor.py:143 - Memory leak
    2. [PROBLEM] coordinator.py:87 - Inefficient algorithm
    3. [SUGGESTION] test_init.py:45 - Improve x variable name
    ```
  - Make sure to include the file and line number when possible in the bullet points, so each one can be posted as an inline PR comment.
