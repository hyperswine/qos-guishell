# QOS Main GUI Shell

The Main profile's graphical shell as a standalone QOS Portable SDK app.
The boot entry and shell source come from QOS revision
`3d66631d8351f27ecbbdee0707038d4759a0bd86` (`programs/main.fpr` and
`programs/mods/guishell.fpr`). The local shell uses the SDK's `mods/`
import paths for shared QOS modules. SDK 0.6.1 supplies the compiler,
runtime, graphics host, and onboard desktop app archives.

On Apple Silicon, install the QOS Portable SDK 0.6.1 archive with
`qosmeta install --archive /path/to/qos-portable-0.6.1-macos-arm64.tar.gz`.
Keep its `SHA256SUMS` beside it, or pass the archive digest with `--sha256`.
This SDK uses Clang and `ld.lld` from your PATH.

From this repository:

```sh
qos build
mkdir -p data
qos disk create data/qos-guishell.disk
qos run
```

Create the disk once. Reuse it on later launches to keep its contents. The
Main disk provides the onboard browser, clock, disk, editor, logs, and
terminal apps. Use `qos run . --disk PATH` to select a different disk.
A disk-only launch opens the Main shell bundled with the SDK.

The SDK includes the host fix from QOS commit `d69d587` for the startup
crash when decoding an immediate Bool. The previous SDK 0.5.0 workaround
required rebuilding the graphical host separately.

Open this repository as its own VS Code workspace. Its workspace settings
clear any user-level `fpr.home` override so the FP-RISC extension can use
the SDK selected by `qos.toml` for diagnostics and module navigation.
