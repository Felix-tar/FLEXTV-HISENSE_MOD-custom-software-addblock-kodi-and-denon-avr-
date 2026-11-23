# Build, integrate and verify Flex TV

The reference sequence keeps every module independently testable. Record the
firmware, the exact component snapshot, the files installed, a checksum for each
artifact, and whether a check was performed on the host or on the TV.

## 1. Prepare on the host

1. Confirm the target model and firmware and arrange an existing authorized
   development session for TV steps. [Hardware and access](hardware-and-access.md)
2. Verify the bundled component files from this repository's root.

   ```sh
   sha256sum -c PROJECT-SHA256SUMS
   ```

3. In `projects/hisense-vidaa-root-mod`, stage the launcher and run the Node
   simulations using the commands in its README. Record the staging files and
   console/HDMI simulation results. [Launcher host steps](../projects/hisense-vidaa-root-mod/README.md#reproduce-the-launcher)
4. In `projects/kodi-for-hisense-vidaa`, use a fresh Linux x86-64 build workspace.
   The build record pins Kodi 21.3-Omega at `a3a448d26b8d560a65655dab2cd122994dc4e146`
   and Zig 0.16.0 for `arm-linux-gnueabi.2.18` with `softfp`. The configuration
   uses DirectFB and GLES 2. Prepare the four matching firmware library inputs
   in a private sysroot as documented. Verify source checksums and inspect the
   ELF and resulting `kodi-tv.tar.gz`. [Kodi build guide](../projects/kodi-for-hisense-vidaa/docs/01-build.md)
5. In `projects/denon-avr-integration`, verify `prebuilt/SHA256SUMS` or rebuild
   the bridge with Zig 0.16.0 for `arm-linux-musleabi`. Run the Node simulations
   before configuring an actual receiver. [Denon host validation](../projects/denon-avr-integration/docs/01-architecture.md#offline-and-hardware-validation)

## 2. Install one module at a time

Before each change, use the existing authorized access to establish the baseline.
Record free space, the currently active app and normal switching behavior. Save
the stock launcher and existing application data separately. Verify a copy off the
TV and keep it available for recovery.

| Order | App scope | Acceptance check and rollback reference |
| --- | --- | --- |
| 1. Launcher | Stage and install the Flex overlay on the matching stock launcher | Confirm Home, live TV, a stock app and HDMI. Keep the original entry point and backup. [Launcher install and rollback](../projects/hisense-vidaa-root-mod/docs/03-launcher-and-recovery.md) |
| 2. Kodi | Transfer the verified local Kodi archive. Keep runtime and user-state backups. Verify direct start, status and stop before configuring launcher handoff | Verify the selected player and app release on exit. [Kodi install](../projects/kodi-for-hisense-vidaa/docs/01-build.md#4-install-and-activate-the-hardware-player) · [Kodi recovery](../projects/kodi-for-hisense-vidaa/docs/06-update-modify-and-troubleshoot.md) |
| 3. Denon | Install the bridge and start script. Set the receiver IPv4 device-locally with `FLEX_DENON_HOST`. Add launcher controls through the Flex integration | Read receiver status first. Confirm TV HDMI and ARC separately from AVR MAIN. [Denon setup and removal](../projects/denon-avr-integration/docs/01-architecture.md#build-and-install) |

The bridge can be built and tested on its own. The receiver tiles and switching
panel use the Flex launcher. Kodi direct startup and playback can be checked
before adding the Flex tile and watcher handoff.

## 3. Verify playback and switching

1. Use clear clips with known frame rates and keep one native media owner at a
   time. Retain the separate outputs for the USB/file player, the HTTP DMP
   fallback and the standalone ES client. [Clear-stream decoder lab](../projects/hisense-vidaa-root-mod/docs/02-hardware-decoder.md#reproduce-the-cadence-measurement)
2. In the reference tests the DMP film clips used the 59.94 Hz family.
   A native Netflix film measured roughly 23.980 internal IRQ/s and the standalone
   clear 23.976p client measured roughly 23.970 IRQ/s. The exact 24p sample gave
   about 23.998 IRQ/s over 120 seconds. The observations concern internal video
   interrupts. For a new build record the selected route, duration, frame-rate
   input and IRQ delta. Keep separate results for physical panel timing.
   [Native profiles and evidence](../projects/hisense-vidaa-root-mod/docs/07-player-profiles-and-cadence.md)
3. In Kodi confirm path selection from its own logs. Test clear DMP and ES clips
   and the documented software fallback for supported material. Verify audio,
   ARC and behavior when playback, the screensaver, or a stock app previously
   held the player. [Kodi frame rate](../projects/kodi-for-hisense-vidaa/docs/03-frame-rate-and-video.md) ·
   [Kodi audio](../projects/kodi-for-hisense-vidaa/docs/04-audio.md)
4. With the operator present, confirm receiver MAIN, the selected TV connector,
   HDMI picture lock and audio return as independent checks. Exercise one timeout
   or cancellation in the provided host simulations before on-device testing.
   [Denon state machine and tests](../projects/denon-avr-integration/docs/02-bugs-fixes-and-lessons.md)
5. Save a short result table with stock behavior, mod behavior, expected output,
   measured output and a recovery action per module. Restore the last verified
   app state if a check fails and reproduce with one change at a time.

## Release contents

| Data | Location |
| --- | --- |
| Launcher source, assets and tests | [Hisense mods](../projects/hisense-vidaa-root-mod/mods/) and [tools](../projects/hisense-vidaa-root-mod/tools/) |
| Decoder reference probe | [Probe source](../projects/hisense-vidaa-root-mod/tools/native-es/flex_es_probe.c) and [ARM probe](../projects/hisense-vidaa-root-mod/prebuilt/README.md) |
| Kodi port sources and runtime scripts | [Overlay and build scripts](../projects/kodi-for-hisense-vidaa/source/kodi-port/) and [native helpers](../projects/kodi-for-hisense-vidaa/source/native/) |
| Kodi checked ARM artifacts | `flex_es_helper.bin` and `flex_app_close` in [Kodi prebuilt](../projects/kodi-for-hisense-vidaa/prebuilt/) |
| Full Kodi runtime artifact | `kodi-tv.tar.gz` generated locally by the [pinned build](../projects/kodi-for-hisense-vidaa/docs/01-build.md#3-build-and-package) |
| Denon implementation and ARM bridge | [Bridge and UI source](../projects/denon-avr-integration/src/) and [ARM bridge](../projects/denon-avr-integration/prebuilt/) |

Keep the operator's stock backups, optional locale downloads, private firmware
link inputs, runtime settings and original testing media in the local workspace.
Use the published sample configuration or generated clear clips for public
reproduction reports.
