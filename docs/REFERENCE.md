# Reference: formulas, constants and thresholds

All values below are the ones in the VIs as shipped. They are constants on the block diagram or default values of subVI controls, and must be adapted to the sensors and the facility in use.

---

## 1. State machine thresholds (`0Ludwieg_automation.vi`)

| State | Quantity | Value | Meaning |
|---|---|---|---|
| WaitVac | `Vac_cutoff_Pa` | `1.1 x P_vac_target` | Vacuum is good enough to start filling |
| FillCoarse | `Fill_cutoff_Pa` | `0.9 x P_driver_target` | End of continuous filling |
| FillFine | upper limit | `0.995 x P_driver_target` | End of pulsed filling |
| FillFine | inlet pulse | 150 ms open in every 1000 ms | Pulsed fill |
| Hold | lower band | `0.995 x P_driver_target` | Below: back to FillFine |
| Hold | upper band | `1.02 x P_driver_target` | Above: exhaust valve pulsed |
| Hold | exhaust pulse | 150 ms open in every 1000 ms | Pulsed vent |
| Hold | `thermal_soak_ms` | 5000 ms | Time in band before Ready |
| all | over-pressure abort | `P_driver > 1.2 x P_driver_target` | Goes to Abort |
| all | loop period | 100 ms | Main loop wait |

SSR outputs are 10 V (on) or 0 V (off).

## 2. Acquisition (`0Ludwieg_automation.vi`)

| Item | Value |
|---|---|
| Monitoring AI (USB-6003) | 2 channels, continuous, `Global_driver_rate` (1000 Hz), 50 000-sample buffer, 0 to 10 V |
| Shot AI (PCIe-6374) | 4 channels, finite, `Frequency (Hz)` (300 kHz), `N = Frequency x Capture_time_ms / 1000` |
| Shot AI trigger | digital edge on `/Dev1/ao/StartTrigger`, start delay `Start_delay_ms / 1000` s |
| Fire AO (PCIe-6374) | 2 channels, 0 to 10 V, finite, `Global_driver_rate`, regeneration allowed, on-board memory only |
| Timing condition | `Start_delay_ms + Capture_time_ms < Pulse_length_ms` |

Graph scales: `6374_data_output` and `Settling_chamber_pressure` use `dt = 1000 / Actual_AI_Rate` ms with an offset of `Start_delay_ms`; `Control_pressures` uses 0.1 s per point.

## 3. Trigger waveform (`Fire_waveform.vi`)

```
N            = round(rate x pulse_length_ms x 0.001)
row 0 (ao0)  camera pulse: amplitude for round(rate x camera_pulse_ms x 0.001) samples,
             starting at round(rate x camera_delay x 0.001)
row 1 (ao1)  valve pulse:  amplitude for round(rate x valve_pulse_ms x 0.001) samples,
             starting at round(rate x valve_delay x 0.001)
amplitude    4.9 V (default)
```

Notes on the block diagram: "camera delay is twice the camera pulse" and "valve delay is twice the valve pulse". Check the `Trigger_pulse_waveform` graph for the actual pulse positions before each shot.

## 4. Vacuum gauge (`Pirani_V_to_Pa.vi`)

```
log10(P_mbar) = linear interpolation of logP_table at V_in in V_table
P_mbar        = 10^(log10 P_mbar)
P_Pa          = 100 x P_mbar
Sensor_fault  = V_in < 1.25 V
At_atm        = V_in >= 9.99 V
```

Calibration table (55 points, 1e-3 to 1000 mbar):

| V_table [V] | log10(P / mbar) |
|---|---|
| 1.320000 | -3.000000 |
| 1.330000 | -2.698970 |
| 1.360000 | -2.522879 |
| 1.400000 | -2.397940 |
| 1.430000 | -2.301030 |
| 1.470000 | -2.221849 |
| 1.510000 | -2.154902 |
| 1.540000 | -2.096910 |
| 1.570000 | -2.045757 |
| 1.960000 | -2.000000 |
| 2.230000 | -1.698970 |
| 2.360000 | -1.522879 |
| 2.510000 | -1.397940 |
| 2.660000 | -1.301030 |
| 2.820000 | -1.221849 |
| 2.940000 | -1.154902 |
| 3.080000 | -1.096910 |
| 3.160000 | -1.045757 |
| 3.260000 | -1.000000 |
| 3.940000 | -0.698970 |
| 4.450000 | -0.522879 |
| 4.910000 | -0.397940 |
| 5.310000 | -0.301030 |
| 5.630000 | -0.221849 |
| 5.910000 | -0.154902 |
| 6.160000 | -0.096910 |
| 6.350000 | -0.045757 |
| 6.500000 | 0.000000 |
| 7.230000 | 0.301030 |
| 7.810000 | 0.477121 |
| 8.150000 | 0.602060 |
| 8.340000 | 0.698970 |
| 8.490000 | 0.778151 |
| 8.590000 | 0.845098 |
| 8.690000 | 0.903090 |
| 8.760000 | 0.954243 |
| 8.880000 | 1.000000 |
| 9.150000 | 1.301030 |
| 9.220000 | 1.477121 |
| 9.280000 | 1.602060 |
| 9.310000 | 1.698970 |
| 9.330000 | 1.778151 |
| 9.350000 | 1.845098 |
| 9.360000 | 1.903090 |
| 9.380000 | 1.954243 |
| 9.390000 | 2.000000 |
| 9.410000 | 2.301030 |
| 9.450000 | 2.477121 |
| 9.510000 | 2.602060 |
| 9.570000 | 2.698970 |
| 9.650000 | 2.778151 |
| 9.750000 | 2.845098 |
| 9.850000 | 2.903090 |
| 9.940000 | 2.954243 |
| 10.010000 | 3.000000 |

## 5. Driver transducer (`Druck_to_Pa.vi`)

```
frac       = (V_in - V_zero) / V_FS
P_gauge_Pa = frac x P_FS_gauge_Pa
P_abs_Pa   = P_gauge_Pa + P_ambient_Pa
```

| Constant | Default |
|---|---|
| `V_FS` | 5 V |
| `P_FS_gauge_Pa` | 1.4e6 Pa (14 bar gauge) |
| `P_ambient_Pa` | 94 000 Pa |
| `V_zero` | 0.0353 V |

## 6. High-speed pressure sensor (`Settling_V_to_Pa.vi`)

```
N_b     = round(baseline_ms x 0.001 x Actual_AI_Rate)
V_0     = mean(V[0 : N_b])
P_Pa    = (V - V_0) x 6894.76 / (sens_mV_per_psi x gain x 0.001)
```

| Constant | Default |
|---|---|
| `sens_mV_per_psi` | 1.26 mV/psi |
| `gain` | 1 |
| `baseline_ms` | 40 ms |

The result is the pressure change from the start of the record (piezoelectric-type sensor).

## 7. Units (`Units_to_Pa.vi`)

| Units | Factor to Pa |
|---|---|
| Pa (default) | 1 |
| kPa | 1000 |
| bar | 100 000 |
| psi | 6894.76 |

## 8. Ratiometric transducer (`Volts_to_pa.vi`, not used by the main VI)

```
frac         = (V_in - 0.10 x V_supply) / (0.80 x V_supply)
P_Pa         = frac x (P_max_Pa - P_min_Pa) + P_min_Pa
Out_of_range = V_in < 0.08 x V_supply  or  V_in > 0.92 x V_supply
```

Defaults: `V_supply` = 5.13 V, `P_min_Pa` = 0, `P_max_Pa` = 103 421 Pa (15 psi).

## 9. Data file (`Write_data_to_file.vi`)

```
t_i  = Start_delay_ms + i x 1000 / Actual_AI_Rate      [ms]
row  = t_i  V_sensor0  V_sensor1  V_sensor2  V_sensor3
```

Tab separated, `%.6f`, default name suffix `_NI6374.txt`, written to the file chosen in the dialog (starting in `Start_path`).
