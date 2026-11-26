# sonic-libmlx
Molex SAI/OTAI and HAL libraries

Each directory contains debian packages needed to build SONiC sonic-ot-molex platform

## Branch ocs-320 (OCS320 / OCS-8RU vendor debs, hal `platform`)

For builds of sonic-buildimage-molex `ocs-hongruz` with `MOLEX_DEV_TYPE=ocs-320` (OCS320, platform
`x86_64-ocs-molex_ocs-8ru-r0`, hwsku `OCS-8RU`). The 544 / QOCS144 debs are on branch `ocs-hongruz`. Not a release branch.

All five debs are from sonic-mlx-debs `ocs-hongruz` 2539466 (2026-11-25) with shasta `carmel-beta-sonic` 33b85b88 at its pins
(mgmt-lib f6fecbc6, thirdparty c0abbdd4 / camera-lib fa153583, cli 52709301, util-lib 16e2e780, util-lib-cpp aa1a6ca0,
dsc-lib 6b7dbfc4, cfg fae6c759, debian 50591494) **except hal, which is `platform` 428a6467** (the OCS320 hal, by design),
built with `sonic-mlx-debs-build.sh ocs-molex OCS-8RU all` against the SAI headers of sonic-buildimage-molex ocs-hongruz.

| deb | what |
|---|---|
| `libhal-mlx-ocs-molex-1.1.0` | hal `platform` 428a6467: OCS320 hal, `/etc/RegisterFile-OCS320.cfg`, local-bus FPGA DMA (`/dev/fpga_dma`) |
| `libhalplatformclient-mlx-1.1.0` | sonic-mlx-debs `platform/` HalPlatformApi client, built with the same hal |
| `libhal-mlx-model-1.1.0` | the OCS-8RU build packages the hal under both names, so this is byte-identical to `libhal-mlx-ocs-molex-1.1.0`; not used by the ocs-molex image |
| `libsai-ocs-mlx-1.1.0` | sonic-mlx-debs `sai/` 2539466: OCS-8RU drives through conn_manager (camera monitor on OCS320); the cross-connect write path refactor (DS2A-670: one bulk create / bulk remove, a single create or remove is a bulk call of one, attribute set NOT_SUPPORTED, the cross-connect holding a port keeps it and a new one on that port reads INVALID, no forced add); bundles libhalapi from hal `platform` and liboplkocs mgmt-lib f6fecbc6 (microsecond exposures, ocs-monitor-cv 0.13.0, OCS320 IL prediction from the K / A models, DC batch write for all 18 cards of an OCS320). It does NOT bundle libocs_monitor_cv.so any more |
| `allied-vision-1.0.4` | shasta `debian/allied-vision` 50591494 with libocs_monitor_cv.so 0.13.0 (md5 11d6dca5), which this liboplkocs needs |

The image must be built from sonic-buildimage-molex ocs-hongruz with the matching write-path refactor (SAI create-only
cross-connect attributes, orchagent move = remove + create) and `allied-vision.mk` choosing 1.0.4 for `ocs-320`.
Tested on OCS320 192.168.6.139 (serial L3095082) with the same sources (2026-11-25). Do not use these debs on a 544 / QOCS144.
