# ADR 004: Use Strategy Pattern for Efficient CPA Computation

We received a requirement that the system should support easy extensibility of its functions. Additionally, we consulted with Solvelt Inc., who advised us to define whether the CPA (Closest Point of Approach) computation should be performed for the entire map (i.e., all aircraft) or only for a specific region. Through Experiment 4, we confirmed that performing CPA calculations for all aircraft incurs significant computation time. Therefore, we designed a filtering mechanism that excludes aircraft that are sufficiently distant based on their current positions, as CPA computation is unnecessary for them. When designing the filter module, it was necessary to make it easy for users to modify or add different types of filters.

## Decision

We will use the [Strategy Pattern](https://en.wikipedia.org/wiki/Strategy_pattern) to allow flexible modification and addition of filters.  
By abstracting the logic using an interface, the client code depends only on that interface, and the implementation classes realize the interface.  
Users can create and apply new filters by implementing the `IRangeFilter` interface.

## Rationale

It is inefficient to perform CPA calculations for all aircraft, including those that are not at risk of collision.  
Thus, a filter design was necessary, and the details were described in Experiment 4.

**case1) When using the Strategy Pattern to implement the filter functionality**

- The client code that uses the filter requires minimal changes, and new filters can be easily added by implementing the `IRangeFilter` interface.
    
- Additionally, **filter algorithms can be dynamically switched at runtime**, enabling flexible application of different strategies depending on the situation.
    

**case2) When using a Static Utility approach instead**

- The filtering logic is implemented using static methods with conditional statements, and adding new filters requires modifying the utility method itself.
    
- As a result, the client code becomes tightly coupled to the static utility class, leading to decreased maintainability and scalability.
    

## Status

Proposed

## Consequences

- When a filter needs to be changed, the modifications to client code are minimized.
    
- New filters can be added simply by implementing the `IRangeFilter` interface.