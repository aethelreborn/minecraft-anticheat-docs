# 13. Handling Latency, TPS, and False Positives

False positives are more damaging to a community than occasional missed detections, because they punish legitimate players and erode trust in the moderation system.

Primary mitigation techniques:

- Explicit ping-based expansion of reach and movement envelopes.
- Dynamic uncertainty that grows near complex geometry, after teleports, explosions, or vehicle dismounts, and in the presence of known problematic blocks.
- TPS monitoring: when the server itself is lagging, timing-sensitive checks are relaxed or temporarily disabled.
- Version-aware physics tables so that a 1.8 client is not judged by 1.21 movement rules and vice versa.
- Per-player compensated worlds that reflect the blocks the client has actually been sent.
- Extensive internal testing against high-ping connections, various legitimate clients (vanilla, popular optimized clients, modded environments), and edge-case movement styles (parkour, ice highways, boat travel, elytra courses).
- Permission-based or world-based exemptions for staff, for minigames that intentionally break vanilla movement, and for debugging.

Configuration is iterative. Operators typically begin with conservative thresholds, collect data on alerts, and gradually tighten while watching false-positive rates.

---

[← Back to Index](../README.md)
