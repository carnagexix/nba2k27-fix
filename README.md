# PoorDS4 – NBA 2K27 Fix

A community fork of [ItsBlurf/PoorDS4](https://github.com/ItsBlurf/PoorDS4) that restores DualShock 4 digital button input in NBA 2K27 on a jailbroken PS5.

**Based on PoorDS4 v0.2.0 · Tested on PS5 firmware 11.60**

[Download the fixed release](https://github.com/carnagexix/nba2k27-fix/releases/tag/v0.2.0-nba2k27-fix) ·
[Usage](docs/USAGE.md) · [Build](docs/BUILD.md) ·
[Upstream firmware compatibility](docs/FIRMWARE_SUPPORT.md) ·
[Original project](https://github.com/ItsBlurf/PoorDS4)

## NBA 2K27 compatibility fix

In the original v0.2.0 build, both analog sticks and analog triggers worked in the tested setup, but face buttons and the D-pad did not respond. Diagnostics showed that digital button data reached the bridge callback.

This fork restores RC44-style connection-type handling in the standard and extended controller-information callbacks in `payload/wireless_ds4.c`: the fallback sets byte `0x0a` to zero, and valid native metadata is no longer unconditionally overwritten with one. Digital button mapping and analog input processing are unchanged. The combined metadata change restored digital input in the tested game; the exact downstream reason for the original filtering has not been isolated.

### Verified results

| Control | Result on PS5 11.60 |
| --- | --- |
| Left and right analog sticks | Working |
| L2 / R2 | Working |
| X / Circle / Square / Triangle | Working |
| D-pad | Working |
| L1 / R1 | Working |
| Options | Working |
| L3 / R3 | Not tested |

- All 24 host regression tests passed on Linux after the change.
- The three PS5 ELF payloads compiled successfully with PS5 Payload SDK v0.43 and Clang/LLVM 22.1.8.
- The copied game-code audit passed with no external-reference failures.
- The released runtime version is `0.2.0-digital-fix-test1`.

The fix was verified in NBA 2K27 on firmware 11.60. Other games, firmware versions, multiplayer, reconnect behavior and additional controller combinations have not been verified with this fork. The upstream compatibility documentation describes the original project, not additional testing of this fix.

## Download and installation

Download the compiled payloads from the [NBA 2K27 DS4 Fix v0.2.0 release](https://github.com/carnagexix/nba2k27-fix/releases/tag/v0.2.0-nba2k27-fix).

| File | Purpose |
| --- | --- |
| [PoorDS4.elf](https://github.com/carnagexix/nba2k27-fix/releases/download/v0.2.0-nba2k27-fix/PoorDS4.elf) | Start the controller bridge. |
| [PoorDS4-status.elf](https://github.com/carnagexix/nba2k27-fix/releases/download/v0.2.0-nba2k27-fix/PoorDS4-status.elf) | Export controller and bridge diagnostics. |
| [PoorDS4-stop.elf](https://github.com/carnagexix/nba2k27-fix/releases/download/v0.2.0-nba2k27-fix/PoorDS4-stop.elf) | Request normal shutdown of the bridge. |

1. Connect the DS4 to the PS5 and select the user profile used by the game.
2. Stop any existing PoorDS4 bridge before replacing it, or reboot and re-enable the jailbreak.
3. Load the fixed `PoorDS4.elf` through the console's ELF loader.
4. Launch NBA 2K27. The payload attaches automatically.

See [usage and troubleshooting](docs/USAGE.md) for runtime states and diagnostic files. Load only one controller bridge at a time.

## About PoorDS4

PoorDS4 forwards a DS4 connected to the console through the controller APIs used by the running application. It runs in the background and follows application changes. The original project is designed to preserve a separate input path for native DualSense controllers.

## Features

- DS4 buttons, sticks, analog triggers and controller data forwarded from Sony's console input source.
- Shared handling for state reads, queued reads and controller information.
- Per-controller source attribution and handle-generation recovery.
- Automatic application handoff and source rediscovery.
- Separate status and stop payloads for diagnostics and normal shutdown.
- Preserved first-failure reports and optional detailed controller logs.

Upstream testing and firmware support are documented in [FIRMWARE_SUPPORT.md](docs/FIRMWARE_SUPPORT.md) and the [upstream changelog](CHANGELOG.md). This fork does not claim to expand that support beyond the results above.

DS4 vibration does not work in this build. The DS4 also lacks the DualSense's
haptic actuators and adaptive-trigger hardware, so those effects are not
supported. Vibration support is a low priority for now, but ideas, technical
findings and pull requests are welcome.

## Development

The project uses [ps5-payload-sdk v0.43](https://github.com/ps5-payload-dev/sdk/releases/tag/v0.43). This fork also adjusts the compiler warning flags in `payload/Makefile` for Clang 22 compatibility.

On Linux, with the SDK and LLVM configured:

```sh
git clone https://github.com/carnagexix/nba2k27-fix.git
cd nba2k27-fix
python3 -m unittest discover -s tests -p 'test_*.py' -v
make -C payload release
```

Build instructions and host regression checks are documented separately:

- [Build from source](docs/BUILD.md)
- [Testing and validation scope](docs/TESTING.md)
- [Architecture and source guide](docs/ARCHITECTURE.md)
- [Contributing](CONTRIBUTING.md)

## Credits and license

The original PoorDS4 project is maintained by [ItsBlurf](https://github.com/ItsBlurf/PoorDS4). This fork contains the NBA 2K27 compatibility changes and does not replace the upstream project.

PoorDS4 builds on [Ghostcontrol](https://github.com/StonedModder/Ghostcontrol-PS5-USB-Controller-Patcher),
with the [PS5 Payload SDK](https://github.com/ps5-payload-dev/sdk) and
[kstuff-lite](https://github.com/EchoStretch/kstuff-lite).
See [NOTICE](NOTICE.md) for attribution. Licensed under [GPL-3.0-or-later](LICENSE).

## Donations & Support

The following donation links belong to the original PoorDS4 developer and are retained from the upstream README. Donations help with upstream development, additional controllers and testing equipment.

- **PayPal:** [paypal.me/nblurf](https://paypal.me/nblurf)
- **Bitcoin (BTC):** `1CW8JWSnWc5w7yKhYk7ixAFG487nU5Jrzc`
- **USDT (TRC20):** `TRcrCeonXzgyxjR4LGzo4JdfEHYGGqD3gX`

For issues specific to this fix, use the [fork issue tracker](https://github.com/carnagexix/nba2k27-fix/issues) if enabled; for general PoorDS4 issues, see the [upstream project](https://github.com/ItsBlurf/PoorDS4/issues).

## Disclaimer

PoorDS4 is an independent, open-source project for controller interoperability,
not affiliated with or endorsed by Sony. Product names and trademarks belong to
their respective owners and are used only to identify compatibility.

PoorDS4 does not supply games, Sony firmware or cryptographic keys and does not
endorse piracy or unauthorized access to copyrighted content. Use it responsibly
and in accordance with applicable law.

Provided **as is**, without warranty, to the extent permitted by law; see
[LICENSE](LICENSE), sections 15–17. Crashes or data loss are possible—back up
important saves. This notice does not alter the GPL license.
