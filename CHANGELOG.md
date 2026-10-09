# Changelog

All notable changes to this project are listed here. Versions follow [Semantic Versioning](https://semver.org/).

## [1.0.0] - 2026-10-10

First public release.

- State machine for a complete Ludwieg tube shot: Idle, WaitVac, FillCoarse, FillFine, Hold, Ready, Firing, Logging, Complete, Abort.
- Two-stage driver filling (continuous, then pulsed) with a hold band, pulsed venting and a thermal-soak timer.
- Hardware-timed firing: two-channel camera / valve trigger waveform on the PCIe-6374 AO, with the AI record started from the AO start trigger after a set delay.
- Continuous monitoring of vacuum (Pirani) and driver pressure on a USB-6003; vacuum pump switched off during the shot.
- Interlocks: manual abort, vacuum gauge fault, driver over-pressure; safe shutdown of all outputs.
- Shot data saved as tab-separated text with a time column.
- Documentation: README, formula and constant reference, LabVIEW printouts of every VI.
