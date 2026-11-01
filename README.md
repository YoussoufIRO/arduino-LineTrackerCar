# Arduino Line-Tracker Car

A four-wheel-drive Arduino car that follows a dark line on a light surface using four infrared reflectance sensors. The two centre sensors keep the car on the line; the two outer sensors recover it when the line is lost (sharp turns).

![Wiring of the four IR modules on the motor shield](ConnectionDesInfrarouges.PNG)

## Hardware

| Part | Notes |
|---|---|
| Arduino Uno (or compatible) | |
| L293D motor shield (Adafruit Motor Shield v1 clone, "MH Electronics") | Stacked on the Uno |
| 4 × DC gear motors | On outputs M1, M2 (one side) and M3, M4 (other side) |
| 4 × IR reflectance modules (digital output) | Powered from the shield 5V / GND |
| Battery pack | On the shield's EXT_PWR terminal, motor power only |

### Sensor wiring

| Sensor | Pin |
|---|---|
| Right | A0 |
| Mid-right | A1 |
| Mid-left | A2 |
| Left | A3 |

All four modules share 5V and GND from the shield's spare header (see picture).

## How it works

The loop reads the two centre sensors and reacts in short 50 ms pulses, releasing the motors between pulses to keep the car controllable:

| Mid-left | Mid-right | Action |
|---|---|---|
| line | line | forward |
| no line | line | pivot right |
| line | no line | pivot left |
| no line | no line | stop; after 5 consecutive misses, check the outer sensors and pivot toward whichever still sees the line |

The sketch treats a sensor reading `HIGH` as "line detected". If your IR modules output the opposite polarity, invert the `digitalRead()` tests in `loop()`.

Pivots run both sides at full speed in opposite directions. Forward speed is deliberately low (50 on M1/M2, 75 on M3/M4) so the sensors have time to react; tune `setSpeed()` values and the `delay(50)` to your chassis and motors.

## Building and uploading

1. Install the **Adafruit Motor Shield library (v1)** — `AFMotor.h` — from the Arduino Library Manager or [Adafruit's repository](https://github.com/adafruit/Adafruit-Motor-Shield-library).
2. Open `LineTrackerCar.ino` in the Arduino IDE, select *Arduino Uno* and the right serial port.
3. Upload. The serial monitor (9600 baud) prints `Motor test` at start-up.

## Limitations and ideas

- Control is bang-bang with blocking `delay()` calls — no PID, no smoothing. Works on gentle curves; oscillates on tight ones.
- Sensor polarity and speed values are hard-coded; a short calibration routine at start-up would make the car chassis-independent.
- No obstacle detection.

## License

See [LICENSE](LICENSE).
