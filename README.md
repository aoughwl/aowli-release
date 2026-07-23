# aowli — release binaries

Prebuilt distribution of the **aowli** interpreter: it runs a nimony program's
typed, semantically-analysed NIF (`.s.nif`) — the form emitted after `nimsem`.
**Binary only** — the source lives in the private `aoughwl/aowli` repo.

This repo is **public**: anyone may download and use these binaries.
**Issues welcome** → <https://github.com/aoughwl/aowli-release/issues>

## v0.2.0 — runtime-complete

This release ships the **runtime-complete** aowli. All six runtime layers are now
implemented, so programs that exercise real memory, the OS, finalization, dynamic
dispatch, concurrency, and parallelism run — no longer just the pure-value subset:

| layer | what landed |
|-------|-------------|
| **flat memory** | a real flat byte heap under the value model — `cast`, `ptr`/`UncheckedArray`, `copyMem`/`equalMem`, pointer arithmetic write through actual bytes |
| **OS / fd** | genuine file descriptors and syscalls — `readFile`/`writeFile` are byte-exact, argv/env, stdio on real fds |
| **finalization + refcount** | ARC-style reference counting with destructors/finalizers firing at scope exit; custom `=destroy` hooks run |
| **loud dispatch** | method/dispatch on nil or an unimplemented shape is a **hard error**, never a silent no-op — the old silent-nil-dispatch class is gone |
| **async** | the cooperative Future/await dispatcher runs; combinators and `{.async.}` sugar execute |
| **threads** | a cooperative thread pool with `parfor` (`||` fork-join) |

Corpus parity (differential vs the nimony compiler as reference):
**tree-walker 432/469 = 92%**, **358 programs PASS-BOTH** engines, **zero
in-scope silent failures**.

## Binaries

| binary | what it does |
|--------|--------------|
| `aowli-interp` | run a program; `--trace` prints the call-tree, `--trace-depth:N` bounds it, `--trace-profile` gives per-routine call counts |
| `aowli-dbg`    | batch breakpoints: `--break:LINE` / `--break-func:NAME`, dumps each frame's locals at every hit (bounded render; caps at 20k hits) |

## Run

```sh
chmod +x bin/aowli-interp bin/aowli-dbg
./bin/aowli-interp <module.s.nif>              # run the program
./bin/aowli-interp --trace-profile <m.s.nif>   # per-routine call counts (wide programs)
./bin/aowli-dbg  --break:29 <module.s.nif>     # break at line 29, dump frame vars
```

## Build & hardening

Built from the private source via the `aowl-release` pipeline:

- **IR control-flow obfuscation (obfnif)** — behaviour-preserving dead-guards, junk discards, and opaque predicates woven into the typed NIF before the backend runs, so the shipped machine code carries un-elided decoy control flow (symbol rename left off; program output is unchanged)
- **string-encrypted 7-day expiry gate** — a fail-closed licence check runs at module-init and again at exit; the refusal message is stored XOR-obfuscated and decrypted into a stack buffer only when it fires, so it never appears in `strings`. The check is dispersed across three sites and output-coupled (an accumulator the exit path verifies), so patching one branch corrupts behaviour rather than cleanly bypassing.
- **anti-debug** — non-invasive `/proc/self/status` TracerPid check; a traced process refuses. Undebugged runs are unaffected.
- **hardened strip** — `strip --strip-all` (symbol table) plus `objcopy` removal of `.comment`, `.note.*`, and `.gnu_debuglink` (compiler/build-id provenance).

Verified before publishing: byte-identical program output vs the source build
across the corpus, and the stripped binaries expose **no aowli source paths, no
internal proc/type names, and no gate strings**.

## Integrity

| binary | size (bytes) | sha256 |
|--------|-------------|--------|
| `bin/aowli-interp` | 1,949,240 | `e1f46255092952fcd7f0caaad565907331c243510cad757af6fe4670aca8d9ee` |
| `bin/aowli-dbg`    | 802,360 | `7e4f137b7cedc55863c2026cc31a9445ebe8139a5ce7bcab6b4b914bff5179af` |

```sh
sha256sum -c <<'EOF'
e1f46255092952fcd7f0caaad565907331c243510cad757af6fe4670aca8d9ee  bin/aowli-interp
7e4f137b7cedc55863c2026cc31a9445ebe8139a5ce7bcab6b4b914bff5179af  bin/aowli-dbg
EOF
```

## VirusTotal

Look up each build by hash:

- aowli-interp — <https://www.virustotal.com/gui/file/e1f46255092952fcd7f0caaad565907331c243510cad757af6fe4670aca8d9ee>
- aowli-dbg — <https://www.virustotal.com/gui/file/7e4f137b7cedc55863c2026cc31a9445ebe8139a5ce7bcab6b4b914bff5179af>

(Scan results populate once a binary is submitted to VirusTotal.)
