# Appendix A: Conceptual Flow of a Typical Check Cycle

1. A serverbound packet arrives from a client.
2. The anticheat's packet listener or event handler captures it together with the current player state snapshot.
3. Relevant check modules are invoked according to packet type and player state (moving, fighting, digging, etc.).
4. Movement modules run prediction, compute offsets, and consult uncertainty.
5. Combat modules evaluate geometry, rotation history, timing, and companion packets.
6. Packet-order, inventory, and statistical modules update their respective buffers.
7. Any failed checks increment VL according to configured weights.
8. If thresholds are crossed, the configured response pipeline executes (alert, setback, cancel, kick, ban).
9. Internal histories and decay timers are advanced.
10. The original packet continues through normal server processing unless it was cancelled by a mitigation.

This loop executes for every relevant packet under tight performance budgets so that the anticheat does not itself become a source of lag.

---

[← Back to Index](../README.md)
