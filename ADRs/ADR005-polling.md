# ADR 005: Use polling-based SDR Monitoring and Recovery

We need to ensure the reliable operation of the dump1090 and ADS-B feed services on Raspberry Pi, especially in the presence of potential USB-based SDR (Software Defined Radio) disconnection. The main forces influencing this decision include the need for high availability and resilience, minimal human intervention, and the limitations of inexpensive consumer-grade SDR hardware.

**Technological pressures:** include the unreliability of consumer-grade SDR dongles, the need for compatibility with `systemd`, and limited OS-level awareness of physical hardware faults.<br>
**Cost-related constraints:** arise from the goal to use low-cost, off-the-shelf hardware without redundant SDR units or hardware watchdogs.<br>
**Project-local pressures:** include a desire to minimize implementation effort, reuse standard Linux infrastructure (e.g., `systemd`), and ensure maintainability by team members already familiar with scripting and system-level configuration.<br>

## Decision
We will implement a polling-based monitoring mechanism that checks the physical presence of the SDR device every 3 seconds using `lsusb | grep -q "Realtek Semiconductor Corp. RTL2832U DVB-T"`. If the check fails, a script will forcibly stop `dump1090` and `adsbhub.sh` using `killall -9`, preventing `systemd` from restarting `dump1090` while the hardware is unavailable. Once the hardware is detected again, `systemd` will automatically restart the `dump1090` service and resume ADS-B data feeding.

## Rationale
This decision was made to address situations where the SDR device becomes physically disconnected or unresponsive, which causes `dump1090` to repeatedly restart and fail without meaningful recovery. While `systemd`’s restart capability is useful, it alone cannot detect hardware-level faults. By introducing a polling-based monitoring tactic, we gain fine-grained control over when the service should be allowed to restart.

We chose to use `systemd` instead of writing a custom init script for several reasons:
- `systemd` is a mature and widely adopted standard for service management on modern Linux systems.
- It offers powerful features such as service dependency handling, watchdogs, and automatic restarts.
- Compared to developing a custom init mechanism from scratch, configuring a `systemd` unit file allowed us to implement a robust and reliable boot-time sequence in significantly less time and with lower maintenance cost.

This decision was derived from the results of [**technical-experiment5**](https://github.com/dpmin7/technical-experiment/blob/L5/experiment5/experiment5.md), in which we verified the behavior of `systemd`, `dump1090`, and `adsbhub.sh` under repeated SDR connection and disconnection scenarios. The experiment confirmed that integrating polling-based logic into a systemd-supervised environment significantly increased system resilience with minimal overhead.

Alternative approaches considered:
- **Relying solely on `Restart=always` in systemd**: Rejected because it blindly restarts the service even when the SDR hardware is disconnected, leading to repeated failures without resolution.
- **Adding custom udev rules**: Rejected due to complexity, unpredictability across environments, and difficulty in managing service logic tied to hardware events.
- **Writing a custom init script instead of using `systemd`**: Rejected because `systemd` is a mature and standardized service manager in modern Linux systems. Implementing a custom init system would require more development and maintenance effort for functionality (such as service restart logic, ordering, dependency management, etc.) that `systemd` already handles robustly. By leveraging `systemd`, we saved both development time and operational risk.

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
