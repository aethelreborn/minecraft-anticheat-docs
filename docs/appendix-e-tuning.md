# Appendix E: Configuration and Tuning Philosophy

No default configuration is optimal for every server. Key tuning dimensions include:

- Per-check enable/disable and sensitivity.
- VL thresholds and decay rates.
- Setback versus kick preferences.
- Alert verbosity and destination (in-game, Discord, etc.).
- Exemptions by permission, world, or gamemode.
- Performance limits (how many expensive checks run under high load).

A prudent approach is to enable core movement prediction and basic reach first, observe false-positive and true-positive rates for several days, then incrementally enable and tighten additional combat and heuristic modules while continuing to monitor. Staff should be trained to distinguish clear violations from borderline alerts that may need configuration adjustment rather than immediate punishment.

---

[← Back to Index](../README.md)
