# 2. Minecraft Client-Server Architecture Fundamentals

Minecraft multiplayer is a classic client-server model with important asymmetries that directly influence both cheating possibilities and detection strategies.

### 2.1 Responsibilities of the Client

The client (the player's game instance) is responsible for:

- Rendering the world, entities, particles, and user interface.
- Handling local input from keyboard, mouse, controller, or touch devices.
- Performing client-side prediction of movement so that the local player feels responsive even under latency.
- Sending packets that report the player's position, rotation (yaw and pitch), on-ground status, actions (digging, placing, attacking, using items), inventory interactions, and other state.
- Receiving and applying packets that update the world state, other players' positions, entity data, block changes, inventory synchronization, and occasional hard corrections (teleports).

Because the client predicts its own movement and then reports the resulting position, there exists an inherent window of trust. The server often accepts the reported position unless it fails relatively basic sanity checks, such as traveling an impossible distance in a single tick or reporting an illegal stance.

### 2.2 Responsibilities of the Server

The server is responsible for:

- Maintaining the authoritative world state, including block data, entity positions and health, player inventories (in most configurations), and game rules.
- Performing validation of client reports to the degree implemented by vanilla code or installed plugins.
- Broadcasting relevant state changes to other connected clients so that everyone sees a coherent shared world.
- Applying damage calculations, block breaking and placement rules, item usage effects, and other authoritative game logic.
- Managing connections, authentication (in online mode), and plugin-driven extensions.

### 2.3 Key Technical Concepts

**Ticks**: The game simulation advances in discrete steps called ticks. The target rate is 20 ticks per second (each tick lasting 50 milliseconds). Movement physics, combat cooldowns, redstone, entity AI, and many other systems are evaluated on a per-tick basis. When a server cannot maintain 20 TPS, timing assumptions used by both vanilla code and anticheats begin to degrade.

**Latency (Ping)**: The time required for a packet to travel from client to server and for a response to return. High ping means the server is always working with slightly outdated information about the player's actions, and the client has more wall-clock time to move between the moment it sends a packet and the moment the server processes it. Anticheats must explicitly compensate for this or they will flag legitimate high-ping players.

**Client-Side Prediction versus Server Authority**: To hide latency, the client applies movement locally immediately upon input. It then sends the resulting state to the server. If the server later decides the movement was invalid, it can send a correction packet that forces the client back to a server-approved position. This correction is experienced by the player as a "rubber-band" or lag-back. Modern anticheats make extensive use of such corrections (setbacks) as a primary mitigation tool.

**Version Differences**: Minecraft has undergone major changes to combat (the 1.9 combat update), movement (elytra, tridents, soul sand, honey, powder snow, etc.), and protocol structure across versions. An anticheat that works well on 1.8.9 may require substantial adaptation for 1.21+, and vice versa. Cross-version support (via ViaVersion and similar) further complicates physics assumptions because a 1.8 client on a 1.21 server behaves differently from a native 1.21 client.

This architecture explains why purely trusting the client is insufficient and why the dominant modern approach is sophisticated server-side simulation of what a legitimate client could have achieved.

---

[← Back to Index](../README.md)
