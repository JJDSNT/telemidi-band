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
