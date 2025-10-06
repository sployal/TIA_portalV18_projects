## TIA Portal v18 – Project Guide (PLC, Safety, WinCC, PLCSIM, Openness)

This README focuses on using Siemens TIA Portal v18 effectively across PLC, Safety, and HMI projects. It captures practical conventions, organization, testing, and commissioning steps directly in TIA.

### Essentials
- Use the same TIA v18 update level across the team to avoid compatibility prompts
- Keep hardware catalog/firmware versions aligned with the physical devices
- Prefer short project paths to avoid issues with long filenames in archives/exports

---

### PLC Program Organization
- Cyclic structure:
  - `OB1` (cyclic): orchestration only; call area/feature FCs/FBs in clear order
  - Time/interrupt `OBs` (e.g., `OB35`) for time-critical tasks
  - Startup `OB100` for initialization
- Blocks:
  - `FB_` for stateful logic with an instance `DB` (consider multi-instance to reduce DB count)
  - `FC_` for pure functions without retained state
  - `UDT_` for structured, reusable data (e.g., device models, statuses)
- Data blocks:
  - Separate `DB` for parameters vs. live process values when helpful
  - Enable “Optimized block access” unless you must keep absolute addressing
- Coding languages:
  - LAD/FBD for interlocks, permissives, and straightforward logic
  - SCL for algorithms, state machines, parsing, array handling, and reusable libraries
- State machines:
  - Implement explicit states and transitions; use enumerations and timers per state
- Diagnostics:
  - Standardize fault bits per device, add readable status words, and wire to HMI alarms

---

### Naming Conventions (TIA-focused)
- Devices: `Area_Unit_Device` (e.g., `Mix_Tank_PU101`)
- Tags: `g<Area>_<Device>_<Signal>` for globals; local temps remain local
- Blocks: `FB_Area_Function`, `FC_Area_Function`, `DB_Area_Object`
- Alarms: `ALM_Area_Equipment_Condition` with plain-English help text
- Safety: prefix safety blocks/tags with `SF_` and segregate networks clearly

---

### Hardware & Networks
- Hardware configuration:
  - Match CPU type, firmware, and module order to the real rack
  - Assign device names and IPs; keep Profinet device names unique
- PROFINET/IO:
  - Import correct GSDML for third-party devices
  - Map IO addresses to coherent `UDT` structures for readability
- Drives:
  - Use technology objects or telegrams consistently; document telegram type

---

### Safety (TIA Safety)
- Use F-CPU or F-runtime; separate standard vs. safety program
- Generate and retain the safety signature; capture in release notes
- Validate safety functions (E-Stop, Guard Doors, STO) with test evidence
- Document F-I/O assignment and test interval; never bypass without procedure

---

### WinCC (Unified/Advanced)
- Tags: derive from PLC `UDT`/status words; avoid redundant calculations in HMI
- Alarming: map standardized alarm bits/texts; include help and troubleshooting tips
- Navigation: simple top bar + area pages; consistent colors and statuses
- Users & Audit: configure roles, login policies, and audit where required
- Recipes: store setpoints separately; version them and log downloads

---

### Communication
- S7 Communication (PUT/GET): enable only if needed; prefer explicit interfaces (OPC UA, blocks)
- OPC UA Server: model readable tags and restrict write access; document endpoints
- Modbus/TCP: use consistent mapping tables; include timeout/fault handling
- I-Device / I-Controller: define clear provider/consumer roles and test handshakes

---

### Simulation (PLCSIM / PLCSIM Advanced)
Basic workflow:
1) Compile hardware and PLC program without errors
2) Start PLCSIM and load the CPU project
3) If using Advanced, configure virtual network to match project PN settings
4) Create watch tables for IO and key states; simulate inputs
5) Run sequences: start/stop, interlocks, E-Stop, and fault recoveries

Tips:
- Use traces for motion/analog tuning; export to CSV for analysis
- Script common input patterns (ramps, pulses) using test tables or SCL helpers

---

### Openness (Automation)
Use TIA Openness to export/import engineering data for review or backup.

Example PowerShell pseudo-steps:
```powershell
# Requires: TIA Portal v18, Openness enabled
# 1) Launch TIA silently, open project
# 2) Export blocks/tag tables/HMI screens to XML into an Exports folder
# 3) Close TIA
```

What to export:
- PLC blocks (FB/FC/DB), tag tables, technology objects (where supported)
- HMI: screens, faceplates, tags, alarms

---

### Diagnostics & Tracing
- Online & diagnostics: check module states, device names, IO health
- Watch tables: create per-area diagnostic tables
- Traces: tune PID/motion; set appropriate sample times; store trace templates
- Cross-references: find unused tags/blocks before releases

---

### Commissioning Checklist (TIA)
- Hardware compiles; device names/IPs match real devices
- Download order: hardware > PLC > HMI; check time sync and diagnostics buffer
- Safety signature verified and documented
- IO check completed (inputs/outputs forced and validated, then unforced)
- Sequences run without alarms of concern; interlocks validated
- Backups: create `.zap18` archive and capture device flash/SD details

---

### Backup & Compatibility
- Always produce a `.zap18` prior to major changes and after commissioning
- Document CPU/module firmware versions and any optional packages used
- Avoid downgrading projects across TIA patch levels; upgrade all team workstations together

---

### Troubleshooting (Common TIA Issues)
- Incompatible firmware/module: update hardware catalog or match device firmware
- Profinet device not reachable: verify device name/IP, topology, and subnets
- Optimized access vs absolute addressing: align settings with HMI/OPC UA clients
- Know-how-protected blocks: ensure you have the necessary keys or unprotected sources
- Online/Offline differences: use compare tool before downloads

---

### Quick References
- OBs: `OB1` (cyclic), `OB35` (time), `OB100` (startup)
- Preferred languages: LAD/FBD for interlocks, SCL for logic and reuse
- Safety: segregate networks, validate, record signature
- HMI: alarms with clear text/help; consistent navigation and colors



