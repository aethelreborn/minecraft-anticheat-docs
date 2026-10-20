# 7. Movement Analysis and Prediction Engines

Movement detection has seen the largest advances in the last several years because the underlying physics are largely deterministic.

### 7.1 Limitations of Simple Threshold Checks

Early anticheats measured distance traveled over one or more ticks and compared the result to a static or lightly adjusted maximum speed. Flight checks looked for prolonged airborne time without supporting blocks. These methods caught the most obvious cheats but suffered from:

- High false-positive rates on ice, slime, blue ice, and steep terrain.
- Difficulty handling legitimate high-speed techniques (boat travel, elytra, riptide).
- Relative ease of bypass by cheats that accelerated gradually or stayed just under the numeric limit.
- Poor behavior under server lag or high player ping.

### 7.2 The Predictive / Simulation Approach

Modern engines maintain, for each player, a parallel model of possible movement:

- Starting from the last accepted state (position, velocity, pose, effects).
- Generating candidate input combinations (forward, backward, left, right, jump, sprint, sneak) consistent with the client's reported actions where available.
- Applying the correct friction, gravity, collision response, fluid push, and special-block interactions for the player's Minecraft version and current environment.
- Producing a set of positions and velocities that a legitimate client could have reached.
- Measuring the offset between the client's reported arrival point and the nearest (or best-matching) simulated possibility.
- Accepting the movement if the offset lies inside an uncertainty budget that accounts for latency, floating-point differences, 0.03-block edge cases, and recent disruptive events; otherwise recording a violation.

Specialized sub-engines or movement tickers handle distinct regimes:

- Ground and air movement under normal walking/sprinting/jumping rules.
- Swimming in water and lava, including bubble-column vertical forces.
- Elytra gliding, with pitch-dependent acceleration and firework rocket impulses.
- Vehicle physics for boats, horses, camels, pigs, striders, and others.
- Temporary states such as riptide, levitation, slow falling, and powder-snow burial.

### 7.3 Compensated World and Latency Awareness

Because the client and server can disagree about the exact set of solid blocks (due to chunk loading latency, recent placements, or plugin-generated fake blocks), advanced systems keep a per-player "compensated" world view. This view is updated from the chunk and block packets the server has sent to that specific client, plus predictions of recent client-side placements. Collision checks then use the compensated view, reducing false flags caused by ghost blocks or delayed updates.

Uncertainty handlers further widen or narrow tolerances dynamically. Near a complex staircase, after an explosion, or immediately following a teleport, the allowed offset grows; on flat open ground with low ping it shrinks.

### 7.4 Supporting Movement Checks

Even with a strong prediction core, additional specialized checks remain useful:

- Timer detection via analysis of packet inter-arrival times relative to real wall-clock time and server TPS.
- Ground-spoof detection when the on-ground flag is inconsistent with collision results.
- Phase and noclip detection when the movement path intersects solid volumes that the simulation cannot traverse.
- Vehicle control and speed validation.
- Elytra-specific rules around firework use and pitch extremes.

The combination yields both high detection rates against movement cheats and acceptable false-positive rates when properly tuned.

---

[← Back to Index](../README.md)
