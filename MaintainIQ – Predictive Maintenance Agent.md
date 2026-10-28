# 🛠️ MaintainIQ

AI maintenance agent for a Tata Steel–style rolling-mill plant: predict failures from sensors, history and the production schedule, then plan maintenance. (Synthetic illustrative data.)

## Machine health – ranked by expected loss (click a row)

## Detail

Sensor readings as % of healthy baseline (last 8 days)

## Agent

Ask the agent. It reasons over the live scores in the table.

## What-if

## Algorithm used by the agent

1. **Ingest** daily vibration, temperature and motor current, maintenance history (days since service, interval, failures in 12 months) and the production schedule (load, cost per downtime hour, next planned shutdown window).
2. **Baseline & anomaly**: per sensor, `z = (recent − baseline mean) ÷ baseline spread`. Anomaly score `A` = average of clipped z-scores (0–6).
3. **Overdue ratio** `O = days since service ÷ service interval`.
4. **Failure probability (7-day)** `p = 1 ÷ (1 + e^−x)` with `x = −3.2 + 0.55·A + 0.9·max(0, O−0.8) + 0.25·past failures`.
5. **Remaining useful life**: vibration trend extrapolated to the alarm limit (7.1 mm/s).
6. **Expected loss** `p × repair hours × downtime cost per hour × load`, compared to the planned-maintenance cost to get savings.
7. **Schedule**: if RUL is shorter than the next planned window, act now; otherwise bundle the work into that window to avoid peak-production downtime.
8. **Diagnose pattern** from the dominant sensor: vibration → bearing/alignment, temperature → cooling/lubrication, current → motor/electrical, none → routine service.