# Appendix C: Combat Geometry and Latency Compensation Examples

Consider a player with 100 ms one-way latency. In the time the attack packet travels, a sprinting target can cover a non-negligible distance. If the anticheat measures reach against the positions known at packet-arrival time without compensation, legitimate hits near the edge of range will be flagged. Expanding the allowed reach by approximately latency × maximum plausible closing speed restores fairness for the lagging player while still rejecting truly impossible distances.

Similar compensation appears in hitbox expansion, in the timing windows for critical hits, and in the interpretation of rotation samples that may themselves be delayed. The art of combat anticheat lies in applying enough compensation to protect legitimate high-ping players without opening large enough windows for cheats to hide inside.

---

[← Back to Index](../README.md)
