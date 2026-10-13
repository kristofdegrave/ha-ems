# IDesign Method and lean process

ha-ems replaces homeassistant-smart-charging. That project's documentation grew to five times the size of its code, and defects that review had found were never fixed. ha-ems is designed with Juval Lowy's IDesign Method and runs on the lean rules below.

## Method

- **System design:** decompose by volatility into Clients, Managers, Engines, ResourceAccess, Resources and Utilities.
- **Call rules:** Clients call Managers. Managers call Engines and ResourceAccess, and call other Managers only through a queue. Engines call ResourceAccess. ResourceAccess calls Resources. Any layer may call Utilities. Calls never go up or sideways.
- **Project design:** the full method, with activities, a network diagram, the critical path, float and time-cost options.

## Process

| Rule | Meaning |
|---|---|
| Ideas carry over after review | An idea from smart-charging enters ha-ems only after an explicit keep / refine / change / drop decision. Code does not carry over. |
| Fixed document set | GLOSSARY.md, ADRs, one system design and one project design. The GitHub issue is the plan. There are no analysis or plan documents per feature. |
| Findings stay code issues | A review finding about code is tracked by an issue that only a code change closes. |
| Done = seen running | A behaviour issue closes when its tests pass and the behaviour has been observed live or in a replay of real data. |
| Lean writing | Docs prefer tables and diagrams over prose. Code comments explain only a non-obvious *why*. They do not restate requirements or ADRs. |
