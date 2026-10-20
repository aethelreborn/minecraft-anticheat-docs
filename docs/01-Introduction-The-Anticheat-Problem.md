# Chapter 1 — Introduction: The Anticheat Problem

## 1.1 Why this document exists

Every Minecraft server administrator eventually confronts the same moment. A player moves across the map in a way no human could, wins every fight they should have lost, or mines a tunnel straight to a base they could not possibly have known about. The question that follows is always the same: how did the server let that happen, and what can be done about it?

The answer, for the modern Minecraft ecosystem, is the server-side anticheat: a piece of software that sits between the vanilla server logic and the network, watches everything every player sends, and decides whether that stream of data could plausibly have been produced by an unmodified client running honest inputs. This document is a complete, ground-up explanation of how those systems work — how cheats operate, how anticheats observe them, how detection logic is actually written, and how you can build your own anticheat or meaningfully improve one that already exists.

The perspective throughout is that of the anticheat developer. We will describe what cheats do and why they are detectable, because you cannot design a detector without understanding the thing it detects. What this document deliberately does not contain is working cheat code, bypass recipes, or exploit instructions. Every cheat mechanism is described at the conceptual and architectural level — the level needed to design detection — and every code sample in this book is detection code that would run inside an anticheat, not inside a hacked client.

## 1.2 The fundamental asymmetry

To understand anticheats, you must first internalize one uncomfortable truth about Minecraft's architecture: **the client is trusted with almost everything.** In vanilla Minecraft Java Edition, the client — not the server — decides:

- Where the player moves, and sends the resulting coordinates to the server.
- When the player attacks, mines, places, or interacts, and sends those intents.
- Whether the player is "on ground," sprinting, sneaking, or using an item.
- Which block the crosshair is pointed at, when the player clicks.

The server receives declarations, not observations. It receives "I am now at x=120.5, y=64, z=-88.25, on ground, sprinting," and by default it has no independent way to verify that those claims are physically possible — until an anticheat adds that verification.

This is the single most important sentence in this book: **an anticheat is a system for checking claims against physics, using only the information the network provides.** Everything else — every check, every simulation, every heuristic — is a technique for making that verification possible with incomplete information.

Compare this with modern games that solve the problem architecturally. Server-authoritative movement — where the client only sends inputs and the server computes positions — is the gold standard, and Minecraft itself introduced optional server-authoritative movement on the Bedrock Edition side via a server property that replays local user input on the server and sends corrections when the client deviates. Java Edition never made that switch, for compatibility reasons that Chapter 3 explores. Java Edition server operators therefore live in a world where the anticheat must *reconstruct* the physics the client ran, on the server, after the fact. That reconstruction is the heart of this book.

## 1.3 What an anticheat actually is

Strip away the marketing and every Minecraft anticheat is a pipeline with the same shape:

1. **Observation.** Capture the packets each player sends — movement updates, block interactions, entity interactions, window clicks, and everything else.
2. **State tracking.** Maintain, per player, an accurate model of what the server knows about them: position, velocity, on-ground status, held item, effects, nearby blocks, entity visibility, ping, and the timing of every recent action.
3. **Verification.** Run each packet or each tick's data through one or more *checks* — small, focused detectors that each answer a narrow question: "Is this horizontal displacement possible?" "Is this click pattern human?" "Could this block have been broken this fast?"
4. **Classification.** Decide whether a violation occurred, weigh it against false-positive risk, and accumulate evidence over time.
5. **Response.** Apply consequences proportionate to certainty: setback (force the player back to a legal state), mitigation (restrict what the cheater can do), alerting staff, or punishment.

Different anticheats implement these five stages very differently, and those differences define their generations:

- **First generation (heuristic):** check simple thresholds on distances and intervals. Fast to build, fast to bypass, noisy. Classic NoCheat and early NoCheatPlus live here.
- **Second generation (statistical/heuristic hybrid):** still threshold-based, but layered — dozens of narrow sub-checks (Vulcan's "Speed A, B, C, D" style taxonomy), violation levels, setback engines, and latency compensation. NoCheatPlus matured here; Vulcan lives here.
- **Third generation (simulation):** run the actual movement physics on the server and check whether the client's claim is among the possible outputs. Grim AC is the defining example, and Polar advertises the same "movement simulation" philosophy as a core feature.
- **Fourth generation (distributed/ML-assisted):** offload detection to cloud infrastructure, track players across instances, and apply statistical or machine-learned behavioral models. Polar's cloud architecture is the most public example of this approach in the Minecraft space.

## 1.4 What this book covers

The book is organized as a progression from foundation to practice:

- **Part I — Foundations (Chapters 2–7).** The history of the anticheat ecosystem; the client-server trust model; the wire protocol and how movement data actually crosses the network; packet interception with PacketEvents and ProtocolLib; the runtime architecture of a real anticheat (threading, ticks, and pipelines); and the player data model that every check depends on.
- **Part II — Movement detection (Chapters 8–14).** The vanilla movement physics an anticheat must replicate; how prediction engines work; and then cheat-by-cheat: speed, flight, timer, surface cheats (Jesus, spider, step), and knockback manipulation — each covered as *how the cheat works conceptually, how it is detected, and where false positives come from*.
- **Part III — Combat and interaction detection (Chapters 15–21).** KillAura and aim analysis; reach and hitbox manipulation; autoclicker detection through statistical analysis of click intervals; scaffold; fast break/place and interaction exploits; x-ray detection (the hardest statistical problem in the field); and inventory, bad-packet, and crash exploits.
- **Part IV — Engineering (Chapters 22–25).** Heuristic and machine-learning detection design; the false-positive battle and latency compensation; a practical roadmap for building your own anticheat; and the future of the field, followed by full references.

## 1.5 How to read the code in this book

Code samples are written in Java, targeting the Bukkit/Spigot/Paper plugin ecosystem, with PacketEvents as the packet library — because PacketEvents is the same foundation that modern packet-level anticheats such as Vulcan build on, and because its API is designed for multi-platform use across Paper, Spigot, Velocity, BungeeCord, Fabric, and Sponge. The samples are deliberately skeletal: they demonstrate the *shape* of a correct implementation — where state is stored, where checks hook in, how violations accumulate — rather than production-hardened code. Error handling, version abstraction, and edge cases are discussed in the surrounding text rather than cluttering the listings.

A note on scope: samples assume the reader can set up a Paper plugin development environment. If you cannot yet, Chapter 24 walks through the full project setup before you return to the earlier chapters' code.

## 1.6 A word on ethics and the community

Anticheat development sits in an adversarial field. There is a legitimate security-research tradition on both sides: cheat developers publish demonstrations that "demonstrate flaws in server security systems to help administrators improve anticheats," as one well-known anticheat researcher put it, and anticheat developers openly discuss bypasses of their own software to drive fixes. This book adopts the defender's side of that tradition. Understanding a cheat's mechanism deeply enough to explain it is the price of detecting it; nothing more is needed, and nothing more is provided here.

Three principles run through the entire book and are worth stating up front:

1. **Detection without false-positive control is worthless.** A check that bans one cheater per hundred innocent players is worse than no check at all, because it destroys trust in the server. Every chapter treats false positives as a first-class design constraint, not an afterthought.
2. **Latency is the eternal alibi.** Most "impossible" client behavior has a lag-based innocent explanation. Every check must be designed with the player's network conditions in mind, and Chapter 23 is devoted entirely to this problem.
3. **Prevention beats punishment.** The newest thinking in the field, exemplified by Polar's mitigation strategy, is that silently restricting a cheater to a fair level of ability is often more effective than banning quickly — because the cheater cannot tell they have been caught, and returns less often. The best anticheat is the one the cheater never notices.

With those principles in hand, we can begin at the beginning: how this ecosystem came to exist.

---

[← Back to Index](../README.md)
