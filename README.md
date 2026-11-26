# QOS Main GUI Shell

The Main profile's graphical shell as a standalone QOS Portable SDK app.
The boot entry and shell source come from QOS revision
`3d66631d8351f27ecbbdee0707038d4759a0bd86` (`programs/main.fpr` and
`programs/mods/guishell.fpr`). The local shell uses the SDK's `mods/`
import paths for shared QOS modules. SDK 0.5.0 supplies the compiler,
runtime, graphics host, and onboard desktop app archives.

On Apple Silicon with QOS Portable SDK 0.5.0 installed, run from this
repository:

```sh
qos build
mkdir -p data
qos disk create data/main.disk
qos run . --disk data/main.disk
```

Use a fresh filename for the disk creation step. Reuse the generated disk
on later launches to keep its contents. It provides the onboard browser,
clock, disk, editor, logs, and terminal apps. The project launch command
opens the shell built from this repository. A disk-only launch opens the
Main shell bundled with the SDK.

If the `qos` launcher cannot discover an SDK 0.5.0 installed in the
default manager store, invoke `~/.qos/0.5.0/bin/qos` directly for each
command.

## SDK 0.5.0 host workaround

The shipped macOS graphical host in SDK 0.5.0 can crash in `h_init` before
loading either this app or the SDK's bundled Main shell. QOS commit
`d69d587` fixes the host's decoding of immediate Bool values. Keep the SDK
compiler for the application archive and run it with a host rebuilt from
the fixed QOS source with the SDK's matching FP-RISC revision.

From this repository, with the QOS and FP-RISC source checkouts alongside it:

```sh
scratch=$(mktemp -d)
mkdir "$scratch/qos" "$scratch/fprisc"
git -C ../qos archive 3d66631 | tar -x -C "$scratch/qos"
git -C ../fprisc archive 151b3eebebebb7eacb66f9c4b893eb7332bfa757 |
  tar -x -C "$scratch/fprisc"
ln -s "$HOME/.qos/0.5.0/libexec/qos-portable/compiler/fpr" "$scratch/fprisc/fpr"
FPRISC_ROOT="$scratch/fprisc" make -C "$scratch/qos/qos" portable-gl
FPR_DISK="$PWD/data/main.disk" "$scratch/qos/qos/qosp-gl" --yes \
  "$PWD/build/qos-guishell.qa"
```

The Apple Silicon smoke test with this host reached the graphical home
screen, attached all six onboard archives, opened Clock, returned home,
and exited with storage flushed.
