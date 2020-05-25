# pynq-on-zedboard
http://www.pynq.io/
- [x] Step 1 - Play with an existing SD image for the Pynq-Z1
- [ ] Step 2 - Recompile an image for the Pynq-Z1
- [ ] Step 3 - Play with an existing SD image for the Zedboard
- [ ] Step 4 - Recompile an image for the Zedboard

## Step 1 - Play with an existing SD image for the Pynq-Z1
- Tutorial: https://pynq.readthedocs.io/en/latest/getting_started/pynq_z1_setup.html
- SD image: https://pynq.readthedocs.io/en/v2.5/getting_started/pynq_image.html

## Step 2 - Recompile an image for the Pynq-Z1
The idea is to verify that the workflow is OK for an existing board.
Based on https://pynq.readthedocs.io/en/latest/pynq_sd_card.html, we have two options :
- Ubuntu 16 LTS, Xilinx 2018.2 tools for a [Pynq v2.3](https://github.com/Xilinx/PYNQ/tree/image_v2.3) image
- Ubuntu 18 LTS, Xilinx 2019.1 tools for a [Pynq v2.5](https://github.com/Xilinx/PYNQ/tree/image_v2.5) image

### Ubuntu 18 LTS + 2019.1 tools
```bash
# Fork adds the Docker repo which was missing
git clone https://github.com/pcotret/PYNQ
cd PYNQ
git checkout image_v2.5
cd sdbuild
# Compile for the existing Pynq-Z1
make BOARDS=Pynq-Z1
```
#### FAQ
```bash
Get:1 http://ports.ubuntu.com/ubuntu-ports bionic InRelease [242 kB]
Err:1 http://ports.ubuntu.com/ubuntu-ports bionic InRelease
  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY the_key
```
```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys the_key
```

## Step 3 - Play with an existing SD image for the Zedboard
- Some are available there: https://onedrive.live.com/?authkey=%21AGHtA0NKDouVp6o&id=90CE8BEE59129AD0%2133068&cid=90CE8BEE59129AD0
(taken from the [official forum](https://discuss.pynq.io/t/3rd-party-images-for-zynq-boards/431))

## Step 4 - Recompile an image for the Zedboard
Equivalent to step 2 with a custom board
