# Resilient SDR Monitoring and Recovery View  
This view documents the runtime structure of the Raspberry Pi-based Flight Tracker system. It focuses on how the system ensures continuous SDR-based flight data capture and recovery from device-level faults using a heartbeat monitoring strategy.

![Runtime View - Resilient SDR Monitoring](../images/flight-tracker-primary.png)

## Element Catalog

#### systemd
- Initializes and supervises `dump1090` and `adsbhub.sh`
- Automatically restarts services on failure unless blocked by monitoring script

#### dump1090
- Binary program that captures ADS-B signals from the SDR device
- Runs continuously once the SDR is available

#### adsbhub.sh
- Shell script that feeds captured ADS-B data to an external hub or aggregator
- Depends on dump1090 being operational

#### sdrmonitor.sh
- Shell script running in a polling loop
- Checks SDR presence using `lsusb`
- Issues `kill` to stop services if SDR is disconnected

#### SDR (RTL2832U)
- USB-based Software Defined Radio dongle
- May disconnect unpredictably due to hardware issues

## Behavior

![Sequence Diagram – Fault Detection & Recovery](../images/flight-tracker-sequence.png)

- At system boot, `systemd` starts `dump1090` and `adsbhub.sh`.
- `sdrmonitor.sh` starts in parallel and enters a loop:
  - Checks SDR connectivity every 3 seconds.
  - If disconnected, kills both `dump1090` and `adsbhub.sh`.
  - Once SDR is reconnected, `systemd` resumes services automatically.
- This loop creates a resilient, self-healing architecture using a heartbeat tactic.

## Related ADRs
- [Use Heartbeat tactic](../ADRs/ADR005-heartbeat.md)

## Related Views
N/A
