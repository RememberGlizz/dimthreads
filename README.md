# CobbleClub Dimensional Threading — Fabric 1.21.1

CobbleClub-maintained Fabric 1.21.1 port of **Dimensional Threading ReForked**.

This fork exists specifically so CobbleClub can validate and maintain dimension-parallel ticking on its Fabric 1.21.1 server stack without depending on the stale published 1.21.1 beta jar.

> **Status: EXPERIMENTAL / validation build.**
> Do not treat this branch as production-safe until Cobblemon, Multiworld, RCT, claims, RTP, cross-world teleporting, world saves/restarts, and high-load resource-world activity have all been stress-tested.

## CobbleClub port

Target branch: `cobbleclub-1.21.1-fabric`

Target environment:
- Minecraft 1.21.1
- Fabric
- Java 21
- Server-side use supported
- Original mod id intentionally retained as `dimthread` for compatibility with the existing gamerules/config/mixin identifiers.

CobbleClub-specific changes currently include:
- Fixed the Fabric 1.21.1 `EntityMixin` injection so Mojang-named `changeDimension` is remapped correctly into production mappings.
- Fixed the Fabric 1.21.1 `MinecraftServerMixin` `getAllLevels()` WrapOperation target so it is remapped correctly.
- Branded the build artifact and metadata as the CobbleClub maintenance port.
- Added an automated Java 21 build workflow for this port.
- Preserved the original LGPLv3 license and upstream attribution.

The first fixes address the startup failures where the published Fabric 1.21.1 jar could not find `changeDimension` and could not find the `getAllLevels()` target at runtime.

## What the mod does

Dimensional Threading moves dimension/world ticking onto independent worker threads and waits for all threaded worlds to complete before the global tick continues. With many active dimensions on a multi-core CPU, this can make significantly better use of available CPU cores.

It does **not** make one individual dimension's tick infinitely parallel. The slowest dimension can still determine overall tick time.

## Gamerules

- `/gamerule dimthread_active true|false` — enables/disables threaded dimension ticking.
- `/gamerule dimthread_thread_count <count>` — controls worker thread count.
- `/gamerule dimthread_skip_crashing true|false` — experimental crash-skipping behavior; not recommended for production.

For CobbleClub, thread count should be chosen from the actual loaded dimension count and available CPU threads after compatibility testing.

## Required CobbleClub validation

Before calling this reliable, test all of the following under simultaneous multi-world load:

- Players active in all CobbleClub worlds
- Cobblemon spawning, catching, evolution and storage
- Player-vs-wild and trainer battles
- RCT trainers
- Multiworld teleports and portals
- Claims/subclaims
- RTP and Wild menu teleporting
- Resource-world mining and chunk activity
- Death/respawn
- Economy, contracts, kits and rewards
- World saving
- Server restart/reload cycles
- C2ME interaction if C2ME remains enabled
- Spark profiling for per-world tick distribution and stalls

## Upstream lineage and license

This is a modified LGPLv3 fork. The original project history and attribution are intentionally retained.

- Dimensional Threading ReForked — SrRapero720
- DimensionalThreading-Reforged — CCr4ft3r
- Dimensional Threading — WearBlackAllDay
- WorldThreaded patches — 2No2Name

The repository's `LICENSE` remains GNU LGPLv3.

## Upstream project notes

The upstream project aims to preserve vanilla-like behavior while ticking dimensions concurrently. Not every third-party mod is guaranteed to be thread-safe, so compatibility must be established with the actual modpack rather than assumed.

The overall server MSPT can still be governed by the slowest individual dimension because the server waits for the threaded dimension ticks to finish before continuing.
