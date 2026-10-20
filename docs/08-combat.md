# 8. Combat Analysis and Reach/Aim Detection

Combat is inherently noisier than movement because human aim and reaction times vary widely, and because latency affects the apparent geometry of every engagement.

### 8.1 Reach Calculation

The vanilla maximum distance from the attacker's eye position to the target's hitbox is approximately 3.0 blocks for entity attacks in survival. Block interaction reach is larger (commonly cited around 4.5–5 blocks depending on version and context).

Because of latency, a simple static 3.0 limit produces false positives: by the time the attack packet arrives, the target may have moved closer, or the attacker's reported position may be slightly stale. A common compensation formula expands the allowed reach by a term proportional to estimated one-way latency multiplied by a conservative movement speed (for example, sprint speed). The exact constants are tuned per anticheat and often made configurable.

More accurate implementations perform proper intersection tests between the attack ray (or the attacker's eye point) and the target's axis-aligned or oriented bounding box at the relevant simulation time, rather than relying solely on Euclidean center-to-center distance.

### 8.2 KillAura and Related Automation

Detection signals include:

- Attacks directed at entities outside the player's current look vector (or only inside it for a single tick).
- Rapid target switching that exceeds plausible human reaction and motor limits.
- Multiple entities damaged in the same tick or in an impossibly short window without corresponding rotation changes.
- Attacks that ignore solid obstruction (wall hits) when line-of-sight checks are enabled.
- Missing arm-swing animations that vanilla clients normally emit with each attack.

Some systems have experimented with spawning transient verification entities or using other active challenges; most production systems rely on passive packet and rotation analysis for performance and simplicity.

### 8.3 Aim and Rotation Heuristics

Sequences of yaw and pitch are examined for:

- Instantaneous snaps toward a target that occur only in the attack packet and are immediately reversed ("silent aim").
- Unnaturally low entropy or overly consistent angular speeds.
- Mathematical artifacts related to mouse sensitivity and the greatest common divisor of rotation deltas.
- Statistical correlation between rotation changes and hit success over sliding windows of several seconds.
- Patterns that persist across many different targets and engagement distances.

These checks are among the most actively developed because ghost clients invest heavily in making aim assistance look human.

### 8.4 Click Patterns and AutoClickers

Clicks are observed primarily through arm-animation packets and interaction packets. Beyond raw clicks-per-second ceilings, modern checks analyze:

- Variance and higher moments of the inter-click interval distribution.
- Presence of perfectly periodic or low-entropy timing even when superficial randomization is applied.
- Relationship between click timing and hit registration success.
- Behavior while the player is also moving, placing blocks, or under different latency conditions.

Human motor control produces characteristic variability; many software click generators, even sophisticated ones, leave residual regularity or statistical signatures.

### 8.5 Knockback and Velocity

After a player is damaged, the server applies (and usually sends) a velocity vector. Subsequent movement packets can be compared against the trajectory expected from that velocity plus normal physics. Consistent under-application of knockback is a strong signal. Prediction engines commonly inject recent velocity events into their simulation so that post-hit movement is validated coherently with the rest of the movement model.

### 8.6 Additional Combat Signals

Critical-hit timing, shield or blocking automation, totem re-equip speed, bow draw-and-release consistency, newer mace smash validation, and end-crystal or anchor placement/detonation patterns all receive dedicated attention in full-featured combat modules.

---

[← Back to Index](../README.md)
