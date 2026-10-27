# nxnative

Play your own Nintendo Switch games natively on iPhone, iPad and Mac. Bring your game (its NSP files) and your `prod.keys`; one script turns them into an IPA that you sideload, or into a Mac app for a Mac with Apple silicon.

It is not an emulator: the game's own code runs directly on the Apple chip, with Switch graphics rebuilt on Metal. Nothing from any game is in this repository, and the project is not affiliated with Nintendo or any game publisher.

> [!TIP]
> **Prefer an app to the command line?** [NX2Apple](https://github.com/LNPANDY/NX2Apple) is a community Mac app that builds your games for the iPhone, iPad or Mac, keeps the Mac games in a library and starts them with their settings. It is a separate project that keeps up with this one; this page describes nxnative's own command-line way. Building a front end of your own: [Front ends](docs/frontends.md).

## Acknowledgements

This project is built on other people's work:

- **[d2r-mobile](https://github.com/jhaizhou-ops/d2r-mobile)** (MIT) developed this native-execution approach, for Diablo II: Resurrected. nxnative started as a copy of its generic parts: the NSO rewriter, the host that stands in for Horizon, the NVN-on-Metal layer and the iOS app shell. Fixes go both ways.
- **Ryujinx** (MIT): its shader translator (`Ryujinx.Graphics.Shader`, `Ryujinx.Common` and `Spv.Generator` 1.3.2, in `dm/third_party/ryujinx` with a few fixes listed in [its README](dm/third_party/ryujinx/README.md)) turns the games' Maxwell shaders into SPIR-V. Its emulation of the kernel and system services is also a reference when the host's behaviour is in doubt.
- **[Atmosphère](https://github.com/Atmosphere-NX/Atmosphere)**: its kernel, mesosphère, is the reference for Horizon's synchronization semantics (address arbiter, condition variables).
- **yuzu**: Maxwell instruction encodings for the shader reference tool, and a cross-check for kernel behaviour.
- **[LibHac](https://github.com/Thealexbarney/LibHac)**: reads NSPs and layers updates over the base game in `dm/tools/nx_extract`. **hactool** did the first extractions.
- **[SPIRV-Cross](https://github.com/KhronosGroup/SPIRV-Cross)**: SPIR-V to Metal Shading Language. **[glslang](https://github.com/KhronosGroup/glslang)** compiles the GLSL of OpenGL games to SPIR-V for it.
- **deko3d** (zlib): its texture layout size algorithm is ported in `dm/gfx/nvn/nvn_tex.c`.
- **libvpx** (VP9 movies), **stb_vorbis** (movie sound), the **Vulkan headers** and **NVIDIA FLIP** ship in `dm/third_party` with their licenses ([list](dm/third_party/README.md)).
- **Capstone**, **lz4**, **fontTools** and **XcodeGen** are used by the build.

## Games

These games are tested on a Mac or an iPhone. The [per-game notes](dm/games/README.md#games) say which, how far each was tested and what its known issues are.

**On iPhone and iPad** they need iOS / iPadOS 26 or later, on an iPhone 15 Pro or newer (A17 Pro chip or later) or an iPad with an M1 chip or later. Older iPhones can't read the games' texture format. **On the Mac** they need Apple silicon.

**Other games** aren't supported yet. The script still tries: it guesses the game's settings and packs an IPA, which may not start, and it writes a report on the game (`survey.md`) for anyone who wants to port it.

Under each game is the size of its IPA. "60 fps with 1.2.0" means the game runs at 60 fps with that update, where the Switch runs it at 30.

<!-- gallery: written by dm/tools/docs_gallery.py from docs/images/gallery.tsv; change the table, then run it -->
<table>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/mario-kart-8-deluxe.jpg" width="256" alt="Mario Kart 8 Deluxe"><br>Mario Kart 8 Deluxe<br><sub>~11.9 GB · no Mii heads</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/mario-vs-donkey-kong.jpg" width="256" alt="Mario vs. Donkey Kong"><br>Mario vs. Donkey Kong<br><sub>~1.6 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/paper-mario-the-thousand-year-door.jpg" width="256" alt="Paper Mario: The Thousand-Year Door"><br>Paper Mario: The Thousand-Year Door<br><sub>~5 GB · 60 fps with 1.0.1</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/super-mario-3d-world.jpg" width="256" alt="Super Mario 3D World + Bowser's Fury"><br>Super Mario 3D World + Bowser's Fury<br><sub>~3 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/super-mario-bros-wonder.jpg" width="256" alt="Super Mario Bros. Wonder"><br>Super Mario Bros. Wonder<br><sub>~4 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/super-mario-galaxy.jpg" width="256" alt="Super Mario Galaxy"><br>Super Mario Galaxy<br><sub>~3 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/super-mario-galaxy-2.jpg" width="256" alt="Super Mario Galaxy 2"><br>Super Mario Galaxy 2<br><sub>~3 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/super-mario-odyssey.jpg" width="256" alt="Super Mario Odyssey"><br>Super Mario Odyssey<br><sub>~6 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/zelda-breath-of-the-wild.jpg" width="256" alt="The Legend of Zelda: Breath of the Wild"><br>The Legend of Zelda: Breath of the Wild<br><sub>~16 GB · 60 fps with 1.9.0</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/zelda-links-awakening.jpg" width="256" alt="The Legend of Zelda: Link's Awakening"><br>The Legend of Zelda: Link's Awakening<br><sub>~6 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/zelda-tears-of-the-kingdom.jpg" width="256" alt="The Legend of Zelda: Tears of the Kingdom"><br>The Legend of Zelda: Tears of the Kingdom<br><sub>~17 GB · 60 fps with 1.4.3</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/13-sentinels.jpg" width="256" alt="13 Sentinels: Aegis Rim"><br>13 Sentinels: Aegis Rim<br><sub>~5 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/absolum.jpg" width="256" alt="Absolum"><br>Absolum<br><sub>~2 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/ace-attorney-investigations.jpg" width="256" alt="Ace Attorney Investigations Collection"><br>Ace Attorney Investigations Collection<br><sub>~7 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/advance-wars-1-2-re-boot-camp.jpg" width="256" alt="Advance Wars 1+2: Re-Boot Camp"><br>Advance Wars 1+2: Re-Boot Camp<br><sub>~7.1 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/ai-the-somnium-files.jpg" width="256" alt="AI: THE SOMNIUM FILES"><br>AI: THE SOMNIUM FILES<br><sub>~8.3 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/ai-the-somnium-files-nirvana-initiative.jpg" width="256" alt="AI: THE SOMNIUM FILES - nirvanA Initiative"><br>AI: THE SOMNIUM FILES - nirvanA Initiative<br><sub>~11.6 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/animal-crossing-new-horizons.jpg" width="256" alt="Animal Crossing: New Horizons"><br>Animal Crossing: New Horizons<br><sub>~10.5 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/animal-well.png" width="256" alt="ANIMAL WELL"><br>ANIMAL WELL<br><sub>~54 MB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/astral-chain.png" width="256" alt="ASTRAL CHAIN"><br><a href="dm/games/01007300020FA000/README.md">ASTRAL CHAIN</a><br><sub>~9.7 GB · 60 fps target with 1.0.1</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/balatro.jpg" width="256" alt="Balatro"><br>Balatro<br><sub>~82 MB · played by touch</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/ball-x-pit.jpg" width="256" alt="BALL x PIT"><br>BALL x PIT<br><sub>~1.1 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/bayonetta.jpg" width="256" alt="Bayonetta"><br>Bayonetta<br><sub>~8.8 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/bayonetta-2.jpg" width="256" alt="Bayonetta 2"><br>Bayonetta 2<br><sub>~12.5 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/bayonetta-3.jpg" width="256" alt="Bayonetta 3"><br>Bayonetta 3<br><sub>~14.2 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/big-brain-academy-brain-vs-brain.jpg" width="256" alt="Big Brain Academy: Brain vs. Brain"><br>Big Brain Academy: Brain vs. Brain<br><sub>~0.8 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/brain-memory-training-academy.jpg" width="256" alt="Brain Memory Training Academy"><br>Brain Memory Training Academy<br><sub>~0.1 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/captain-toad-treasure-tracker.jpg" width="256" alt="Captain Toad: Treasure Tracker"><br>Captain Toad: Treasure Tracker<br><sub>~2 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/catherine-full-body.jpg" width="256" alt="Catherine: Full Body"><br>Catherine: Full Body<br><sub>~14 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/celeste.jpg" width="256" alt="Celeste"><br>Celeste<br><sub>~0.9 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/cuphead.jpg" width="256" alt="Cuphead"><br>Cuphead<br><sub>~5 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/dark-souls-remastered-creation.jpg" width="256" alt="DARK SOULS: REMASTERED"><br>DARK SOULS: REMASTERED<br><sub>~4 GB · 60 fps with 1.0.3</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/digimon-story-cyber-sleuth.jpg" width="256" alt="Digimon Story Cyber Sleuth: Complete Edition"><br>Digimon Story Cyber Sleuth: Complete Edition<br><sub>~5 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/donkey-kong-country-tropical-freeze.jpg" width="256" alt="Donkey Kong Country: Tropical Freeze"><br>Donkey Kong Country: Tropical Freeze<br><sub>~7.3 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/dragon-quest-vii-reimagined.jpg" width="256" alt="DRAGON QUEST VII Reimagined"><br>DRAGON QUEST VII Reimagined<br><sub>~5.9 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/dragon-quest-xi-s.jpg" width="256" alt="DRAGON QUEST XI S: Echoes of an Elusive Age – Definitive Edition"><br>DRAGON QUEST XI S: Echoes of an Elusive Age – Definitive Edition<br><sub>~15 GB · 60 fps with 1.0.4</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/final-fantasy-resonance.jpg" width="256" alt="FINAL FANTASY RESONANCE"><br>FINAL FANTASY RESONANCE<br><sub>~10 GB · 60 fps with 1.0.1</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/final-fantasy-tactics.jpg" width="256" alt="FINAL FANTASY TACTICS - The Ivalice Chronicles"><br>FINAL FANTASY TACTICS - The Ivalice Chronicles<br><sub>~11 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/fire-emblem-engage.jpg" width="256" alt="Fire Emblem Engage"><br>Fire Emblem Engage<br><sub>~15 GB · 60 fps with 2.0.0</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/fire-emblem-three-houses.jpg" width="256" alt="Fire Emblem: Three Houses"><br>Fire Emblem: Three Houses<br><sub>~13 GB · 60 fps with 1.2.0</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/ghost-trick-phantom-detective.jpg" width="256" alt="Ghost Trick: Phantom Detective"><br>Ghost Trick: Phantom Detective<br><sub>~3 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/the-great-ace-attorney-chronicles.jpg" width="256" alt="The Great Ace Attorney Chronicles"><br>The Great Ace Attorney Chronicles<br><sub>~4 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/hades.jpg" width="256" alt="Hades"><br>Hades<br><sub>~4 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/hades-ii.jpg" width="256" alt="Hades II"><br>Hades II<br><sub>~4 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/hollow-knight-silksong.jpg" width="256" alt="Hollow Knight: Silksong"><br>Hollow Knight: Silksong<br><sub>~6.4 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/hyrule-warriors-age-of-calamity.jpg" width="256" alt="Hyrule Warriors: Age of Calamity"><br>Hyrule Warriors: Age of Calamity<br><sub>~15 GB · 60 fps with 1.3.0</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/kirby-and-the-forgotten-land.jpg" width="256" alt="Kirby and the Forgotten Land"><br>Kirby and the Forgotten Land<br><sub>~6.4 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/luigis-mansion-3.jpg" width="256" alt="Luigi's Mansion 3"><br>Luigi's Mansion 3<br><sub>~7.5 GB · 60 fps with 1.4.0</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/metroid-dread.jpg" width="256" alt="Metroid Dread"><br>Metroid Dread<br><sub>~5 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/monster-hunter-rise.jpg" width="256" alt="Monster Hunter Rise"><br>Monster Hunter Rise<br><sub>~22 GB · 60 fps with 16.0.2</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/monster-train-2.jpg" width="256" alt="Monster Train 2"><br>Monster Train 2<br><sub>~1.6 GB · 60 fps with 1.1.1</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/octopath-traveler.jpg" width="256" alt="OCTOPATH TRAVELER"><br>OCTOPATH TRAVELER<br><sub>~3.6 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/octopath-traveler-0.jpg" width="256" alt="Octopath Traveler 0"><br>Octopath Traveler 0<br><sub>~4 GB · 60 fps with 1.0.5</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/octopath-traveler-ii.jpg" width="256" alt="Octopath Traveler II"><br>Octopath Traveler II<br><sub>~5.9 GB · 60 fps with 1.1.1</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/patricks-parabox.jpg" width="256" alt="Patrick's Parabox"><br>Patrick's Parabox<br><sub>~0.2 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/persona-5-royal.jpg" width="256" alt="Persona 5 Royal"><br>Persona 5 Royal<br><sub>~14.9 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/pikmin-4.jpg" width="256" alt="Pikmin 4"><br>Pikmin 4<br><sub>~10.8 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/pokemon-brilliant-diamond.jpg" width="256" alt="Pokémon Brilliant Diamond"><br>Pokémon Brilliant Diamond<br><sub>~4.6 GB · 60 fps with 1.3.0</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/pokemon-legends-z-a.jpg" width="256" alt="Pokémon Legends: Z-A"><br>Pokémon Legends: Z-A<br><sub>~6 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/pokemon-scarlet.png" width="256" alt="Pokémon Scarlet"><br>Pokémon Scarlet<br><sub>~7.0 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/pokemon-sword.jpg" width="256" alt="Pokémon Sword"><br>Pokémon Sword<br><sub>~12.4 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/pokemon-shield.jpg" width="256" alt="Pokémon Shield"><br>Pokémon Shield<br><sub>~12.4 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/portal.jpg" width="256" alt="Portal"><br>Portal<br><sub>~2.6 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/raging-loop.jpg" width="256" alt="Raging Loop"><br>Raging Loop<br><sub>~0.9 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/rhythm-heaven-groove.jpg" width="256" alt="Rhythm Heaven Groove"><br>Rhythm Heaven Groove<br><sub>~3 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/romance-of-the-three-kingdoms-8-remake.jpg" width="256" alt="ROMANCE OF THE THREE KINGDOMS 8 REMAKE"><br>ROMANCE OF THE THREE KINGDOMS 8 REMAKE<br><sub>~16.8 GB · controller only</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/sea-of-stars.jpg" width="256" alt="Sea of Stars"><br>Sea of Stars<br><sub>~5.1 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/shin-megami-tensei-v-vengeance.jpg" width="256" alt="SHIN MEGAMI TENSEI V: Vengeance"><br>SHIN MEGAMI TENSEI V: Vengeance<br><sub>~14.6 GB · 60 fps with 1.0.3</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/stardew-valley.jpg" width="256" alt="Stardew Valley"><br>Stardew Valley<br><sub>~0.6 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/steins-gate-reboot.jpg" width="256" alt="STEINS;GATE RE:BOOT"><br>STEINS;GATE RE:BOOT<br><sub>~6 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/super-smash-bros-ultimate.jpg" width="256" alt="Super Smash Bros. Ultimate"><br>Super Smash Bros. Ultimate<br><sub>~17.5 GB · offline</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/taiko-risshiden-v-dx.jpg" width="256" alt="Taiko Risshiden V DX"><br>Taiko Risshiden V DX<br><sub>~3.3 GB · Japanese · played by touch</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/unicorn-overlord.jpg" width="256" alt="Unicorn Overlord"><br>Unicorn Overlord<br><sub>~4 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/yoshis-crafted-world.jpg" width="256" alt="Yoshi's Crafted World"><br><a href="dm/games/01006000040C2000/README.md">Yoshi's Crafted World</a><br><sub>~6.1 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/nier-automata.png" width="256" alt="NieR:Automata The End of YoRHa Edition"><br>NieR:Automata The End of YoRHa Edition<br><sub>~10.9 GB · 60 fps with 1.0.3</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/zelda-echoes-of-wisdom.jpg" width="256" alt="The Legend of Zelda: Echoes of Wisdom"><br>The Legend of Zelda: Echoes of Wisdom<br><sub>~7.8 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/donkey-kong-country-returns-hd.png" width="256" alt="Donkey Kong Country Returns HD"><br>Donkey Kong Country Returns HD<br><sub>~11.6 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/mario-rabbids-sparks-of-hope.jpg" width="256" alt="Mario + Rabbids Sparks of Hope"><br>Mario + Rabbids Sparks of Hope<br><sub>~9.8 GB</sub></td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/xenoblade-chronicles-2.jpg" width="256" alt="Xenoblade Chronicles 2"><br>Xenoblade Chronicles 2<br><sub>~17.1 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/xenoblade-chronicles-3.jpg" width="256" alt="Xenoblade Chronicles 3"><br>Xenoblade Chronicles 3<br><sub>~19.2 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/final-fantasy-crystal-chronicles.jpg" width="256" alt="FINAL FANTASY CRYSTAL CHRONICLES Remastered Edition"><br>FINAL FANTASY CRYSTAL CHRONICLES Remastered Edition</td>
</tr>
<tr>
<td align="center" valign="top" width="33%"><img src="docs/images/return-of-the-obra-dinn.png" width="256" alt="Return of the Obra Dinn"><br><a href="dm/games/010032E00E6E2000/README.md">Return of the Obra Dinn</a><br><sub>~0.9 GB</sub></td>
<td align="center" valign="top" width="33%"><img src="docs/images/blasphemous-2.png" width="256" alt="Blasphemous 2"><br><a href="dm/games/010040801A4C8000/README.md">Blasphemous 2</a><br><sub>~1.5 GB</sub></td>
<td></td>
</tr>
</table>
<!-- end of gallery -->

## Pack an IPA

Prefer a graphical interface? **[NX2Apple](https://github.com/LNPANDY/NX2Apple)**, the community Mac app from the tip at the top, runs these steps for you and can pack slimmer IPAs. The steps below are nxnative's own command-line way.

**1. Get nxnative and its tools** (once, on a Mac with macOS 26.6 or later, [Xcode](https://apps.apple.com/app/xcode/id497799835) 27.1 and [Homebrew](https://brew.sh)). The graphics are tested with Xcode 27.1's Metal compiler, so the script checks for it and, if it finds another version, stops and says how to install the right one. [#242](https://github.com/seedds/nxnative/issues/242) reports broken graphics with another compiler; the cause is still being investigated.

```
xcodebuild -downloadComponent MetalToolchain
git clone https://github.com/seedds/nxnative.git && cd nxnative
brew install xcodegen zstd glslang
brew install --cask dotnet-sdk
python3 -m pip install lz4 capstone fonttools "backports.zstd; python_version < '3.14'"
```

Use Python 3.12 or newer. The game's files stay in one image file (`RomFS.img`), in the build folder and in the app; nothing unpacks them into a folder.

**2. Put one game's NSPs in a folder**: the base game (an NSP, or an XCI cartridge dump), plus its update and DLC if you have them:

```
~/Games/MarioWonder/
  Super Mario Bros. Wonder [010015100B514000][v0].nsp
  Super Mario Bros. Wonder [010015100B514800][v327680].nsp
```

NSZ and XCZ files (compressed NSPs and XCIs) work too: the script converts them once with [nsz](https://github.com/nicoboss/nsz) (`python3 -m pip install nsz`) and keeps the NSPs in `<title ID>_build/nsp/`, which takes their full size on disk.

Any version works, with or without an update: the script packs the newest version in the folder. DLC is optional too: the game gets the DLC that is in the folder, and without any it sees none, as on a Switch. Each game was tested on one version; where the gallery names an update, its 60 fps needs that one. Another version still builds, with its own shaders, and the script says it was not tested. Fixes made for the tested version, such as the 60 fps ones, don't apply to it.

**3. Run the script:**

```
python3 scripts/build_ipa_from_nsp.py ~/Games/MarioWonder -k ~/keys/prod.keys
```

The IPA appears in the same folder (here `~/Games/MarioWonder/SMBWMobile.ipa`).

- **Time:** the first build takes 30–60 minutes. Later builds redo only what changed: after a pull that changes only the host (nxnative's stand-in for the Switch's system), about a minute. Keep the small `.idx` file beside the IPA (`.app.key` beside a Mac app); it records what was packed.
- **Disk space:** about twice the size of the NSPs, plus 10 GB.
- **Options** such as `-o` (output path) and `--app-name`: `-h` lists them all.

**4. Install** the IPA with your sideloading tool (AltStore, Sideloadly, LiveContainer, …). The app gets the game's name, icon and version (the update's version, for example 1.2.1 for Mario Wonder; the base game's when there is no update). If your tool can, turn on the **Increased Memory Limit** capability when signing (its ID is `com.apple.developer.kernel.increased-memory-limit`). Without it, iOS gives the app about 3.4 GB and closes the bigger games: Hyrule Warriors: Age of Calamity and Hades II need more, and Breath of the Wild and Cuphead come close. With it, a recent iPhone gives about 6 GB.

To play on a Mac with Apple silicon instead, `--platform mac-app` packs a Mac app of the game that you open from Finder. It appears in the NSP folder as `<game name>.app`; `-o "/Applications/<game name>.app"` puts it elsewhere. It shows the game's settings in a window before the game starts ([On the Mac](#on-the-mac), [A Mac app](docs/development.md#a-mac-app)).

With an Apple developer account you can instead build and install from Xcode: `--prepare-xcode` makes an Xcode project for the game rather than an IPA ([Build and run on a device with Xcode](docs/development.md#build-and-run-on-a-device-with-xcode)).

## Play

On every device:

- **Game language** is English by default. Pick another when packing with `--language` (`-l`), by code or by name: `-l ja`, `-l zh-Hans`, `-l French` (`LANGUAGE` in Spyder). It must be one the game has; the script prints the game's languages and stops on one it lacks. To change it without rebuilding, pick another in the settings screen (the settings window on the Mac).
- **Short stutters** the first time a new scene appears are normal: the app prepares that scene's graphics once and remembers them. On later starts it prepares everything it remembers, and what the game is known to need, before the game begins, showing "Preparing shaders" and a progress bar when that takes more than half a second.

**Motion (gyro).** A controller that supplies gyro data through Apple's controller support (DualSense, DualShock 4) drives the game's motion controls, on the Mac and the iPhone: aiming and tilt puzzles in games that read the controller's motion, such as Breath of the Wild with its Motion Controls option on. Motion follows the controller in docked mode, the default.

- **On the iPhone,** a controller without gyro (Backbone One, Razer Kishi and other controllers the phone mounts in) uses the iPhone's own gyro, as a Switch in handheld. `DM_PHONE_MOTION=0` in `Documents/env.txt` stops the phone's gyro.
- **On the Mac,** a Switch Pro Controller's gyro is read from its raw reports, because macOS passes on only zeroes. This works with one Pro Controller at a time and is not yet confirmed on hardware ([details](docs/reference/services.md#switch-pro-controller-advertises-motion-but-sends-zeroes)).
- **Turning it off:** `DM_MOTION=0` in the `env.txt` turns off all motion. If motion misbehaves, pack with `--no-motion` (`MOTION = False` in Spyder) for an app without it; `DM_MOTION=1` in the `env.txt` turns it back on.

### On iPhone and iPad

**Settings.** A screen before the game starts offers what the game supports: frame rate, render scale, docked or handheld mode, game language, the frame-rate counter and rumble. Everything is on one scrollable screen, grouped into **Game**, **Controls** and **Performance**, with menus, sliders and a reset to the settings the game was tested with. Tap **Play** to start; the app remembers your choices. What each setting does: [Settings](docs/settings.md).

**Touch controls.** There are two layouts, 2D and 3D. Tap **2D** / **3D** at the top to switch; the app remembers your choice.

- **Games that start in 2D:** 13 Sentinels: Aegis Rim, Absolum, Ace Attorney Investigations Collection, Big Brain Academy: Brain vs. Brain, Blasphemous 2, Brain Memory Training Academy, Celeste, Cuphead, Fire Emblem Engage, The Great Ace Attorney Chronicles, The Legend of Zelda: Link's Awakening, Mario vs. Donkey Kong, Metroid Dread, Octopath Traveler 0, Patrick's Parabox, Pokémon Legends: Z-A, Raging Loop, Rhythm Heaven Groove, STEINS;GATE RE:BOOT, Super Mario Bros. Wonder and Unicorn Overlord. All other games start in 3D.
- **The right stick in 3D** (the camera in most games) is not drawn. Swipe on the right third of the screen instead, starting anywhere off the buttons.
- **Touching the game's own screen:** in Captain Toad: Treasure Tracker, touches outside the controls touch the game's screen (to touch blocks and stun enemies), and in Big Brain Academy: Brain vs. Brain too (its menus and activities). In Super Mario Galaxy and Super Mario Galaxy 2 they move the pointer.
- **STEINS;GATE RE:BOOT's quick save** is R3, which only the 3D layout has.
- **A connected game controller** hides the touch controls and keeps every button.

**Rumble.** Game vibration uses the iPhone's haptic engine or a compatible connected controller. Controllers with separate left/right haptic outputs keep those effects separate. The launcher's **Game rumble** and **Rumble strength** settings control it independently of **Touch feedback**. Phone rumble is an approximation of the Switch's, and not every controller has been tried.

**Shaking.** Shake the iPhone to shake the controller, in games that read its motion: Super Mario Odyssey's motion moves (such as the cap throw on a shake) need it, and Odyssey only takes them in its default docked mode. A light bump does not count; a sharp flick of the phone does.

- **Saves and logs** are in the Files app, under the app's name: `save/` and `log.txt`. When something goes wrong, send `log.txt` (the previous run is `log.prev.txt`).
- **Reinstalling:** some sideloading tools give a reinstalled app a new data folder. Copy `save/` and `pso_user.tsv` (the graphics the app has learned to prepare) over from the old one, to keep your progress and avoid the first-time stutters again.
- **Settings in a file:** a line like `DM_OPMODE=0` in `Documents/env.txt` (in the Files app, under the app's name) wins over the settings screen, which then shows that setting greyed out. Fully close and reopen the app after editing the file. Useful lines:
  - `DM_OPMODE=0`: handheld mode, with a lower resolution and a higher frame rate.
  - `DM_FPS_TARGET=30`: the original 30 fps in Breath of the Wild, Tears of the Kingdom, Octopath Traveler 0 and Octopath Traveler II, which saves battery. `DM_PRESENT_INTERVAL=game` does the same for Monster Train 2.
  - `DM_SHOW_FPS=1`: the frame rate at the top left. `--show-fps` when packing turns it on from the start.
  - `DM_LAUNCHER=0`: skip the settings screen; your saved choices still apply.

  Every variable: [Settings in a file](docs/settings.md#settings-in-a-file).

### On the Mac

The Mac app keeps everything of yours outside the app, so building it again after a `git pull` replaces only the game and keeps your progress.

- **Where things are:** `~/Library/Application Support/nxnative/<title ID>/` holds the save (`save/` and `pso_user.tsv`) and your `env.txt`. The log is `~/Library/Logs/nxnative/<title ID>.log`, and the previous run's is `<title ID>.prev.log`. The title ID is the base game's: the script's first line names it (`game profile: 0100F2C0115B6000` for Tears of the Kingdom), and the [per-game notes](dm/games/README.md#games) list each one.
- **Settings:** a window before the game starts offers the game's settings, the most used first and the rest under **Advanced settings** ([The Mac settings window](docs/settings.md#the-mac-settings-window)). Click **Play** or press Return; the app remembers your choices, also when a front end such as [NX2Apple](https://github.com/LNPANDY/NX2Apple) starts it. Turn off **Show this window at launch** to start at once; hold Option while the app opens to see the window again.
- **Settings in a file:** `KEY=VALUE` lines in that `env.txt`, such as `DM_FPS_TARGET=30` or `DM_METAL_HUD=1`, win over the window, which then shows them greyed out. Every variable: [Settings in a file](docs/settings.md#settings-in-a-file).
- **Fullscreen** (the **Display mode** setting, the green button or ⌃⌘F) gives the most even frames on a 144 or 165 Hz screen: the app runs the screen at 120 Hz, a multiple of 60, until the window leaves fullscreen. In a window on such a screen, frames are shown at uneven intervals.
- **Keyboard:** WASD is the left stick, the arrow keys the D-pad, J K U I are A B X Y, Q E are L R, Z C are ZL ZR, F G press the sticks, Return is + and Tab is −. Space shakes the controller, and the left mouse button touches the screen. ⌘Q quits.
- **Controllers:** buttons go by position, as on the iPhone (right A, bottom B, top X, left Y). Each controller is a player of its own for local co-op: the first one connected is player 1, together with the keyboard.

## Known issues

- No amiibo and no online features.
- Fire Emblem Engage: after switching away from the app and back, some sounds may stay silent until you restart it.
- Fire Emblem: Three Houses: small square patches on the ground where decals land.
- Super Mario Galaxy and Super Mario Galaxy 2: effects drawn as points or lines come out one pixel wide.

## For developers

How it works, the script's options, running games on the Mac and adding a game: [docs/development.md](docs/development.md). Per-game notes: [dm/games](dm/games/README.md#games). In-depth bring-up and debugging notes: [docs/agents](docs/agents/README.md). What a graphical front end can build on: [Front ends](docs/frontends.md). Reporting a problem or sending a change: [CONTRIBUTING.md](CONTRIBUTING.md).

## License

MIT (see [LICENSE](LICENSE)). Third-party code keeps its own license; see [dm/third_party](dm/third_party/README.md).
