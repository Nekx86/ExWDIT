<div align="center">

![ExWDIT – Executable and Digital Investigation Toolkit for PE malware analysis and digital forensics](ExWDITBanner.jpg)

# ExWDIT

**Executable / Digital Investigation and Behavioral Analysis Toolkit**

*A C++ desktop forensic analysis suite for static PE analysis, runtime behavior monitoring, evidence-based risk scoring, and investigation reporting.*

[Türkçe](README.tr.md) · [Features](#features) · [Roadmap](#roadmap) · [Security](SECURITY.md) · [Contributing](CONTRIBUTING.md)

![Status](https://img.shields.io/badge/status-early%20development-orange)
![Language](https://img.shields.io/badge/C%2B%2B-20-blue)
![UI](https://img.shields.io/badge/UI-Dear%20ImGui-informational)
![Platform](https://img.shields.io/badge/platform-Windows%20x64-lightgrey)
![License](https://img.shields.io/badge/license-MIT-green)

</div>

---

## What is ExWDIT?

**ExWDIT** is an open-source **digital forensics and executable behavior analysis toolkit** written in **C++20** with a **Dear ImGui** desktop interface. It is designed for **security researchers, malware analysts, reverse engineers, anti-cheat teams, and digital forensics investigators** who need to understand a suspicious Windows executable (PE file).

ExWDIT is built around one question:

> *What is this executable, what does it contain, what does it load, what does it access, what does it communicate with, and what does it do while running?*

It combines **static PE analysis** (what a file contains without running it) and **dynamic behavioral analysis** (what a process does while running) into a single **evidence → finding → risk score → report** pipeline.

> **Project status:** ExWDIT is in **early development**. The architecture and feature set are fully specified; implementation proceeds in phases (see [Roadmap](#roadmap)). Features below describe the design goals unless marked otherwise in the roadmap table.

## Why ExWDIT?

Most analysis utilities are isolated tools: a PE viewer here, a process monitor there, a string extractor somewhere else. ExWDIT is designed as **one investigation platform** where every feature feeds the same evidence/event/finding model, so an analyst can click from a finding to its evidence, the runtime event, the process, and the affected module, file, registry key, or network endpoint.

**Core principles**

- **Evidence-based, not verdict-based.** ExWDIT reports `Indicator`, `Suspicious`, `Heuristic`, and `Requires Investigation`. It never claims "this is definitely malware."
- **Transparent risk scoring.** Weights are configurable and every score change is explained.
- **Evidence integrity.** The original sample is treated as read-only and hashed before analysis.
- **Static and dynamic analysis are separate.** Opening a file never executes it. Execution is an explicit analyst action.
- **Defensive use only.** No features to bypass, disable, or evade anti-cheat or security products.

## Features

### Static analysis (no execution)

- PE32 / PE32+ parsing (x86, x64, ARM/ARM64 where feasible)
- DOS header, NT headers, optional header, all data directories
- RVA ↔ file offset ↔ virtual address conversion
- Section analysis with entropy visualization and RWX / W+X / high-entropy heuristics
- Import and export analysis with API categorization (process, memory, file, registry, network, injection-related, cryptography, and more)
- String extraction: ASCII, UTF-8, UTF-16LE with categories (URLs, domains, IPs, file paths, registry paths, commands, PowerShell indicators)
- Resources, TLS callbacks, relocations, debug directory, load config, overlay
- MD5, SHA-1, SHA-256 hashing in background workers
- Authenticode signature inspection (distinguishes *signature exists* from *signature is trusted*)
- Packer, compiler, and linker indicators
- Optional **YARA** rule scanning

### Dynamic analysis (explicit, controlled execution)

- Process creation, termination, and clickable **process tree**
- **Module / DLL** loading with signature, hash, path, and architecture checks
- **File-system** monitoring (create, open, read, write, rename, delete)
- **Registry** monitoring with persistence-location heuristics
- **Network** monitoring (TCP, UDP, DNS, process-to-IP/domain mapping, connection timeline)
- **Memory map** inspection (RWX, executable private memory, unbacked executable regions)
- Thread and handle activity

### Investigation layer

- Unified, searchable, filterable **behavior timeline**
- **Behavior graph** built only from observed evidence (process, file, registry, IP, domain, module nodes)
- **Findings engine** where each finding carries severity, category, description, evidence, confidence, source, and timestamp
- **Explainable risk engine** with configurable weights
- **Global search** across processes, modules, files, registry, network, strings, imports, exports, events, and findings
- **Case management** (new, open, save, export) with analyst notes and tags
- **JSON and HTML reports**

### Example finding

```text
⚠ Suspicious Executable Memory

A private memory region is executable and writable.

Evidence:
  Protection: RWX
  Backing:    Private
  Size:       64 KB

Confidence: Medium
```

## Architecture

ExWDIT uses a layered, modular architecture so new analysis engines can be added without rewriting the UI.

```text
Evidence
   ├── Static Analysis ──┐
   └── Dynamic Analysis ─┴─► Evidence Model ─► Finding Engine
                                                  ├─► Risk Engine ─┐
                                                  └─► Timeline ────┴─► Investigation UI ─► Reports
```

```text
src/
├── Core/      Application, AppState, EventBus, Logger, Result, TaskSystem
├── PE/        PE parser and header/section/import/export/resource analyzers
├── Static/    Hashing, strings, entropy, signatures, packer detection, YARA
├── Runtime/   Process, file, registry, network, DNS, module, memory monitors
├── Security/  Risk engine, indicator database, signature verification
├── Reports/   JSON and HTML report generation
└── UI/        Dear ImGui views (dashboard, PE, imports, timeline, findings, ...)
```

**Technology:** C++20 · Dear ImGui (docking) · CMake · vcpkg · fmt · spdlog · nlohmann/json · Windows cryptographic APIs or OpenSSL · optional YARA.

## Roadmap

| Phase | Scope | Status |
|------:|-------|--------|
| 1 | Foundation: CMake, C++20, Dear ImGui shell, docking, dark theme, logging | Planned |
| 2 | PE engine: validation, headers, sections, data directories, RVA conversion | Planned |
| 3 | Static analysis: hashes, strings, entropy, imports/exports, resources, signature | Planned |
| 4 | Findings engine and explainable risk score | Planned |
| 5 | Runtime engine: process tree, module loads, file and network activity | Planned |
| 6 | Network / file / registry telemetry | Planned |
| 7 | Memory analysis | Planned |
| 8 | YARA integration | Planned |
| 9 | JSON / HTML reporting and case export | Planned |
| 10 | Behavior graph, global search, investigation workspace | Planned |

## Safe analysis recommendations

- **Static analysis** does not execute the sample, but treat every analyzed file as untrusted.
- **Dynamic analysis** executes the target. Run it only in an **isolated environment** such as a virtual machine or Windows Sandbox, with snapshots, restricted or disabled networking, no shared folders, and no personal data.
- Process-level sandboxing (job objects, restricted tokens, AppContainer) is an additional layer, **not** a replacement for a VM.

ExWDIT does not upload samples anywhere without an explicit user action.

## Intended use

ExWDIT is intended for **defensive security research, malware analysis, incident response, digital forensics, and anti-cheat investigation.** See [SECURITY.md](SECURITY.md) for responsible use and vulnerability reporting.

## Frequently asked questions

**What is ExWDIT?**
ExWDIT is an open-source C++ desktop toolkit for static PE analysis, runtime behavior monitoring, evidence-based risk scoring, and forensic reporting of Windows executables.

**What does the name stand for?**
Suggested expansion: *Execution & Workflow Digital Investigation Tool.* Product descriptor: *Application Behavior & Digital Forensics.*

**Is ExWDIT a malware scanner or antivirus?**
No. It is an investigation and analysis tool. It produces explainable indicators and evidence, not definitive verdicts.

**Can ExWDIT help anti-cheat teams?**
Yes. It focuses on loaded modules, unexpected DLLs, process trees, memory maps, executable private memory, and unsigned components for defensive investigation. It contains no anti-cheat bypass functionality.

**Which platforms are supported?**
Windows x64 first. The architecture keeps platform-specific code behind interfaces to allow Linux and macOS support later.

**Is it ready to use?**
Not yet. The project is in early development; see the [Roadmap](#roadmap).

## Keywords

`digital forensics` · `malware analysis` · `PE analysis` · `PE parser` · `portable executable` · `static analysis` · `dynamic analysis` · `behavioral analysis` · `reverse engineering` · `anti-cheat` · `process monitoring` · `YARA` · `threat hunting` · `incident response` · `C++20` · `Dear ImGui` · `Windows security` · `forensic toolkit`

## Contributing

Contributions are welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) first.

## License

Released under the [MIT License](LICENSE).

## Citation

If you use ExWDIT in research or documentation, see [CITATION.cff](CITATION.cff).
