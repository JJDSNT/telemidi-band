# Reference implementations and research targets

These are **study candidates**, not selected dependencies or claims of AROS compatibility. Audit upstream version, licenses, transitive dependencies and maintenance before vendoring or porting.

| Project | URL | Why inspect |
| --- | --- | --- |
| SonoBus | https://github.com/sonosaurus/sonobus | Individual monitor mixes, collaborative audio UX, latency/jitter controls |
| AOO (Audio over OSC) | https://github.com/Spacechild1/aoo | Reusable real-time network audio, timing, buffering and transport |
| Jamulus | https://github.com/jamulussoftware/jamulus | Centralized low-latency audio sessions and mixer tradeoffs |
| Opus | https://github.com/xiph/opus | Portable audio codec candidate for voice and instruments |
| libremidi | https://github.com/celtera/libremidi | Cross-platform MIDI application API and interoperability design |
| rtpmidid | https://github.com/davidmoreno/rtpmidid | External RTP-MIDI interoperability peer |
| VMPK | https://github.com/pedrolcl/VMPK | Virtual keyboard and MIDI UI reference |
| Open Piano | https://github.com/ja-ortiz-uniandes/open-piano | Collaborative visual instrument UX |
| TeleMIDI Connect | https://devcandoit.com/portfolio/telemidi-connect | Browser-based collaborative MIDI concept |
| AROS MIDIHub | https://github.com/JJDSNT/aros-midihub | Primary AROS MIDI integration dependency |
| AROS | https://github.com/aros-development-team/AROS | Native CAMD, audio and networking source |

## Investigation checklist
- Identify exact upstream license and compatible reuse boundaries.
- Confirm source accessibility and build requirements.
- Compare C/C++ standards, atomics, threading, timing, sockets and audio I/O assumptions with AROS.
- Find code-level seams for platform adapters, especially AHI and CAMD.
- Document what is reusable as a library versus what is only a design reference.
- Avoid using an application's embedded MIDI transport in place of the native MIDIHub stack.
