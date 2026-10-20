# 11. Heuristic, Statistical, and Machine-Learning Approaches

Hard simulation works extremely well when the rules are deterministic (most movement). Combat and subtle assistance require looking at aggregate behavior:

- Distributions of inter-click intervals and rotation deltas.
- Entropy measures and tests for excessive regularity.
- Aim-offset statistics relative to target hitboxes over time.
- Cross-correlation among multiple individually weak indicators so that a player who is mildly suspicious on several axes simultaneously accumulates VL faster.

A growing number of systems incorporate machine-learning models trained on large corpora of labeled legitimate and illegitimate combat traces. These models typically output a continuous anomaly or cheat-probability score rather than a binary decision on any single packet. Cloud offloading appears in a few commercial offerings for the heaviest models.

Challenges include obtaining clean training data, handling version and play-style drift, controlling computational cost on the server, and ensuring that the models do not introduce new classes of false positives against skilled legitimate players.

---

[← Back to Index](../README.md)
