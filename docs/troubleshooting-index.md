# Troubleshooting Index

This index maps common **symptoms** to the existing troubleshooting page that
covers them. It does not duplicate any procedure — every entry links to an
existing guide. Use this page as the entry point when you know *what* is going
wrong but not *which document* explains how to fix it.

> **How to use:** Find the symptom that matches what you are seeing, then follow
> the link to the relevant guide. Each guide contains the full procedure, error
> messages, and remediation steps.

---

## Setup symptoms

| Symptom | What you might see | See |
|---|---|---|
| Simulator binary missing | `simulator binary not found`; `glassbox doctor --fix` suggests building the simulator | [Operator Runbook — Simulator binary not found](./operator-runbook.md#simulator-binary-not-found) |
| Build artifact missing / invalid WASM | `--wasm: file not found`; `not a valid WASM binary (bad magic bytes)` | [Debug command — Build Artifact Discovery](./debug-command.md#build-artifact-discovery) |
| Source discovery exhausted | `contract source not found: all discovery stages exhausted` | [Source Mapping Troubleshooting — Step 6](./source-mapping-troubleshooting.md#step-6--registry-and-github-fallback) |
| Configuration invalid / unreadable | Config file unreadable or invalid (`CONFIG_ERROR`) | [Stable Error Codes — General](./stable-error-codes.md#general) |
| Watch mode hangs / pending / timeout | Watcher stays pending, network timeout, cancellation | [Watch Mode — Troubleshooting](./WATCH_MODE.md#troubleshooting-watch-mode) |

---

## RPC symptoms

| Symptom | What you might see | See |
|---|---|---|
| RPC connection failed | `RPC connection failed: dial tcp ...`; all failover endpoints report the same error | [Operator Runbook — RPC connection failures](./operator-runbook.md#rpc-connection-failures) |
| RPC timeout | Debug run hangs then fails with timeout; `--show-metrics` shows high `rpc_avg_ms`; `request_timeout` config too low | [Operator Runbook — RPC timeouts](./operator-runbook.md#rpc-timeouts) |
| Transaction not found | `transaction not found` after successful RPC connection | [Operator Runbook — Transaction not found](./operator-runbook.md#transaction-not-found) |
| Rate limit exceeded | `rate limit exceeded` from RPC; multiple rapid requests fail | [Operator Runbook — Rate limiting](./operator-runbook.md#rate-limiting) |
| Unauthorized / invalid auth token | `unauthorized` or `missing/invalid auth token` | [Operator Runbook — Authentication errors](./operator-runbook.md#authentication-errors) |
| All RPC endpoints failed | `ALL_RPC_FAILED`; every endpoint in the failover pool is down | [Operator Runbook — RPC connection failures](./operator-runbook.md#rpc-connection-failures) |

---

## Source-map symptoms

| Symptom | What you might see | See |
|---|---|---|
| Missing build artifacts | `source discovery: local build directory not found`; `no .wasm files found` | [Source Mapping Troubleshooting — Symptom table](./source-mapping-troubleshooting.md#symptom-table) |
| Invalid / corrupt WASM | `does not have a valid WASM magic number`; `could not read ... (skipped)` | [Source Mapping Troubleshooting — Symptom table](./source-mapping-troubleshooting.md#symptom-table) |
| Hash mismatch | `build mismatch: local WASM hash ... does not match on-chain hash ...` | [Source Mapping Troubleshooting — Symptom table](./source-mapping-troubleshooting.md#symptom-table) |
| DWARF debug info missing | `mapping_quality: heuristic` or `unknown`; WAT shown instead of source | [Source Mapping Troubleshooting — Step 3](./source-mapping-troubleshooting.md#step-3--dwarf-debug-symbols-present) |
| Wrong source paths | `Location: <path>:L:C` printed with no source excerpt; DWARF resolves to `unknown` | [Source Mapping Troubleshooting — Step 4](./source-mapping-troubleshooting.md#step-4--project-root-and-source-paths) |
| Stale source alias | `alias: target for "..." must point to an existing directory` | [Source Mapping Troubleshooting — Symptom table](./source-mapping-troubleshooting.md#symptom-table) |
| Mapping disabled | `skip_source_mapping_env` preflight flag | [Source Mapping Troubleshooting — Symptom table](./source-mapping-troubleshooting.md#symptom-table) |

---

## Session symptoms

| Symptom | What you might see | See |
|---|---|---|
| Session integrity failure | `Session integrity check FAILED` on `session resume`; checkpoint validation errors after a crash | [Operator Runbook — Session integrity failures](./operator-runbook.md#session-integrity-failures) |
| Crash recovery | Session left in partially saved state after crash/SIGKILL/power loss | [Debug command — Session Recovery and Integrity](./debug-command.md#session-recovery-and-integrity) |
| Session write conflict | `SESSION_WRITE_CONFLICT`; concurrent writer saved a newer revision | [Session Locking](./session-locking.md#optimistic-revision-check) |
| Advisory lock held | `SESSION_LOCK_HELD`; lock file held by a live process | [Session Locking](./session-locking.md#advisory-lock-lifecycle) |
| Stale lock after crash | Lock file remains after owning process died | [Session Locking — Stale-lock cleanup](./session-locking.md#stale-lock-cleanup) |

---

## Signing symptoms

| Symptom | What you might see | See |
|---|---|---|
| Module file not found (PKCS#11) | `module file not found: ...` | [Audit Signing — Troubleshooting](./audit-signing.md#troubleshooting) |
| No slots with tokens / token label not found | `no slots with tokens found`; `no token with label "..." found` | [Audit Signing — Troubleshooting](./audit-signing.md#troubleshooting) |
| PIN incorrect / locked | `PIN authentication failed`; `the token PIN is locked` | [Audit Signing — Troubleshooting](./audit-signing.md#troubleshooting) |
| Key not found | `signing key "..." not found` | [Audit Signing — Troubleshooting](./audit-signing.md#troubleshooting) |
| Mechanism not supported (CKM_EDDSA) | `the CKM_EDDSA mechanism is not supported` | [Audit Signing — Troubleshooting](./audit-signing.md#troubleshooting) |
| Software key wrong format | OpenSSH / SEC1 / RSA key instead of PKCS#8 Ed25519 PEM | [Audit Signing — Software provider input validation](./audit-signing.md#software-provider-input-validation) |
| PKCS#11 validation limitation | `module_load` fails with `C_Initialize is not available` in the current build | [Audit Signing — Limitation in the current build](./audit-signing.md#limitation-in-the-current-build) |

---

## Doctor symptom-to-check mapping

`glassbox doctor` checks the 11 environmental dependencies and subsystems required
for full local debugging and simulation. When a check fails, the doctor marks
it with `[FAIL]` (or `[FAIL*]` if an automated fixer is available via `glassbox doctor --fix`).

Use this table to map observed symptoms to the exact doctor check, its identifier,
and the recommended remediation:

| Symptom / Error Observed | Doctor Check (`id`) | Check Name | Fixable? (`--fix`) | Remediation / Fix Hint |
|---|---|---|---|---|
| `simulator binary not found`; simulation cannot run | `simulator` | Glassbox simulator binary | **Yes** (`[FAIL*]`) | Run `glassbox doctor --fix` (or `make build-simulator` / `cargo build --release -p glassbox-simulator`) |
| `cache directory not found`; cannot persist session or RPC caches | `cache` | Cache directory | **Yes** (`[FAIL*]`) | Run `glassbox doctor --fix` (creates and initializes `~/.glassbox/cache`) |
| `glassbox://` links not handled by OS; deep links do not open | `protocol` | Protocol handler (`glassbox://`) | **Yes** (`[FAIL*]`) | Run `glassbox doctor --fix` (or `glassbox protocol:register`) |
| Missing or unverified Go modules; compilation errors in Go | `gomod` | Go module dependencies | **Yes** (`[FAIL*]`) | Run `glassbox doctor --fix` (executes `go mod download`) |
| Syntax errors in configuration; invalid TOML fields | `config` | Configuration file (`glassbox.toml`) | No (`[FAIL]`) | Inspect `glassbox.toml` or run `glassbox config init` to generate a valid default file |
| `RPC connection failed`; network unreachable; RPC timeout | `rpc` | RPC endpoint connectivity | No (`[FAIL]`) | Verify network connectivity or update RPC endpoint via `glassbox config set rpc_url <url>` |
| `glassbox://doctor-probe` fails dispatch; OS launches wrong binary | `deeplink` | Deep link dispatch | No (`[FAIL]`) | Run `glassbox protocol:repair` or verify OS default URL scheme associations |
| Telemetry spool full (>50MB) or spool directory permission denied | `telemetry` | Telemetry spool queue | No (`[FAIL]`) | Clear corrupted spool files: `rm -rf ~/.glassbox/telemetry/spool/*` or check permissions |
| Go compiler missing or version older than 1.22 | `go` | Go compiler | No (`[FAIL]`) | Install Go 1.22+ from [golang.org/doc/install](https://golang.org/doc/install) |
| Rust compiler (`rustc`) not found | `rust` | Rust toolchain | No (`[FAIL]`) | Install Rust via [rustup.rs](https://rustup.rs) |
| Cargo package manager not found | `cargo` | Cargo package manager | No (`[FAIL]`) | Install Cargo via [rustup.rs](https://rustup.rs) |

### Doctor Execution Examples

#### 1. Successful Health Check

When all dependencies are properly provisioned:

```bash
$ glassbox doctor
Glassbox Environment Diagnostics
=============================

[OK] Go compiler (go1.26.0)
[OK] Rust toolchain (rustc 1.85.0)
[OK] Cargo package manager (cargo 1.85.0)
[OK] Glassbox simulator binary (1.0.0)
[OK] Cache directory (~/.glassbox/cache)
[OK] Protocol handler (glassbox://)
[OK] Go module dependencies
[OK] Configuration file (glassbox.toml)
[OK] RPC endpoint connectivity (https://soroban-testnet.stellar.org)
[OK] Deep link dispatch
[OK] Telemetry spool queue

[OK] All dependencies are installed and ready!
```

#### 2. Failure Path with Automated Fixes (`[FAIL*]`)

When dependencies are missing or misconfigured:

```bash
$ glassbox doctor
Glassbox Environment Diagnostics
=============================

[OK] Go compiler (go1.26.0)
[OK] Rust toolchain (rustc 1.85.0)
[OK] Cargo package manager (cargo 1.85.0)
[FAIL*] Glassbox simulator binary
  → Build the simulator: make build-simulator or cargo build --release -p glassbox-simulator
[FAIL*] Cache directory
  → Create cache directory: mkdir -p ~/.glassbox/cache
[OK] Protocol handler (glassbox://)
[OK] Go module dependencies
[OK] Configuration file (glassbox.toml)
[FAIL] RPC endpoint connectivity
  → Check internet connection or configure a valid RPC endpoint: glassbox config set rpc_url <url>
[OK] Deep link dispatch
[OK] Telemetry spool queue

⚠ Some dependencies are missing. Follow the hints above to fix.

Tip: Run 'glassbox doctor --fix' to attempt automatic fixes for 2 issue(s).
```

#### 3. Automated Remediation

To apply all automatic fixes non-interactively (e.g. in CI or setup scripts):

```bash
glassbox --non-interactive doctor --fix --yes
```

#### 4. Diagnostics Archive for Escalation

When diagnosing an unreproducible issue or reporting a bug, create a redacted `.gbdiag` archive:

```bash
glassbox doctor --bundle --bundle-output ./diag.gbdiag
```

---

## Stable error-code reference

Every structured Glassbox error carries a stable `Code` (automation contract) that
you can key on instead of parsing free-form text. Codes are grouped below by
symptom area; the full catalogue with exit codes is in
[Stable Error Codes](./stable-error-codes.md).

| Area | Code | Meaning |
|---|---|---|
| Setup | `SIMULATOR_NOT_FOUND` | Simulator binary absent or not executable |
| Setup | `SOURCE_DISCOVERY_FAILED` | Source mapping unavailable for contract |
| RPC | `RPC_CONNECTION_FAILED` | Endpoint unreachable |
| RPC | `RPC_TIMEOUT` | Request timed out |
| RPC | `ALL_RPC_FAILED` | All failover endpoints failed |
| RPC | `RPC_ERROR` | RPC server returned a non-200 / error response |
| RPC | `TRANSACTION_NOT_FOUND` | TX hash not found on the selected network |
| RPC | `LEDGER_NOT_FOUND` | Ledger sequence unavailable |
| RPC | `LEDGER_ARCHIVED` | Ledger has been archived |
| RPC | `RATE_LIMIT_EXCEEDED` | RPC rate limit hit |
| RPC | `UNAUTHORIZED` | Missing or invalid auth token |
| RPC | `INVALID_NETWORK` | Unrecognised `--network` value |
| RPC | `NETWORK_NOT_FOUND` | Named network has no known endpoint |
| Session | `SESSION_WRITE_CONFLICT` | Concurrent writer saved a newer revision |
| Session | `SESSION_LOCK_HELD` | Advisory lock held by a live process |

Exit-code taxonomy: 0 = success, 1 = user error, 2 = config/environment error,
3 = internal/runtime error, 130 = interrupted (SIGINT). See
[Exit Codes](./exit-codes.md) for the full mapping.

---

## Command-help reference

When you know the flag or command but want to confirm accepted values and
defaults, run:

```bash
glassbox debug --help          # all debug flags and their defaults
glassbox --help                # global flags (--profile, --log-level, --json, ...)
glassbox session --help        # session subcommands
glassbox audit:sign --help     # signing flags
glassbox protocol:diagnose     # glassbox:// registration status
glassbox doctor                # comprehensive environment health check
```

For machine-readable output pipe through `--json` or `--format json`, then key
on `error.code` in the JSON envelope (see
[Stable Error Codes — Output adapters](./stable-error-codes.md#output-adapters)).

---

## See also

- [Debug command](./debug-command.md) — complete `glassbox debug` reference
- [Operator runbook](./operator-runbook.md) — symptom-based RPC/simulator/session remediation
- [Source mapping troubleshooting](./source-mapping-troubleshooting.md) — source-mapping wizard
- [Audit signing](./audit-signing.md) — software and PKCS#11 HSM signing
- [Watch mode](./WATCH_MODE.md) — `--watch` and `--watch-files` symptoms
- [Session locking](./session-locking.md) — concurrency and revision handling
- [Stable error codes](./stable-error-codes.md) — error code catalogue
- [Exit codes](./exit-codes.md) — exit-code registry
