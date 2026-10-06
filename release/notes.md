GameGear Catalog, storage v4 (32 KiB blocks, 2 solid LZMA2 groups of up to 256 MiB). Metadata only: **no ROM payloads are published**; `compression_groups`, `chunks` and `object_chunks` are empty.

- First release of this platform.
- One schema for all twenty-three platforms: the header tables of the eight new platforms (Game Gear, PC Engine, SuperGrafx, MSX, MSX2, Virtual Boy, Game & Watch, Super A'Can: `pce_hardware`, `msx_hardware`, `vb_hardware`) and `rom_annotations` exist in every Catalog; tables of other platforms and provider tables have no rows.
- RetroAchievements reports look up sibling databases (NES<->FDS, SNES<->Satellaview, WonderSwan<->WonderSwan Color, NeoGeo Pocket<->NeoGeo Pocket Color, PC Engine<->SuperGrafx, MSX<->MSX2): a game whose ROM is stored there is `local_other_platform`, not a gap.
- Source: 1,459 ZIPs (nointro 1,319, retroachievements 140), 254.2 MiB (1,459 ROM files, 501.3 MiB uncompressed). Populated database: 80.0 MiB (31.5% of the ZIPs). All source ZIPs are reproduced byte-for-byte.
- Contents: 927 ROM records, 474 games, 928 releases; DAT versions: 20260706-223743, 20260725-204513, 20260822-061808.
- RetroAchievements: 165 of 167 games with achievements have a local ROM.
- Export (Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz, idle, all checks): whole newest-DAT set with export_set.py 47.5 MiB/s (905 files); single file with a cold cache 1.913 s (ROM) / 1.784 s (TorrentZip) on average.
- Full audit of the populated database: 934 objects, 2 groups, 1,306 archive plans, no errors.

The release workflow starts from the base Catalog pinned by SHA256 in `release/catalog-release.json`, injects the engine and documents of the tagged commit, checks every data-table digest, SQLite integrity and foreign keys, runs the Catalog audit and the repository tests. Verify the download with `SHA256SUMS`.

[中文说明](https://github.com/rshi0212/RetroBoxDB-GameGear/blob/main/README.zh-CN.md)
