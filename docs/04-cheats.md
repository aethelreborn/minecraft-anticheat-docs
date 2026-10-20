# 4. Categories of Cheats and High-Level Operation

Cheats are grouped by the advantage conferred and by the technical layer they primarily modify. Understanding the categories helps map detection strategies to the right signals.

### 4.1 Movement Cheats

These alter locomotion:

- **Speed and Timer**: Increasing movement speed beyond sprint + potion + attribute limits, or accelerating the client's effective tick rate so that more movement packets are generated per real second.
- **Flight, Hover, Jetpack**: Sustained upward or zero-gravity motion without elytra, creative flight, or other legitimate sources of lift.
- **Step and Spider**: Ascending heights or climbing surfaces that normal collision and step-up rules prohibit.
- **NoFall**: Suppressing or reducing fall damage by manipulating on-ground flags, velocity, or damage application.
- **Jesus / WaterWalk, NoSlow, Phase / Noclip, Blink**: Walking on liquids as solid, ignoring slowdown from cobwebs or soul sand, moving through solid blocks, or temporarily desynchronizing position (blink) to gain bursts of mobility.

High-level operation: The modified client may rewrite local physics before packaging position updates, directly construct position packets with illegal deltas, or change the rate at which the client advances its simulation and emits packets. Because the server ultimately receives a stream of positions, any of these techniques can be challenged by asking whether the sequence of positions is physically reachable under vanilla rules.

### 4.2 Combat Cheats

These improve the outcome of player-versus-player or player-versus-entity fights:

- **KillAura / Forcefield / MultiAura**: Automatically selecting and attacking one or more nearby entities, often without requiring the player to look at them or with the ability to switch targets faster than human reaction allows.
- **Reach**: Extending the effective attack distance beyond the normal approximately 3-block entity reach (or the corresponding block-interaction reach).
- **Aimbot and AimAssist**: Fully automatic or partially assisted rotation toward targets, ranging from silent snaps that exist only for the attack packet to smooth tracking that still exceeds human precision and consistency.
- **AutoClicker**: Generating attack or place inputs at rates or with regularity that exceed normal human capability, even when "randomized."
- **Velocity / Anti-Knockback**: Reducing, delaying, or canceling the knockback that should result from being hit.
- **Criticals, AutoBlock, AutoTotem, W-Tap assistance, and similar**: Automating or perfecting timing-dependent combat techniques.

High-level operation: The client calculates target positions, generates the necessary attack packets (sometimes with fabricated or adjusted rotations), and may suppress or alter knockback application on the local simulation. Silent-aim techniques are particularly interesting because the player's visible rotation may remain natural while the attack packet carries a different look direction.

### 4.3 Visual and Information Cheats

These grant knowledge the player should not possess:

- **X-Ray**: Rendering specific block types (ores, spawners, etc.) as visible through solid stone and dirt.
- **ESP (Extra Sensory Perception)**: Drawing outlines, boxes, or information overlays for players, items, or containers through walls.
- **Tracers, Radar, Extended Minimap**: Lines or map markers showing entity locations beyond normal visibility or radar range.
- **Freecam**: Detaching the camera from the player body to scout safely.

High-level operation: These features almost exclusively modify the client's rendering and memory. They frequently produce no abnormal serverbound packets at all. Consequently, pure server-side analysis sees only the downstream effects (lucky mining patterns, perfect awareness of hidden players) rather than a direct protocol violation. This category is the primary motivation for client-side or hybrid anticheat components.

### 4.4 Inventory, World Interaction, and Protocol Exploits

- FastBreak and Nuker: Breaking blocks faster than tool, enchantment, and hardness calculations permit, or breaking many blocks in a short window.
- Scaffold and Tower: Automated or semi-automated placement of blocks under or in front of the player for bridging or vertical ascent, often with unnatural timing or rotation patterns.
- Inventory automation: Chest stealers, auto-armor, auto-totem, rapid slot switching.
- Illegal items, duplication glitches, and various protocol-level exploits that send malformed or unexpected packet sequences.

High-level operation: These usually leave clearer packet-sequence and timing signatures because inventory windows, dig packets, and place packets have relatively strict expected orders and rate limits in vanilla behavior.

### 4.5 Client Classification: Ghost, Blatant, and Hybrid

- **Blatant clients** maximize advantage and are obvious to both spectators and basic checks (full flight, spinning multi-aura, extreme speed).
- **Ghost clients** deliberately stay close to legitimate statistics: fractional reach increases, humanized click distributions, mild velocity reduction, subtle aim assistance. Their design goal is to raise the detection bar and to look plausible under casual observation.
- **Hybrid clients** expose both styles, often with profiles or "legit mode" toggles.

Effective anticheat design must therefore address both the extreme outliers and the carefully tuned near-legitimate behaviors.

---

[← Back to Index](../README.md)
