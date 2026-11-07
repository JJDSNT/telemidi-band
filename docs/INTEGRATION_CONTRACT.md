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
