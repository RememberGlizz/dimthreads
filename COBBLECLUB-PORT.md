# CobbleClub Dimensional Threading Port

## Build identity

- Branch: `cobbleclub-1.21.1-fabric`
- Minecraft: 1.21.1
- Loader: Fabric
- Java: 21
- Mod id: `dimthread` (intentionally unchanged)
- Build name: `CobbleClub Dimensional Threading`
- Port version: `1.2.1-cobbleclub.1`

## Why this port exists

The published Fabric 1.21.1 beta failed during Mixin application on the CobbleClub server before Minecraft could finish startup.

The observed failures were:
1. `EntityMixin` could not resolve the Mojang-named `changeDimension` target in the production intermediary runtime.
2. `MinecraftServerMixin` could not resolve its `getAllLevels()` invocation target in the production intermediary runtime.

Both affected injections explicitly disabled remapping even though they target Minecraft-owned named members. The CobbleClub port removes those two incorrect remap suppressions while leaving the Java/JDK `Iterator.hasNext()` target un-remapped.

## Port policy

Keep changes surgical until runtime compatibility is proven:
- Preserve original mod id and gamerule names.
- Preserve original package names.
- Preserve LGPLv3 licensing and upstream attribution.
- Do not redesign the threading model unless a real CobbleClub compatibility issue requires it.
- Validate each compatibility fix against the exact CobbleClub Fabric 1.21.1 stack.

## Reliability gates

This port is not production-approved until all gates pass:

1. Clean Java 21 Gradle build.
2. Dedicated-server startup with the exact CobbleClub modpack.
3. Startup with DimThread enabled and disabled.
4. Players present in several worlds simultaneously.
5. Cross-world player and entity teleporting.
6. Cobblemon spawning/catching/evolution/storage.
7. Cobblemon wild battles and RCT trainer battles.
8. Claims/subclaims and protection checks.
9. RTP/Wild teleports.
10. Resource-world mining/chunk loading.
11. Death and respawn.
12. Economy/contracts/kits/rewards.
13. Save + clean shutdown + restart.
14. C2ME coexistence if C2ME remains installed.
15. Sustained load profiling with Spark and no concurrency exceptions/deadlocks.

## Release naming

Until those gates pass, builds should be treated as validation builds.

When the port passes the production checklist, bump to the next CobbleClub port revision and document the exact tested CobbleClub server version/modpack.
