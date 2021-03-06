# pynq-on-zedboard
> Gathering some notes/thoughts on my journey towards a PYNQ image for the Zedboard which is not supported by default.
>
> - Main ressource: https://pynq.readthedocs.io/en/latest/
>
> - Homemade generated images available here: https://drive.google.com/drive/folders/11lJLcf2pXwInf4wrj_IYowJZJRFcz-u2?usp=sharing

- [x] Step 1 - Play with an existing SD image for the Pynq-Z1
- [x] Step 2 - Recompile an image for the Pynq-Z1
- [x] Step 3 - Play with an existing SD image for the Zedboard
- [ ] Step 4 - Recompile an image for the Zedboard

## Step 1 - Play with an existing SD image for the Pynq-Z1
- Tutorial: https://pynq.readthedocs.io/en/latest/getting_started/pynq_z1_setup.html
- SD image: https://github.com/Xilinx/PYNQ/releases/tag/v2.5

## Step 2 - Recompile an image for the Pynq-Z1
The idea is to verify that the workflow is OK for an existing board.
Based on https://pynq.readthedocs.io/en/latest/pynq_sd_card.html, we have two options :
- Ubuntu 16 LTS, Xilinx 2018.2 tools for a [Pynq v2.3](https://github.com/Xilinx/PYNQ/tree/image_v2.3) image
- Ubuntu 18 LTS, Xilinx 2019.1 tools for a [Pynq v2.5.2](https://github.com/Xilinx/PYNQ/tree/image_v2.5.2) image (`image_v2.5` is a bit buggy, see the Crosstool issue below)

### Ubuntu 18 LTS + 2019.1 tools

#### Minimal instructions to generate an image

Requirements : 

- A board-agnostic image (see http://www.pynq.io/board.html/)
- A user with `sudo` rights

```bash
git clone https://github.com/pcotret/PYNQ
cd PYNQ
git checkout image_v2.5.2
cd sdbuild/scripts
./setup_host.sh
```

Reboot and :

```bash
cd PYNQ/sdbuild
make PREBUILT=<agnostic image path>
```

On a decent VM (4 cores, 4 GB RAM), the whole process took ~2hrs.

![demo](./img/pynq-z1-demo.png)

#### Issues with the building script...

- In order to optimize the overall compilation time for only one board, just delete other board directories in `PYNQ/boards`. This deletion must be pushed in the [PYNQ repository](https://github.com/pcotret/PYNQ/commit/948c9a27b796b14ee4b1812d500564ddfc218934). Otherwise, [build.sh](https://github.com/pcotret/PYNQ/blob/image_v2.5.2/build.sh) will build bitstreams for all boards available.
```bash
# build all bitstreams since they will be included in the sdist
cd $script_dir/boards
boards=`find . -maxdepth 2 -name '*.spec' -printf '%h\n' | cut -f2 -d"/"`
for b in $boards ; do
	build_bitstreams "$script_dir/boards" "$b"
done
```
- Still some errors in the script for building default overlays, microblaze bsp's and binaries...
> ./build.sh: line 70: cd: bsp_iop_rpi_mb/iop_rpi_mb: No such file or directory :cry:
> xsdk -batch -source build_xsdk.tcl ../Pynq-Z1/base/base.hdf hw_base
>
> Starting xsdk. This could take few seconds... done
>
> INFO: [Hsi 55-1698] elapsed time for repository loading 0 seconds
>
> Creating new BSP bsp_iop_arduino_mb ...
>
> /opt/Xilinx/SDK/2019.1/gnu/microblaze/lin
>
> BSP project 'bsp_iop_arduino_mb' created successfully.
>
> /opt/Xilinx/SDK/2019.1/gnu/microblaze/lin
>
> Creating new BSP bsp_iop_pmoda_mb ...
>
> /opt/Xilinx/SDK/2019.1/gnu/microblaze/lin
>
> BSP project 'bsp_iop_pmoda_mb' created successfully.
>
> /opt/Xilinx/SDK/2019.1/gnu/microblaze/lin
>
> Creating new BSP bsp_iop_pmodb_mb ...
>
> /opt/Xilinx/SDK/2019.1/gnu/microblaze/lin
>
> BSP project 'bsp_iop_pmodb_mb' created successfully.
>
> /opt/Xilinx/SDK/2019.1/gnu/microblaze/lin
>
> Building '/bsp_iop_arduino_mb'

Yes, `bsp_iop_rpi_mb` BSP project isn't generated...

Let's look at the difference between `PYNQ/boards/Pynq-Z1/base/base.py` and `PYNQ/boards/Pynq-Z2/base/base.py`. Those files define the base overlay for both boards:

![diff](./img/diff.png)

As you can see, Pynq-Z2 has more peripherals (look at `iop ***`, especially the Raspberry-Pi interface available **only** in the Pynq-Z2).

![rpi](./img/z2.png)

And, unfortunately, the `build.sh` script will use Pynq-Z2 overlay for all boards instead of having something more "board-dependent". Two solutions:

- [x] Modify the `build.sh` script to fit only the Pynq-Z1 in my [fork](https://github.com/pcotret/PYNQ).

- [ ] Take some time to make a clever `build.sh` which would generate BSPs for peripherals of a given board (would be a great PR in the main repo).

#### Public key not verified
```bash
Get:1 http://ports.ubuntu.com/ubuntu-ports bionic InRelease [242 kB]
Err:1 http://ports.ubuntu.com/ubuntu-ports bionic InRelease
  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY the_key
```
```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys the_key
```
#### Issue with `crosstool-ng` on the `image_v2.5` branch
- http://crosstool-ng.github.io/docs/known-issues/
- https://discuss.pynq.io/t/pynq-2-5-ct-ng-build-error/1267


## Step 3 - Play with an existing SD image for the Zedboard
- Some are available there: https://onedrive.live.com/?authkey=%21AGHtA0NKDouVp6o&id=90CE8BEE59129AD0%2133068&cid=90CE8BEE59129AD0
(taken from the [official forum](https://discuss.pynq.io/t/3rd-party-images-for-zynq-boards/431))

## Step 4 - Recompile an image for the Zedboard
Equivalent to step 2 with a custom board. The first step is to be able to generate a "minimal" `base` overlay for the Pynq-Z1 before migrating it to the Zedboard (only a matter of pins).
- [x] Removing RGB LEDs
- [x] Removing audio support
- [ ] Removing HDMI support
- [ ] Removing ChipKit support
- [ ] Removing Arduino support
