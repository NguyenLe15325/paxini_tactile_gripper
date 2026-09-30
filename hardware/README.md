# Safe EEPROM settings for Feetech STS3215 / STS3250 (12 V variants)

Ready-to-load configuration files for **Feetech FT SCServo Debug** (tested with V1.9.8.3).
Each servo has a `*_default.xdat` (factory values, for restoring) and a `*_safe.xdat` (safer limits).

| File | Servo | Purpose |
|---|---|---|
| `sts3215_default.xdat` | STS3215 (12 V) | Factory values, ID 2 |
| `sts3215_safe.xdat` | STS3215 (12 V) | Safer protection settings, ID 2 |
| `sts3250_default.xdat` | STS3250 | Factory values, ID 1 |
| `sts3250_safe.xdat` | STS3250 | Safer protection settings, ID 1 |

> **These files are for the 12 V versions powered from about 12 V.** Do not use them on the 7.4 V STS3215: the voltage window (8-14 V) would shut it down or, worse, allow too much voltage. If your supply is not about 12 V, edit the voltage fields yourself.

## How to load a file

1. Power the servo (12 V) and connect it with the bus adapter. **Connect only the servo you are configuring.**
2. Open FT SCServo Debug, choose the COM port and baud rate (1000000 by default), open the port and click **Search** to find the servo.
3. Go to the **Programming** tab.
4. Load the `.xdat` file with the file load/open button (the label may differ slightly between versions).
5. Write the values to the servo (the write/save button in the same tab). Only the `rw` rows are writable.
6. **Power-cycle** the servo, then re-read the values and check they match the tables below.
7. Test with **no load** first (Debug tab, Auto debug sweep works well) and watch that State stays `Normal`.

## What the safe files change

### STS3250 (`sts3250_safe.xdat`)

| Address | Setting | Factory | Safe | Why |
|---|---|---|---|---|
| 13 | Max Temperature | 80 °C | **70 °C** | Shut down earlier before heat damages the motor or gears |
| 14 | Max Input Voltage | 160 (16.0 V) | **140 (14.0 V)** | 16 V is too high for a 12 V servo |
| 15 | Min Input Voltage | 60 (6.0 V) | **80 (8.0 V)** | A sagging supply trips the alarm instead of browning out under load |
| 16 | Max Torque Limit | 1000 (100%) | **600 (60%)** | Limits how hard a jam or bad command can push |
| 19 | Protection Switch | 45 | **47** | Adds sensor-fault protection |
| 20 | LED Alarm Condition | 45 | **47** | LED flashes on the same faults |
| 35 | Overload Protection Time | 200 (2 s) | **100 (1 s)** | Reacts faster to an overload |
| 36 | Overload Torque | 80% | **50%** | Kept below the 60% cap so it can actually trigger |
| 38 | Over-current Protection Time | 250 (2.5 s) | **100 (1 s)** | Reacts faster to over-current |

### STS3215 (`sts3215_safe.xdat`)

| Address | Setting | Factory | Safe | Why |
|---|---|---|---|---|
| 13 | Max Temperature | 70 °C | **65 °C** | Shut down earlier |
| 15 | Min Input Voltage | 40 (4.0 V) | **80 (8.0 V)** | Trips on a sagging 12 V supply |
| 16 | Max Torque Limit | 1000 (100%) | **600 (60%)** | Limits push force |
| 19 | Protection Switch | 44 | **47** | **Turns on the voltage protection, which was off at the factory**, and adds sensor-fault protection |
| 35 | Overload Protection Time | 200 (2 s) | **100 (1 s)** | Faster overload reaction |
| 36 | Overload Torque | 80% | **50%** | Kept below the 60% cap |
| 38 | Over-current Protection Time | 200 (2 s) | **100 (1 s)** | Faster over-current reaction |

Max Input Voltage stays at the factory 14.0 V on the STS3215, which is already correct for 12 V.

### Left at factory values on purpose

Protection Current (about 2 A), Holding Torque (20%, the torque the servo drops to after an overload trips), PID gains, baud rate, operating mode, position offset and Setting Byte.

## What you MUST still do for your application

1. **Set a unique ID for every servo.** The files carry ID 1 (STS3250) and ID 2 (STS3215). Two servos with the same ID on one bus will conflict. Change the ID with only that one servo connected, then rewrite it.
2. **Set the position limits (addresses 9 and 11).** Both files leave them at 0 and 4095 (no limit). Set them to your joint's real mechanical range, so a bad command cannot drive the joint into a hard stop. If the joint can turn freely, you can leave them.
3. **Check the position offset (address 31).** The files write an offset of 0. If you already calibrated your servos (for example, a robot arm's middle position), loading a file will overwrite that calibration. Note the offset first and put it back, or recalibrate.
4. **Check your supply.** A 12 V supply that regularly reads below 8 V or above 14 V under load will trigger the protections. Use a supply with current limiting or a fuse if you can.
5. **Set Acc and Speed in your code.** Acc 0 and Speed 0 mean maximum speed with no acceleration ramp, which is hard on the gears.

## Tuning notes

- If a servo cannot hold your load, raise **Max Torque Limit** and **Overload Torque** together, always keeping Overload Torque below Max Torque Limit.
- If protection trips under normal use, raise the overload or over-current timers first, before disabling any protection bits.
- Max Torque Limit is the permanent cap stored in EEPROM. The runtime **Torque Limit** (address 48) resets to it at every power-up.
- Protection Switch value 47 means voltage (1) + sensor (2) + temperature (4) + current (8) + overload (32) protection on.

## Limits of this guide

- Register names for addresses 34, 35 and 38 come from a third-party Feetech memory table, not from Feetech's own documentation.
- I did not check the exact 12 V stall current of your servos against a datasheet, which is why Protection Current is left at factory.
- The files were checked by decoding and comparing them byte by byte against the factory files, and the STS3215 safe file was sweep-tested on hardware.
- Use at your own risk, and always test with no load first.
