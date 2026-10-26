# Rise of the Dragon - Sega CD Audio Copy & ScummVM Voice Mod

## Voiceover Hack (Pre-Release Build)
This project includes an experimental voiceover integration hack. 
- **Important Note:** This repository currently uses a pre-release build (`RoDAudioExt.zip`) containing an updated `RoDData.xml` file with expanded string mappings. 
- Ensure you keep the included `RoDData.xml` in your project data path so newer text strings properly trigger their corresponding audio files.
- *Note:* This structure is experimental and will be officially updated once the author releases a stable version on GitHub.

**Credits & Acknowledgement:** 
* The mod architecture and database mapping was originally created by [BehindTimes](https://github.com/BehindTimes). 
* The audio extraction relies on the `mcd_pcm` utility created by **GerbilSoft** (Source available on their [GitHub repository](https://github.com/GerbilSoft/gens-gs-ii/blob/master/src/tools/mcd_pcm.c)).

This project extracts the Sega CD audio and formats it so it can be streamed directly into the PC DOS version of the game using a patched version of ScummVM.

## 📁 Repository Overview
* `RoDData.xml`: A list of every dialog in the game, the amount of time it should be played, and the Sega PCM Audio files associated with it.
* `audio.csv`: A tracker file showing what audio files belong to what dialogs.
* `audiocopy/`: The cross-platform C# .NET application that processes the audio database.
* `apply_patch.py`: A Python script that uses dynamic regex to patch ScummVM's `dgds` engine (highly resilient against future ScummVM updates).
* `extract_sd4.py`: A Python utility to safely rip all audio files from the mounted Sega CD.

---

## 🚀 Step 1: Extract & Process the Audio

### Option A: Windows
**Dependencies:** Install the [**.NET SDK**](https://dotnet.microsoft.com/download).

1. Download the pre-built release package: [RoDAudioExt.zip](https://github.com/BehindTimes/RiseOfDragonAudioCopy/releases/download/v2.11/RoDAudioExt.zip).
2. **⚠️ IMPORTANT:** Do NOT extract the `.zip` file directly into this repository, as it will overwrite updated scripts. Open the `.zip` and extract **ONLY** the following binaries into the root of this repository:
   * `SegaPCM.exe`
   * `RemovePop.exe`
   * `CombineWave.exe`
3. Create a folder named `SD4` in this directory and copy all `.sd4` files from your Sega CD disc into it (you can use `python extract_sd4.py` to automate this).
4. Run `makewav.bat`.
5. Run `process_wav.bat`.
6. Rename the newly generated `audio_back` directory (located inside `WAV`) to `audio` and copy it directly into your PC DOS *Rise of the Dragon* game folder.

### Option B: Linux / macOS (Native Pipeline)
This fork features robust `.sh` pipelines that seamlessly execute the legacy Windows binaries via Wine (bypassing Linux file-case crashes) before finishing the database mapping with native cross-platform `.NET`.

**Dependencies:** Install `wine` and the `.NET SDK`.
*(On Arch Linux / CachyOS, run: `sudo pacman -S wine dotnet-sdk`)*

1. Download [RoDAudioExt.zip](https://github.com/BehindTimes/RiseOfDragonAudioCopy/releases/download/v2.11/RoDAudioExt.zip). Extract `SegaPCM.exe`, `RemovePop.exe`, and `CombineWave.exe` into the root of this repository.
2. Run `python3 extract_sd4.py` to automatically locate and copy all `.sd4` files from your mounted Sega CD into the `SD4/` directory.
3. Make the shell scripts executable:
   ```bash
   chmod +x makewav.sh process_wav.sh
   ```
4. Run `./makewav.sh` to extract the audio using Wine.
5. Run `./process_wav.sh` to clean, merge, and organize the files into the final format.
6. Rename the newly generated `audio_back` directory (located inside `WAV`) to `audio` and copy it directly into your PC DOS *Rise of the Dragon* game folder.

---

## 🎮 Step 2: Patching ScummVM (All Platforms)
Because ScummVM updates frequently, static diff patches break easily. Use the provided `apply_patch.py`, which dynamically searches the `dgds` engine source code and patches it flawlessly.

1. Clone the latest [ScummVM source code](https://github.com/scummvm/scummvm).
2. Copy `apply_patch.py` from this repo into the root of the cloned `scummvm` folder.
3. Run the patcher:
   ```bash
   python3 apply_patch.py
   ```
4. Configure and build a lightweight version of ScummVM specifically for the DGDS engine:
   ```bash
   ./configure --disable-all-engines --enable-engine=dgds --enable-optimizations
   make -j$(nproc)
   ```
5. Launch ScummVM, add your PC DOS copy of *Rise of the Dragon*. Click **Edit Game...**, select the **Audio** tab, check **Override global audio settings**, and set Text and Speech to **Both speech and subtitles**.
