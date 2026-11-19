# Validation plan

## Test matrix
| ID | Scenario | Expected evidence |
| --- | --- | --- |
| V01 | Native AROS MIDI endpoint enumeration and lifecycle | Actual API path, device logs, teardown |
| V02 | MIDI1 note-on/note-off through native routing | Correct notes, no stuck notes |
| V03 | UMP/per-note expression if supported | Byte/event-level integrity and audible renderer behavior |
| V04 | AHI microphone capture and playback | Full-duplex loopback, buffer/latency metrics |
| V05 | Two AROS peers exchange MIDI and voice | Bidirectional media capture and playback |
| V06 | Personal mix | Gain/pan/mute affect only the local listener |
| V07 | Peer disconnect mid-note | Panic/cleanup, no stuck notes, no leaked routes |
| V08 | Network loss/jitter/reordering | Dropouts, buffer behavior, recovery and CPU metrics |
| V09 | Multitrack recording | Separate MIDI/audio tracks with participant attribution and aligned timestamps |
| V10 | Discovery and IPv4/IPv6 where supported | Browse/advertise, TTL and interface-change behavior |
| V11 | External protocol peer | Interoperability without requiring a non-AROS TeleMIDI Band client |
| V12 | Four-person band | Each participant hears other three with independent mix |

## MultitrackStudio interoperability candidate (planned, not executed)

Reference: [MIDI Association article](https://midi.org/multitrackstudio-brings-practical-midi-2-0-support-to-audio-and-midi-recording). This is a DAW feature/interchange benchmark; it is **not** a validated TeleMIDI Band network peer. Before running tests, confirm MultitrackStudio version, supported MIDI 2.0 features, available device/virtual port, operating system, and whether a real bidirectional UMP/MIDI-CI connection exists.

| ID | Proposed scenario | Expected evidence / boundary |
| --- | --- | --- |
| VM01 | Compare MIDI 2.0 note, velocity and per-note expression round-trip against a compatible DAW endpoint | Captured UMP events and audible behavior; extend V03; flag any MIDI 1.0 downgrade |
| VM02 | Discover instrument capabilities via MIDI-CI Profiles and Property Exchange where both endpoints support them | Request/reply traces, versions, supported resources and error handling; consume platform MIDI-CI rather than implementing it here |
| VM03 | Export/import separately attributed MIDI tracks into a compatible MIDI 2.0 file workflow | Event identity, note expression, timing and track attribution before/after; extend V09/G5; verify file-format support in the tested version |
| VM04 | Change programs/profiles and disconnect during recording | Preserved note lifecycle, correct recorded events and deterministic cleanup; extend V07/V16 |

Record tested application version, AROS/CAMD/MIDIHub/TeleMIDI Band commits, transport, OS, virtual/physical devices, input/output captures, expected/observed results and limitations. Mark all VM checks **planned** until executed. A successful file import/export does not establish live UMP transport interoperability; a successful local DAW exchange does not establish multi-peer network synchronization, latency, jitter or audio performance. Those remain covered by V05/V08/V12 and their own measurements.

## Evidence requirements
For every run record: build commit, AROS target architecture, hardware, connected MIDI/audio devices, network topology, transport and codec configuration, sample rate, buffer size, test duration, latency distribution, jitter, packet loss, CPU load, underruns and known limitations.

## Gates
- **G0:** verified platform API inventory and executable native smoke tests.
- **G1:** reproducible duplex audio and codec/transport feasibility.
- **G2:** V01, V02, V04–V07 on **two AROS peers**.
- **G3:** V12 and recording V09.
- **G4:** Internet trials with security, NAT/relay and measured latency.
- **G5:** asynchronous overdub/edit/export.

Do not mark a capability complete based solely on a design document or an upstream feature claim.

## Instrument fidelity tests
- **V13:** Same SF2 asset/hash on both AROS peers; resolve cached bank and correct bank/program, with documented renderer settings.
- **V14:** Bank absent; consented authorized transfer, size/hash verification, cache reuse, cancellation and failed download.
- **V15:** Non-redistributable bank or hardware/proprietary synth; do not copy asset, select source audio or explicit approximation.
- **V16:** Instrument/preset changes and peer disconnect during transfer; preserve note lifecycle, free resources, no real-time callback stalls.
- **V17:** Different synth engines/effects with same bank; report compatibility without claiming identical rendered audio.

## Open collaboration implementation comparison (planned)

The following scenarios complement V01–V17 and VM01–VM04; none is an executed AROS test. See [reference inventory](REFERENCES.md) and [timing experiments](TIMING_AND_LATENCY.md).

| ID | Scenario | Expected evidence |
| --- | --- | --- |
| VC01 | Immediate remote MIDI with jitter/loss | Captured event/callback/remote-audio timestamps, p50/p95/p99 audible delay, note-on/off ownership and cleanup |
| VC02 | Optional beat/bar-quantized session | BPM, phase error, missed-grid deadlines, added musical delay, explicit late-event policy |
| VC03 | Reliable room/instrument state and late join | Snapshot/revision, participant identity, rejoin convergence and no duplicated/stuck notes |
| VC04 | Hybrid MIDI/audio time mapping | Source clock epochs, offset/drift uncertainty, audio sample-frame mapping, recording alignment |
| VC05 | Performer versus audience/recorded timeline | Original event time, arrival time, live render time and offline reconstruction; never conflate live improvement with postproduction |
| VC06 | Direct versus relay versus server mix | Identical hardware/network load, per-user bandwidth, CPU, jitter, p95 audible delay and personal-mix independence |

Evidence must name source projects as **comparators**, not implementations used, unless the actual code is integrated and tested. For example, netpd is a state synchronization comparator, NINJAM is a musical-delay comparator, AOO is an audio timestamp/transport candidate, and Fast MIDI Jam is archived source material.
