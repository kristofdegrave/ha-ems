# Glossary

The shared language of ha-ems. One entry per term; edit via the `domain-modeling` skill.

## Energy

**Solar surplus**:
The power the house exports plus what the flexible assets draw now: the power they could use without importing. One value for the whole house.
_Avoid_: excess, export

**Solar forecast**:
The expected solar production for the coming hours and days.

**Grid share**:
How much of a flexible asset's power may come from the grid while it follows the solar surplus. Solar allows a little, SolarOnly allows none.

**Tariff**:
The price of grid energy at a given time, import or export.

**Home battery**:
The stationary battery that stores and releases energy for the house.

## Peak and fuse

**Month peak**:
The highest quarter-hour average import of the current month, as the meter reports it.

**Effective peak limit**:
The quarter-hour import the house stays under: the month peak, raised to a floor and capped at a configured maximum.
_Avoid_: peak cap

**Quarter-hour budget**:
The energy the house may still import in the current Fluvius quarter-hour without exceeding the effective peak limit.

**Peak protection**:
Keeping every quarter-hour within the effective peak limit by steering the flexible assets.

**Household baseline**:
The power drawn by everything in the house that is not a flexible asset.

**Headroom**:
The power left for the flexible assets: the effective peak limit minus the safety margin and the household baseline. One value for the whole house.

**Fuse clamp**:
The hard, instantaneous limit on the grid connection current. It applies above every profile and override.
_Avoid_: fuse protection

## Assets and vehicles

**Flexible asset**:
A device whose power ha-ems steers, such as the charger or the home battery.
_Avoid_: load, consumer

**Managed vehicle**:
A vehicle configured in ha-ems with its own state of charge, charge limit and location.

**Guest vehicle**:
A vehicle at the charger that is not a managed vehicle. It charges without SOC rules.

**Vehicle at charger**:
The vehicle currently plugged into the charger: a managed vehicle or a guest vehicle.

**Charge session**:
One EV charging run, from plug-in to unplug.

## Charging goals

**Profile**:
A named strategy that sets the priority order between goals and between flexible assets, such as Maximum solar, Manual or Cheapest.
_Avoid_: mode (a mode is what you choose inside the Manual profile)

**SOC target**:
The state of charge a managed vehicle must reach by the deadline. A temporary target replaces it until unplug.

**Solar max**:
The state of charge above the SOC target up to which a vehicle charges from solar surplus only.

**Deadline**:
The next future departure of the vehicle at the charger.
_Avoid_: departure time (that is the input the deadline comes from)

**Required power**:
The power needed to reach the SOC target by the deadline, counting charging loss and the time margin.

**Time margin**:
The time before the deadline by which charging aims to be finished.

**Horizon**:
The period from now until the next real departure.

**Solar reserve**:
The part of the SOC target left for expected solar to fill within the horizon, instead of charging it on the night tariff.

## Anti-cycling

**Anti-cycling**:
Limiting how often charging starts and stops. Changing the current is not limited.

**Hold time**:
How long charging stays at minimum current after the solar surplus drops, before it stops.

**Cooldown**:
The minimum time after ha-ems stops charging before it may start again.

**Start condition**:
Charging starts only when the quarter-hour budget covers the minimum current until the quarter ends.

## System boundary

**Fact**:
A sensor or binary sensor ha-ems exposes for people and automations.
_Avoid_: notification, alert

**Input**:
A switch, select, date or number owned by ha-ems through which a person steers it.

**Mapped entity**:
An HA entity outside ha-ems that the user links to a role ha-ems needs, such as the charger current.

**Setpoint**:
A value ha-ems writes to a mapped entity to control a device.
_Avoid_: command

**Read-back**:
The applied value a device reports after a setpoint, confirming it landed.

**Blueprint**:
An optional HA automation template, shipped with ha-ems, that turns facts into notifications or prompts.

## IDesign Method

**Volatility**:
An area of likely change that the design encapsulates in one component.

**Core use case**:
One of the few essential behaviours of the system. Every other use case is a variation of one.

**Client**:
The entry point a person or another system uses, such as the HA entities and services.

**Manager**:
A component that encapsulates the volatility of a sequence: it orchestrates a use case.

**Engine**:
A component that encapsulates the volatility of an activity, such as a calculation or a rule.

**ResourceAccess**:
A component that encapsulates access to a Resource behind business verbs.

**Resource**:
A physical store or device outside the code, such as an HA entity or the meter.

**Utility**:
A cross-cutting service any component may use, such as logging.
