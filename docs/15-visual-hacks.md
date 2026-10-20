# 15. Visual Hacks and Why Server-Side Alone Is Insufficient

Because x-ray, ESP, and similar features operate entirely inside the client's rendering and memory spaces, they need not generate any abnormal serverbound traffic. The server observes a player who finds ores at an unusually high rate or who always seems to know where opponents are hiding, but the packets themselves can be indistinguishable from legitimate play.

Server-side responses are therefore limited to:

- Statistical heuristics on mining or movement patterns (noisy).
- Increased logging and staff attention for suspicious players.
- World-generation and resource-distribution choices that reduce the payoff of pure information advantages.
- Requirements that players in sensitive modes run additional client-side verification.

This is the fundamental reason hybrid architectures exist.

---

[← Back to Index](../README.md)
