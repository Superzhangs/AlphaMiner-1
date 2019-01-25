# AlphaMiner

### Linux implementation of a linux external AwesomeMiner-compatible profit switching miner

## Prepare a minimal Ubuntu 18.04 desktop for mining on NVIDIA

### Update the Apt repository and upgrade the distribution before starting
```
sudo dpkg --add-architecture i386
sudo apt-get update && sudo apt-get dist-upgrade
```

### Install some basic workspace tools
```
sudo apt install build-essential libc6:i386 git wget curl net-tools
```

### (OPTIONAL) Configure remote access
```
sudo apt-get install openssh-server  # and tightvncserver for noobs
```

### Installing proprietary NVIDIA drivers 
#### Download the latest official NVIDIA drivers
##### http://www.nvidia.com/Download/index.aspx
```
cd ~
wget https://us.download.nvidia.com/XFree86/Linux-x86_64/415.13/NVIDIA-Linux-x86_64-415.13.run
```

#### Disable the nouveu driver, and reboot
```
sudo bash -c "echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf"
sudo bash -c "echo options nouveau modeset=0 >> /etc/modprobe.d/blacklist-nvidia-nouveau.conf"
sudo update-initramfs -u
sudo reboot
```

#### Shutdown the display manager and turn off any running X servers by changing to console mode
```
sudo telinit 3
```
#### NVIDIA driver installation
##### Install for systems with Secure Boot disabled in UEFI BIOS, or if not using UEFI
```
sudo bash NVIDIA-Linux-x86_64-415.13.run
```
Choose "No" for signing the kernel module

##### Install for systems with Secure Boot enabled in UEFI BIOS.
```
sudo bash NVIDIA-Linux-x86_64-415.13.run
```
"Continue Installation" -> "Sign the kernel module" -> "Generate a new key pair" -> "No" (don't delete the key

Take note of the cert file name and path, ie: `/usr/share/nvidia/nvidia-modsign-crt-65E0FA91.der`.
press "OK"

Take note of the private key filename and path, ie: `/usr/share/nvidia/nvidia-modsign-key-65E0FA91.key`.
"Install signed kernel module" -> "OK" -> "Install and overwrite existing files" -> "Yes" -> "OK".

###### Import your new public cert to the trusted kernel keystore
```
sudo mokutil --import /usr/share/nvidia/nvidia-modsign-crt-65E0FA91.der
```

###### reboot and when promted by the UEFI BIOS to install the new key, chose "Yes" or "OK".
```
sudo reboot

```

### Testing that it all worked as expected
#### Run the NVIDIA SMI tool to query a list of available GPUs
```
nvidia-smi
```
##### the output should resemble the following:
```
Thu Jan 24 18:59:30 2019
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 415.13       Driver Version: 415.13       CUDA Version: 10.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce GTX 1080    Off  | 00000000:02:00.0 Off |                  N/A |
| 37%   26C    P8     9W / 180W |     40MiB /  8119MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   1  GeForce GTX 1080    Off  | 00000000:03:00.0 Off |                  N/A |
| 37%   29C    P8     9W / 180W |      2MiB /  8119MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   2  GeForce GTX 1080    Off  | 00000000:04:00.0 Off |                  N/A |
| 24%   30C    P8     6W / 180W |      2MiB /  8119MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   3  GeForce GTX 107...  Off  | 00000000:05:00.0 Off |                  N/A |
| 27%   28C    P8     5W / 180W |      2MiB /  8119MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   4  GeForce GTX 1080    Off  | 00000000:08:00.0 Off |                  N/A |
|  0%   29C    P8     8W / 215W |      2MiB /  8119MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   5  GeForce GTX 1080    Off  | 00000000:0A:00.0 Off |                  N/A |
|  0%   32C    P8     7W / 200W |      2MiB /  8119MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   6  GeForce GTX 107...  Off  | 00000000:0B:00.0 Off |                  N/A |
| 27%   30C    P8     5W / 180W |      2MiB /  8119MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   7  GeForce GTX 1080    Off  | 00000000:0C:00.0 Off |                  N/A |
| 37%   28C    P8     9W / 180W |      2MiB /  8119MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|    0       908      G   /usr/lib/xorg/Xorg                            32MiB |
|    0      1353      G   /usr/bin/gnome-shell                           6MiB |
+-----------------------------------------------------------------------------+
```
