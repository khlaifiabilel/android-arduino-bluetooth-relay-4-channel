# Android Arduino Bluetooth Relay Controller

An Android and Arduino demonstration for controlling four relay channels over
Bluetooth Classic. The Android app selects an already-paired controller and
sends one-byte commands over an insecure RFCOMM serial connection; the Arduino
sketch reads serial input from an HC-05-style module and toggles pins 4 through
7.

## Important protocol mismatch

The checked-in Android and Arduino components do **not** currently speak the
same command protocol:

- Android sends numeric bytes `1` through `4` for on and `5` through `8` for
  off.
- `arduino/hc05_4relay.ino` expects ASCII characters `A` through `H`.

They will not operate together without aligning one side in a separate code
change. This repository is therefore best treated as reference material, not a
ready-to-deploy controller.

## Hardware represented

- Android device with Bluetooth Classic support
- Arduino-compatible board
- HC-05-compatible serial Bluetooth module at 9600 baud
- Four-channel, active-low relay module on digital pins 4, 5, 6, and 7

The sketch uses Arduino `SoftwareSerial` with pin 2 as RX and pin 3 as TX.
Repository diagrams also cover other relay/Raspberry Pi hardware; they are
reference assets and are not driven by the Android application.

## Android build

The app uses Android Gradle Plugin 3.0.1, Gradle 4.1, SDK 26, the old Android
Support Library, and JCenter. Reproduce it only in an isolated Java 8-era
Android environment.

```bash
git clone https://github.com/khlaifiabilel/android-arduino-bluetooth-relay-4-channel.git
cd android-arduino-bluetooth-relay-4-channel
./gradlew assembleDebug
```

The manifest declares minimum SDK 19, target SDK 26, and Bluetooth/Bluetooth
Admin permissions. Pair the controller in Android system settings before
selecting it in the app. Modern Android releases impose additional Bluetooth
permission and compatibility requirements that this target-26 app does not
implement.

## Arduino sketch

Open `arduino/hc05_4relay.ino` in the Arduino IDE, select the correct board and
port, verify the wiring and relay polarity, then compile before upload. The
sketch initializes every relay output `HIGH` (off for a typical active-low
module).

Do not connect mains voltage on a breadboard or without appropriately rated,
enclosed hardware and qualified supervision. Test first with low-voltage loads.

## Checks

The Android project contains only generated test stubs. The smallest checks are
`./gradlew assembleDebug` in a compatible toolchain and Arduino IDE **Verify**
for the selected board. Hardware behavior requires a paired device and a
low-voltage bench test.

## Configuration and security

No API key or password is required. Android stores the selected device name and
MAC address in app-private shared preferences and uses the standard serial-port
UUID. The RFCOMM socket is explicitly insecure and has no application-level
authentication or encryption; pair only with a trusted controller.

## Provenance

The Android package and source correspond to
[`aagarwal1012/Home-Automation`](https://github.com/aagarwal1012/Home-Automation),
an MIT-licensed project created in 2018. This repository imported that Android
code and added Arduino/relay reference material in 2020; its Git history has two
initial roots from that import. `AwesomeToggle.java` also retains its original
author annotation. This repository is not marked as a GitHub fork, so the
relationship is recorded explicitly here.

## License

The upstream Android project is MIT licensed, but this repository does not
contain its `LICENSE` file. The Arduino `ReadMe.adoc` says "Public Domain" while
also retaining unfilled template fields; that statement applies at most to the
sketch material and is not a clear repository-wide license. No overall license
is asserted here. Review the upstream MIT terms and each bundled asset's origin
before reuse.
