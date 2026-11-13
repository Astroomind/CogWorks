# CogWorks game design

Every number the game needs is on this page. When an issue says "use the values from the design doc", this is the place.

These are starting values. The "Balance pass" issue is where they get tuned after playing the game for real.

## The loop

1. Click to mine Iron Ore.
2. Spend ore on Drills, which mine ore on their own.
3. Spend ore on Furnaces, which turn ore into Iron Plates.
4. Spend plates on Gear Presses, which turn plates into Gears.
5. Gears pay for upgrades and unlock the copper line.
6. When progress slows down, rebuild the factory for Blueprints, which make everything faster forever.

## Resources

| Id | Name | Comes from |
|----|------|------------|
| `iron-ore` | Iron Ore | Clicking, Drill |
| `iron-plate` | Iron Plates | Furnace |
| `gear` | Gears | Gear Press |
| `copper-ore` | Copper Ore | Copper Drill |
| `copper-plate` | Copper Plates | Copper Furnace |
| `circuit` | Circuits | Assembler |

The `Id` is what the code uses. The `Name` is what the player sees.

## Machines

Rates are per second, for one machine.

| Id | Name | First one costs | Uses | Makes | Appears when |
|----|------|-----------------|------|-------|--------------|
| `drill` | Drill | 10 Iron Ore | nothing | 1 Iron Ore | Always |
| `furnace` | Furnace | 50 Iron Ore | 2 Iron Ore | 1 Iron Plate | 30 Iron Ore produced in total |
| `press` | Gear Press | 40 Iron Plates | 2 Iron Plates | 1 Gear | 10 Iron Plates produced in total |
| `copper-drill` | Copper Drill | 100 Gears | nothing | 1 Copper Ore | 50 Gears produced in total |
| `copper-furnace` | Copper Furnace | 150 Gears | 2 Copper Ore | 1 Copper Plate | 20 Copper Ore produced in total |
| `assembler` | Assembler | 100 Copper Plates | 1 Iron Plate + 2 Copper Plates | 1 Circuit | 50 Copper Plates produced in total |

## Upgrades

Each upgrade can be bought once.

| Id | Name | Description | Affects | Effect | Cost |
|----|------|-------------|---------|--------|------|
| `sharper-bits` | Sharper Bits | Drills mine twice as fast. | `drill` | output x2 | 100 Iron Plates |
| `hotter-fires` | Hotter Fires | Furnaces smelt twice as much from the same ore. | `furnace` | output x2 | 100 Gears |
| `oiled-gears` | Oiled Gears | Gear Presses make twice as many gears. | `press` | output x2 | 250 Gears |

## Formulas

**Machine cost.** Each machine you own makes the next one 15% more expensive, rounded up to a whole number:

```
cost of next = round up( first cost x 1.15 ^ number owned )
```

So Drills cost 10, 12, 14, 16, 18, 21, 24 and so on.

**Production.** Every tick, each kind of machine does this:

```
made = rate x number owned x seconds x efficiency x upgrade multiplier x blueprint multiplier
```

**Efficiency.** A machine that needs an input can only run as fast as its input allows. If 4 Furnaces want 8 ore this second and only 4 ore exist, they run at 50% and make half as much. A machine with two inputs runs at the speed of whichever input is scarcest.

**Offline progress.** When the game loads, it works out how long you were away and runs the factory for that long, up to a maximum of 8 hours.

**Prestige.** Rebuilding the factory gives Blueprints based on the Gears made in this run:

```
blueprints gained = round down( square root( gears made this run / 1000 ) )
```

Each Blueprint adds 10% to all production, forever.

**Score.** Every Gear ever made, across all runs. This is what goes on the leaderboard.

## Achievements

| Id | Name | Unlocks when |
|----|------|--------------|
| `first-click` | Getting Your Hands Dirty | You mine by hand once |
| `first-drill` | Automation! | You own 1 Drill |
| `ten-drills` | Drill Sergeant | You own 10 Drills |
| `first-gear` | Cog in the Machine | 1 Gear produced |
| `first-circuit` | Going Electric | 1 Circuit produced |
| `first-prestige` | Back to the Drawing Board | You rebuild the factory once |

## Colours

The page uses CSS variables so every colour is defined once.

| Variable | Value | Used for |
|----------|-------|----------|
| `--bg` | `#14171c` | Page background |
| `--panel` | `#1e232b` | Panels and the top bar |
| `--border` | `#2e3541` | Borders and empty bars |
| `--text` | `#e6e9ee` | Main text |
| `--muted` | `#8a94a3` | Labels and hints |
| `--accent` | `#f2a900` | The brand, owned counts, anything that should stand out |
| `--good` | `#4caf7d` | Positive rates, healthy machines |
| `--bad` | `#e5604d` | Negative rates, starved machines, dangerous buttons |

## Target pacing

Rough targets for a new player. The balance pass checks the game against these.

| Moment | Should happen at about |
|--------|------------------------|
| First Drill | 15 seconds |
| First Furnace | 1 minute |
| First Gear | 3 minutes |
| Copper Drill unlocked | 8 minutes |
| First Circuit | 15 minutes |
| First rebuild is worth doing | 30 minutes |
