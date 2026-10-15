# sonic-libmlx
Molex SAI/OTAI and HAL libraries

Each directory contains debian packages needed to build SONiC sonic-ot-molex platform

## Branch ocs-hongruz (personal build branch, CARMEL hal: 544x544 and ocs-13ru platforms)

Branched from `ocs544` (7172ad9) for builds of sonic-buildimage-molex `ocs-hongruz` with
`MOLEX_DEV_TYPE=ocs-hongruz`. Not a release branch.

| deb | built from |
|---|---|
| `libhal-mlx-ocs-molex-1.1.0` | shasta `carmel-beta-sonic` e02ab69b (hal e233becd = the DMA / master-SPI DC hal with the halcli ocs-monitor commands, per-poll slot match and DC mfg dumps at debug; util-lib 79c1e4e6, mgmt-lib 1eb67bb, thirdparty b7308155), sonic-mlx-debs ocs-hongruz c624f53 (libhalapi maps the ocs-13ru platform to the CARMEL parameter range, DS2A-633), `sonic-mlx-debs-build.sh ocs-molex CARMEL all` (`-D__CARMEL__`; drives the local-bus FPGA 544 (10ee:7011) only - an XDMA 544 (10ee:9031) cannot drive its DC cards with it; packaged /etc/RegisterFile is the local-bus one, the image's hwsku RegisterFile overrides it) |
| `libhalplatformclient-mlx-1.1.0` | sonic-mlx-debs `platform/` (ocs-hongruz c624f53: HalPlatformApi with Chassis/Module GetMfgJson, cherry-picked from ocs-carmel-dev 06080dc; `sonic-mlx-debs-build.sh ocs-molex CARMEL all`) |
| `libsai-ocs-mlx-1.1.0` | sonic-mlx-debs `sai/` (ocs-hongruz c624f53: OcsMonMemsStrategy on conn_manager's staged API; the DC write path follows sai.profile (DS2A-629); NR1301-QOCS144-01 is a CARMEL product (DS2A-633); camera-monitor IL source and correction-proposed alarm; open-loop correction queue; Lu's OCS switching time improvement; OCS544 batch DC write), linked against and bundling the libhalapi / liboplkocs (mgmt-lib 1eb67bb: the round drift gate per camera on signed offsets (DS2A-632); ba68bbe: the RUNPATH also names /usr/local/lib/ocs-monitor, so halcli loads liboplkocs without syncd's LD_LIBRARY_PATH; 0153fb4: NR1301-QOCS144-01 is a known OCS; b47f708: the owner picks the DC write path; a37adb8 and before: ocs_monitor.conf loader, IL rule over valid records with the two-layer rise check, per-panel exposure calibration) above |
| `allied-vision-1.0.3` | shasta `debian/allied-vision` recipe, with `libocs_exposure.so` 0.5.0 from thirdparty b7308155 (md5 9a6e8a99), which the new liboplkocs links |

The hal, platform-client and libsai debs were built from sonic-mlx-debs ocs-hongruz c624f53 (2026-10-14), for sonic-buildimage-molex ocs-hongruz f9858ce22 (release QOCS144-2026-10-14-Release). The allied-vision deb is the one of images 11-14, byte for byte.

An image build of sonic-buildimage-molex `ocs-hongruz` also needs `INCLUDE_EXTERNAL_PATCHES=y` (the platform kernel config in
`platform/ocs-molex/non-upstream-patches`, e.g. `CONFIG_IGC=m`); its default is `n`.

The image recipe must ask for `allied-vision-1.0.3` (`platform/ocs-molex/allied-vision.mk`); the
libsai here does not load with 1.0.2. `libhal-mlx-model-1.1.0` is the unchanged `ocs544` copy (not
used by the ocs-molex image). These debs are CARMEL-only: do not use them on an OCS-2RU.
