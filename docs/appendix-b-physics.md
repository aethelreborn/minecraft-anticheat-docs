# Appendix B: Detailed Movement Physics Considerations

Minecraft movement is governed by a combination of:

- Base speeds for walking, sprinting, sneaking, and crawling.
- Friction coefficients that differ on ice, slime, soul sand, honey, and normal blocks.
- Gravity and drag that differ in air, water, and lava.
- Collision resolution against the voxel world, including step-up height and the ability to walk over non-full blocks.
- Status effects (Speed, Slowness, Jump Boost, Levitation, Slow Falling, etc.) and attribute modifiers.
- Special movement sources: elytra, fireworks, riptide, vehicles, bubble columns, pistons, and explosions.

A prediction engine must replicate enough of this logic, for each supported client version, that the set of reachable states is accurate. Small discrepancies in friction application order, in the handling of edge cases around 0.03 blocks, or in vehicle passenger physics quickly become either bypasses or false-positive sources. This is why mature prediction engines are large, carefully tested codebases rather than simple speed multipliers.

---

[← Back to Index](../README.md)
