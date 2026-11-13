# Changelog

All notable changes to this project are listed here. Versions follow [Semantic Versioning](https://semver.org/).

## [1.1.0] - 2026-11-13

### Added
- `Shot_timing.vi`: sets the shot timing from the flow events (valve delay, flow lag, pre-flow margin, gate length, trigger gap) and derives the gate start, camera delay and waveform length. Checks the timing (fail codes 1 to 5, `Timing_OK`, `Timing_msg`); the VI goes to Abort from Idle if the timing is not valid.
- Memory gate: a digital gate pulse on `Dev1/port0/line1`, clocked by the AO sample clock so it is synchronous with the trigger waveform, and shown on the `Trigger_pulse_waveform` graph.
- Multiple runs: `N_runs`, `Run_count`, `Next_run?` and `Last_run?`. After a shot, `Next_run?` returns to WaitVac for the next one; `Last_run?` selects the waveform with or without the camera trigger pulse.
- `Fire_waveform.vi`: gate inputs and `Gate` output, and `Camera_trigger_on` to switch the camera pulse off.

### Changed
- `Pulse_length_ms` and `Camera_delay` are now computed instead of entered.
- `Start_delay_ms` and `Capture_time_ms` renamed `AI_Start_delay_ms` and `AI_Capture_ms`.
- Default timing: `valve_delay` 100 ms, `Valve_pulse_ms` 30 ms, `AI_Start_delay_ms` 115 ms, `AI_Capture_ms` 40 ms.
- Thermal soak time reduced from 5000 ms to 3000 ms.
- Vacuum pump relay moved to `Dev1/port1/line1`.

## [1.0.0] - 2026-10-10

First public release.

- State machine for a complete Ludwieg tube shot: Idle, WaitVac, FillCoarse, FillFine, Hold, Ready, Firing, Logging, Complete, Abort.
- Two-stage driver filling (continuous, then pulsed) with a hold band, pulsed venting and a thermal-soak timer.
- Hardware-timed firing: two-channel camera / valve trigger waveform on the PCIe-6374 AO, with the AI record started from the AO start trigger after a set delay.
- Continuous monitoring of vacuum (Pirani) and driver pressure on a USB-6003; vacuum pump switched off during the shot.
- Interlocks: manual abort, vacuum gauge fault, driver over-pressure; safe shutdown of all outputs.
- Shot data saved as tab-separated text with a time column.
- Documentation: README, formula and constant reference, LabVIEW printouts of every VI.
