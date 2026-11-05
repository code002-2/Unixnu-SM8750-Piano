# Unixnu-SM8750-Piano — macOS build host

macOS-only build stages for the Xiaomi Pad 8 Pro (`piano`, SM8750) UniXNU port.
The Linux host cannot finish this port: the community components call `hdiutil`/`diskutil`,
`llvm-objcopy` with Mach-O section removal, and brew-provided tools.

Workflows publish their results to **Releases**, because this repository is private.

## `kernel.yml`

Builds, on a `macos-26` runner:

| Step | Produces |
| --- | --- |
| `defconfig`, `status` | resolved profile/patch order (uploaded as `status.json`) |
| `ipsw fetch` | `kernelcache.decompressed`, `DeviceTree`, `StaticTrustCache` from iPad7,1 17.7.11 (21H461) |
| `build kernel` | `kernel.macho` + ADT sidecar (prelink of the source kernel with the original kernelcache) |
| `build package` | `UniXNU.efi` and its UEFI drivers |

Inputs checked in here:

| Path | What |
| --- | --- |
| `inputs/piano-live.dtb` | the device tree dumped from the running tablet (`/sys/firmware/fdt`, 1,111,663 bytes, model "Qualcomm Technologies, Inc. Piano based on SM8750") |

Repositories the workflow clones (all public):

| Repository | Role |
| --- | --- |
| `IssacL04/UniXNU_Enc3l` | UniXNU main checkout with the community component catalog (53 components). The upstream `UniXNU/UniXNU` does not carry them. |
| `xiaomi-pad-8-pro-development/android_device_xiaomi_piano-unixnu` | device repository |
| `modules/Pagani` submodule of the device repository | `IssacL04/UniXNU-Pagani-Drivers`; passed with `--local-drivers/--local-kits` because `os:patches/xnu/pagani-series` resolves `component:NativePrelink/patches/...` to the repository root for git-fetched components |

Set the platform seed with the workflow input `seed` (default `0x0123456789abcdef`, the bring-up
value the upstream ports document). The same value has to be passed to `build payload` later.
