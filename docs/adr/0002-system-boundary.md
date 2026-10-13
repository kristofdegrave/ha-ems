# System boundary: entities in and out

ha-ems talks to people and devices only through Home Assistant entities. What informs a person is exposed. What closes the control loop is written directly.

| Direction | Through | Rule |
|---|---|---|
| To people | **Facts**: sensors and binary sensors | ha-ems never sends a notification. HA automations do, and ha-ems ships optional **blueprints** for the common ones, such as the peak warning. |
| From people | **Inputs**: entities owned by ha-ems, such as switches, selects and dates | The active profile and the "home until" date are examples. |
| To devices | **Setpoints** on **mapped entities**, such as a `number`, `input_number` or `switch` | Each setpoint has a **read-back** of the applied value. A setpoint that doesn't land is exposed as a fact. |
| Device specifics | HA config or the device integration | Modbus registers, the Alfen heartbeat and vendor quirks never enter ha-ems. |

## Why

- **Volatility:** notification style and device access both change. Keeping them outside ha-ems keeps it stable when the Tesla is replaced by the Volvo or the charger changes.
- **Safety:** a setpoint is part of the control loop. The read-back keeps the loop observable, which a fire-and-forget automation would not.
- **Testable:** tests assert on entity state.

## Considered options

- **A notify adapter inside ha-ems**, as in smart-charging. Dropped: notifications become HA automations and blueprints.
- **Device code inside ha-ems** (direct Modbus). Rejected: every new charger or car would change ha-ems.
