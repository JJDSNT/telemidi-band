# AROS integration contract — audit-first

**Status:** proposed boundaries, NOT a verified API inventory. G0 must inspect source and validate each contract against the target AROS build.

| Capability | Intended provider | Consumer contract to verify | Ownership if missing |
| --- | --- | --- | --- |
| MIDI endpoint enumeration and subscriptions | CAMD 43 | Enumeration, lifecycle, notifications, UMP/MIDI1 representation, timestamps | CAMD |
| MIDI routing and device services | MIDIHub | Route creation/removal, ownership, event delivery, device identity | MIDIHub |
| Network MIDI session | AROS MIDI transport components | Connection setup, peer state, transport status, timing, errors | MIDI infrastructure |
| MIDI-CI capability discovery | CAMD/MIDI-CI service (when implemented) | Profiles, property exchange, negotiation, timeouts, cancellation | MIDI-CI infrastructure |
| Local synthesis | MIDIHub synth service (when available) | Instrument mapping, render output, latency, SoundFont compatibility | Synth infrastructure |
| Local discovery | AROS mDNS/DNS-SD | Browse, advertise, TXT metadata, IPv4/IPv6, TTL, interface changes | Network/discovery infrastructure |
| UDP/TCP and time | AROSTCP/AROS | Sockets, clocks, nonblocking I/O, thread synchronization | OS |
| Audio I/O | AHI or validated AROS-native audio interface | Capture/playback duplex, buffers, sample rate, callbacks, device lifecycle | OS audio |
| Voice codec and audio networking | Candidate AOO + Opus | Codec, packet transport, jitter/drift, loss handling, diagnostics | Reusable audio component |
| Rooms, member roles, personal mixer, multitrack recording | TeleMIDI Band | Application domain and UI | TeleMIDI Band |

## Mandatory audit record for each row
- Source repository, exact path/commit, public API entry points and version
- Actual status: unavailable / proposed / prototype / validated / upstreamed
- Target architecture and reproducible build evidence
- Thread/task ownership, resource cleanup, callback context and concurrency constraints
- Timestamp units and clock domain; data ownership and serialization
- Error paths, cancellation, reconnect behavior, compatibility and test evidence

## Rules
1. Do not vendor or reimplement CAMD, Network MIDI, mDNS/DNS-SD or MIDIHub routing inside TeleMIDI Band.
2. Use thin adapters only when required by actual native APIs; never assume hypothetical function names are available.
3. Log infrastructure gaps in their owning project and reference them here.
4. Preserve UMP when supported; if downgraded to MIDI1, make information loss visible.
5. Ensure synthesis capability matches transmitted expressivity; parsing UMP alone is insufficient.
6. AOO and Opus are candidates; licensing, AROS compiler support, sockets, threads, clock and AHI feasibility must be verified.
7. An interoperable wire protocol does not imply a commitment to native Linux/Windows/macOS application builds.

## G0 deliverable
A source-linked capability matrix, minimal integration harnesses, confirmed blockers and ownership decisions. Only after G0 should adapters or concrete APIs be frozen.

## Instrument fidelity and soundbank responsibility

| Concern | Intended owner | Verify in G0 |
| --- | --- | --- |
| Soundbank catalog, cache, load/unload and preset selection | MIDIHub synth service or existing native AROS synth facilities, **if available** | Actual API, formats (SF2/SF3 etc.), lifecycle, version, resource limits |
| Session instrument manifest and fidelity status | TeleMIDI Band | Source identity, hash, synth compatibility, permission, UI reporting |
| Authorized asset exchange and cache orchestration | TeleMIDI Band coordination + reusable OS services | Content hash, transport, consent, integrity, licensing, failure recovery |
| Source hardware/proprietary synth fidelity | Original musician's audio stream | Audio capture, source/monitor routing, hybrid operation |

A MIDI-CI profile/property exchange is **not** a universal soundbank transfer protocol. Do not claim MIDI-CI can deliver arbitrary soundbanks or guarantee identical timbres. MIDIHub synthesizer and asset-management APIs are proposed boundaries until source-verified. Avoid loading assets in real-time audio callbacks.


## Source-backed gap investigation — 2026-11-19

This is a **code/documentation audit, not a successful AROS runtime test**. Do not promote a planned API to a verified production capability. Evidence paths below are in [AROS](https://github.com/aros-development-team/AROS) and [aros-midihub](https://github.com/JJDSNT/aros-midihub); pin exact commits before G0 acceptance.

| Capability | Verified evidence / current boundary | Classification | Owner and next validation |
| --- | --- | --- | --- |
| AHI audio capture | AROS `workbench/tools/AHIRecord/AHIRecord.c` uses `AHIA_RecordFunc`, `AHIC_Record`; `workbench/devs/AHI/Device/devcommands.c` handles `CMD_READ` | **Existing API/implementation**, not an absent platform feature | G1: run capture + playback concurrently on target device, measure buffers/latency and failures |
| AHI duplex | `devcommands.c` explicitly checks `FullDuplex` and returns `AHIE_HALFDUPLEX` when incompatible read/write activity overlaps; `rom/usb/classes/audio/usbaudio.class.c` handles recording modes and full-duplex mode reporting | **Device/driver-dependent, unvalidated** | AHI/USB Audio if a reproduced driver defect; otherwise TeleMIDI audio adapter |
| Native UMP and receive-time metadata from drivers | MIDIHub `docs/camd-backlog.md` **B32**: driver boundary lacks native UMP and timed receive; provisional CAMD 43 published application endpoints are not equivalent to a native driver path | **Documented interface gap**, not proven G2 blocker | CAMD; verify current upstream branch/PR and measure loss of timestamp/UMP before proposing ABI change |
| Physical endpoint identity/presence/display | MIDIHub CAMD backlog **B29/B30/B31** describes driver identity, offline presence and display name gaps | **Documented interface gaps** | CAMD and drivers; test multi-device/reconnect cases, distinguish endpoint IDs from room participant IDs |
| AppleMIDI timed delivery | MIDIHub `docs/applemidi-backlog.md`, `docs/rtp-midi-backlog.md`, `network/rtpmidi/timing.c`: peer clock mapping and scheduled transport delivery exist | **Transport feature**, not application-visible provenance guarantee | Audit actual netmidid→driver→CAMD event metadata; T01/T03 |
| Network MIDI 2.0 JR timing | MIDIHub `docs/network-midi2-backlog.md` NM09: JR-clock-based scheduled receive; native UMP at CAMD driver boundary remains limited | **Transport feature**, not end-to-end UMP guarantee | Capture event data at both sides of driver and synth; T01/T03 |
| Audio-over-network / voice codec | AOO and Opus are candidates in this project's G1; no AROS-native working integration demonstrated by this audit | **Feasibility unverified**, not proof of platform impossibility | G1: audit upstream AOO/Opus dependency trees, licensing, compiler/threads/sockets; prototype before choosing |
| Shared MIDI/audio clock correlation | MIDIHub `docs/telemidi-wan-performance-research.md` and `docs/realtime-fastforward-future-research.md` identify clock-domain questions; TeleMIDI `docs/TIMING_AND_LATENCY.md` defines required offset/drift/uncertainty | **Unverified cross-media contract** | TeleMIDI coordinator initially; platform timing enhancement only if a measured reusable gap appears |
| Instrument asset/preset control | MIDIHub Preferences/synth docs describe startup SoundFont loading; TeleMIDI needs hash-based matching, renderer status and potentially live preset changes | **API maturity unverified** | MIDIHub synth for native lifecycle API; TeleMIDI for manifest, consent and fidelity reporting |
| Peer/session telemetry | MIDIHub transports maintain session/loss state; a stable app-facing control/status API has not been established here | **API exposure unverified** | Transport owner; inspect netmidid public messages/driver interfaces before inventing controls |

### Dependency-sensitive implementation order

1. **G0:** compile a concrete API inventory from the target AROS revision: AHI recording and playback mode, CAMD 43 endpoint/driver UMP and timestamp paths, network peer status, synth lifecycle and DNS-SD. Record exact source commit, API signatures, target build and behavior.
2. **G1 first experiment:** full-duplex local microphone capture and headphone playback with measured latency, using existing AHI APIs. Include a half-duplex device to verify clear error handling. Do **not** open an AHI implementation issue merely because the test has not been run.
3. **G1 second experiment:** minimal AOO/Opus build and native voice loopback; if impractical, evaluate alternatives with the same latency/CPU criteria. No dependency adopted yet.
4. **G2 MIDI experiment:** use existing AppleMIDI/Network MIDI 2.0 transport scheduling; measure send/arrival/due/CAMD/synth/audible times and distinguish what metadata is actually available to a CAMD client. A basic LAN MIDI demo may work without B32.
5. **After evidence:** open narrow platform issues only for reproducible missing contracts (e.g., B32 for native driver UMP/timed receive). Avoid a new global musical clock, new MIDI stack, or forced CAMD ABI dependency for the first demo.

### Acceptance traps

- `AHIRecord` working alone does **not** prove simultaneous capture/playback, device compatibility or low-latency performance.
- `AHIDB_FullDuplex` or a driver mode label does **not** prove the selected hardware performs well under load.
- A network transport scheduling events does **not** prove original source timestamps reach a CAMD application or multitrack recorder.
- CAMD 43 native UMP for published application endpoints does **not** establish native UMP delivery through every physical/network driver.
- mDNS/DNS-SD discovery does **not** provide Internet rendezvous, admission security or NAT traversal.
- AOO/Opus presence in reference material does **not** imply a working AROS port.

**Status:** all new G0/G1 test obligations remain open. This audit changes the classification of audio capture from *possibly missing* to *present in source, duplex/performance unverified*.


## AHI source follow-up — 2026-11-19

Additional source paths in [AROS](https://github.com/aros-development-team/AROS) clarify the **first executable feasibility test**:

- `workbench/devs/AHI/Examples/Low-level/HardDiskRecord/HardDiskRecord.c`: existing recording example uses `AHI_AllocAudio`, `AHIA_RecordFunc`, `AHIC_Record` and a signal-driven buffer handoff. Reuse as an API reference, **not** as proof of full-duplex or low latency.
- `workbench/tools/AHIRecord/AHIRecord.c`: production-oriented recording client with `AHIA_RecordFunc` and `AHIC_Record`; useful for verifying capture independent of TeleMIDI.
- `workbench/devs/AHI/Device/devcommands.c`: `CMD_READ` and playback path reject concurrent operation when `!FullDuplex` with `AHIE_HALFDUPLEX`; do not mask this error or silently substitute half-duplex for a live session.
- `workbench/devs/AHI/Drivers/HDAudio/main.c`: implements a query case for `AHIDB_FullDuplex`; the result must be checked on actual hardware.
- `rom/usb/classes/audio/usbaudio.class.c`: recording-mode pairing and `AHIDB_FullDuplex` handling exist; USB device-specific modes and runtime results still need measurement.

### G1-A: minimum native AHI experiment (proposed, not executed)

1. Select and record AHI mode, hardware/driver, full-duplex capability, sample format/rate, channel count and requested buffer size.
2. Verify microphone capture in isolation using AHIRecord or a minimal client; record buffer callbacks and dropouts.
3. Verify headphone playback in isolation using an AHI client.
4. Run capture and playback **simultaneously** with bounded ring buffers and no file/network I/O in the record callback. If `AHIE_HALFDUPLEX`, record the device limitation and try another valid mode/device rather than changing the MIDI stack.
5. Measure input-to-output loopback latency (not merely callback intervals), callback jitter, underruns, overruns, CPU and memory for sustained runs. Test startup/shutdown, device removal, stop/restart and sample-rate mismatch.
6. Repeat on a second AROS peer before adding a voice codec; compare native target builds, including m68k only when it is an intended target for the experiment.

**Pass criteria:** demonstrable simultaneous capture/playback on specified target hardware, reproducible log and latency distribution (p50/p95/p99), no sustained buffer corruption or leaked resources. Numerical acceptable limits are to be selected from observed musical use cases, not invented in advance.

**Follow-up:** only after G1-A, audit upstream AOO and Opus build/link/runtime requirements, then test local codec loopback before networking. The existence of AHI APIs does not select AOO or establish their portability.

**Implementation ownership:** an AHI driver bug requires a minimal AHI/USB/HDAudio reproduction and belongs upstream; TeleMIDI owns the capture/playback adapter, stream coordination and measurements. No TeleMIDI application code or AROS driver code was changed by this research.
