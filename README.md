# RetroBoxDB GameGear

English | [中文说明](README.zh-CN.md)

Single-file SQLite preservation database for Sega Game Gear. The public Catalog holds metadata only (checksums, DAT and provenance records, header fields, archive recipes and the processing code); it contains no ROM data and cannot restore files. The populated database stays local.

| Item | Value |
| --- | --- |
| Original size | 1,459 source ZIPs, 254.2 MiB (No-Intro 1,319, RetroAchievements sets 140); 1,459 ROM files, 501.3 MiB uncompressed |
| Stored size | populated database 80.0 MiB; public Catalog 13.7 MiB (no ROM data) |
| Ratio | 31.5% of the source ZIPs, 16.0% of the uncompressed ROM files |
| Technology | storage v4: SHA256-deduplicated 32 KiB blocks packed in No-Intro family order into solid LZMA2 groups of up to 256 MiB (256 MiB dictionary); per-block SHA256 and per-object CRC32/MD5/SHA1/SHA256 verification; source ZIPs reproduced byte-for-byte from TorrentZip plans |
| Export performance | Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz, idle, Python 3.14.4, all checks included. whole newest-DAT set with `export_set.py` (905 files, each checked against the DAT hashes): 47.5 MiB/s, 7 ms per file on average; single file with a cold cache (the group is decoded up to the file): ROM 1.913 s, TorrentZip 1.784 s on average |

## Downloads and documents

| File / document | Content |
| --- | --- |
| [RetroBoxDB.GameGear.Catalog.sqlite](https://github.com/rshi0212/RetroBoxDB-GameGear/releases/latest/download/RetroBoxDB.GameGear.Catalog.sqlite) | Public Catalog (Release asset with `SHA256SUMS`) |
| [Storage v4 guide](RetroBoxDB.Storage-v4.en.md) / [中文](RetroBoxDB.Storage-v4.zh-CN.md) | Storage evaluation, contents, RA, names and maintenance for every platform |
| [Technical design](RetroBoxDB.Storage-v4.Technical-Design.en.md) | Storage format, platform adapters, incremental updates, verification |
| [RA list](reports/ra-gamegear-games.csv) / [summary](reports/ra-gamegear.json), [build report](reports/gamegear-build-report.json), [audit resolution](reports/audit-resolution-20261004.md) | Detailed data |

## Storage choice and platform specifics

24 block/group combinations measured on the whole local collection (`assessment/data/storage-experiment-gamegear.json`): smallest 32 KiB / 256 MiB at 63.83 MiB; by the rule (within 0.5% of the smallest, the smallest block, then the smallest group) 32 KiB / 256 MiB at 63.83 MiB. ZIPs 254.16 MiB, per-file LZMA 139.89 MiB.

- ROMs are 32 KiB–1 MiB; the No-Intro folders hold 1,230 licensed and 89 aftermarket ZIPs, and the RetroAchievements set adds 140 files (hacks, translations, homebrew). Three local DAT versions are imported and diffed against the newest.
- Game Gear cartridges carry the Master System `TMR SEGA` header (`sms_hardware`) with the Game Gear region codes (Japan, Export, International). The Game Gear BIOS does not check the checksum and most cartridges declare 0: `v_sms_headers.checksum_status` reports these as `not_declared` (not as a mismatch or a warning). Cartridges that run in Master System mode carry an SMS region code; 25 dumps have no header and stay `unclassified`.
- RetroAchievements console 15 (whole-file MD5).

## Contents

| Item | Value |
| --- | --- |
| ROM records / games / releases | 927 / 474 / 928 |
| DAT coverage per version | 20260706-223743: 905/927; 20260725-204513: 905/928; 20260822-061808: 905/928 |
| Local ROMs in no DAT | 22 |
| ROM files of the RetroAchievements set | in a No-Intro DAT 118, RA only 22, hash not in the latest RA snapshot 0 ([list](reports/ra-gamegear-collection-unknown.csv)); RA games still without a local ROM: [gap list](reports/ra-gamegear-missing.csv) |
| No-Intro DB Export + Dump Log 20260822-061808 | 928 archives, 951 file identities, 354 documented hardware assertions; Dump Log Verified 443 |
| RetroAchievements (console 15) | 167 games with achievements: 165 with a local ROM (192 ROMs), 0 with the ROM in a sibling database, 0 DAT only, 0 DB file only, 2 without a No-Intro counterpart |
| Chinese names | 825 of 825 rows translated (411 unique); 823 local ROMs have a Chinese name |
| Populated-database audit | 934 objects, 2 groups, 1,306 archive plans, all passed |

Every source ZIP is reproduced byte-for-byte from its TorrentZip plan (`v_file_checksums.exported_bytes_equal_source`).

## Usage

```bash
# Query-only audit with the Catalog's embedded engine (also: stats, checksums FILE_ID, help)
python3 -B -c 'import sqlite3,sys; c=sqlite3.connect(sys.argv[1]); s=c.execute("SELECT content FROM resources WHERE name=?",("engine.py",)).fetchone()[0]; c.close(); exec(compile(s,"RetroBoxDB:engine.py","exec"))' ./RetroBoxDB.GameGear.Catalog.sqlite audit
# Populated database: export by DAT version, 1G1R, RA achievements, TorrentZip or plain ROMs
python3 -B tools/export_set.py RetroBoxDB.GameGear.sqlite OUT --set 1g1r --ra achievements --container torrentzip --layout ra-category
# Add new DATs, DB Export / Dump Log snapshots, ROMs and an RA snapshot incrementally
python3 -B tools/update_db.py RetroBoxDB.GameGear.sqlite --discover --ra --catalog RetroBoxDB.GameGear.Catalog.sqlite
```

Python 3.10+ standard library only. `engine.py` and the other `resources` entries are executable code; run them only from a database you built or a Release asset whose SHA256 you verified. Releases are produced by `.github/workflows/publish-catalog.yml`: it starts from the base Catalog pinned in `release/catalog-release.json`, injects the engine and documents of this commit, checks every data-table digest, runs the tests and the audit, then publishes.
