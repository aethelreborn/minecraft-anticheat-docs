# 12. Violation Levels, Setbacks, and Enforcement

Almost every production anticheat uses some form of buffered scoring:

- Each failed check contributes a quantity of violation level (VL), scaled by severity and sometimes by confidence.
- VL decays toward zero when the player exhibits normal behavior for a period of time.
- Crossing successive thresholds triggers escalating responses: verbose logging, staff alerts (chat, Discord webhooks, etc.), setbacks, cancellation of the offending action, kicks, temporary bans, or permanent bans.
- Many systems expose fine-grained configuration so that server operators can choose different curves for different checks or different worlds.

**Setbacks** deserve special emphasis. By forcibly returning the player to the last position that passed simulation, the anticheat neutralizes the practical benefit of a movement cheat even if the player is not immediately removed. This is often preferable to an instant kick for borderline or first-time detections, and it reduces the incentive to use movement cheats that are only intermittently detectable.

Staff-facing tools commonly include per-player violation history, real-time alerts with context (ping, TPS, check name, VL), freeze commands, and integration points for external punishment or replay systems.

---

[← Back to Index](../README.md)
