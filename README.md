# pynq-on-zedboard
http://www.pynq.io/
- [x] Step 1 - Play with an existing SD image for the Pynq-Z1
- [ ] Step 2 - Recompile an image for the Pynq-Z1
- [ ] Step 3 - Play with an existing SD image for the Zedboard
- [ ] Step 4 - Recompile an image for the Zedboard

## Step 1 - Play with an existing SD image for the Pynq-Z1
- Tutorial: https://pynq.readthedocs.io/en/latest/getting_started/pynq_z1_setup.html
- SD image: https://github.com/Xilinx/PYNQ/releases/tag/v2.5

## Step 2 - Recompile an image for the Pynq-Z1
The idea is to verify that the workflow is OK for an existing board.
Based on https://pynq.readthedocs.io/en/latest/pynq_sd_card.html, we have two options :
- Ubuntu 16 LTS, Xilinx 2018.2 tools for a [Pynq v2.3](https://github.com/Xilinx/PYNQ/tree/image_v2.3) image
- Ubuntu 18 LTS, Xilinx 2019.1 tools for a [Pynq v2.5.2](https://github.com/Xilinx/PYNQ/tree/image_v2.5.2) image

### Ubuntu 18 LTS + 2019.1 tools
```bash
git clone https://github.com/Xilinx/PYNQ
cd PYNQ
git checkout image_v2.5.2
cd sdbuild
# Compile for the existing Pynq-Z1
make BOARDS=Pynq-Z1
```
- In order to optimize the overall compilation time for only one board, just delete other board directories in `PYNQ/boards`. This deletion must be pushed in the [PYNQ repository](https://github.com/pcotret/PYNQ/commit/948c9a27b796b14ee4b1812d500564ddfc218934). Otherwise, [build.sh](https://github.com/pcotret/PYNQ/blob/image_v2.5.2/build.sh) will build bitstreams for all boards available.
```bash
# build all bitstreams since they will be included in the sdist
cd $script_dir/boards
boards=`find . -maxdepth 2 -name '*.spec' -printf '%h\n' | cut -f2 -d"/"`
for b in $boards ; do
	build_bitstreams "$script_dir/boards" "$b"
done
```
- Generation still looking for Pynq-Z2 files during HDF generation... as there are some dependencies with Pynq-Z2 in the [`build.sh`](https://github.com/pcotret/PYNQ/blob/image_v2.5.2/build.sh) script :cry:
```bash
# build all the microblaze bsp's and binaries using Pynq-Z2's hdf
```

#### Public key not verified
```bash
Get:1 http://ports.ubuntu.com/ubuntu-ports bionic InRelease [242 kB]
Err:1 http://ports.ubuntu.com/ubuntu-ports bionic InRelease
  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY the_key
```
```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys the_key
```
#### Issue with `crosstool-ng`
- http://crosstool-ng.github.io/docs/known-issues/
- https://discuss.pynq.io/t/pynq-2-5-ct-ng-build-error/1267


## Step 3 - Play with an existing SD image for the Zedboard
- Some are available there: https://onedrive.live.com/?authkey=%21AGHtA0NKDouVp6o&id=90CE8BEE59129AD0%2133068&cid=90CE8BEE59129AD0
(taken from the [official forum](https://discuss.pynq.io/t/3rd-party-images-for-zynq-boards/431))

## Step 4 - Recompile an image for the Zedboard
Equivalent to step 2 with a custom board
