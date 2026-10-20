# 5. Server-Side vs Client-Side Anticheat Approaches

### 5.1 Server-Side Anticheats

These run entirely on the Minecraft server as plugins or server-side mods. They see only the packets and game events that reach the server. 

**Strengths**:
- Cannot be turned off by the connecting player.
- Apply uniformly to every client that joins.
- Excel at validating movement physics and combat geometry.
- Can integrate deeply with server state (world, inventories, effects).

**Weaknesses**:
- Completely blind to pure visual modifications that never alter outbound packets.
- Must invest significant engineering in latency and version compensation to avoid false positives.
- Performance cost scales with player count and check complexity.

### 5.2 Client-Side Anticheats

These run on the player's computer, either as a required modification, a separate process, or part of a custom launcher.

**Strengths**:
- Can inspect memory, loaded modules, rendering hooks, and file integrity.
- Capable of detecting many visual and injection-based cheats that leave no server-side trace.

**Weaknesses**:
- Require the player to install and run the software (friction and compatibility issues).
- Sophisticated cheats invest heavily in anti-detection (obfuscation, memory cloaking, timing of injection).
- Raise privacy and trust concerns among players.
- Can themselves become targets for bypass or for malicious impersonation.

### 5.3 Hybrid Models

The most robust public deployments often combine both layers. Server-side systems handle the physics and combat that must cross the network; client-side verification is required for access to ranked queues, tournaments, or other high-stakes modes where visual advantages matter most. Some systems use encrypted handshakes or challenge-response protocols so that the server can confirm a trusted client component is present and active.

---

[← Back to Index](../README.md)
