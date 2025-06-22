# ADR 004: Use Strategy Pattern for Efficient CPA Computation

We received a requirement stating that the system must support easy extensibility of its functions. Additionally, after consulting with Solvelt Inc., we were advised to define whether the CPA (Closest Point of Approach) functionality should operate across the entire map (i.e., for all aircraft) or only within a specific region. Through Experiment 4, we confirmed that performing CPA calculations for all aircraft incurs significant computation time. Therefore, we designed a filter to exclude aircraft that are sufficiently distant and do not require CPA computation based on their current positions.

While designing the filter module, we aimed to make it easy for users to modify or add new types of filters.

## Decision

We will adopt the [Strategy Pattern](https://en.wikipedia.org/wiki/Strategy_pattern) to enable flexible modification and extension of filters.

By abstracting the filter logic through an interface, the client code depends only on that interface, and the implementation code realizes it. Users can create and apply new filters by implementing the `IRangeFilter` interface.

## Rationale

It is inefficient to perform CPA calculations for all aircraft, including those that are not at risk of collision. Thus, we needed to design a filter, as described in Experiment 4. The filter and CPA computation modules are designed with the following structure:

<img src="../images/adr004-pipefilter1-diagram.png" width="400">

**Case 1) When the strategy pattern is not used to implement filter functionality**

- If the pattern is not used, modifying or adding filters causes the client code to depend on multiple filter classes, which increases the amount of modification required in the client.
    

<img src="../images/adr004-normalfilter-diagram.png" width="400">

**Case 2) When the strategy pattern is used to implement filter functionality**

- Client code modifications are minimized, and new filters can be added by simply conforming to the `IRangeFilter` interface.
    

<img src="../images/adr004-pattern_filter-diagram.png" width="400">

## Status

Proposed

## Consequences

- When a filter needs to be changed, modifications to the client code are minimized.
    
- New filters can be added by implementing the `IRangeFilter` interface.
