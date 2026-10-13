---
name: ha-integration-knowledge
description: Everything you need to know to build, test and review the ha-ems Home Assistant custom integration (HACS, Python). If you're writing or reviewing code under custom_components/ or tests/, you must use this as your primary reference.
---

Adapted from [home-assistant/core](https://github.com/home-assistant/core/tree/4eb235287822d33574cb138104a2754b8f3ad2e5/.claude/skills/ha-integration-knowledge) (Apache-2.0, see [LICENSE](../LICENSE-home-assistant-core.md)) for a custom integration distributed through HACS.

## File Locations
- **Integration code**: `./custom_components/<domain>/`
- **Integration tests**: `./tests/`, run with `pytest-homeassistant-custom-component`
- **Translations**: `./custom_components/<domain>/translations/en.json`, edited directly (custom integrations have no `strings.json` build step)
- **HACS metadata**: `./hacs.json`; `manifest.json` carries `domain` (matching the directory), `name`, `version`, `documentation` (not a home-assistant.io URL), `issue_tracker`, `codeowners` and `iot_class`

## General guidelines

- When looking for examples, prefer integrations with the platinum or gold quality scale level in [home-assistant/core](https://github.com/home-assistant/core/tree/dev/homeassistant/components) first.
- Polling intervals are NOT user-configurable. Never add scan_interval, update_interval, or polling frequency options to config flows or config entries.
- Do NOT allow users to set config entry names in config flows. Names are automatically generated or can be customized later in UI. Exception: helper integrations may allow custom names.
- For entity actions and entity services, avoid requesting redundant defensive checks for fields already enforced by Home Assistant validation schemas and entity filters; only request extra guards when values bypass validation or are transformed unsafely.
- When validation guarantees a key is present, prefer direct dictionary indexing (`data["key"]`) over `.get("key")` so invalid assumptions fail fast.
- Device protocols, registers and vendor quirks stay out of ha-ems; they belong to the device integration ([ADR 0002](../../../docs/adr/0002-system-boundary.md)).
- Do not work around limitations of another integration or library inside ha-ems. File the fix upstream.
- Where a rule here conflicts with an ADR in `docs/adr/`, the ADR wins.

The following platforms have extra guidelines:
- **Diagnostics**: [`platform-diagnostics.md`](platform-diagnostics.md) for diagnostic data collection
- **Repairs**: [`platform-repairs.md`](platform-repairs.md) for user-actionable repair issues

## Entity platforms

- Ensure `async_added_to_hass()` and `async_will_remove_from_hass()` have symmetrical behavior. For example, if a subscription is created in `async_added_to_hass()`, it should be unsubscribed in `async_will_remove_from_hass()`. Also, if something is torn down in `async_will_remove_from_hass()`, it should be set up in `async_added_to_hass()`.
- Entity base class (e.g. `SensorEntity`, `NumberEntity`) provide a stable API for child classes to inherit from. Do not suggest redeclaring or duplicating attributes, properties, or methods the base class already provides, and do not add guards against the parent's behavior changing — rely on the base class instead.

## Integration Quality Scale

- Rules: https://developers.home-assistant.io/docs/core/integration-quality-scale/rules
- The target tier is the `quality_scale` key in `manifest.json`. When implementing or reviewing, always consider the rules for that tier.
- Track rule status in `custom_components/<domain>/quality_scale.yaml`, starting from the [core template](https://github.com/home-assistant/core/blob/dev/script/scaffold/templates/integration/integration/quality_scale.yaml). hassfest skips this file for custom integrations, so it is a self-kept checklist:
  - `done`: Rule implemented
  - `exempt`: Rule doesn't apply (with reason in comment)
  - `todo`: Rule needs implementation
- Bronze rules always apply; higher tiers apply up to the target tier.

## Testing Requirements

- Tests drive the integration through Home Assistant (config entries, states, services), not through its internals. See https://developers.home-assistant.io/docs/development_testing/#writing-tests-for-integrations
- Custom integrations load in tests only with the `enable_custom_integrations` fixture; enable it autouse in `tests/conftest.py`. Request fixtures such as `recorder_mock` before it.
- Set pytest-asyncio `asyncio_mode = auto`.
