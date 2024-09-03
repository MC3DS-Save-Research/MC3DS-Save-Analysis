# MC3DS-Save-Analysis

This repo contains preliminary analysis on the extra data save files for Minecraft: New Nintendo 3DS Edition

This document in an *extremely* early stage, so it doesn't make much sense yet

## Credits

- Thanks to Cracko298, Offroaders123, and Dexrn ZacAttack, who all helped massively

## ExtData

At the root are several files with the extension `.txt`.  Despite the extension, these are not quite plain text files.

* `clientId.txt` is a 19-digit ASCII decimal number.  The file padded to 128 KiB with null bytes. 
* `options.txt` and `achievements.txt` start with a 4-byte header (version of file) and are Text spaced with NULL Chars.
* `minecraftWorlds/` is a directory that is described below.

- Do note, all Text Files are stored as binary and not actually Text Files.
- Instead of Using UniCode Character Byte `0x20` they use UniCode NULL Byte `0x00`, or UniCode Character `0x01` Depending on the SaveGame Version.

The actual world files are stored inside the `minecraftWorld` directory.  Each world is a subdirectory with a base64 name (for example, `/minecraftWorlds/igAAAJ8dAgA=`).  The rest of this document will be describing the content inside the world folders, from now on `/level.dat` means `/minecraftWorlds/<world>/level.dat`

## Worlds

Each world has these files:

* `level.dat`: a standard NBT file, can be parsed with some libraries such as [Dovetail](https://offroaders123.github.io/Dovetail/)
* `level.dat_old`: a backup of `level.dat`. If `level.dat` become corrupted game reverts to previous SaveGame then.
* `db/`
  * `savemarker`: 4-byte file
  * `cdb/`: Cubical Database.
    Chunk data
    * `index.cdb`: maps CDB files to the VDB files, acts as a backup for newindex.cdb
    * `newindex.cdb`: The main index, has backup (index.cdb), if becomes corrupted, reverts to previous SaveGame then.
    * `slt0.cdb`, `slt1.cdb`, ...: chunk data
  * `vdb/`: Variable Database.
    "Player Inv, Health, Stamina, Exp, and achievement progress along with Map Exploration and current positions" - Cracko298
    * `index.vdb`: maps VDB files to the CDB files, acts as a backup for newindex.vdb
    * `newindex.vdb`: The main index, has backup (index.cdb), if becomes corrupted, reverts to previous SaveGame then.
    * `slt0.vdb`, `slt1.vdb`, ...: data

### CDB file format

#### SLT files

*Currently working on this*

Each file has a 128-byte header.
It starts with chunk metadata and then defines where the chunks are.
All data after the header is the chunks

### VDB file format
There are 3 types of .vdb files.
One of the types is based on NBT.
Other two are unknown.

### Level.dat
The level.dat file has different formats depending on the version.
We have only looked at 0.1.0 and 1.9.19 formats.

 0.1.0 vs. 1.9.19 format:
```
This is the 0.1.0 format:
{
  DayCycleStopTime: -1,
  Difficulty: 1,
  ForceGameType: 0b,
  GameType: 0,
  Generator: 1,
  LastPlayed: 1725111871l,
  LevelName: "My World",
  LevelSize: 2,
  LimitedWorldOriginX: 592,
  LimitedWorldOriginY: 128,
  LimitedWorldOriginZ: 4,
  NetworkVersion: 82,
  PackID: 2039825748,
  Platform: 2,
  RandomSeed: 3567040199l,
  SpawnX: 592,
  SpawnY: 128,
  SpawnZ: 4,
  StorageVersion: 4,
  Time: 13252l,
  currentTick: 13252l,
  eduLevel: 0b,
  globalPermissions: 2,
  hasBeenLoadedInCreative: 0b,
  lightningLevel: 0.0f,
  lightningTime: 82748,
  rainLevel: 0.0f,
  rainTime: 34748,
  spawnMobs: 1b,
  worldStartCount: 4294967294l
}

This is the 1.9.19 format:

{
  RandomSeed: 2685709277l,
  GameType: 1,
  Difficulty: 0,
  ForceGameType: 0b,
  SpawnX: 4,
  SpawnY: 32767,
  SpawnZ: 16,
  Time: 0l,
  LastPlayed: 1293915253l,
  LevelName: "Flat Creative",
  StorageVersion: 5,
  NetworkVersion: 115,
  Platform: 2,
  spawnMobs: 1b,
  Generator: 2,
  LimitedWorldOriginX: 4,
  LimitedWorldOriginY: 32767,
  LimitedWorldOriginZ: 16,
  DayCycleStopTime: 0,
  worldStartCount: 4294967249l,
  currentTick: 1176073l,
  rainLevel: 0.0f,
  rainTime: 22167,
  lightningLevel: 0.0f,
  lightningTime: 10702,
  hasBeenLoadedInCreative: 1b,
  achievementsDisabled: 1b,
  eduLevel: 0b,
  immutableWorld: 0b,
  LevelSize: 2,
  MultiplayerGame: 0b,
  LANBroadcast: 0b,
  XBLBroadcast: 0b,
  PackID: 2039825748,
  commandsEnabled: 0b,
  texturePacksRequired: 0b,
  lastOpenedWithVersion: [1, 1, 7, 1],
  commandblockoutput: 1b,
  dodaylightcycle: 0b,
  doentitydrops: 1b,
  dofiretick: 1b,
  domobloot: 1b,
  domobspawning: 1b,
  dotiledrops: 1b,
  doweathercycle: 1b,
  drowningdamage: 1b,
  falldamage: 1b,
  firedamage: 1b,
  keepinventory: 0b,
  mobgriefing: 1b,
  pvp: 1b,
  sendcommandfeedback: 1b,
  fixedInventory: {
    fixedInventoryItems: []
  },
  SpawnRotX: 0.0f,
  SpawnRotY: 0.0f,
  StructuresForceGenerated: 0b,
  StructuresForceGeneratedAttempted: 1b,
  ForcedMansionX: -2147483648,
  ForcedMansionZ: -2147483648
}
