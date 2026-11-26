# sonic-libmlx
Molex SAI/OTAI and HAL libraries

Each directory contains debian packages needed to build SONiC sonic-ot-molex platform

## Branch ocs-hongruz (personal build branch, CARMEL hal: 544x544 and ocs-13ru platforms)

Branched from `ocs544` (7172ad9) for builds of sonic-buildimage-molex `ocs-hongruz` with
`MOLEX_DEV_TYPE=ocs-hongruz`. Not a release branch.

| deb | built from |
|---|---|
| `libhal-mlx-ocs-molex-1.1.0` | shasta `carmel-beta-sonic` 33b85b88 (hal 3816308e: halcli ocs-monitor exposures in microseconds; mgmt-lib f6fecbc6; dsc-lib 6b7dbfc4, util-lib 16e2e780, util-lib-cpp aa1a6ca0, cfg fae6c759, thirdparty c0abbdd4), sonic-mlx-debs `ocs-hongruz-544` bf3cb0f, `sonic-mlx-debs-build.sh ocs-molex CARMEL all` (CARMEL DMA build) |
| `libhalplatformclient-mlx-1.1.0` | sonic-mlx-debs `platform/` of `ocs-hongruz-544` bf3cb0f (= ocs-hongruz aa13cd0's hal packaging) |
| `libsai-ocs-mlx-1.1.0` | sonic-mlx-debs `sai/` of `ocs-hongruz-544` bf3cb0f (= ocs-hongruz 2539466's sai/: DS2A-670 one bulk write path, the holder keeps its port, an INVALID row for a create the switch cannot carry out; OCS-8RU conn_manager backend), against SAI headers 382df82 (sonic-buildimage-molex ocs-hongruz); bundles libhalapi / liboplkutil / liboplkocs (mgmt-lib f6fecbc6: monitor exposures in microseconds, needs ocs-monitor-cv 0.13.0). `libocs_monitor_cv.so` is NOT in this deb |
| `allied-vision-1.0.4` | shasta `debian/allied-vision` recipe 50591494; ships `/usr/local/lib/ocs-monitor/libocs_monitor_cv.so` 0.13.0 (md5 11d6dca5), which this liboplkocs needs |
| `libhal-mlx-model-1.1.0` | built with the set; byte-identical to the hal deb; not used by the ocs-molex image |
| `allied-vision-1.0.9` | kept from the previous set (89c7dd3) for older recipes only; it does NOT work with this liboplkocs |

Built 2026-11-26 from sonic-mlx-debs `ocs-hongruz-544` bf3cb0f: ocs-hongruz from fced378 on packages hal for hal `platform`
(OCS320) only, so the 544 / QOCS144 set is built from a branch that keeps aa13cd0's hal packaging and takes sai/ and the shasta pin
of 2539466. For sonic-buildimage-molex ocs-hongruz 6c75abb3e or later (allied-vision.mk asks for 1.0.4). Not yet tested on a
QOCS144 or 544 unit. The previous set (89c7dd3: sonic-mlx-debs aa13cd0, allied-vision 1.0.9) is in this branch's history.

An image build of sonic-buildimage-molex `ocs-hongruz` also needs `INCLUDE_EXTERNAL_PATCHES=y` (the platform kernel config in
`platform/ocs-molex/non-upstream-patches`, e.g. `CONFIG_IGC=m`); its default is `n`.

The image recipe must ask for `allied-vision-1.0.4` (`platform/ocs-molex/allied-vision.mk`, sonic-buildimage-molex
ocs-hongruz 6c75abb3e or later); the liboplkocs here does not load with 1.0.9, 1.0.3 or 1.0.2. These debs are CARMEL-only: do not use them on an OCS-2RU.
