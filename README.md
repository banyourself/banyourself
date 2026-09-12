<!-- repo named exactly `banyourself`, as README.md at the root. -->
<!-- Banner is built by tools/build_mc_banner.py in the kevinle.tech repo -->

<img src="https://kevinle.tech/assets/img/mc-banner.gif" alt="Kevin Le, Security Operations and Cloud Security">

```
  SUBJECT .............. Le, Kevin · @banyourself
  LOCATION ............. Westminster, California
  AUTHORIZATION ........ Authorized to work in the U.S. without sponsorship
  EDUCATION ............ Coastline College, A.S. Cybersecurity, class of 2027 · GPA 3.54
  CREDENTIALS .......... CompTIA ×4 · Microsoft ×2 · & MORE · 2 in progress
  STATUS ............... Seeking 2027 Internship for Cyber · IT · Cloud/Network Security · & MORE
```

I look for vulnerabilities in the games I play every day, mostly because it's a
good challenge. Find the flaw, work out what it actually lets someone do, write the patch,
and send it to the maintainer before anyone else hears about it. It stays private until
they ship the fix.

<p>
  <a href="https://kevinle.tech"><img src="https://img.shields.io/badge/PORTFOLIO-b3261e?style=for-the-badge" alt="Portfolio"></a>
  <a href="https://www.linkedin.com/in/kevin-le-cyber"><img src="https://img.shields.io/badge/LINKEDIN-0A66C2?style=for-the-badge" alt="LinkedIn"></a>
  <a href="https://www.credly.com/users/kevin-le-cyber"><img src="https://img.shields.io/badge/CREDLY-f5c518?style=for-the-badge&logo=credly&logoColor=333" alt="Credly"></a>
  <a href="mailto:kevin@kevinle.tech"><img src="https://img.shields.io/badge/EMAIL-3d6349?style=for-the-badge&logo=maildotru&logoColor=white" alt="Email"></a>
  <a href="https://kevinle.tech/assets/LE_KEVIN_RESUME.pdf"><img src="https://img.shields.io/badge/RESUME-c2410c?style=for-the-badge&logo=adobeacrobatreader&logoColor=white" alt="Resume (PDF)"></a>
</p>

**211 ungated packets found across 64 Forge mods, then reported. Two maintainers shipped
fixes and credited me, in mods with 20M+ and 29.1M downloads. One published a GitHub
security advisory naming me as the finder.**
[GHSA-x6cg-7cqm-2pqf](https://github.com/TinyModularThings/Chunk-Pregenerator-Issue-Tracker/security/advisories/GHSA-x6cg-7cqm-2pqf)
&nbsp;·&nbsp;
[Trinkets commit](https://github.com/XzeroAir/Trinkets/commit/e07d279901cbf64c85d1adee7dd7aa60284a840a)
&nbsp;·&nbsp;
[CurseForge release](https://www.curseforge.com/minecraft/mc-mods/trinkets-and-baubles/files/8703456)

---

## <img src="https://kevinle.tech/assets/img/enchanted-book.gif" align="absmiddle" alt=""> CASE FILES

Six bodies of work. Each one written up properly: what I found, why it happened,
how to fix it, and who I told. Full versions live on the
**[portfolio](https://kevinle.tech)**.

<details>
<summary><b>CASE FILE 001 &nbsp;·&nbsp; Modded Minecraft - Missing Packet Authorization</b> &nbsp;<code>vuln research</code></summary>

<br>

Decompiled 400+ Forge mods, the ones other mods depend on or that ship inside
modpacks totaling 30M+ downloads on CurseForge and other modded
Minecraft loaders. Digging through them myself, I found 211 packets with no
permission gate that nobody had reported. Most of it looks like stuff that just
got missed, one handler gated and the one right next to it not. That's still
enough for anyone with bad intent to walk onto a server and wreck it.

Every one of these is the same weakness, **CWE-862 Missing Authorization**: a handler
registered on `Side.SERVER` that acts on whatever the client sent without checking whether
the sender is allowed to. What changes between them is the severity, and that is graded on
what the packet actually lets you do.

| Severity | Mods | Packets | What it means | Status |
|:--|:--|:--|:--|:--|
| **Critical** | 4 | 9 | Wipes a whole dimension, or reaches level-2 command execution | **2 of 4 shipped and credited**, rest reported |
| High | 22 | 60 | Changes any entity or tile by ID, arbitrary teleport, or attack with no reach check | Reported, fix committed |
| Medium | 13 | 29 | Self-contained or read-only, but the gate is still missing | Reported, fix committed |
| Low | 25 | 113 | `Side.CLIENT` so a client cannot send it, a no-op handler, or self-only | Reported, fix committed |

**Trinkets & Baubles shipped the fix and credited me.** Version 0.33.4 went out on
2026-08-21 crediting "KL BanYourself" for reporting the packet exploits, and the same
commit hardened `SyncItemDataPacket`, the exact handler I reported. Verify it on the
[CurseForge release](https://www.curseforge.com/minecraft/mc-mods/trinkets-and-baubles/files/8703456)
or in [commit e07d279](https://github.com/XzeroAir/Trinkets/commit/e07d279901cbf64c85d1adee7dd7aa60284a840a).
That mod has 20M+ downloads on its own.

<img src="https://kevinle.tech/assets/img/trinkets-credit-github.webp" alt="The commit adding KL BanYourself to the mod credits in mcmod.info">

<img src="https://kevinle.tech/assets/img/trinkets-credit-curseforge.webp" alt="CurseForge release notes for 0.33.4, crediting the packet exploit report and hardening the network packets">

<img src="https://kevinle.tech/assets/img/trinkets-credit-discord.webp" alt="The maintainer's reply the same day the report was sent">

**Chunk-Pregenerator shipped the fix and published an advisory crediting me.** The
maintainer (Speiger) released [GHSA-x6cg-7cqm-2pqf](https://github.com/TinyModularThings/Chunk-Pregenerator-Issue-Tracker/security/advisories/GHSA-x6cg-7cqm-2pqf) on 2026-08-27, naming me as the
finder, and patched every supported branch at once: 4.4.9.3 for 1.7.10 to 1.12.2, 4.5.4 for
1.19.2/1.20.1/1.21.1, 4.4.7 for 1.20.5, and 4.4.6 for 1.14.4 to 1.21.x. That mod has 29.1M
downloads and nine years of history.

The advisory is graded Moderate, CVSS 4.3 `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:N/I:N/A:L`,
and it covers the packets the maintainer fixed: `DiscPacket`, `MemoryPacket`,
`ProgressPacket.Cancel`, `RetrogenPacket.Sync`, `SyncStatePacket` and `ServerMapPacket`.
Their changelog is explicit that no remote code execution was reachable. I graded 2.5.1 on
the 1.12.2 line higher than that. Where our numbers disagree, theirs is the one that shipped.

The low tier matters as much as the top one. Most of those turned out to be registered
server-to-client, which means a client cannot forge them at all, and calling those
vulnerabilities would have been wrong.

**Method:** decompile with Vineflower and [CFR](https://github.com/leibnitz27/cfr), find the network registration
(`SimpleNetworkWrapper#registerMessage`), trace every server-bound handler, then
check whether it validates the sender before doing anything that matters. Most
did. The interesting ones didn't.

**Scope:** all testing against my own local servers. Reported privately to
maintainers before publishing anything.

<details>
<summary><b>Full packet appendix</b> &nbsp;·&nbsp; every one of the 211 packets, all 64 mods</summary>

<br>

Sorted by severity, then mod. `crit` and `high` are the ones a client can actually send.
Channel names and fully-qualified handler classes are on the site, they were too wide for
a table here.

| Mod | Packet | Sev | What it can do |
|:--|:--|:--|:--|
| ChunkPregenerator | `DeletionTaskPacket` | **crit** | Starts a `DeleteProcessor` task deleting an arbitrary region of chunks (deletes chunk data). |
| ChunkPregenerator | `DimensionTaskPacket` | **crit** | DELETE ENTIRE DIMENSION FILES: with `unload=true` it unloads a dimension; with `unload=false` it recursively deletes the Overworld `region/` and `data/` folders, or any `DIM<n>` folder on disk. A full world wipe from an ungated packet. |
| ChunkPregenerator | `KillRequest` | **crit** | Kills ALL entities of an arbitrary registry name in the chunk at (x,z) via `setDead()`, or breaks all tile-entities of an arbitrary class at that chunk. |
| ChunkPregenerator | `KillWorldRequest` | **crit** | Kills ALL entities (by registry class) or breaks ALL tile-entities (by registry class) in an entire dimension (`tiles` bool). |
| ChunkPregenerator | `RemoveStructurePacket` | **crit** | Deletes a structure (by `type` string, for example 'Village' or 'Stronghold') at a chosen FilePos, and can kick off a DeleteProcessor task that erases generated terrain inside a box. |
| Grappling Hook (grapplemod) | `PlayerMovementMessage` | **crit** | A client sends an entityId + position (x,y,z) + velocity (mx,my,mz). |
| RecurrentComplex | `PacketEditTileEntity` | **crit** | The headline primitive: a client sends `PacketEditTileEntity` (disc 5, Side.SERVER) with a BlockPos + NBT. |
| RecurrentComplex | `PacketWorldData` | **crit** | A client sends a `worldData` NBT + source + two capture points. |
| Trinkets & Baubles | `SyncItemDataPacket` | **crit** | A client sends an entityID + slot + handler + ItemStack. |
| AutoRegLib | `MessageDropIn` | high | Executes a `DropInHandler.executeDropIn(player, slot, stack)` on the sender's server thread with a client-supplied slot index and ItemStack. |
| ChunkPregenerator | `ManualTaskPacket` | high | Interrupts/starts generation tasks on the server `ChunkProcessor`/`DeleteProcessor`. |
| ChunkPregenerator | `MassPregenTaskPacket` | high | Starts a mass chunk-pregen task (shape/dim/center/radius/split/genType) → massive chunk generation → server lag/DoS. |
| ChunkPregenerator | `PregenTaskPacket` | high | Starts a chunk-pregen task (type/dim/middle/radiusX/radiusZ/postProc) → chunk generation. |
| ChunkPregenerator | `RetrogenChangePacket` | high | Server-side enable/disable a retrogen generator by `id` string (`RetrogenHandler.enableGenerator/disableGenerator`). |
| CollisionDamage | `PacketCollisionS` | high | A client sends an `accel` double. |
| ElenaiDodge | `SDodgeMessage` | high | A client sends a `dir` string and `cooldown` int. |
| FantasticLib | `ControlEventPacket` | high | A client sends a `ControlEvent` (name, state, lastState, identifier). |
| Fish's Undead Rising | `PacketMountSpecial` | high | Looks up any entity by client-supplied entity ID (`world.func_73045_a(message.Id)`), then spawns 8 `EntitySmallFireball`s from that entity aimed along its look vector, plus plays a sound. |
| Grappling Hook (grapplemod) | `GrappleEndMessage` | high | A client sends an entityId and a set of arrowIds. |
| Grappling Hook (grapplemod) | `GrappleModifierMessage` | high | A client sends a BlockPos and a `GrappleCustomization`. |
| Ice and Fire | `MessageDragonArmor` | high | A client sends a dragonId + armor_index + armor_type. |
| Ice and Fire | `MessageDragonControl` | high | A client sends a dragonId + controlState + posX/Y/Z. |
| Ice and Fire | `MessageHippogryphArmor` | high | A client sends a dragonId + slot_index + armor_type. |
| Ice and Fire | `MessageMultipartInteract` | high | A client sends a creatureID + dmg. |
| Ice and Fire | `MessagePlayerHitMultipart` | high | A client sends a creatureID. |
| Ice and Fire | `MessageStoneStatue` | high | A client sends an entityId + isStone. |
| InventoryTweaks | `ITPacketClick` | high | A client sends a slot + data + ClickType + window. |
| ItemPhysic | `DropPacket` | high | A client sends a `power` int. |
| ItemPhysic | `PickupPacket` | high | A client sends a UUID + rightClick. |
| Level Up! 2 | `SkillsPacket` | high | A client sends a button + levelSpend + skill data. |
| MultiMine | `PartialBlockPacket` | high | A client sends a username + x,y,z + value + regenerating. |
| NuclearCraft | `ClearAllFluidsPacket` | high | Client sends a BlockPos. If the tile is an `IMultiblockPart` whose multiblock is `IMultiblockFluid` the server calls `clearAllFluids()`, draining every tank in the entire multiblock, a full reactor included. |
| NuclearCraft | `EmptyTankPacket` | high | Client sends a BlockPos and a tank number. The server resolves the tile with no distance or ownership check and calls `machine.clearTank(tankNo)` on any `ITileFluid`, emptying that machine's fluid tank anywhere in the loaded world. |
| NuclearCraft | `OpenGuiPacket` | high | Client sends a BlockPos and a guiID, and the server hands both straight to `FMLNetworkHandler.openGui`. Unlike the other two GUI packets this one never checks the tile is an `IGui` first, so the sender picks both the window and the coordinates, then `beginUpdatingPlayer` starts streaming that tile's state back. |
| NuclearCraft | `OpenSideConfigGuiPacket` | high | Client sends a BlockPos. If the tile is an `IGui` the server opens its side-config GUI (`getGuiID() + 1000`) for the sender, with no distance or ownership check. |
| NuclearCraft | `OpenTileGuiPacket` | high | Client sends a BlockPos. If the tile is an `IGui` the server opens that tile's GUI for the sender and starts streaming its updates, with no distance or ownership check. |
| NuclearCraft | `ResetItemSorptionsPacket` | high | Client sends a BlockPos, a slot and a defaults flag, and the server resets item sorption across all six faces of any `ITileInventory` at that position. |
| NuclearCraft | `ResetTankSorptionsPacket` | high | Client sends a BlockPos, a tank and a defaults flag, and the server resets tank sorption across all six faces of any `ITileFluid` at that position. |
| NuclearCraft | `ToggleAlternateComparatorPacket` | high | Client sends a BlockPos and a boolean, and the server sets the alternate-comparator flag on any `ITile` at that position. |
| NuclearCraft | `ToggleInputTanksSeparatedPacket` | high | Client sends a BlockPos and a boolean, and the server sets the input-tanks-separated flag on any `ITileFluid` at that position. No distance or ownership check. |
| NuclearCraft | `ToggleItemOutputSettingPacket` | high | Client sends a BlockPos, a slot and a setting ordinal. Sets the slot's output mode on any `ITileInventory`, and when the setting is VOID the server empties that slot's ItemStack, so it deletes items out of a machine the sender never touched. |
| NuclearCraft | `ToggleItemSorptionPacket` | high | Client sends a BlockPos, a face, a slot and a sorption ordinal, and the server rewrites which slots accept or export items on that face of any `ITileInventory`. The ordinal indexes `ItemSorption.values()` with no range check. |
| NuclearCraft | `ToggleRedstoneControlPacket` | high | Client sends a BlockPos and a boolean, and the server enables or disables redstone control on any `ITile` at that position. |
| NuclearCraft | `ToggleTankOutputSettingPacket` | high | Client sends a BlockPos, a tank and a setting ordinal. Sets the tank's output mode on any `ITileFluid`, and when the setting is VOID the server clears that tank, deleting the fluid in a machine the sender never touched. |
| NuclearCraft | `ToggleTankSorptionPacket` | high | Client sends a BlockPos, a face, a tank and a sorption ordinal, and the server sets tank sorption on that face of any `ITileFluid`. The ordinal indexes `TankSorption.values()` with no range check. |
| NuclearCraft | `ToggleVoidExcessFluidOutputPacket` | high | Client sends a BlockPos, a tank number and an output-setting ordinal. Sets the tank output setting on any `ITileFluid` at that position. The ordinal indexes `TankOutputSetting.values()` with no range check. |
| NuclearCraft | `ToggleVoidUnusableFluidInputPacket` | high | Client sends a BlockPos, a tank number and a boolean, and the server sets the void-unusable-fluid-input flag on any `ITileFluid` at that position. |
| PotionCore | `CToSMessage` | high | A client sends a raw byte payload with a type discriminator. |
| QualityTools | `CToSMessage` | high | A client sends a type discriminator + BlockPos + dimension. |
| Quark | `MessageRequestEmote` | high | A client sends an emoteName. |
| RebornCore | `PacketButtonID` | high | A client sends a BlockPos + ID. |
| RebornCore | `PacketConfigSave` | high | A client sends a BlockPos + NBT slot config. |
| RebornCore | `PacketFluidConfigSave` | high | A client sends a BlockPos + fluid config NBT. |
| RebornCore | `PacketFluidIOSave` | high | A client sends a BlockPos + input/output booleans. |
| RebornCore | `PacketIOSave` | high | A client sends a BlockPos + slotID + input/output/filter booleans. |
| RebornCore | `PacketSlotSave` | high | A client sends a BlockPos + slot config NBT. |
| RecurrentComplex | `PacketSpawnTweaks` | high | A client sends a `TObjectFloatMap<String>` of spawn-tweak values. |
| Reskillable | `InvalidateRequirementPacket` | high | A client sends a UUID + cacheTypes. |
| Reskillable | `MessageDodge` | high | A client sends an empty MessageDodge. |
| RLArtifacts | `PacketBottledCloudJump` | high | A client sends an isFart boolean. |
| RLCombat | `PacketMainhandAttack` | high | A client sends an entityId + motion. |
| RLCombat | `PacketOffhandAttack` | high | Same as PacketMainhandAttack but for the offhand. A client sends an entityId and the server attacks that entity by ID with the offhand weapon, with no reach check. |
| SimpleDifficulty | `MessageConfigLAN` | high | A client sends an empty MessageConfigLAN. |
| SpartanWeaponry | `PacketLongReachAttack` | high | A client sends an entityId + velocity. |
| Trinkets & Baubles | `IncreasedReachPacket` | high | A client sends an entityID + hand + targetEntityID + xyz. |
| Trinkets & Baubles | `OpenTrinketGui` | high | A client sends a guiID. |
| Trinkets & Baubles | `SyncRaceDataPacket` | high | A client sends an entityID + NBT. |
| Varied Commodities | `TRADE_ACCEPT` | high | A client sends a TRADE_ACCEPT packet. |
| WolfArmorAndStorage | `WolfDropChestMessage` | high | A client sends an entityId. |
| Antique Atlas Auto Marker | `AddedStructureMarkersPacket` | medium | Takes a client-supplied `atlasID`, dimension and list of `Marker`s (id, type, label, x, z, visibleAhead) and calls `MarkersData.loadMarker` on the server's atlas data for that atlasID. Nothing checks who owns the atlas, so markers can be written into anyone's. |
| CarbonConfig | `BulkSyncPacket` | medium | The same write as SyncPacket, batched: it carries a list of SyncPackets and runs `processEntry` on each, so one message can rewrite and persist entries across any number of named configs at once. |
| CarbonConfig | `SyncPacket` | medium | Client sends a config identifier plus a map of entry keys to raw bytes. `processEntry` looks the config up by that identifier, deserializes the client's bytes into every matching synced entry, then calls `saveQuietly()`, so the write lands on disk. No permission check anywhere in the path. |
| firstaid | `MessageApplyHealingItem` | medium | Client picks a body part and a hand, and the server applies the healing item and consumes one. Gated on the held item being a registered healer, not on permission. |
| firstaid | `MessageClientRequest` | medium | Client sends a `Type` byte. |
| FishingMadeBetter | `PacketKeybindS` | medium | Client sets its own fishing keybind (REEL_IN / REEL_OUT) while fishing. |
| InfernalMobs | `MobModsPacket` | medium | Client sends an entity ID and the server replies with that entity's infernal modifier. Read-only. |
| librarianlib | `PacketSyncSlotVisibility` | medium | Client sends a `boolean[]` visibility mask, which applies to the sender's own open container slots. |
| Lycanites Mobs | `MessagePlayerAttack` | medium | Client sends an entity ID, and the server forces `meleeAttack` on that entity with no reach or ownership check, so any entity by ID. |
| Lycanites Mobs | `MessagePlayerControl` | medium | Client sends a byte of control states, and the server applies it to the sender's own control state only. |
| Lycanites Mobs | `MessagePlayerLeftClick` | medium | Client triggers the left-click action of the equipment item in the sender's active hand. |
| Lycanites Mobs | `MessageSummoningPedestalSummonSet` | medium | Client sends a summon-set (type/subspecies/variant/behavior) and an arbitrary `BlockPos`, and the server writes it as NBT into any Summoning Pedestal tile at that position. |
| Lycanites Mobs | `MessageTileEntityButton` | medium | Client sends a button ID and an arbitrary `BlockPos`, and the server triggers that GUI button on any Lycanites `TileEntityBase` at that position, a Summoning Pedestal included. |
| Mantle | `PacketUpdateSavedPage` | medium | Client sends a page name, and the server writes it as a saved-page NBT tag onto the held book. |
| Painting Select GUI | `SPacketPainting` | medium | Looks up any entity by client-supplied entity ID (`player.world.func_73045_a(packet.id)`), and if it is an `EntityPainting` the server changes its art and broadcasts, with no ownership or reach check. |
| Quark | `MessageChangeHotbar` | medium | Client sends a bar index (1-3), and the server swaps the sender's hotbar with one of three saved rows. Feature-gated only, no permission check. |
| Quark | `MessageDeleteItem` | medium | Client sends a slot index, and the server deletes the item in that inventory slot, or the cursor stack when the slot is -1. The only check is that the item is not favorited. |
| Quark | `MessageDropoff` | medium | Client triggers a dropoff of the player's inventory into nearby chests. |
| Quark | `MessageMatrixEnchanterOperation` | medium | Client sends an operation plus three args, and the server runs it against the `TileMatrixEnchanter` behind the sender's open `ContainerMatrixEnchanting`. Feature-gated only. |
| Quark R1.6-179 | `MessageRequestPassengerChest` | medium | Client requests the chest-inventory of a `EntityChestPassenger` riding the sender's boat. |
| Quark R1.6-179 | `MessageRestock` | medium | Client triggers a restock of the player's inventory from nearby chests. |
| Quark R1.6-179 | `MessageSortInventory` | medium | Client sorts the player's inventory. |
| RecurrentComplex | `PacketOpenGui` | medium | A client sends a modId + guiId + data. |
| SpartanShields | `PacketShieldBash` | medium | Client sends a hand, an entity ID and an attack flag, and the server shield-bashes that entity by ID for knockback plus 1.0 damage. Gated on holding a shield and on cooldown, not on ownership or range. |
| SpartanWeaponry | `PacketKeyHandle` | medium | Client opens the quiver GUI for the sender's own quiver (hotbar or bauble slot). |
| SRParasites | `SRPPacketEntityBodyHit` | medium | Client sends a target ID and a part ID, and the server deals damage to any parasite body-part by ID. The only check is that the target is an `EntityBodyParts`. |
| SRParasites | `SRPPacketMeleeRange` | medium | Client sends an entity ID, and the server attacks that entity if the sender holds an `IHaveReach` weapon and the target is within reach. Gated on the held item and distance, not on permission. |
| Waystones | `MessageRemoveWaystone` | medium | Client sends an index, and the server removes that waystone from the sender's own list. |
| Waystones | `MessageSortWaystone` | medium | Client sends two indices, and the server reorders the sender's own waystone list. |
| Antique Atlas | `AddMarkerPacket` | low | Creates a marker on the sender's atlas at a client-supplied position and broadcasts a `MarkersPacket` to all players. |
| Antique Atlas | `DeleteMarkerPacket` | low | Registered on both sides (bidirectional). |
| Antique Atlas | `GridPositionPacket` | low | This packet does NOT exist. |
| Antique Atlas | `PutBiomeTilePacket` | low | Registered on both sides. |
| Antique Atlas | `RegisterTileIdPacket` | low | Client sends an arbitrary tile-name string, and the server registers it as a new biome or pseudo-biome id and broadcasts it to every player, so the registry can be polluted server-wide. |
| AutoRegLib | `TileEntityMessage` | low | Not actually client-sendable. `TileEntityMessage` is an abstract base class that is never registered itself, so only concrete subclasses get a registration and there is nothing to send here. |
| Baubles | `PacketOpenBaublesInventory` | low | `Side.SERVER` (disc 0). |
| Baubles | `PacketOpenNormalInventory` | low | `Side.SERVER` (disc 1). |
| BetterQuesting | `chapter_sync` | low | Client requests chapter (quest-line) config sync and the server replies with the data. Read-only. |
| BetterQuesting | `main_sync` | low | Client requests a full questing-data sync and the server replies with it. Read-only. |
| BetterQuesting | `name_sync` | low | Client sends a list of UUIDs and/or player names and the server replies with the matching name-cache entries. Read-only. |
| BetterQuesting | `party_sync` | low | Client requests party data and the server replies with it. Read-only. |
| BetterQuesting | `quest_action` | low | Client sends `action` (0=claim, 1=detect) plus an array of `questIDs`. |
| BetterQuesting | `quest_sync` | low | Client requests quest config/progress sync for a set of quest IDs. |
| Callable Horses | `PressKeyPacket` | low | `Side.SERVER` (disc 0). |
| Carry On | `SyncKeybindPacket` | low | `Side.SERVER` (disc 0). |
| CD4017BE lib | `SyncNetworkHandler.handlePlayerPacket` | low | Deprecated generic dispatch. |
| ChunkPregenerator | `ChunkRequest` | low | Read-only request for a chunk's generation state. |
| ChunkPregenerator | `DimRequestPacket` | low | Read-only query. |
| ChunkPregenerator | `EntityRequestPacket` | low | Read-only request for entity data in a chunk. |
| ChunkPregenerator | `PermissionRequestPacket` | low | Read-only query. |
| ChunkPregenerator | `ProcessRequestPacket` | low | Read-only query of the server's generation/deletion processor state. |
| ChunkPregenerator | `RetrogenCheckPacket` | low | Read-only query of retrogen generator state. |
| ChunkPregenerator | `StructureRequestPacket` | low | Read-only query/handshake for the structure-manager UI browse. |
| ChunkPregenerator | `TPChunkPacket` | low | Teleports the sender to an arbitrary (x,z) in the sender's own dimension. |
| ChunkPregenerator | `TrackerRequestPacket` | low | Read-only query for the server's chunk-generation tracker state. |
| Classy Hats | `PacketHatGuiOpen` | low | Opens the hat GUI for the sender with a client-supplied `target` int. |
| Classy Hats | `PacketSyncLastSelectedSection` | low | Sets the sender's `CapabilityHatContainer` current-hat-section to a client int. |
| Dynamic Surroundings | `PacketEntityData` | low | `Side.CLIENT` (server→client, disc 3) - NOT client-sendable. |
| Dynamic Surroundings | `PacketEnvironment` | low | `Side.CLIENT` (disc 5) - NOT client-sendable. |
| Dynamic Surroundings | `PacketServerData` | low | `Side.CLIENT` (disc 6) - NOT client-sendable. |
| Dynamic Surroundings | `PacketSpeechBubble` | low | `Side.CLIENT` (disc 2) - NOT client-sendable. |
| Dynamic Surroundings | `PacketThunder` | low | `Side.CLIENT` (disc 4) - NOT client-sendable. |
| Dynamic Surroundings | `PacketWeatherUpdate` | low | `Side.CLIENT` (disc 1) - NOT client-sendable. |
| EnhancedVisuals | `DamagePacket` | low | Client-bound visual packet. |
| EnhancedVisuals | `ExplosionPacket` | low | Client-bound visual packet. |
| EnhancedVisuals | `PotionPacket` | low | Client-bound visual packet. |
| Grappling Hook | `DetachSingleHookMessage` | low | Handler runs on `Minecraft.getMinecraft()` and calls `receiveGrappleDetachHook`, so it only executes client-side. Not client-sendable. |
| Grappling Hook | `GrappleAttachMessage` | low | Not actually client-sendable - registered `Side.CLIENT` (`grapplemod.java:342`). |
| Grappling Hook | `GrappleAttachPosMessage` | low | Handler resolves the entity from `WorldClient` and calls `setAttachPos` on a `grappleArrow`, so it only executes client-side. Not client-sendable. |
| Grappling Hook | `GrappleDetachMessage` | low | Not actually client-sendable (Side.CLIENT, `grapplemod.java:348`). |
| Grappling Hook | `LoggedInMessage` | low | Handler calls `GrappleConfig.setserveroptions` on the client, pushing the server's config down at login. Not client-sendable. |
| Grappling Hook | `SegmentMessage` | low | Handler resolves the entity from `WorldClient` and edits that arrow's `SegmentHandler`, so it only executes client-side. Not client-sendable. |
| Ice and Fire | `MessageDaytime` | low | `onServerReceived` is empty. A no-op on the server, client-only render sync. |
| Ice and Fire | `MessageDeathWormHitbox` | low | Calls `initSegments(scale)` on any death worm by client-supplied entity ID, resizing its hitbox. |
| Ice and Fire | `MessageGetMyrmexHive` | low | Overwrites an entire Myrmex hive's village data (rooms and chambers) by hive UUID from client-supplied NBT, so a colony can be wiped or rewritten. |
| Ice and Fire | `MessageSetMyrmexHiveNull` | low | `onServerReceived` is empty. A no-op on the server, client-only render sync. |
| Ice and Fire | `MessageSirenSong` | low | Client-sendable (registered on both sides via llibrary `AbstractMessage.registerOnSide` → true). |
| Ice and Fire | `MessageUpdatePixieHouse` | low | `onServerReceived` is empty. A no-op on the server, client-only render sync. |
| Ice and Fire | `MessageUpdatePixieHouseModel` | low | No server-side effect. |
| Ice and Fire | `MessageUpdatePixieJar` | low | no server-side effect. |
| Ice and Fire | `MessageUpdatePodium` | low | no server-side effect. |
| iChunUtil | `PacketEntityLocation` | low | Not actually live in this pack - the `iChun_WorldPortals` channel is only created if some mod calls the WorldPortals API, and no in-pack caller was found (reconcile §3). |
| iChunUtil | `PacketPatronInfo` | low | Adds/removes a `PatronInfo` (playerId / patronRewardType / showPatronReward) to the server's patron list and broadcasts `PacketPatrons` to all players. |
| iChunUtil | `PacketPatrons` | low | NOT client-sendable. |
| iChunUtil | `PacketRequestBlockEntityData` | low | Read-only info request. |
| Inspirations | `InventorySlotSyncPacket` | low | `Side.CLIENT` (`registerPacketClient`, `InspirationsNetwork.java:43`) - NOT client-sendable. |
| Inspirations | `MilkablePacket` | low | `Side.CLIENT` - NOT client-sendable. |
| Inspirations | `RenderBlockUpdatePacket` | low | `Side.CLIENT` - NOT client-sendable. |
| IvToolkit | `PacketGuiAction` | low | Not actually client-sendable in this pack - IvToolkit itself registers NO network channel, and no mod in the pack registers `PacketGuiAction` on its own wrapper. |
| IvToolkit | `PacketTileEntityClientEvent` | low | Not client-sendable in this pack - same as PacketGuiAction, library-only with no in-pack registration. |
| Level Up! 2 | `ClassChangePacket` | low | Client picks its own class/specialization (mining/craft/combat bonus). |
| llibrary | `SurvivalTabMessage` | low | Client-sendable (registered on both sides). |
| Locks | `CheckPinPacket` | low | `Side.SERVER` (disc 3). |
| Lost Cities | `PacketRequestProfile` | low | `Side.SERVER`. |
| Lycanites Mobs | `MessageBeastiary` | low | Registered `Side.CLIENT`, so a client cannot send it. |
| Lycanites Mobs | `MessageCreature` | low | client-boundary. |
| Lycanites Mobs | `MessageCreatureKnowledge` | low | client-boundary. |
| Lycanites Mobs | `MessageEntityPerched` | low | client-boundary. |
| Lycanites Mobs | `MessageEntityPickedUp` | low | client-boundary. |
| Lycanites Mobs | `MessageEntityVelocity` | low | client-boundary. |
| Lycanites Mobs | `MessageGUIRequest` | low | Requests a GUI open for the sender. Self-only. |
| Lycanites Mobs | `MessageMobEvent` | low | client-boundary. |
| Lycanites Mobs | `MessagePetEntry` | low | Modifies one of the sender's own pet entries. |
| Lycanites Mobs | `MessagePetEntryRemove` | low | Removes one of the sender's own pet entries. |
| Lycanites Mobs | `MessagePlayerStats` | low | client-boundary. |
| Lycanites Mobs | `MessageSummoningPedestalStats` | low | client-boundary. |
| Lycanites Mobs | `MessageSummonSet` | low | Writes summon-set NBT (type/subspecies/variant/behaviour) into the sender's own summon set. |
| Lycanites Mobs | `MessageSummonSetSelection` | low | Selects one of the sender's own summon sets. |
| Lycanites Mobs | `MessageSyncRequest` | low | Requests a full player sync (`needsFullSync`). Self-only. |
| Lycanites Mobs | `MessageWorldEvent` | low | client-boundary. |
| MmmMmmMmmMmm | `DamageMessage` | low | client-boundary. |
| MmmMmmMmmMmm | `SyncEquipmentMessage` | low | client-boundary. |
| MoBends | `MessageConfigResponse` | low | `Side.CLIENT` (server→client only) - not client-sendable. |
| MoBends | `MessageViewRequest` | low | This packet does not exist. |
| Quark | `MessageChangeConfig` | low | Not actually client-sendable - registered as `Side.CLIENT` (MessageRegister.java:60), so it is server→client only. |
| Reach Fix | `CPacketHandlerSyncConfig` | low | `Side.CLIENT` (registered `ReachFix.java:69`) - NOT client-sendable. |
| Reskillable | `MessageDataSync` | low | `Side.CLIENT` (server→client only) - not client-sendable. |
| Reskillable | `MessageLockedItem` | low | `Side.CLIENT` (server→client only) - not client-sendable. |
| Rustic | `MessageDismountChair` | low | `Side.SERVER` (disc 2). |
| Rustic | `MessageVaseMeta` | low | `Side.SERVER` (disc 1). |
| ScalingHealth | `MessageDataSync` | low | client-boundary. |
| ScalingHealth | `MessageDebugData` | low | client-boundary. |
| ScalingHealth | `MessageMarkBlight` | low | client-boundary. |
| ScalingHealth | `MessagePlaySound` | low | client-boundary. |
| ScalingHealth | `MessageWorldDataSync` | low | client-boundary. |
| Serene Seasons | `MessageSyncConfigs` | low | `Side.CLIENT` (disc 4) - NOT client-sendable. |
| Serene Seasons | `MessageSyncSeasonCycle` | low | Handler returns unless `ctx.side == Side.CLIENT`, then writes `clientSeasonCycleTicks` for the player's own dimension. Not client-sendable. |
| SilentLib | `MessageLeftClick` | low | Client-sendable (`Side.SERVER`, registered in `SilentLib.preInit`). |
| SRParasites | `SRPPacketBiomeChange` | low | client-boundary. |
| SRParasites | `SRPPacketEntityBodyDead` | low | client-boundary. |
| SRParasites | `SRPPacketFog` | low | client-boundary. |
| SRParasites | `SRPPacketMovingSound` | low | client-boundary. |
| SRParasites | `SRPPacketParticle` | low | client-boundary. |
| Standard Expansion | `choice_reward` | low | A client picks a `selection` index for a `RewardChoice` reward and the server stores that selection. |
| Standard Expansion | `task_checkbox` | low | Client sends `questID`/`taskID`, and the server marks that `TaskCheckbox` task complete for the sender's questing UUID. A quest-completion cheat. |
| Standard Expansion | `task_interact` | low | Client sends `isMainHand`/`isHit`, and the server runs `TaskInteractItem.onInteract` for the sender's active quests. |
| Trinkets & Baubles | `EffectsRenderPacket` | low | A client sends an entityID + effectID + color + coords. |
| Trinkets & Baubles | `KeybindPacket` | low | A client sends an entityID + ability + key state. |
| Trinkets & Baubles | `MovementKeyPacket` | low | A client sends an entityID + key + state. |
| Varied Commodities | `SAVE_BOOK` | low | Client sends a `BlockPos` and an NBT book, and the server writes pages, author and title into any `TileBook` at that position. The only checks are that the tile is a `TileBook` and not already written. |
| Varied Commodities | `SAVE_SIGN` | low | Client sends a `BlockPos` and text, and the server writes it into any `TileBigSign` at that position, so another player's sign can be overwritten. The only checks are that the tile is a `TileBigSign` and `canEdit`. |
| Wearable Backpacks | `MessageOpenBackpack` | low | `Side.SERVER` (disc 3). |

</details>

→ [Read the full writeups](https://kevinle.tech/case/MC-001)

</details>

<details>
<summary><b>CASE FILE 002 &nbsp;·&nbsp; CS:GO Server Plugins - Anti-Cheat, Anti-VPN, Gamemodes</b> &nbsp;<code>defensive tooling</code></summary>

<br>

Fourteen SourcePawn plugins and one C++ Metamod extension for the CS:GO servers I run.
Roughly 30,000 lines of SourcePawn plus 800 of C++. Grouped below so you can open only
the part you care about.

<details>
<summary><b>Anti-Cheat</b> &nbsp;<code>2 projects</code></summary>

<br>

**KevAC** · SourcePawn · ~7,400 lines · mine

Server-sided anticheat, about 45 detectors across movement, aim, command cadence and
cvar state. Client-side anticheat trusts the machine you're trying to catch;
server-side only trusts what the server can observe, so it's behavioral detection
against a noisy signal. Same job as writing SIEM rules with a different hat on.

The detector I like best is the cheat-cvar probe: a legitimate client physically cannot
change an `FCVAR_CHEAT` cvar while the server has `sv_cheats 0`, so a mismatch means
patched cvar protection. Zero false positives, which is rare when everything else is
statistical.

Detections write to a ban queue rather than banning live, so there's a review step.
Banning one innocent regular costs more than missing one cheater.

**KevAC Extension** · C++ · ~800 lines · mine

In Source, the client tells the server which network events it wants, and you can't
touch that list from SourcePawn at all. Injected DLLs register extra listeners there,
which makes it the cleanest catch in the project: not a threshold, just a list that
shouldn't have that entry. So this half is a Metamod extension that detours
`ListenEvents`, built with AMBuild and safetyhook.

</details>

<details>
<summary><b>Network Defense</b> &nbsp;<code>1 project</code></summary>

<br>

**KevVPN** · SourcePawn · ~2,200 lines · mine

Two layers, cheapest first. Static CIDR ranges for datacenter and hosting ASNs held in
RAM, so no network call per connect, then a reputation API for what the ranges miss,
with results cached in SQL.

The decision worth asking me about: what happens when the lookup fails. I fail open,
and specifically, if the database is unreachable nobody gets punished, because an
unreachable database means the whitelist never loaded and punishing then kicks the exact
people who were explicitly exempted.

Mobile carrier ranges are recorded and never acted on: a cellular address proves nothing
and blocking it hits every 5G player. Cloudflare WARP and GeForce Now each needed
handling, because their egress reads as datacenter, so an admin allowlist gets the last
word over every feed.

</details>

<details>
<summary><b>Gamemodes</b> &nbsp;<code>4 projects</code></summary>

<br>

**hnsmix** · SourcePawn · ~11,800 lines · mine

Captain-based ranked matches, 1v1 to 10v10, with Elo persisted in SQL and live Discord
embeds through REST in Pawn. The status card edits one existing message instead of
posting a new one, which means storing a message id and having a way out when that
message gets deleted.

**hnsova** · SourcePawn · ~4,200 lines · fork of ceLoFaN's hidenseek

I added One Versus All: one T against everybody, and whoever lands the stab becomes the
new T where they stand. The in-place handover is the whole feel of the mode, so it uses
`CS_SwitchTeam` rather than a respawn, which means refreshing the player model by hand.
Stats and a Discord leaderboard in SQL, with a schema migration for tables made before
the stab columns existed.

**KevFJ** · SourcePawn · ~870 lines · fork of hiiamu's amuFJ

Funjump practice mode, voted on by players. The tricky bit is that three plugins
all want to own `mp_roundtime`, so this one hooks `round_start` as Post specifically
because hnsmix hooks it as Pre and always runs first.

**antifrag** · SourcePawn · ~620 lines · fork of oqyh's HNS Anti Frag

Knife damage cap. Upstream compensated for kevlar's 85% reduction with an
eighteen-branch if/else ladder of hand-tuned constants; I replaced it with the actual
calculation. Also hooked `OnTakeDamageAlive` so the engine's backstab bonus and
third-party perk plugins can't push a stab back over the cap.

</details>

<details>
<summary><b>Movement</b> &nbsp;<code>4 projects</code></summary>

<br>

**gstrafe** · SourcePawn · ~290 lines · fork of zwolof's EFRAG GStrafe

The original speed modifier was two-state, so speed sawtoothed around 400 forever
instead of settling. Mine multiplies by a gain and clamps to a max, so a boost lands
exactly on the cap. It also calls my own anticheat's `KevAC_IgnoreMovement` on the ticks
where it teleports someone, because otherwise the movement plugin trips the detector.

**MovementTweaker** · SourcePawn · ~690 lines · fork of danzayau's

Prestrafe and air acceleration tuning, with a ground cap that tracks the live prestrafe
modifier instead of pinning everyone at one number. Also tracked down constant
`DataTable` out-of-range spam: the engine packs snapshots straight from entity memory at
end of frame, so the fix was clamping the stored value in post-think.

**csgo_movement_unlocker** · SourcePawn · 85 lines · Peace-Maker's, my syntax pass

Finds `CGameMovement::WalkMove` by byte signature and NOPs the speed clamp. Not my work,
I only brought it forward to modern SourcePawn. It's here because it's the reason
MovementTweaker has to enforce its own cap.

**movementhud** · SourcePawn · 14 files · fork of Sikarii's MovementHUD

Speed and key-press readouts. Reading how it allocates HUD channels is what told me the
right fix for the spectator-list flicker was a synchronizer, not a hard-coded channel.

</details>

<details>
<summary><b>Quality of Life</b> &nbsp;<code>3 projects</code></summary>

<br>

**hextags** · SourcePawn · ~1,470 lines · fork of Hexer10's HexTags

Players kept losing their chosen tag after a reconnect. The saved selection was stored
as a KeyValues section symbol, and that symbol isn't stable when the config has
duplicate selector names, which every real config does. Fixed by storing the tag name,
with the old cookie read once for migration. Also added an external prefix API so
hnsmix can put a rank tag on the scoreboard without the two plugins fighting over it.

**speclist** · SourcePawn · ~510 lines · fork of MandoCSGO's Spectator-List

210 lines upstream, 510 here, almost all of it chasing one flicker with two causes: a
`-1` channel that made the engine pick a new slot every send, and the fact that
replacing a HUD message blanks its channel for a frame, so a slower refresh made it
worse rather than better. Fix was a channel synchronizer plus splitting rebuild from
draw.

**EasySpawnProtection** · SourcePawn · ~450 lines · fork of Invex and Byte's

Spawn protection, extended so One Versus All can hand the T role over mid-round without
handing the new T a few seconds of invulnerability. The exception went in the shared
function rather than the two call sites that looked like they needed it, because
guarding only `player_spawn` left the round-start loop still granting protection.

</details>

<br>

`Anti-Cheat` · `Anti-VPN` · `Custom Gamemodes` · `C++ / Metamod` · `SQL`

Every fork is published with credits to the original author to the best of my abilities. I run every single one of these SourcePawn plugins on two CS:GO servers (NA/EU), and I help manage an active CS2 network at [edan.gg](https://edan.gg/).

→ [Read the full writeups](https://kevinle.tech/case/CS-002)

</details>

<details>
<summary><b>CASE FILE 003 &nbsp;·&nbsp; Minecraft & CS:GO Servers - Config and Network Management</b> &nbsp;<code>infrastructure</code></summary>

<br>

Running public game servers is a sysadmin job wearing a hoodie, and the two games are
genuinely different jobs. Minecraft was one box I owned end to end. CS:GO is two rented
servers on two continents.

<details>
<summary><b>Minecraft server</b> &nbsp;<code>owned end to end</code></summary>

<br>

**Networking.** The server sat on its own subdomain rather than a bare IP, and behind that
name connections were split across three proxies running on Proxmox. Players hold a name, I
hold the topology, and the two change independently. DDoS protection ran through Cloudflare,
with the caveat worth stating out loud: their edge is built for HTTP, and Minecraft is a TCP
protocol on a non-web port, so it is not the same "just proxy it" story as a website.

Minecraft also needs a record type nobody else meets. A `_minecraft._tcp` SRV record hands
the client both the target host and the port, so people connect with a bare domain even
though the server is not on 25565.

**Runtime.** Forge running RLCraft Dregora v1.1.2b on Temurin JDK 8, headless in tmux. Java 8
is not nostalgia, it is what Forge for that version supports, and a heavily modded pack is the
least forgiving place to go off the supported runtime. A Minecraft server is a long-running
JVM, so its performance problems are JVM problems: heap size and GC pauses are what a stutter
usually is, and more heap makes pauses longer, not shorter.

**Admin access.** rcon bound to localhost and reached over SSH, with an IP allowlist on SSH
itself. rcon then inherits SSH's key auth instead of relying on its own cleartext password,
and the same allowlist covers the friends who needed FileZilla. One door with real auth on it
instead of several with weak auth.

**Operations.** A 3AM restart on cron, and the box rebooted itself on the same schedule.
Restarting nightly is the pragmatic answer to a modded server's memory creeping up: chase a
leak through a hundred mods you did not write, or restart when nobody is online. The honest
gap is that tmux does not restart a crashed process the way a systemd unit with
`Restart=on-failure` would.

</details>

<details>
<summary><b>CS:GO servers</b> &nbsp;<code>NA + EU</code></summary>

<br>

**Hosting.** NA on NFOservers, EU on dathost. rcon has its own password, but I do not drive
the servers through it day to day: both hosts give you a web console, so the normal admin path
is an authenticated dashboard. That is the real win, because when the console you reach for
every day already sits behind a proper login, rcon stops being the thing you leave open for
convenience.

**FastDL.** EU serves it from dathost alongside the game server, NA from Cloudflare R2. R2 is
the better shape: object storage behind a CDN, so a map pack landing on twenty joining players
is bandwidth Cloudflare eats instead of bandwidth competing with tickrate. It also has no
egress fee, which for a pure-outbound workload is the whole argument. Worth knowing that
anything under `sv_downloadurl` is public and its paths are derivable, so what gets synced
there is an access-control decision, not a deployment detail.

**Config.** A Source server reads config from several places in a fixed order and the last
writer wins: `server.cfg`, then gamemode and map configs, then anything a plugin writes into
`cfg/sourcemod/`. That ordering is why my movement plugin re-applies its cvar on
`OnConfigsExecuted` rather than `OnMapStart`.

**Two databases, on purpose.** NA uses SQLite local to the game server, EU points at
MySQL on NFOservers web hosting. Same plugins, same schema, two different engines, which only
works because everything goes through SourceMod's database layer. `databases.cfg` makes
sharing trivial and I chose not to: one database would be a single point of failure for both
regions, cross-continent queries land mid-tick, and an Elo pool mixing two populations with
different ping is not really one ladder.

**Admins and secrets.** `admins.cfg` with two groups, Owner on root and Admin on a hand-picked
flag set. Root is a blast radius, not a seniority label. Credentials (rcon, database user,
proxycheck.io key, Discord webhooks) are all `FCVAR_PROTECTED` convars with empty defaults,
never in source. Backups are daily from both hosts, which on NA sweeps up the SQLite file for
free.

</details>

<br>

`Network Configuration` · `DNS & Domains` · `Load Balancing` · `Performance Configuration` · `Proxmox` · `Cloudflare`

→ [Read the full writeups](https://kevinle.tech/case/SRV-003)

</details>

<details>
<summary><b>CASE FILE 004 &nbsp;&middot;&nbsp; kevinle.tech</b> &nbsp;<code>front end</code></summary>

<br>

Hand written, no framework and no build step, because most templates and site
builders hand you the same layout with different colors on it. Runs at
[kevinle.tech](https://kevinle.tech) and, from the same repo, at
[banyourself.github.io](https://banyourself.github.io/). Two themes, a case file dossier and a Minecraft GUI, off one
`data-theme` attribute.

The bug worth telling you about is one my own security header caused. The
decorative layer was dead in production for weeks: `style-src` has no
`unsafe-inline`, so the browser refused every `setAttribute("style", ...)` the
animation code wrote. Thirty particles stacked in one corner with a zero second
duration.

It survived because **it worked on my machine**. The dev server sends no security
headers, so the policy only existed in production. I found it by copying the site,
injecting the real policy as a `<meta>` tag and serving that. Same code went from
1 distinct position across 30 particles to 28.

The fix is that the CSSOM path is not blocked, only markup attributes are, so
`style.cssText` works where `setAttribute` does not. The lesson I actually kept is
that a security control which only exists in production is one you are not testing.

`HTML` &middot; `CSS` &middot; `Vanilla JS` &middot; `Security Headers` &middot; `Cloudflare Workers`

&rarr; [How it is built](https://kevinle.tech/case/WEB-004)

</details>

<details>
<summary><b>CASE FILE 005 &nbsp;·&nbsp; Network-Wide DNS Filtering - Pi-hole on Raspberry Pi</b> &nbsp;<code>blue team</code></summary>

<br>

DNS sinkhole for the whole house. Started as an ad-blocker, turned into a lesson
in how much a network says when nobody is listening, and in treating DNS as a
detection surface rather than a convenience.

Also documented honestly: what it does **not** catch. DoH walks straight past it.

`Raspberry Pi` · `Pi-hole` · `Unbound` · `DNS`

→ [What the logs showed](https://kevinle.tech/case/DNS-005)

</details>

<details>
<summary><b>CASE FILE 006 &nbsp;·&nbsp; Home Security Lab - Build & Detection Log</b> &nbsp;<code>blue team</code></summary>

<br>

A segmented lab where I run attacks against myself and then try to catch them in
the logs. I keep a detection log with three columns: what I ran, what fired, and
**what didn't fire and why**. The third column is the one I actually learn from.

`Proxmox` · `pfSense` · `Suricata` · `Windows Server` · `Kali`

→ [Topology and detection log](https://kevinle.tech/case/LAB-006)

</details>

---

## <img src="https://kevinle.tech/assets/img/enchanted-book.gif" align="absmiddle" alt=""> CREDENTIALS

<!-- LinkedIn carries more than this. Nothing here should contradict it. -->

```
  EARNED

    CompTIA
      CS0-003  CySA+ .......................................................... May 2026
      SK0-005  Server+ ........................................................ May 2026
      N10-009  Network+ ....................................................... Dec 2025
      SY0-701  Security+ ...................................................... Aug 2025

    Microsoft
      SC-500   Cloud and AI Security Engineer ................................. Aug 2026
      SC-200   Security Operations Analyst .................................... May 2026

  ADDITIONAL CERTIFICATES COMPLETED              a certificate is not a certification

      CompTIA stacks .......... Security Analytics (CSAP), Network Infrastructure (CNIP)
      Google .................. Cybersecurity, IT Support
      Cisco ................... Python Essentials 1 & 2
      IBM ..................... Cybersecurity Fundamentals
      AWS ..................... Educate: Security, Networking, Cloud
      ISC2 .................... Candidate

  IN PROGRESS

      SC-100   Microsoft Cybersecurity Architect
      CISSP    ISC2 Certified Information Systems Security Professional

  SCHOLARSHIPS

      Microsoft Cybersecurity Scholarship ......... Last Mile Education Fund, Aug 2025
      Cybersecurity Scholarship ................... Women in Cloud, Sep 2025
      Osher Scholars Award I ...................... Bernard Osher Foundation, Apr 2026

  LEADERSHIP

      President ................................... E-Sports Club
      Secretary ................................... Associated Student Government
      Secretary ................................... WiCyS Student Chapter, Coastline
```

Exam codes are included and the issue date as well. All CompTIA badges verifiable on
**[Credly](https://www.credly.com/users/kevin-le-cyber)**, the full list of certificates
are on **[LinkedIn](https://www.linkedin.com/in/kevin-le-cyber/details/certifications/)**
with a picture of certificate attached to each one as authentication.

---

## <img src="https://kevinle.tech/assets/img/enchanted-book.gif" align="absmiddle" alt=""> ARSENAL

Color is the grade, not decoration. Green means I built or broke something real with
it, amber means I'm mid-way and would still reach for docs, gray means I've started and
that's all. No badge here is aspirational.

**DETECTION & MONITORING**

<p>
  <img src="https://img.shields.io/badge/Detection_engineering-3d6349?style=for-the-badge" alt="Detection engineering">
  <img src="https://img.shields.io/badge/False--positive_tuning-3d6349?style=for-the-badge" alt="False-positive tuning">
  <img src="https://img.shields.io/badge/Threat_intelligence_feeds-3d6349?style=for-the-badge" alt="Threat intelligence feeds">
  <img src="https://img.shields.io/badge/IP_reputation_%26_enrichment-3d6349?style=for-the-badge" alt="IP reputation & enrichment">
  <img src="https://img.shields.io/badge/Log_analysis-3d6349?style=for-the-badge" alt="Log analysis">
  <img src="https://img.shields.io/badge/Pi--hole_/_DNS_sinkholing-3d6349?style=for-the-badge&logo=pihole&logoColor=white" alt="Pi-hole / DNS sinkholing">
  <img src="https://img.shields.io/badge/Wireshark_/_tcpdump-3d6349?style=for-the-badge&logo=wireshark&logoColor=white" alt="Wireshark / tcpdump">
  <img src="https://img.shields.io/badge/Microsoft_Sentinel-87701d?style=for-the-badge" alt="Microsoft Sentinel">
  <img src="https://img.shields.io/badge/Microsoft_Defender_XDR-87701d?style=for-the-badge" alt="Microsoft Defender XDR">
  <img src="https://img.shields.io/badge/SIEM_%28Splunk%2C_QRadar%29-87701d?style=for-the-badge&logo=splunk&logoColor=white" alt="SIEM (Splunk, QRadar)">
  <img src="https://img.shields.io/badge/Suricata_/_Snort-87701d?style=for-the-badge" alt="Suricata / Snort">
  <img src="https://img.shields.io/badge/Incident_response-87701d?style=for-the-badge" alt="Incident response">
  <img src="https://img.shields.io/badge/MITRE_ATT%26CK-87701d?style=for-the-badge" alt="MITRE ATT&CK">
  <img src="https://img.shields.io/badge/EDR_%28Defender%2C_CrowdStrike%29-87701d?style=for-the-badge" alt="EDR (Defender, CrowdStrike)">
</p>

**CLOUD & IDENTITY**

<p>
  <img src="https://img.shields.io/badge/IAM_/_SSO_/_OAuth_2.0-3d6349?style=for-the-badge" alt="IAM / SSO / OAuth 2.0">
  <img src="https://img.shields.io/badge/MFA_rollout-3d6349?style=for-the-badge" alt="MFA rollout">
  <img src="https://img.shields.io/badge/Least_privilege_/_RBAC-3d6349?style=for-the-badge" alt="Least privilege / RBAC">
  <img src="https://img.shields.io/badge/SSH_hardening_%26_allowlists-3d6349?style=for-the-badge&logo=openssh&logoColor=white" alt="SSH hardening & allowlists">
  <img src="https://img.shields.io/badge/Microsoft_Azure-87701d?style=for-the-badge" alt="Microsoft Azure">
  <img src="https://img.shields.io/badge/Microsoft_Entra_ID-87701d?style=for-the-badge" alt="Microsoft Entra ID">
  <img src="https://img.shields.io/badge/Defender_for_Cloud-87701d?style=for-the-badge" alt="Defender for Cloud">
  <img src="https://img.shields.io/badge/AWS_%28IAM%2C_EC2%29-87701d?style=for-the-badge&logo=amazonwebservices&logoColor=white" alt="AWS (IAM, EC2)">
</p>

**INFRASTRUCTURE**

<p>
  <img src="https://img.shields.io/badge/Linux_administration-3d6349?style=for-the-badge&logo=linux&logoColor=white" alt="Linux administration">
  <img src="https://img.shields.io/badge/Windows_Server_/_Active_Directory-3d6349?style=for-the-badge" alt="Windows Server / Active Directory">
  <img src="https://img.shields.io/badge/Proxmox_/_VM_labs-3d6349?style=for-the-badge&logo=proxmox&logoColor=white" alt="Proxmox / VM labs">
  <img src="https://img.shields.io/badge/VirtualBox_/_Kali_Linux-3d6349?style=for-the-badge&logo=kalilinux&logoColor=white" alt="VirtualBox / Kali Linux">
  <img src="https://img.shields.io/badge/pfSense_/_firewalls-3d6349?style=for-the-badge&logo=pfsense&logoColor=white" alt="pfSense / firewalls">
  <img src="https://img.shields.io/badge/TCP/IP_%26_DNS-3d6349?style=for-the-badge" alt="TCP/IP & DNS">
  <img src="https://img.shields.io/badge/Raspberry_Pi_/_ARM-3d6349?style=for-the-badge&logo=raspberrypi&logoColor=white" alt="Raspberry Pi / ARM">
  <img src="https://img.shields.io/badge/Load_balancing-3d6349?style=for-the-badge" alt="Load balancing">
  <img src="https://img.shields.io/badge/Cloudflare_/_DDoS_mitigation-3d6349?style=for-the-badge&logo=cloudflare&logoColor=white" alt="Cloudflare / DDoS mitigation">
  <img src="https://img.shields.io/badge/Automation_%28cron%2C_systemd%29-3d6349?style=for-the-badge" alt="Automation (cron, systemd)">
  <img src="https://img.shields.io/badge/Network_segmentation-87701d?style=for-the-badge" alt="Network segmentation">
  <img src="https://img.shields.io/badge/Backup_%26_recovery-87701d?style=for-the-badge" alt="Backup & recovery">
</p>

**ANALYSIS & RESEARCH**

<p>
  <img src="https://img.shields.io/badge/Java_decompilation_%28Vineflower%2C_CFR%29-3d6349?style=for-the-badge" alt="Java decompilation (Vineflower, CFR)">
  <img src="https://img.shields.io/badge/Protocol_/_packet_analysis-3d6349?style=for-the-badge" alt="Protocol / packet analysis">
  <img src="https://img.shields.io/badge/Coordinated_disclosure-3d6349?style=for-the-badge" alt="Coordinated disclosure">
  <img src="https://img.shields.io/badge/Secure_code_review-3d6349?style=for-the-badge" alt="Secure code review">
  <img src="https://img.shields.io/badge/CWE_classification_%26_triage-3d6349?style=for-the-badge" alt="CWE classification & triage">
  <img src="https://img.shields.io/badge/Bash_scripting-3d6349?style=for-the-badge&logo=gnubash&logoColor=white" alt="Bash scripting">
  <img src="https://img.shields.io/badge/Vulnerability_management-87701d?style=for-the-badge" alt="Vulnerability management">
  <img src="https://img.shields.io/badge/Python_tooling-87701d?style=for-the-badge&logo=python&logoColor=white" alt="Python tooling">
  <img src="https://img.shields.io/badge/Burp_Suite-7a7263?style=for-the-badge&logo=burpsuite&logoColor=white" alt="Burp Suite">
</p>

**DEVELOPMENT & DATA**

<p>
  <img src="https://img.shields.io/badge/SourcePawn_/_SourceMod-3d6349?style=for-the-badge" alt="SourcePawn / SourceMod">
  <img src="https://img.shields.io/badge/C%2B%2B_%28Metamod_extensions%29-3d6349?style=for-the-badge&logo=cplusplus&logoColor=white" alt="C++ (Metamod extensions)">
  <img src="https://img.shields.io/badge/Function_hooking_/_detours-3d6349?style=for-the-badge" alt="Function hooking / detours">
  <img src="https://img.shields.io/badge/SQLite-3d6349?style=for-the-badge&logo=sqlite&logoColor=white" alt="SQLite">
  <img src="https://img.shields.io/badge/MySQL-3d6349?style=for-the-badge&logo=mysql&logoColor=white" alt="MySQL">
  <img src="https://img.shields.io/badge/Parameterized_/_escaped_SQL-3d6349?style=for-the-badge" alt="Parameterized / escaped SQL">
  <img src="https://img.shields.io/badge/REST_API_integration-3d6349?style=for-the-badge" alt="REST API integration">
  <img src="https://img.shields.io/badge/Discord_webhooks-3d6349?style=for-the-badge&logo=discord&logoColor=white" alt="Discord webhooks">
  <img src="https://img.shields.io/badge/Git_/_GitHub-3d6349?style=for-the-badge&logo=github&logoColor=white" alt="Git / GitHub">
  <img src="https://img.shields.io/badge/Secrets_hygiene-3d6349?style=for-the-badge" alt="Secrets hygiene">
  <img src="https://img.shields.io/badge/C_/_memory_safety-7a7263?style=for-the-badge" alt="C / memory safety">
</p>

**AI SECURITY**

<p>
  <img src="https://img.shields.io/badge/OWASP_LLM_Top_10-87701d?style=for-the-badge&logo=owasp&logoColor=white" alt="OWASP LLM Top 10">
  <img src="https://img.shields.io/badge/Prompt_injection_%28direct_%26_indirect%29-87701d?style=for-the-badge" alt="Prompt injection (direct & indirect)">
  <img src="https://img.shields.io/badge/Excessive_agency-87701d?style=for-the-badge" alt="Excessive agency">
  <img src="https://img.shields.io/badge/MCP_server_authorization-87701d?style=for-the-badge" alt="MCP server authorization">
  <img src="https://img.shields.io/badge/Tool_poisoning-87701d?style=for-the-badge" alt="Tool poisoning">
  <img src="https://img.shields.io/badge/SAST_on_AI--generated_code-87701d?style=for-the-badge" alt="SAST on AI-generated code">
</p>

**GOVERNANCE & SUPPORT**

<p>
  <img src="https://img.shields.io/badge/PII_handling_%28FERPA%29-3d6349?style=for-the-badge" alt="PII handling (FERPA)">
  <img src="https://img.shields.io/badge/Ticketing_%26_escalation-3d6349?style=for-the-badge" alt="Ticketing & escalation">
  <img src="https://img.shields.io/badge/Open_source_licensing_%28GPL%29-3d6349?style=for-the-badge" alt="Open source licensing (GPL)">
  <img src="https://img.shields.io/badge/OWASP_ASVS-87701d?style=for-the-badge&logo=owasp&logoColor=white" alt="OWASP ASVS">
  <img src="https://img.shields.io/badge/NIST_CSF-87701d?style=for-the-badge" alt="NIST CSF">
  <img src="https://img.shields.io/badge/PCI_DSS-87701d?style=for-the-badge" alt="PCI DSS">
</p>

### Roadmap

**Cleared**

- [x] CompTIA Security+, Network+, CySA+, Server+
- [x] Microsoft SC-200 & SC-500
- [x] Tracked two MC-001 findings through to a deployed patch: Trinkets & Baubles 0.33.4, and Chunk-Pregenerator across every supported branch with advisory GHSA-x6cg-7cqm-2pqf crediting me as finder
- [x] Segmented home lab standing, attacks run against it, detections logged
- [x] Pi-hole sinkhole live network-wide, including what it misses
- [x] Over 400+ modded Minecraft mods (.jar) from #1 most downloaded modpack (RLCraft *30M+ downloads* & RLCraft Dregora *1M downloads*) decompiled and scanned to find packets that are not permission gated. 64 mods and 211 total ungated packets found, reported to their developer & RLCraft development team, privately tested by me through a client-side mod (C2S), graded the severity of packets found based on their impact, and documented

**In progress**

- [ ] Microsoft SC-100, Cybersecurity Architect
- [ ] ISC2 CISSP - Certified Information Systems Security Professional

**Next**

- [ ] A.S. Cybersecurity, Coastline College, 2027
- [ ] Accepted and enrolling into a University for a bachelor's degree in Cybersecurity, IT, or Informatics
- [ ] 2027 internship: Cybersecurity, IT, Cloud/Network Security, SOC, or Security Analyst field

---

## <img src="https://kevinle.tech/assets/img/enchanted-book.gif" align="absmiddle" alt=""> DISCLOSURE ETHICS

All of this ran on my own systems. I never touched an external device, never did
anything illegal, and never went in with bad intent. Findings go to the developer or
maintainer first. Anything I test after that stays private and exists only as
documentation, and it stays redacted until a patch ships or the maintainer tells me
I can publish.

```
════════════════════════════════════════════════════════════
  END OF FILE            github.com/banyourself
════════════════════════════════════════════════════════════
```
