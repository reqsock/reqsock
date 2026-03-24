<p align="center">
  <img src="https://www.reqsock.com/assets/img/white-logo.svg" alt="Reqsock" width="200"/>
</p>
<p align="center">
  <strong>AI-Powered Web Antimalware Engine for Linux Servers</strong><br>
  <em>Detect & Neutralize. AI-Powered Defense.</em>
</p>

<p align="center">
  <a href="https://github.com/reqsock/reqsock-project/releases/latest"><img src="https://img.shields.io/github/v/release/reqsock/reqsock-project?label=release&color=0e73cc" alt="Release"></a>
  <a href="https://reqsock.com"><img src="https://img.shields.io/badge/website-reqsock.com-0e73cc" alt="Website"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-proprietary-red" alt="License"></a>
  <img src="https://img.shields.io/badge/platform-linux%20x86__64%20%7C%20aarch64-333" alt="Platform">
  <img src="https://img.shields.io/badge/rust-1.85%2B-orange" alt="Rust">
</p>

---

Reqsock is a next-generation antimalware engine that uses artificial intelligence to detect and neutralize web threats in real-time with minimal server impact. It combines YARA signature scanning with SMART, a neural network-based heuristic engine, to identify web shells, backdoors, crypto miners, rootkits, SEO spam, and zero-day payloads across PHP, JavaScript, Python, Perl, Ruby, and HTML files — with sub-millisecond response times and near-zero resource consumption.

Native plugins for **cPanel/WHM**, **DirectAdmin**, and **Plesk** integrate directly into hosting panels with real-time upload scanning, web dashboards, and one-command deployment. Built for hosting companies, agencies, and enterprises requiring fleet-wide protection.

<p align="center">
  <code>94.5% detection rate</code> · <code>< 50ms response</code> · <code>250K+ threats blocked daily</code> · <code>Deploy in under 60 seconds</code>
</p>

---

## Table of Contents

- [Features](#features)
- [Components](#components)
- [System Requirements](#system-requirements)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [Panel Plugins](#panel-plugins)
- [Reqsock SDK](#reqsock-sdk)
- [Architecture](#architecture)
- [Usage Guide](#usage-guide)
- [Recommendations](#recommendations)
- [Documentation](#documentation)
- [Changelog](#changelog)
- [License](#license)
- [Support](#support)

---

## Features

### Multi-Engine Detection
- **YARA Scanning** — Signature-based pattern matching with auto-updating rules
- **SMART Engine** — Neural network heuristic analysis with 9-class malware classification: `clean`, `seo_spam`, `js_malware`, `backdoor`, `webshell`, `obfuscation`, `script_bot`, `rfi_uploader`, `wp_exploit`
- **SHA256 Whitelist** — Hash-verified database to prevent false positives on known-good files

### Real-Time Protection
- File system watcher with configurable depth, cooldown, and rate limiting
- Upload hook scanning for FTP/SFTP transfers (< 1 second response)
- Event-driven architecture for low-latency threat response
- Automatic batch processing of file system events

### Hosting Panel Integration
- Native plugins for **cPanel/WHM**, **DirectAdmin**, and **Plesk**
- Web dashboards embedded in each panel's UI
- Automatic account discovery and watcher configuration
- Per-user scan reports and detection history
- Hook-based real-time upload scanning

### Safety and Reliability
- Automatic backup before any file removal (rollback on demand)
- Configurable detection thresholds and action modes
- Dry-run mode for safe testing
- Disk-full protection and emergency memory thresholds
- License enforcement with clear diagnostics at startup

### Performance
- Scan caching (mmap-based) — unchanged files skipped automatically
- Memory-mapped file access for high-throughput scanning
- Rayon-based parallel processing with configurable thread pools
- SMART inference < 1ms per file on CPU (~50KB model footprint)
- Lock-free model hot-reload — zero engine downtime on updates

---

## Components

```
reqsock/
├── reqsock-engine        Core detection engine (daemon)
├── reqsock-client        CLI tool (reqsock command)
├── reqsock-sdk           Communication library (encrypted UDP)
├── plugins/
│   ├── plugin-core       Shared plugin infrastructure
│   ├── directadmin       DirectAdmin plugin + dashboard
│   ├── cpanel            cPanel/WHM plugin + dashboard
│   └── plesk             Plesk plugin + dashboard
├── reqtools              Shared utilities and data structures
└── yara-z/               YARA pattern matching engine
```

| Component | Version | Description |
|-----------|---------|-------------|
| **reqsock-engine** | 4.0.9 | Core daemon: YARA + SMART scanning, file watcher, event system |
| **reqsock** (client) | 1.0.4 | CLI interface for scans, license, rules, and model management |
| **reqsock-sdk** | 1.0.0 | Encrypted UDP communication library (AES-256-GCM) |
| **reqsock-plugin-core** | 1.0.0 | Shared logic: config, scanner, log parser, dashboard output |
| **reqsock-directadmin** | 1.0.0 | DirectAdmin plugin with 21 CLI commands + web UI |
| **reqsock-cpanel** | 1.0.0 | cPanel/WHM plugin with 21 CLI commands + WHM sidebar |
| **reqsock-plesk** | 1.0.0 | Plesk extension with 21 CLI commands + native integration |

---

## System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| OS | Linux x86_64 or aarch64 | Ubuntu 22.04+ / Debian 12+ / RHEL 9+ |
| CPU | 2 cores | 4+ cores |
| RAM | 512 MB | 2 GB+ |
| Disk | 100 MB (install) | 500 MB+ (logs, backups, cache) |

### Supported Distributions

- Ubuntu 20.04, 22.04, 24.04
- Debian 11, 12
- CentOS / RHEL 8, 9
- Amazon Linux 2, 2023
- Alpine Linux 3.18+ (musl)

### Supported Hosting Panels

- cPanel/WHM 108+
- DirectAdmin 1.64+
- Plesk Obsidian 18.0+

---

## Installation

### From .deb Package

```bash
# Download the latest release
wget https://github.com/reqsock/reqsock-project/releases/download/v4.0.9/reqsock-4.0.9.deb

# Install
sudo dpkg -i reqsock-4.0.9.deb

# Register license
reqsock license reg <YOUR-LICENSE-KEY>

# Start the service
sudo systemctl enable --now reqsock-engine
```

### With Panel Plugin (One-Command Installer)

The installer automatically detects your OS and hosting panel:

```bash
# cPanel/WHM
curl -sSL https://reqsock.com/install | bash -s -- --panel cpanel --license <KEY>

# DirectAdmin
curl -sSL https://reqsock.com/install | bash -s -- --panel directadmin --license <KEY>

# Plesk
curl -sSL https://reqsock.com/install | bash -s -- --panel plesk --license <KEY>
```

### Verify Installation

```bash
# Engine status
sudo systemctl status reqsock-engine

# Version check
reqsock --version

# Health check
reqsock-cpanel status   # or reqsock-directadmin / reqsock-plesk
```

---

## Quick Start

### 1. Scan a File

```bash
reqsock scan /path/to/file
```

### 2. Scan a Directory

```bash
reqsock scan /var/www/html
```

### 3. Scan with Dry-Run (Detection Only)

```bash
reqsock scan /var/www/html --dryrun
```

### 4. Check Engine Statistics

```bash
reqsock stats
```

### 5. Score a Suspicious File

```bash
reqsock score /path/to/suspicious.php
```

### 6. Manage YARA Rules

```bash
reqsock rules version    # Check current rules version
reqsock rules update     # Update from server
```

### 7. Manage SMART Model

```bash
reqsock model version    # Check model version
reqsock model update     # Hot-reload latest model (zero downtime)
```

---

## Configuration

Main configuration file: `/opt/reqsock-engine/engine.conf` (INI format)

### Key Sections

#### Scan Engine

```ini
[Scan]
cpu_usage = 0.35              # CPU core percentage for scanning
performance = balanced         # minimal | low | balanced | high
timeout_profile = balanced     # fast (3s) | balanced (4s) | thorough (10s)
max_file_size = 100M
```

#### SMART Heuristic Engine

```ini
[SMART]
enabled = true
threshold = 0.9                # Detection confidence threshold (0.0 - 1.0)
action = none                  # none (report only) | remove (with backup)
remove_threshold = 0.9         # Minimum score for removal (strict > comparison)
```

#### File Watcher (Real-Time Monitoring)

```ini
[Watcher]
enabled = true
paths = /var/www,/home
depth = 8
cooldown = 60                  # Seconds between re-scans of same file
rate_limit = 100               # Max events/sec global
```

#### Scan Cache

```ini
[Cache]
enabled = true
cache_max_files = 5000000      # Max cached file entries
```

#### Plugin Configuration

Plugins use a separate file: `/opt/reqsock-engine/plugin.conf`

```ini
[Plugin]
scan_uploads = true
upload_action = block          # block | quarantine | log | allow
scan_on_cron = true
cron_interval = daily
notifications = true

[Exclusions]
paths = */node_modules/*, */.git/*, */cache/*
```

For the complete reference, see [docs/ENGINE_CONFIGURATION.md](docs/ENGINE_CONFIGURATION.md).

---

## Panel Plugins

All three plugins share the same 21 CLI commands and a unified web dashboard.

### Commands

```bash
# Replace <plugin> with: reqsock-cpanel | reqsock-directadmin | reqsock-plesk

# Scanning
<plugin> scan <path> [--dryrun]
<plugin> scan-account <user> [--dryrun]
<plugin> scan-domain <user> <domain> [--dryrun]

# Monitoring
<plugin> stats
<plugin> status
<plugin> dashboard [--json]
<plugin> user-summary <user> [--json]
<plugin> log [--user <user>] [--lines <n>]

# Management
<plugin> whitelist <path> [--global]
<plugin> rollback <path>
<plugin> rules <version|update|reload>
<plugin> model <version|update>

# Hook Handlers (called automatically by the panel)
<plugin> hook upload <user> <path>
<plugin> hook account-created <user>
<plugin> hook account-deleted <user>
<plugin> hook domain-added <user> <domain>

# Setup
<plugin> configure
<plugin> reconcile
<plugin> install
<plugin> uninstall
```

### Web Dashboard

Each plugin installs a web dashboard into its respective panel:

- **cPanel/WHM** — WHM > Plugins > Reqsock Antimalware
- **DirectAdmin** — Admin Level > Reqsock
- **Plesk** — Extensions > Reqsock

Dashboard features:
- Engine status with live indicators
- License, rules, and watcher status cards
- Recent detections table with threat classification
- Scan statistics (total scans, threats, cleaned, errors)
- Manual scan form
- Log viewer with filtering
- Rules and model management

### Upload Scanning

When `scan_uploads = true`, uploaded files are scanned in real-time:

```
User uploads file.php via FTP
  → Panel hook triggers: <plugin> hook upload <user> /path/to/file.php
    → SDK sends ApiScan to engine (< 1 second)
      → Clean: upload allowed
      → Threat: action taken (block/quarantine/log)
```

---

## Reqsock SDK

The SDK provides a standalone Rust library for communicating with the engine over encrypted UDP.

### Usage

```rust
use reqsock_sdk::{Connection, Command, ScanVerdict};

// Connect to the engine
let conn = Connection::new()?;

// Scan a file
let response = conn.send(Command::Scan {
    path: "/var/www/html/suspicious.php".into(),
    dryrun: false,
})?;

// Check the verdict
match response.verdict() {
    ScanVerdict::Clean => println!("File is clean"),
    ScanVerdict::Threat(info) => println!("Threat: {}", info),
    ScanVerdict::Error(e) => eprintln!("Scan error: {}", e),
    ScanVerdict::EngineDown => eprintln!("Engine not responding"),
}
```

### Available Commands

| Command | Description |
|---------|-------------|
| `Scan` | Scan a file or directory |
| `FullScan` | Full recursive scan |
| `SafeScan` | Detection only, no actions |
| `ApiScan` | Quick scan for upload hooks |
| `Score` | Get threat score for a file |
| `Stats` | Engine statistics |
| `Whitehash` | Add file to whitelist |
| `Rollback` | Restore quarantined file |
| `RulesVersion` | Current YARA rules version |
| `RulesUpdate` | Update rules from server |
| `ModelVersion` | Current SMART model info |
| `ModelUpdate` | Hot-reload latest model |
| `ReloadWhitelist` | Reload whitelist database |
| `LicenseCheck` | Verify license status |

---

## Architecture

```
                        ┌──────────────────────────────┐
                        │         Panel Plugin         │
                        │  (cPanel / DA / Plesk)       │
                        │                              │
                        │  ┌────────────────────────┐  │
                        │  │    Web Dashboard       │  │
                        │  └────────────────────────┘  │
                        │  ┌────────────────────────┐  │
                        │  │   21 CLI Commands      │  │
                        │  └───────────┬────────────┘  │
                        └──────────────┼───────────────┘
                                       │
                              ┌────────▼────────┐
                              │   Reqsock SDK   │
                              │  AES-256-GCM    │
                              │  UDP :10888     │
                              └────────┬────────┘
                                       │
┌──────────────────────────────────────┼───────────────────────────────┐
│                            Reqsock Engine                            │
│                                                                      │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │              Event System (priority-based workers)             │  │
│  └──────┬──────────┬───────────┬───────────┬──────────┬──────────┘  │
│         │          │           │           │          │              │
│  ┌──────▼───┐ ┌────▼────┐ ┌───▼────┐ ┌────▼───┐ ┌───▼──────┐      │
│  │ mod_scan │ │ mod_cli │ │mod_api │ │mod_lic │ │mod_logger│      │
│  │ ├─ YARA  │ │ Client  │ │ HTTP   │ │ JWT    │ │ Syslog   │      │
│  │ ├─ SMART │ │ Batch   │ │ Pool   │ │ Device │ │ JSON     │      │
│  │ └─ Cache │ │ Rate    │ │ Auth   │ │ Verify │ │ Audit    │      │
│  └──────────┘ └─────────┘ └────────┘ └────────┘ └──────────┘      │
│  ┌──────────────┐  ┌──────────────┐                                 │
│  │ mod_rollback │  │ mod_elastic  │                                 │
│  │ Backup/Restore│ │ Elasticsearch│                                 │
│  └──────────────┘  └──────────────┘                                 │
│                                                                      │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │           File Watcher (inotify) — Real-time FS monitoring    │  │
│  └────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘
```

### Detection Pipeline

```
File event or scan request
  │
  ├─ SHA256 Whitelist ── match ──→ SKIP (known-good)
  │
  ├─ Scan Cache ── unchanged ──→ SKIP (already scanned)
  │
  ├─ YARA Scan ── match ──→ REPORT / ACTION
  │
  └─ SMART Analysis
       ├─ score > threshold ──→ REPORT / ACTION
       └─ score ≤ threshold ──→ CLEAN
```

---

## Usage Guide

### Deployment Workflow

1. **Install and register**
   ```bash
   sudo dpkg -i reqsock-4.0.9.deb
   reqsock license reg <KEY>
   sudo systemctl enable --now reqsock-engine
   ```

2. **Start in detection-only mode**
   ```ini
   [SMART]
   enabled = true
   action = none
   ```

3. **Monitor detections** (48h minimum recommended)
   ```bash
   journalctl -u reqsock-engine -f
   reqsock stats
   ```

4. **Whitelist false positives** (if any)
   ```bash
   reqsock whitelist /path/to/known-good-file.php --global
   ```

5. **Enable automatic removal** (after tuning)
   ```ini
   [SMART]
   action = remove
   remove_threshold = 0.85
   ```

6. **Install panel plugin** (optional)
   ```bash
   reqsock-cpanel install
   reqsock-cpanel configure
   ```

### Operating Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| **Normal** | Real-time monitoring + on-demand scans | Production servers |
| **Dryrun** | Detection only, no file actions | Initial deployment |
| **SafeScan** | Non-destructive analysis | Auditing, compliance |
| **ApiScan** | Single-file quick scan via SDK | Upload hooks, integrations |

### Log Format

Detections:
```
SMART;Malware detected;/var/www/html/shell.php;backdoor (score: 0.873)
YARA;Malware detected;/var/www/html/uploader.php;php_webshell_generic
```

Actions:
```
ACTION;Malware removed;/var/www/html/shell.php;backdoor (score: 0.873)
ACTION;File quarantined;/var/www/html/uploader.php;php_webshell_generic
```

---

## Recommendations

### Security

- Start with `action = none` for at least 48 hours in new environments
- Use the SHA256 whitelist for custom files specific to your applications
- Keep `remove_threshold` at 0.9+ and lower gradually after review
- Monitor the rollback directory — removed files are always backed up
- Ensure network connectivity for rule and model updates

### Performance

- Set watcher `depth` to the minimum needed for your directory structure
- Increase `cooldown` on servers with heavy write loads (file uploads, builds)
- Use scan `Cache` (enabled by default) to skip unchanged files
- Adjust `cpu_usage` if sharing resources with other services

### Integration

- **systemd** — Automatic startup, restart on failure, journald logging
- **Syslog** — Compatible with rsyslog, syslog-ng, journald
- **Elasticsearch** — Enable `mod_elastic` for centralized log aggregation
- **SIEM** — JSON log output for Splunk, ELK, Wazuh, or similar platforms

---

## Documentation

| Document | Description |
|----------|-------------|
| [ENGINE_CONFIGURATION.md](docs/ENGINE_CONFIGURATION.md) | Complete configuration reference |
| [SMART.md](docs/SMART.md) | SMART detection engine overview |
| [CHANGELOG-4.0.9.md](CHANGELOG-4.0.9.md) | Latest release notes |
| [CHANGELOG-4.0.8.md](CHANGELOG-4.0.8.md) | Previous release notes |
| [CHANGELOG-4.0.7.md](CHANGELOG-4.0.7.md) | SMART v1 release notes |

---

## Changelog

### v4.0.9 (2026-03-19)
- SMART model v16r4: 93.7% detection, 0% FP on 518K clean files
- Reqsock SDK: encrypted UDP communication library
- Native plugins for cPanel/WHM, DirectAdmin, Plesk with web dashboards
- Lock-free model hot-reload via `reqsock model update`
- One-command installer with auto panel detection

### v4.0.8 (2026-03-17)
- SMART engine v2: 94.5% detection, 0% FP on 2M clean files
- mmap-based scan cache for unchanged file skipping
- Mandatory license verification at startup

### v4.0.7 (2026-03-05)
- SMART detection engine (initial release)
- SHA256 whitelist expansion
- Candle ML inference integration

See individual changelogs for full details.

---

## License

Copyright (c) 2024–2026 Reqsock. All Rights Reserved.

This is proprietary software. Unauthorized copying, distribution, modification, reverse engineering, or use of this software is strictly prohibited. See [LICENSE](LICENSE) for the full End-User License Agreement.

Third-party component licenses: [https://reqsock.com/third-party-licenses](https://reqsock.com/third-party-licenses)

---

## Support

| Channel | Link |
|---------|------|
| Website | [https://reqsock.com](https://reqsock.com) |
| Issues | [https://github.com/reqsock/reqsock-project/issues](https://github.com/reqsock/reqsock-project/issues) |
| Email | support@reqsock.com |

---

<p align="center">
  Built with Rust. Designed for production.
</p>
