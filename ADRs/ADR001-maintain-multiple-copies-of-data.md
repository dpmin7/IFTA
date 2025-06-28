# ADR 001: Use "Maintain multiple copies of data" Tactic for UI Display

In our client program (RUI), a large number of objects such as aircraft and airports are displayed on the screen.
Since we render these objects using OpenGL in a dedicated rendering thread, it was essential to identify and apply the most efficient drawing method.

## Decision

We plan to apply a caching strategy. Objects will first be cached on the GPU, and the screen will be rendered based on the cached data.
Rendering will occur at a fixed frame rate (e.g., every 333ms), independent of the frequency of aircraft or object updates.
This means the rendering thread will pull data periodically from the internal aircraft table instead of being triggered directly by data changes.

## Rationale

This decision was made to ensure that the user experience remains unaffected even when rendering a large number of objects on the screen.
When rendering a high volume of objects with OpenGL, the most significant performance bottleneck typically occurs during draw calls (e.g., GL.DrawElements).
The second major bottleneck is the per-frame transfer of vertex data from the CPU to the GPU.

To eliminate this CPU-to-GPU vertex data transfer bottleneck, we decided to apply the "Maintain multiple copies of data" (caching) tactic.
Furthermore, by rendering at a **fixed rate** rather than on every object update, we improve rendering stability and avoid unnecessary redraws, ensuring smoother UI responsiveness.

**Why we chose "Maintain multiple copies of data":**
- Although there are many objects displayed on screen, the number of unique object types is limited (e.g., aircraft, fighters, airports, etc.).
- By caching the model data (VBO) for each object type in the GPU and reusing it, this approach proves to be more efficient than the previous method.
- It is compatible with OpenGL 2.x (our current implementation level), so no additional technical review or OpenGL upgrade is required.
- Periodic rendering ensures consistent performance regardless of object update frequency, and avoids overloading the rendering thread.

This decision was derived from the results of [*Experiment 1*](https://github.com/dpmin7/technical-experiment/blob/L5/experiment1/experiment1.md), where we evaluated the performance of different rendering strategies.
The experiment confirmed that the caching-based rendering approach combined with fixed-rate rendering resulted in a meaningful performance improvement.

**Rejected alternatives and rationale:**
**Instanced Rendering:** Expected to be the fastest in terms of performance. However, since caching already provided a significant improvement and switching to OpenGL 4.x could affect other components, it was not adopted.
**Immediate Mode (Fixed-Function Pipeline):** Already implemented and stable, but rejected due to performance limitations.

## Status

Accepted

## Consequences

Through the application of the "Maintain multiple copies of data" tactic and fixed-interval rendering, we observed a significant improvement in performance.
However, if the number of objects to be rendered increases further, it may be necessary to consider adopting Instanced Rendering as an additional optimization.

