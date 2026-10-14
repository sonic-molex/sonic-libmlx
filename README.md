# sonic-libmlx
Molex SAI/OTAI and HAL libraries

Each directory contains debian packages needed to build SONiC sonic-ot-molex platform

## Branch ocs-hongruz (personal build branch, CARMEL hal: 544x544 and ocs-13ru platforms)

Branched from `ocs544` (7172ad9) for builds of sonic-buildimage-molex `ocs-hongruz` with
`MOLEX_DEV_TYPE=ocs-hongruz`. Not a release branch.

| deb | built from |
|---|---|
| `libhal-mlx-ocs-molex-1.1.0` | shasta `carmel-beta-sonic` 831323c0 (hal 4adebb1a = the DMA / master-SPI DC hal with the halcli ocs-monitor commands; util-lib 79c1e4e6, mgmt-lib b47f708, thirdparty b7308155), sonic-mlx-debs ocs-hongruz 980d538 (libhalapi maps the ocs-13ru platform to the CARMEL parameter range, DS2A-633), `sonic-mlx-debs-build.sh ocs-molex CARMEL all` (`-D__CARMEL__`; drives the local-bus FPGA 544 (10ee:7011) only - an XDMA 544 (10ee:9031) cannot drive its DC cards with it; packaged /etc/RegisterFile is the local-bus one, the image's hwsku RegisterFile overrides it) |
| `libhalplatformclient-mlx-1.1.0` | sonic-mlx-debs `platform/` (ocs-hongruz 980d538, `sonic-mlx-debs-build.sh ocs-molex CARMEL all`) |
| `libsai-ocs-mlx-1.1.0` | sonic-mlx-debs `sai/` (ocs-hongruz 980d538: OcsMonMemsStrategy on conn_manager's staged API; the DC write path follows sai.profile (DS2A-629); camera-monitor IL source and correction-proposed alarm; open-loop correction queue; Lu's OCS switching time improvement; OCS544 batch DC write), linked against and bundling the libhalapi / liboplkocs (mgmt-lib b47f708 (the owner picks the DC write path) on a37adb8: ocs_monitor.conf loader, IL rule over valid records with the two-layer rise check, per-panel exposure calibration, the round mean pixel drift gate) above |
| `allied-vision-1.0.3` | shasta `debian/allied-vision` recipe, with `libocs_exposure.so` 0.5.0 from thirdparty b7308155 (md5 9a6e8a99), which the new liboplkocs links |

The hal, platform-client and libsai debs were built from sonic-mlx-debs ocs-hongruz 980d538 (2026-10-14), for sonic-buildimage-molex ocs-hongruz e79f7bd33; no image has been built from exactly these yet. The allied-vision deb is the one of images 11-13, byte for byte.

The image recipe must ask for `allied-vision-1.0.3` (`platform/ocs-molex/allied-vision.mk`); the
libsai here does not load with 1.0.2. `libhal-mlx-model-1.1.0` is the unchanged `ocs544` copy (not
used by the ocs-molex image). These debs are CARMEL-only: do not use them on an OCS-2RU.
