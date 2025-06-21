# ADR 005: Use Heartbeat Tactic for SDR Monitoring and Recovery

We need to ensure the reliable operation of the dump1090 and ADS-B feed services on Raspberry Pi, especially in the presence of potential hardware failures (e.g., SDR disconnects). The main forces influencing this decision include the need for high availability, minimal human intervention, and the limitations of inexpensive consumer-grade SDR hardware.

## Decision
We will implement a heartbeat-based monitoring mechanism that checks the physical presence of the SDR device every 3 seconds using `lsusb | grep -q "Realtek Semiconductor Corp. RTL2832U DVB-T"`. If the check fails, a script will forcibly stop `dump1090` and `adsbhub.sh` using `killall -9`, preventing `systemd` from restarting `dump1090` while the hardware is unavailable. Once the hardware is detected again, `systemd` will automatically restart the `dump1090` service and resume ADS-B data feeding.

## Rationale
This decision was made to address situations where the SDR device becomes physically disconnected or unresponsive, which causes `dump1090` to repeatedly restart and fail without meaningful recovery. While `systemd`’s restart capability is useful, it alone cannot detect hardware-level faults. By introducing a heartbeat tactic, we gain fine-grained control over when the service should be allowed to restart.

We chose to use `systemd` instead of writing a custom init script for several reasons:
- `systemd` is a mature and widely adopted standard for service management on modern Linux systems.
- It offers powerful features such as service dependency handling, watchdogs, and automatic restarts.
- Compared to developing a custom init mechanism from scratch, configuring a `systemd` unit file allowed us to implement a robust and reliable boot-time sequence in significantly less time and with lower maintenance cost.

This decision was derived from the results of [**technical-experiment5**](https://github.com/dpmin7/technical-experiment/blob/L5/experiment5/experiment5.md), in which we verified the behavior of `systemd`, `dump1090`, and `adsbhub.sh` under repeated SDR connection and disconnection scenarios. The experiment confirmed that integrating heartbeat-based logic into a systemd-supervised environment significantly increased system resilience with minimal overhead.

Alternative approaches considered:
- Relying solely on `Restart=always` in systemd: Rejected because it blindly restarts the service even when hardware is disconnected.
- Adding custom udev rules: Rejected due to complexity and lack of consistency across devices and environments.

Assumptions:
- SDR hardware will be correctly identified via `lsusb`.
- Reconnecting the SDR will restore systemd-managed services without manual intervention.

## Status
Accepted

## Consequences
- ✅ System is more resilient to hardware disconnection events.
- ✅ False restarts due to missing hardware are prevented.
- ✅ Service automatically resumes when hardware is restored.
- ⚠ Slight increase in system complexity due to custom monitoring script.
- ⚠ Requires root privileges and process-killing logic, which may raise maintainability or safety concerns if misused.
