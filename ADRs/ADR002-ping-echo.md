# ADR 002: Use TCP-Based Connection Checks for Network Disconnection Detection and Recovery
Our system must maintain high availability and recoverability in environments where network connections may be intermittently lost. Monitoring network status is critical for triggering user notifications, initiating failover logic, and restarting critical components like ADS-B data feeds.

The initial approach was to use ICMP echo requests (ping) to detect network availability, due to its simplicity and ubiquity. However, during testing, we discovered that the ADS-B server did not respond to ICMP requests even it's alive, making this method unreliable in our target deployment environment.

In addition, frequent monitoring operations must avoid overloading limited devices like Raspberry Pi and should prevent denial-of-service-like behavior toward external services such as the ADS-B Hub.

## Decision
We will adopt a TCP connection attempt-based mechanism for monitoring network availability instead of relying on ICMP echo requests.

- TCP connections to the **ADS-B Hub** and the **local server** will be attempted every **10 seconds**.
- TCP connections to the **Raspberry Pi** will be attempted every **2 seconds**.
- ICMP-based monitoring will not be used, as the target ADS-B servers do not reliably respond to echo requests.

## Rationale
Although ICMP-based detection was initially considered for its lightweight nature, testing showed that the ADS-B server did not respond to ICMP packets, making this approach infeasible.

TCP connection attempts proved to be effective in detecting both disconnection and successful recovery:
- **Internet disconnection/recovery detection** succeeded in all 50 out of 50 test cases.
- **USB disconnection detection between Raspberry Pi and SDR** also succeeded in all 50 out of 50 cases.
- In all scenarios, the system recovered and resumed normal operation within the expected 1-minute window.

We accepted the small overhead introduced by TCP polling in exchange for reliability and deterministic detection.

## Status
Accepted

## Consequences
- **Positive:**
  - Reliable detection of network disconnection and recovery.
  - The system remains resilient even if ICMP is blocked or deprioritized.
  - Verified to meet the design requirement of recovery within 1 minute.

- **Negative:**
  - TCP connection attempts generate slightly more system and network overhead compared to ICMP pings.
  - Connection intervals must be tuned carefully to avoid overwhelming the server or the network.
