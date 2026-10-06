# sonic-libmlx
Molex SAI/OTAI and HAL libraries

Each directory contains debian packages needed to build SONiC sonic-ot-molex platform

## Branch ocs-hongruz (personal build branch, CARMEL / 544x544)

Branched from `ocs544` (7172ad9) for builds of sonic-buildimage-molex `ocs-hongruz` with
`MOLEX_DEV_TYPE=ocs-hongruz`. Not a release branch.

| deb | built from |
|---|---|
| `libhal-mlx-ocs-molex-1.1.0` | shasta `carmel-beta-sonic` 166840dd (hal f51894ac = DMA hal 13b98d28 + halcli ocs-monitor correction and config commands; util-lib 79c1e4e6, mgmt-lib ed29bb13), sonic-mlx-debs ocs-hongruz c879add, `sonic-mlx-debs-build.sh ocs-molex CARMEL all` (`-D__CARMEL__`; supports both 544 FPGA variants: XDMA and local-bus + DC DMA; packaged /etc/RegisterFile is the local-bus one, the image's hwsku RegisterFile overrides it) |
| `libhalplatformclient-mlx-1.1.0` | sonic-mlx-debs `platform/` (ocs-hongruz c879add, `sonic-mlx-debs-build.sh ocs-molex CARMEL all`) |
| `libsai-ocs-mlx-1.1.0` | sonic-mlx-debs `sai/` (ocs-hongruz c879add: OcsMonMemsStrategy on conn_manager's staged API; Lu's OCS switching time improvement; OCS544 batch (vol-all) DC write), linked against the libhalapi / liboplkocs (ed29bb13) above |
| `allied-vision-1.0.3` | shasta `debian/allied-vision` recipe; adds `libocs_exposure.so`, which the new liboplkocs links |

The image recipe must ask for `allied-vision-1.0.3` (`platform/ocs-molex/allied-vision.mk`); the
libsai here does not load with 1.0.2. `libhal-mlx-model-1.1.0` is the unchanged `ocs544` copy (not
used by the ocs-molex image). These debs are CARMEL-only: do not use them on an OCS-2RU.
