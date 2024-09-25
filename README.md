# MC3DS-Save-Analysis

This repo contains preliminary analysis on the extra data save files for Minecraft: New Nintendo 3DS Edition

This document in an *extremely* early stage, so it doesn't make much sense yet

There is also an ImHex hexpat at https://github.com/DexrnZacAttack/MC3DS-Hexpat

## Credits

- Thanks to [Cracko298](https://github.com/Cracko298), [Offroaders123](https://github.com/Offroaders123), and [Dexrn ZacAttack](https://github.com/DexrnZacAttack), who all helped massively

## ExtData

At the root are several files with the extension `.txt`.  Despite the extension, **these are not plain text files**.
Instead of using Unicode character `0x20` (space) they use the NULL byte `0x00`, or character `0x01` depending on the game version.

* `clientId.txt` is a 19-digit ASCII decimal number.  The file padded to 128 KiB with null bytes. 
* `options.txt` and `achievements.txt` start with a 4-byte header (version of file) and text is spaced with NULL Chars.
* `minecraftWorlds/` is a directory that is described below.


The actual world files are stored inside the `minecraftWorld` directory.  Each world is a subdirectory with a base64 name (for example, `/minecraftWorlds/igAAAJ8dAgA=`).  The rest of this document will be describing the content inside the world folders, from now on `/level.dat` means `/minecraftWorlds/<world>/level.dat`

## Worlds

Each world has these files:

* `level.dat`: a standard NBT file, can be parsed with some libraries such as [NBTify](https://github.com/Offroaders123/NBTify), and can be edited with a GUI in [Dovetail](https://offroaders123.github.io/Dovetail/).
  - The file is saved with the uncompressed little-endian NBT variant, which coincides with that of Bedrock Edition's `level.dat` format.
  - The file is prefixed with an [8-byte header](https://wiki.bedrock.dev/nbt/nbt-in-depth.html#bedrock-nbt-file-header), which consists of two `uint32` values (also in little-endian). The first one is the version of the save file, which matches the `StorageVersion` key in the NBT itself. The second value is a validation on the byte length of the NBT data itself, which is essentially the length of the whole file, subtract 8, for the header.
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
There are 4 types of `.vdb` files.
One of the types is based on NBT.
Other two are unknown.

How the data is stored in the file itself is unique to this database format, however the save data is for the most part related to similar values used in Bedrock Edition's save format.

NBT is saved in uncompressed little-endian, and (as of yet) it doesn't seem to be using [VarInt-encoded NBT](https://wiki.vg/VarInt_And_VarLong), which is also synonymous with Bedrock Edition, which only uses VarInt encoding for NBT sent in network packets. I thought I'd only mention this extra part just in case we encounter 'malformed NBT', and we don't know why it isn't working.

### Level.dat
The `level.dat` file has different formats depending on the version.
We have only looked at 0.1.0 and 1.9.19 formats.

 0.1.0 vs. 1.9.19 format:
```ts
// The types here are represented with NBTify's TypeScript typings for NBT tags.
// `BooleanTag` is just an alias to a `ByteTag<0 | 1>` value.

// This is the 0.1.0 format:

interface Level {
  DayCycleStopTime: IntTag;
  Difficulty: IntTag;
  ForceGameType: BooleanTag;
  GameType: IntTag;
  Generator: IntTag;
  LastPlayed: LongTag;
  LevelName: StringTag;
  LevelSize: IntTag;
  LimitedWorldOriginX: IntTag;
  LimitedWorldOriginY: IntTag;
  LimitedWorldOriginZ: IntTag;
  NetworkVersion: IntTag;
  PackID: IntTag;
  Platform: IntTag;
  RandomSeed: LongTag;
  SpawnX: IntTag;
  SpawnY: IntTag;
  SpawnZ: IntTag;
  StorageVersion: IntTag;
  Time: LongTag;
  currentTick: LongTag;
  eduLevel: BooleanTag;
  globalPermissions: IntTag;
  hasBeenLoadedInCreative: BooleanTag;
  lightningLevel: FloatTag;
  lightningTime: IntTag;
  rainLevel: FloatTag;
  rainTime: IntTag;
  spawnMobs: BooleanTag;
  worldStartCount: LongTag;
}

// This is the 1.9.19 format:

interface Level {
  DayCycleStopTime: IntTag;
  Difficulty: IntTag;
  ForceGameType: BooleanTag;
  ForcedMansionX: IntTag;
  ForcedMansionZ: IntTag;
  GameType: IntTag;
  Generator: IntTag;
  LANBroadcast: BooleanTag;
  LastPlayed: LongTag;
  LevelName: StringTag;
  LevelSize: IntTag;
  LimitedWorldOriginX: IntTag;
  LimitedWorldOriginY: IntTag;
  LimitedWorldOriginZ: IntTag;
  MultiplayerGame: BooleanTag;
  NetworkVersion: IntTag;
  PackID: IntTag;
  Platform: IntTag;
  RandomSeed: LongTag;
  SpawnRotX: FloatTag;
  SpawnRotY: FloatTag;
  SpawnX: IntTag;
  SpawnY: IntTag;
  SpawnZ: IntTag;
  StorageVersion: IntTag;
  StructuresForceGenerated: BooleanTag;
  StructuresForceGeneratedAttempted: BooleanTag;
  Time: LongTag;
  XBLBroadcast: BooleanTag;
  achievementsDisabled: BooleanTag;
  commandblockoutput: BooleanTag;
  commandsEnabled: BooleanTag;
  currentTick: LongTag;
  dodaylightcycle: BooleanTag;
  doentitydrops: BooleanTag;
  dofiretick: BooleanTag;
  domobloot: BooleanTag;
  domobspawning: BooleanTag;
  dotiledrops: BooleanTag;
  doweathercycle: BooleanTag;
  drowningdamage: BooleanTag;
  eduLevel: BooleanTag;
  falldamage: BooleanTag;
  firedamage: BooleanTag;
  fixedInventory: {
    // `Item` is likely a Compound tag in the shape of an NBT inventory item.
    fixedInventoryItems: Item[];
  };
  hasBeenLoadedInCreative: BooleanTag;
  immutableWorld: BooleanTag;
  keepinventory: BooleanTag;
  lastOpenedWithVersion: [IntTag, IntTag, IntTag, IntTag];
  lightningLevel: FloatTag;
  lightningTime: IntTag;
  mobgriefing: BooleanTag;
  pvp: BooleanTag;
  rainLevel: FloatTag;
  rainTime: IntTag;
  sendcommandfeedback: BooleanTag;
  spawnMobs: BooleanTag;
  texturePacksRequired: BooleanTag;
  worldStartCount: LongTag;
}
```
