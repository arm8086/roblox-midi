# roblox-midi

A Roblox Lua implementation to read midi files using a callback function.

## Fixes

* Switched all of the bit operations to use their bit32 counterpart (<< becomes bit32.lshift, | becomes bit32.bor)

* Now returns the FIO library, which can be used to emulate io.open's functionality with a string instead of a real file.

## Usage

The library allows not only reading all tracks in a midi file at once, but also reading only the header (e.g. to a find out the track count) and then reading a single, specific midi track.

Reading all tracks in a midi file:

```lua
local midi = require(game.ReplicatedStorage.midi)

local file = game.ReplicatedStorage.MidiString.Value
midi.process(file, print)
```

Reading only the last track in a midi file:

```lua
local midi = require(game.ReplicatedStorage.midi)

local file = game.ReplicatedStorage.MidiString.Value
local tracks = midi.processHeader(file) -- find number of tracks

midi.processTrack(file, print, tracks)
```

---

## Library

The library consists of a single Lua file, namely [midi.lua](lib/midi.lua).

### Reading full midi files

The following functions require a stream of a real midi file and use a callback to report individual midi events:

```lua
function midi.process(stream, callback, onlyHeader, onlyTrack)
function midi.processHeader(stream, callback)
function midi.processTrack(stream, callback, track)
```

All functions return the total number of tracks in the midi file.

| Parameter    | Description                                                                                           |              |
|--------------|-------------------------------------------------------------------------------------------------------|--------------|
| `stream`     | A stream (e.g. `file*`) that points to the start of a midi file.                                      | **required** |
| `callback`   | A callback function which is invoked for all midi events.                                             | *optional*   |
| `onlyHeader` | When set to `true`, only the header chunk will be processed.                                          | *optional*   |
| `onlyTrack`  | When set to any integer, only the header chunk and track with this one-based index will be processed. | *optional*   |
| `track`      | Same as `onlyTrack` but required.                                                                     | **required** |

### Reading single midi events

The following function simply reads a single midi event (excluding the usually preceeding delta-time) from the given stream:

```lua
function midi.processEvent(stream, callback, runningStatus)
```

It returns how many bytes it had to read from the stream, followed by the updated runningStatus.

| Parameter       | Description                                              |              |
|-----------------|----------------------------------------------------------|--------------|
| `stream`        | A stream (e.g. `file*`) that points to a midi event.     | **required** |
| `callback`      | A callback function which is invoked for the midi event. | **required** |
| `runningStatus` | The running status of a previous midi event.             | *optional*   |

---
