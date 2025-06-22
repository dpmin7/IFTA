# ADR 003: Adoption of C#/WPF for GUI Implementation

We evaluated whether C#/WPF would be a suitable replacement technology for the current C++/Embarcadero implementation of our system’s UI and visualization layer. This evaluation was influenced by the desire to improve development maintainability, modernize the technology stack, and assess if equivalent performance and functionality could be achieved using widely supported, modern tools such as .NET and WPF. Resource availability (Windows PCs, existing development experience), and prototyping cost (estimated at 5 person-days) were also factors.

## Decision

We will adopt C#/WPF as a viable alternative implementation technology for the current UI system. This decision is based on the results of a successful prototype that demonstrates equivalent functionality and performance to the current C++/Embarcadero implementation.

## Rationale

The rationale behind this decision is based on the findings from [*Experiment 3*](https://github.com/dpmin7/technical-experiment/blob/L5/experiment3/experiment3.md), which involved developing a C#/WPF-based prototype that implemented the following critical components:

- **UI Layer**: Successfully replicated existing UI layout and interactive elements using WPF.
- **TCP Communication**: Implemented a TCP client that reliably connected, received streaming data, and handled connection states.
- **Data Handling**: Verified real-time message parsing and dispatching logic.
- **OpenGL Rendering**: Integrated OpenTK with WPF and achieved acceptable rendering behavior.
- **Map and Aircraft Display**: Successfully rendered map tiles and aircraft icons with performance comparable to the existing system.

Although the migration effort is non-trivial and requires a certain amount of development investment, it is **technically feasible** and well within reach. Importantly, adopting C# is considered **strategic** for enabling richer UI enhancements and new feature integration as required by the SolveIt use cases. Our team also includes experts familiar with C# and .NET, which reduces the risk and accelerates future development.

Rejected alternatives included:
- Continuing with the existing C++/Embarcadero stack: This was considered less desirable due to maintainability and tooling limitations.
- Switching to cross-platform GUI frameworks like Qt or Avalonia: These were out of scope for this experiment and may be revisited in future ADRs.

Assumptions and constraints:
- Target platform remains Windows-based.

## Status  
Accepted

## Consequences

- **Positive**:
  - Enables future migration from legacy tooling (Embarcadero) to modern, actively supported technologies.
  - Improves maintainability and onboarding for developers familiar with C#/.NET.
  - Unlocks richer tooling and IDE support via Visual Studio.

- **Negative/Neutral**:
  - Requires future effort to complete full system migration (e.g., integration with other modules, full feature coverage).
  - Team members unfamiliar with WPF/OpenTK may require ramp-up time.
  - OpenTK integration is functional but may need optimization or abstraction for advanced rendering scenarios.
