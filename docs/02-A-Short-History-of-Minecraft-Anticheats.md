# Chapter 2 — A Short History of Minecraft Anticheats

## 2.1 The problem appears (2010–2012)

Multiplayer Minecraft inherited its anticheat problem the moment its protocol did. The earliest servers were small and social; moderation was a human activity. But as server hosting became a hobby industry, three forces collided:

- The protocol was documented (the community-maintained wiki.vg effort), which meant anyone could write a modified client.
- The client was a modifiable Java program, and community tooling for bytecode manipulation matured rapidly.
- Competitive game modes — most notably player-versus-player arenas descended from the 1.8 combat system — created real incentives to cheat.

The first anticheats were plugins named after their function. **NoCheat**, by Evenprime, established the template in the early 2010s: intercept player actions, compare them against conservative limits, and revert (set back) violations. Its check names — Speed, Flying, FastBreak, Nuker — are still the industry's vocabulary today.

## 2.2 The NoCheatPlus era (2012–2018)

**NoCheatPlus (NCP)** forked NoCheat in 2012 and became the default answer to server cheating for half a decade. Its design — a continuation built by NeatMonster and Asofold on Evenpret's code base — still runs on thousands of servers. Understanding NCP's architecture, which we examine in depth in Chapter 6, matters because nearly every structural convention in the field descends from it:

- Checks organized into **groups** (moving, fighting, blockbreak, blockplace, chat, combined), each with a config-driven list of narrow sub-checks.
- **Violation levels (VLs)** that accumulate with each flag and decay over time, with configurable actions at thresholds.
- **Setback enforcement:** the server maintains a "last legal position" per player and forcibly teleports violators back to it.
- Heavy use of **ProtocolLib** to observe the packet stream — the project's own documentation notes that ProtocolLib is needed "for full efficiency of the fight checks," a candid admission that the Bukkit event API alone does not expose what a detector needs.

NCP's weakness was generational: its checks compared observed behavior against *hardcoded thresholds and curve tables* derived from vanilla physics. A cheat author who learned the thresholds could design modifications that stayed just inside them. The arms race of the era was exactly this: NCP tightened a check, a client adjusted a slider, the check was bypassed, repeat. The community learned an enduring lesson — **static thresholds lose to adaptive adversaries** — which set the stage for the next generation.

## 2.3 The statistical generation (2016–2020)

A wave of closed-source anticheats — including prominent names such as **Spartan**, **AAC (Advanced Anti-Cheat)**, **Matrix**, and later **Vulcan** — attacked the threshold problem with breadth: many narrow checks per behavior (Vulcan ships an "Aim" check with twenty sub-checks A through T, and an "Auto Clicker" check with the same range), each observing a slightly different signature of the same cheat, so that bypassing one sub-check exposes you to the others. This era also normalized the field's standard tooling:

- **Violation logging and staff alerts** with per-check, per-subcheck granularity.
- **Client brand detection** from the channel payload the client sends at login (forge, vanilla, or a lying client).
- **Ping and transaction-based latency compensation** as an explicit design concern rather than a patch.
- **Discord integrations and punishment pipelines** reflecting servers' operational reality.

The limiting factor of this generation is that each check is a *hand-authored classifier*: a human noticed a signature, wrote a detector, and tuned a threshold. Coverage is bounded by developer attention, and every detector leaks information to the adversary about what is being measured.

## 2.4 The simulation revolution: Grim (2020–present)

**Grim Anticheat** changed the field's physics. Instead of asking "is this movement plausible?", Grim asks "is this movement *identical* to one a real client could produce?" It does so by implementing, server-side, a faithful simulation of Minecraft's movement code across protocol versions, then checking whether the client's reported position is one of the *possible outputs* of that simulation given the player's actual inputs and world. Grim's own description is the clearest statement of the philosophy: rather than relying on "bypassable tricks," it "simulates the client and does calculations using cold, hard math," with checks running on Netty threads for lag-free operation, asynchronous movement processing, efficient world replication, and "ground-breakingly precise player movement replication" across versions from legacy 1.8 servers to modern networks.

The consequences were dramatic:

- **Movement cheats became nearly extinct** on Grim-protected servers. A flight cheat must now produce positions a real client could produce, and the honest client cannot fly.
- **Setbacks became surgical.** Because the simulation knows the full space of legal positions, it can push the player to the nearest legal one instead of a coarse "last legal block."
- **False positives on movement collapsed**, because the check is derived from the game's actual physics rather than an approximation of them.
- **The remaining attack surface shifted** to combat, aim, and statistical cheats — domains where physics simulation does not apply because human input cannot be simulated, only characterized.

Grim is open source, which matters: it made the simulation approach auditable, forkable, and citable, and it explicitly supports the platforms operators actually deploy — Spigot, Paper and forks, Folia, and Fabric, including region-threaded and modded environments.

## 2.5 The cloud and ML generation: Polar (2022–present)

**Polar Anticheat** represents the current frontier, and its public positioning is unusually explicit about its philosophy. Its core ideas:

- **Prevention and mitigation over punishment.** Polar describes its strategy as "prevention and mitigation," applying "mitigations that often involve restricting a cheater's combat abilities, ensuring they have equal or lesser advantage compared to a legitimate player, without always resorting to a ban." The design rationale is behavioral: mitigations are harder for the cheater to notice than a ban, "increasing the time it takes a cheater to return with a fresh account."
- **Cloud-offloaded detection.** Most checks run in Polar's cloud: the server instance "sends relevant player packets to the cloud for inspection," and detections that do not require real-time action "are ran in the cloud," reducing CPU and memory load on the customer's server while allowing "an extended processing period to accurately detect suspicious client behaviour."
- **Machine-learned behavioral models.** Polar advertises "advanced machine learning models that analyze player behavior patterns in real-time to identify sophisticated cheating methods," alongside physics-based "movement simulation … recreating most movement possibilities on the server."
- **Cross-instance tracking.** Because detection is centralized, Polar "tracks cheaters across multiple server instances" — an account-level memory that local plugins cannot achieve.

The trade-offs are structural: a cloud dependency means a third party processes your players' packet data, and mitigation-heavy policy means the operator sees fewer explicit "caught!" moments. But the direction is clear — the field is moving from *detecting cheats in packets* toward *characterizing cheaters across sessions*.

## 2.6 The ecosystem today

The 2020s market structure is remarkably stable, and worth mapping for anyone choosing or building a tool:

| Anticheat | Model | Approach | Position in the field |
|---|---|---|---|
| NoCheatPlus | Free, open source (GPLv3) | Heuristic thresholds, VL system | Legacy workhorse; huge check surface; still maintained by its community continuation |
| Grim AC | Free, open source | Full movement simulation, packet-level | The movement-detection benchmark; runs on Netty threads; supports Paper/Spigot/Folia/Fabric |
| Vulcan | Paid, closed source | Packet-level heuristic, hundreds of sub-checks | Mainstream closed-source choice; detects "blatant cheaters who use common and publicly available hacked clients" |
| Polar | SaaS | Cloud + simulation + ML + mitigation | The frontier: prevention-first, cross-instance, ML-assisted |
| Spartan, Matrix, AAC | Paid | Heuristic/statistical | Established alternatives with their own detection portfolios |

Two supporting libraries underpin nearly all of them. **ProtocolLib** provided the original packet-interception API for Bukkit and remains NCP's recommended companion. **PacketEvents**, founded by retrooper in 2020, is the newer Netty-based protocol library whose wrappers and multi-version support made modern packet-level anticheats far cheaper to build; it is the required dependency of Vulcan and powers Grim's packet access. Chapter 5 covers both in detail.

## 2.7 The other side: the cheat ecosystem

A history that ignores the adversary is a history that cannot explain its own design decisions. The cheat ecosystem evolved through the same generations:

- **The mod-loader era.** Early cheats were simple Minecraft mods (the lineage of "hacked clients" begins with edited jars) exposing toggles for reach, speed, and flying. Detection was easy because the behaviors were blatant.
- **The client era.** Dedicated clients (the families behind names like Flux, Vape, and countless successors) packaged dozens of modules — KillAura, Scaffold, Velocity, Timer — with configuration profiles per server, including per-server bypass lists tuned to specific anticheats. The existence of *per-server bypass profiles* is the clearest possible evidence that the arms race is real and bilateral.
- **The stealth era.** Modern clients minimize their signature: humanized aim curves, randomized click intervals, physics-compliant movement. The industry response — simulation and statistical detection — exists precisely because blatant signatures disappeared.
- **The free-cam and bot era.** Tools like **Baritone** (a pathfinding bot, not a combat cheat) forced the field to detect *automation* rather than *modification*: Vulcan's check list includes a Baritone check precisely because pathfinding bots produce superhumanly consistent navigation.

The recurring lesson of this history is directional: **every generation of cheat removes a signature, and every generation of anticheat moves closer to the ground truth of physics and statistics.** The end state of that trajectory — simulated physics, statistical characterizations of humanity, and cross-session memory — is where the field now stands, and it is where this book spends most of its pages.

---

[← Back to Index](../README.md)
