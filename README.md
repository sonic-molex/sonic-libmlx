# sonic-libmlx
Molex SAI/OTAI and HAL libraries

Each directory contains debian packages needed to build SONiC sonic-ot-molex platform

## Branch ocs-hongruz (personal build branch, CARMEL hal: 544x544 and ocs-13ru platforms)

Branched from `ocs544` (7172ad9) for builds of sonic-buildimage-molex `ocs-hongruz` with
`MOLEX_DEV_TYPE=ocs-hongruz`. Not a release branch.

| deb | built from |
|---|---|
| `libhal-mlx-ocs-molex-1.1.0` | shasta `carmel-beta-sonic` c56bba49 (hal 64da919d = carmel-beta-sonic rebased onto 544-dma-fea-dev e76b3b9c: the DMA / master-SPI DC driver as rebased by its developer, CPcieBusDriverMmap for carmel, QOCS144 product support (DS2A-634), HAL read cache removed, LED driver CORE-1088, under our halcli ocs-monitor commands, per-poll slot match and DC mfg dumps at debug, halcli naming a value the sai.profile sets; dsc-lib c59b00fa (DS2A-634), util-lib 79c1e4e6, mgmt-lib 17f2bbe, thirdparty b7308155), sonic-mlx-debs ocs-hongruz 533bd37 (libhalapi maps the ocs-13ru platform to the CARMEL parameter range, DS2A-633), `sonic-mlx-debs-build.sh ocs-molex CARMEL all` (`-D__CARMEL__`; drives the local-bus FPGA 544 (10ee:7011) only - an XDMA 544 (10ee:9031) cannot drive its DC cards with it; packaged /etc/RegisterFile is the local-bus one, the image's hwsku RegisterFile overrides it) |
| `libhalplatformclient-mlx-1.1.0` | sonic-mlx-debs `platform/` (ocs-hongruz 533bd37: HalPlatformApi with Chassis/Module GetMfgJson (06080dc); from ocs-dev: fan-card hot-plug fan speed (CORE-1107), FPC and SCC LEDs green after startup, temporary (CORE-1098), reversed DC card and PSU panel silkscreen remapped (CORE-1063); `sonic-mlx-debs-build.sh ocs-molex CARMEL all`) |
| `libsai-ocs-mlx-1.1.0` | sonic-mlx-debs `sai/` (ocs-hongruz 533bd37, built and unit-tested with `build-libsai.sh` (131 tests): a port reads connected only while its cross-connect reads enabled (DS2A-675); drive-curve-missing is not reported on QOCS144, temporary (DS2A-678); a single create refuses what a bulk create refuses (CORE-1061); a pulled DC card's ports read FAILED and an open ejector is a pull (DS2A-655); 99 dB for a cross-connect that is not enabled (DS2A-646); the calibrated IL source (DS2A-663); no SUCCESS for a freed bulk-create entry (DS2A-664); from ocs-dev: CORE-1063, CORE-1051, CORE-1035/1036/1042, CORE-1076; and before: OcsMonMemsStrategy on conn_manager's staged API; the DC write path follows sai.profile (DS2A-629); NR1301-QOCS144-01 is a CARMEL product (DS2A-633); camera-monitor IL source and correction-proposed alarm; open-loop correction queue; Lu's OCS switching time improvement; OCS544 batch DC write), linked against and bundling the libhalapi / liboplkocs (mgmt-lib 17f2bbe: an open ejector handle is out of service like a pull, mirror_live (DS2A-655); 34d9543: the disk floor (DS2A-658); 62b1f05: il-propose, the monitor proposes nothing for now (DS2A-657); b7e4c5d: DC card hot-plug (DS2A-655); b31864f: the mirror score, a weighted form of the consistency check; 997deff: the monitor settings and schedule of 2026-10-19 (exposure maximum from sai.profile, rounds start to start, walk recalibration retries, no recalibration after a walk); 1eb67bb: the round drift gate per camera on signed offsets (DS2A-632); ba68bbe: the RUNPATH also names /usr/local/lib/ocs-monitor, so halcli loads liboplkocs without syncd's LD_LIBRARY_PATH; 0153fb4: NR1301-QOCS144-01 is a known OCS; b47f708: the owner picks the DC write path; a37adb8 and before: ocs_monitor.conf loader, IL rule over valid records with the two-layer rise check, per-panel exposure calibration) above |
| `allied-vision-1.0.3` | shasta `debian/allied-vision` recipe, with `libocs_exposure.so` 0.5.0 from thirdparty b7308155 (md5 9a6e8a99), which the new liboplkocs links |

All three debs were built from sonic-mlx-debs ocs-hongruz 533bd37 (2026-10-30), for sonic-buildimage-molex ocs-hongruz d8c439201. The release QOCS144-2026-10-14-Release used the c624f53 debs of 1abe6d8. The allied-vision deb is the one of images 11-14, byte for byte.

An image build of sonic-buildimage-molex `ocs-hongruz` also needs `INCLUDE_EXTERNAL_PATCHES=y` (the platform kernel config in
`platform/ocs-molex/non-upstream-patches`, e.g. `CONFIG_IGC=m`); its default is `n`.

The image recipe must ask for `allied-vision-1.0.3` (`platform/ocs-molex/allied-vision.mk`); the
libsai here does not load with 1.0.2. `libhal-mlx-model-1.1.0` is the unchanged `ocs544` copy (not
used by the ocs-molex image). These debs are CARMEL-only: do not use them on an OCS-2RU.
