# Lodestone

A friendly, open-source digital audio workstation for Linux that opens the projects you
made somewhere else. Built on [Tracktion Engine](https://github.com/Tracktion/tracktion_engine)
and [JUCE](https://juce.com). Licensed AGPL-3.0-or-later.

## Install (Linux Mint 22+, Ubuntu 24.04+, Debian 13+)

```
sudo apt install ./lodestone_0.10.0_amd64.deb
```

Use `apt`, not `dpkg -i`: apt pulls in the dependencies (FluidSynth, a General MIDI
SoundFont, Python 3, ffmpeg). The package is built on Ubuntu 24.04, so it needs glibc 2.39 or
newer - on Mint 21.x build from source instead (see below).

## What it does

- **Practice mode** (`Ctrl+P`): play your controllers and mic through any sound with no
  project or tracks - splits, layers, effects, tuner, hold-to-sample, metronome, recorder and
  *Save what I played*. See [Practice mode](#practice-mode) below.
- **Every MIDI controller at once.** Each controller either follows the selected track or is
  pinned to one track - e.g. an Akai MPD218 always plays the drums while your keyboard plays
  whatever track you click. Pad controllers are pinned to the Drums track automatically.
  No controller? `Ctrl+K` turns your computer keyboard into a piano (A W S E D F T G Y H U J K,
  Z/X octave, C/V velocity), and those notes record like any other MIDI input.
- **Drum machine.** 16 pads laid out like an MPD218 (pad 1 = note 36, bottom-left), velocity
  layers, choke groups (closed hat cuts the open hat), tune/decay/tone/pan per pad, sample
  start/end slicing, reverse, MIDI-learn any pad, and *Hold to Sample* to record your mic
  straight onto a pad. Four synthesized kits included (808, Punch, Lo-Fi Dust, Hand
  Percussion); Hydrogen drumkits are found automatically; save your own kits.
- **Step sequencer** with swing, Euclidean rhythms, fills and randomize, and a **piano roll**
  with scale lock, chord stamping, quantize, humanize, legato and arpeggiate.
- **Sounds:** the 128 General MIDI instruments + GM drum kits (FluidSynth), 15 synth presets
  (Tracktion's 4OSC), a sampler, and your plugins.
- **Effects:** reverb, delay, chorus, phaser, compressor, EQ, filter, pitch shift and all
  **186 Airwindows** effects, plus plugins.
- **Plugins:** VST3, LV2 and LADSPA, scanned out-of-process so a crashing plugin can't take
  Lodestone down. Windows **VST3** plugins work through
  [yabridge](https://github.com/robbert-vdh/yabridge). VST2 isn't supported (hosting it
  needs Steinberg's VST2 SDK, which can't be redistributed), so VST2-only plugins - Linux or
  Windows - won't load. Audio Units are macOS-only and can't exist on Linux.
- **Mixer** with inserts, pan, meters and master effects; **export** to WAV/FLAC/OGG (optional
  -14 LUFS loudness normalising), per-track stems, MIDI or DAWproject.
- Autosave every minute with crash recovery, undo for everything, a command palette (`F1`).

## Practice mode

For when you just want to play. Open it with `Ctrl+P`, the **Practice** button in the
toolbar, `lodestone --practice`, or right-click the app icon > *Practice Mode*. Your project
stays loaded underneath, undo history and all, and comes back exactly as you left it when you
press **Back** (or `Ctrl+P` again).

**Sounds.** Each sound is a card:

| On the card | What it does |
|---|---|
| Sound button | GM instruments, synths, drum kits, your plugins |
| PLAYS FROM | which MIDI controllers play it (tick several, or none) |
| KEYS | the key range it answers to - drag or scroll the notes, or press **Learn** and hit the lowest then highest key |
| TRANSPOSE | semitones, with -12 / +12 buttons |
| NOW PLAYING | the notes reaching it right now (if you play and nothing shows, check PLAYS FROM) |
| M / S, slider | mute, solo, volume |
| ... menu | rename, duplicate, delete, only-one-MIDI-channel, reset keys |

- **Layer:** put one controller on two sounds.
- **Split:** give them different KEYS. The bars over the keyboard map show every range; drag
  their ends to move a split. Clicking the on-screen keys plays them.
- New controllers are assigned when they appear: anything named like a pad controller
  (MPD, pads, drum...) goes to Drums, keyboards go to the first instrument.
- Click a card to edit its instrument and effects in the panel at the bottom
  (**+ Effect**). Master effects are in the bar along the bottom.

**Audio inputs (mic, line).** The Mic card picks the interface input (Input 1, Input 2 or both
as stereo) and has a level meter and a tuner.
- **Hear it (monitor)** plays the input through its effects. Use headphones with a mic or it
  will feed back. If you listen through your interface's direct monitoring (e.g. the
  Scarlett's *Direct Monitor*), leave it off - the tuner, sampler and capture still work.
- **Hold to sample:** hold, make a sound, let go.
  - *to keys* - the sample plays across the keyboard; its root note is set from its pitch,
    so the note you sang plays back at its real pitch.
  - *to pads* - it's cut onto the 16 drum pads at each hit (evenly if there are no clear hits).
  - The new sound takes over the controllers of the instrument you last clicked and mutes
    that one (unmute it to layer). Sampling again with a sample sound selected replaces it.
- **+ Audio input** adds another (a second mic, a guitar, a synth's line out).

**Top bar.**

| | |
|---|---|
| `Space` / metronome button | start/stop the click; BPM, **TAP** (`T`), time signature, click volume |
| `R` / record button | record everything you hear to a WAV (the click isn't recorded) |
| **Save what I played** | turn what you just played into a new project: *the last thing I played* (back to your last pause of ~6 s), or the last 30 s / 2 min / 5 min |
| keyboard button / `Ctrl+K` | play with the computer keyboard |

*Save what I played* includes the notes from every sound and the audio from every input that
was actually played (room hiss is ignored). If the metronome was running the whole time, the
notes land on the beat at the same tempo. The last 5 minutes are kept **in memory only**
(about 58 MB per audio input at 48 kHz); nothing is written to disk until you click.

**Where things go.**

| | |
|---|---|
| `~/.local/share/lodestone/Practice/` | your practice setup (saved automatically) and the samples you record |
| `~/Music/Lodestone/Practice Recordings/` | recorder WAVs |
| `~/Music/Lodestone/Practice Take N/` | projects made by *Save what I played* |

*Reset to defaults* (on the last card) goes back to Grand Piano, Drums and Mic.

## Importing other DAWs' projects

`File > Open` (or `lodestone song.flp` from a terminal). The import becomes a new Lodestone
project in `~/Music/Lodestone/<name>/`, with the audio it uses copied next to it. A summary
lists anything that couldn't come across.

| Format | What carries over | What can't |
|---|---|---|
| **LMMS** `.mmp` `.mmpz` | tempo, time signature, all tracks and patterns, Beat/Bassline patterns and their placements (looped like LMMS does), sample tracks, volumes/pans/mutes, mixer-channel and master levels, AudioFileProcessor samples incl. start/end slices, DrumSynth `.ds` sounds (rendered by a port of LMMS's DrumSynth), SF2 Player soundfonts | LMMS's own synths (TripleOscillator, ZynAddSubFX, LB302...) become the nearest built-in sound; effects and automation are skipped |
| **FL Studio** `.flp` (FL 12 and newer, tested on FL 20 files; also zipped loop packages) | tempo, time signature, patterns and notes, playlist placements, audio clips, channel volume/pan, sampler channels, layers; VST plugins are matched by name if installed | FL's own instruments/effects only exist inside FL (replaced with similar sounds); FL factory samples aren't on Linux (drum sounds are substituted from the built-in kits); automation clips are skipped |
| **GarageBand** `.band`, **Logic Pro** `.logicx` (folder or `.zip`) - *experimental* | tempo and tempo changes, time signature, MIDI regions and notes, audio regions (position, trim, fades) with their audio (CAF/ALAC converted via ffmpeg), track names | Apple instruments/effects (MIDI tracks get a GM piano or drum kit); Drummer tracks and Live Loops; GarageBand 6 (2011) and older |
| **DAWproject** | tracks, MIDI and audio clips, volume/pan (import and export) | plugin states |
| **MIDI** `.mid` | every track, GM program changes and drum channel | - |

Imported mixes arrive without their compressors and limiters, so Lodestone renders the song
once after importing; if it would clip, every track is turned down by the same amount, and an
Airwindows *Clip Only* on the master catches any stray peaks (remove it in the Mixer if you like).

GarageBand/Logic import uses a reverse-engineered reader of Apple's undocumented ProjectData
format ([logicx](https://github.com/jonkubis/LogicProFormatWriter), MIT). It has been tested on
Logic sessions, not on a wide range of real GarageBand songs - if a project won't import,
GarageBand's *Share > Export Song to Disk* and *Export MIDI* always work.

## Keys worth knowing

| | |
|---|---|
| `Space` play/stop · `R` record · `L` loop · `C` metronome · `T` tap tempo | `Home` back to start |
| `S` split at playhead · `Q` quantize · `G` snap on/off · `Ctrl+D` duplicate | `Ctrl+Z` / `Ctrl+Shift+Z` undo/redo |
| `E` editor · `I` instrument · `X` mixer · `B` browser | `F1` / `Ctrl+Shift+P` command palette |
| `Ctrl+K` musical typing · `Ctrl+,` settings | Help > Keyboard Shortcuts for the full list |
| `Ctrl+P` Practice mode (there: `Space` metronome, `R` record, `T` tap tempo) | |

## Command line

```
lodestone [project]              open a project or import one
lodestone --practice             start in Practice mode
lodestone --render IN OUT.wav    open/import IN and render it (.wav/.flac/.ogg), then exit

LODE_AUDIO_TYPE=JACK lodestone   pick the audio driver type (JACK or ALSA) at startup
```

## Building from source

```
sudo apt install build-essential cmake ninja-build pkg-config git libfluidsynth-dev \
    libasound2-dev libjack-jackd2-dev libfreetype-dev libfontconfig1-dev libx11-dev \
    libxrandr-dev libxinerama-dev libxcursor-dev libxcomposite-dev librsvg2-bin dpkg-dev python3
scripts/fetch_deps.sh        # Tracktion Engine + JUCE at the pinned commits, + patches/
scripts/build_deb.sh         # -> dist/lodestone_<version>_<arch>.deb
```

The first build compiles Tracktion Engine and JUCE (15-30 minutes on a laptop); after that
only Lodestone's own sources rebuild. `scripts/make_kits.py` (needs numpy + scipy) regenerates
the drum kits; `tests/` has the UI smoke-test and render-check helpers used during development,
and `tests/practice_selftest.sh` drives Practice mode end to end against JACK's dummy driver
(needs `jackd2`): routing, splits, metronome alignment, recorder, tuner, sampling, takes.

`patches/0001-juce-linux-nested-dispatch.patch` fixes a JUCE use-after-free on Linux when a
modal progress window runs inside a message callback (it crashed imports).

## Licence

Lodestone is free software under the GNU Affero General Public License v3 or later
(`LICENSE.md`). It statically links Tracktion Engine (GPLv3+), JUCE (AGPLv3), Airwindows (MIT),
SoundTouch (LGPL-2.1+) and Signalsmith Stretch (MIT), and uses FluidSynth (LGPL-2.1+).
The importers include a Python port of LMMS's DrumSynth (GPL-2.0+) and a subset of logicx (MIT).
The drum kits are synthesized from scratch and released as CC0. See `packaging/copyright`.
