# 3. Network Packets: The Foundation of Everything

All multiplayer interaction is mediated by the Minecraft network protocol. Packets are structured binary messages with a defined ID and fields. The protocol has evolved significantly; modern versions contain well over one hundred distinct packet types in the Play state alone.

### 3.1 Directionality

- **Serverbound packets** travel from client to server. These include player position and look updates, use-entity (attacks and interactions), player digging (mining start/finish/cancel), block placement, animation (arm swing), inventory click and close, keep-alive responses, and many others.
- **Clientbound packets** travel from server to client. These include entity position and velocity updates, block changes, chunk data, player teleports, inventory synchronization, chat, titles, and scoreboard updates.

### 3.2 Movement Packets in Particular

Typical serverbound movement reporting includes absolute or relative position (x, y, z), rotation (yaw, pitch), and a boolean on-ground flag. In some protocol versions and movement modes the client also reports velocity or input vectors. The frequency of these packets is normally tied to the client's tick rate; a vanilla client sends movement updates regularly even when standing still (to keep the on-ground state and other flags fresh).

### 3.3 Combat Packets

Attacking an entity is reported via a use-entity packet that carries the target entity ID and an interaction type (attack). In vanilla behavior this is almost always accompanied by an arm-animation packet representing the swing. The absence of the expected companion packet, or the presence of attacks without plausible preceding rotations, is a classic logic-check signal.

### 3.4 How Anticheats Observe Packets

On Bukkit, Spigot, Paper, and related platforms, anticheats typically use:

- High-level Bukkit events (PlayerMoveEvent, EntityDamageByEntityEvent, etc.) for convenience, though these can be noisy or post-facto.
- Packet listening libraries such as PacketEvents or ProtocolLib for direct access to the raw protocol messages before or as they are processed.
- Direct NMS (net.minecraft.server) access in more invasive plugins for deeper state inspection.

By examining both the values inside packets and the sequences, timings, and correlations across packets, anticheats build a picture of whether the client's behavior is consistent with an unmodified game under the observed conditions.

A modified client can change field values, alter send rates, suppress expected packets, inject extra packets, or spoof client brand and other metadata. Detection therefore cannot rely on any single packet in isolation; it requires state tracking across time.

---

[← Back to Index](../README.md)
