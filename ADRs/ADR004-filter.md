# ADR 004: Use Filters for Efficient CPA Computation

## Context

Our system is required to compute CPA (Closest Point of Approach) between aircraft to assess potential collision risks. Based on communication with the customer, the CPA function may be applied either to all aircraft globally or to aircraft within a selected region.
In **Experiment 4**, computing CPA for all aircraft (without any filtering) took **13.48 seconds**, which is not suitable for real-time or near-real-time use. To reduce unnecessary computation, we applied filtering techniques and observed significantly improved performance.

## Decision

We will apply **filters using aircraft's latitude, longitude, and altitude** to reduce the number of aircraft pairs for which CPA must be computed.

## Rationale

Performing CPA computations between all aircraft regardless of their relative positions is highly inefficient, especially in high-density traffic scenarios.

We compared two alternative approaches to reduce computation:

1. **Filtering aircraft that do not require CPA calculation**

   - _Advantages_: Significantly reduces computation; high extensibility (can add various filters like range, altitude difference, region-based, etc.)
   - _Disadvantages_: If filter thresholds are too conservative, there's a risk of missing valid CPA cases.

2. **Computing CPA only for newly updated aircraft**
   - _Advantages_: Reduces computations when updates are sparse.
   - _Disadvantages_: If many aircraft update simultaneously, CPA must be calculated between updated aircraft and all others, potentially leading to more computations than filtered approach. Also, risk of missing mutual CPA between updated aircraft unless handled carefully.

Given these trade-offs, the filter-based approach provides a **more consistent and scalable** solution for both periodic and real-time CPA computation. This decision was derived from the results of [*Experiment 4*](https://github.com/dpmin7/technical-experiment/blob/L5/experiment4/experiment4.md).

## Status

Proposed

## Consequences

- Filtering leads to a simpler and more efficient implementation, especially for **periodic CPA evaluations**.
- Allows for future extension by adding modular filter strategies (e.g., by distance, altitude, airspace region).
- Requires careful tuning of filter thresholds to avoid overlooking potential collision candidates.
