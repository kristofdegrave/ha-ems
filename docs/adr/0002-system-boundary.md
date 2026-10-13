# System boundary: entities in and out

ha-ems talks to people and devices only through Home Assistant entities. What informs a person is exposed. What closes the control loop is written directly.

| Direction | Through | Rule |
|---|---|---|
| To people | **Facts**: sensors and binary sensors | ha-ems never sends a notification. HA automations do, and ha-ems ships optional **blueprints** for the common ones (peak warning, deadline unreachable). |
| From people | **Inputs**: switches, selects, dates and numbers owned by ha-ems | The active profile, the departure times and the temporary SOC target are examples. |
| To devices | **Setpoints** on **mapped entities** (`number`, `switch`) | Each setpoint has a **read-back** of the applied value. A setpoint that doesn't land is exposed as a fact. |
| Device specifics | HA config or the device integration | Modbus registers, the Alfen heartbeat and vendor quirks never enter ha-ems. |

## Why

- **Volatility:** notification style and device access both change. Keeping them outside ha-ems means replacing the Tesla with the Volvo, or changing the charger, is a mapping change only.
- **Safety:** a setpoint is part of the control loop. The read-back keeps the loop observable, which a fire-and-forget automation would not.
- **Testable:** tests assert on entity state.

## Considered options

- **A notify adapter inside ha-ems**, as in smart-charging. Dropped: it couples ha-ems to notification channels and message wording, which the user changes more often than the charge logic.
- **Device code inside ha-ems** (direct Modbus). Rejected: every new charger or car would change ha-ems.
