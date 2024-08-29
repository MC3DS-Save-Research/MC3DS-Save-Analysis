# MC3DS-Save-Analysis

This repo contains preliminary analysis on the extra data save files for Minecraft: New Nintendo 3DS Edition

This document in an *extremely* early stage, so it doesn't make much sense yet

## Credits

Thanks to Cracko298, who helped massively

## ExtData

At the root are several files with the extension `.txt`.  Despite the extension, these are not quite plain text files.

* `clientId.txt` is a 19-digit ASCII decimal number.  The file padded to 128 KiB with null bytes
* `options.txt` and `achievements.txt` start with a 4-byte header (checksum?) and are plaintext after that
* `minecraftWorlds/` is a directory that is described below

The actual world files are stored inside the `minecraftWorld` directory.  Each world is a subdirectory with a base64 name (for example, `/minecraftWorlds/igAAAJ8dAgA=`).  The rest of this document will be describing the content inside the world folders, from now on `/level.dat` means `/minecraftWorlds/<world>/level.dat`

## Worlds

Each world has these files:

* `level.dat`: a standard NBT file, can be parsed with some libraries such as [Dovetail](https://offroaders123.github.io/Dovetail/)
* `level.dat_old`: a backup of `level.dat`
* `db/`
  * `savemarker`: 4-byte file
  * `cdb/`
    Chunk data
    * `index.cdb`: maps CDB files to the VDB files
    * `newindex.cdb`: another index file for some reason
    * `slt0.cdb`, `slt1.cdb`, ...: chunk data
  * `vdb/`
    "Player Inv, Health, Stamina, Exp, and achievement progress along with Map Exploration and current positions" - Cracko298
    * `index.vdb`: maps VDB files to the CDB files
    * `newindex.vdb`: another index file for some reason
    * `slt0.vdb`, `slt1.vdb`, ...: data

### CDB file format

#### slt files

*Currently working on this*

Each file has a 128-byte header.  It starts with chunk metadata and then defines where the chunks are.  All data after the header is the chunks

### VDB file format

Unknown
