Original repository: https://github.com/fastoe/RTL8812BU | README.md updated

# Realtek RTL8812BU Driver for Linux

[![Build Status](https://travis-ci.com/fastoe/RTL8812BU.svg?branch=master)](https://travis-ci.com/fastoe/RTL8812BU)

Driver for 802.11ac USB adapter with RTL8812BU chipset, only STA/Monitor mode is supported, no AP mode.

A few known wireless cards that use this driver include:
* [Fastoe AC1200 USB Wi-Fi Adapter](https://www.amazon.com/1200Mbps-ChromeBook-802-11ac-Compatible-Raspbian/dp/B081TGWCVB/ref=as_li_ss_tl?m=A9879GOT1YWJ2&marketplaceID=ATVPDKIKX0DER&qid=1581225299&s=merchant-items&sr=1-3&linkCode=ll1&tag=fastoe-20&linkId=5648949a51280f0323dd599dc27dbae4&language=en_US)
* Cudy WU1200 AC1200 High Gain USB Wi-Fi Adapter
* TP-Link Archer T3U
* TP-Link Archer T3U Plus
* TP-Link Archer T4U V3
* Linksys WUSB6400M
* Dlink DWA-181
* Dlink DWA-182

Currently tested with Linux kernel 4.12.14/4.15.0/5.3.0 on X86_64 platform **only**.

### For Raspberry Pi
* https://github.com/fastoe/RTL8812BU_for_Raspbian

## Set proxy when connected from VPN

```bash
export http_proxy="http://defra1c-proxy.emea.nsn-net.net:8080"
export https_proxy="http://defra1c-proxy.emea.nsn-net.net:8080"
export ftp_proxy="http://defra1c-proxy.emea.nsn-net.net:8080"
echo "Acquire::http::proxy \"http://defra1c-proxy.emea.nsn-net.net:8080/\";" | sudo tee /etc/apt/apt.conf
git config --global http.proxy http://defra1c-proxy.emea.nsn-net.net:8080
```

## Unset proxy if disconnected from VPN

```bash
export http_proxy=""
export https_proxy=""
export ftp_proxy=""
echo "Acquire::http::proxy \"\";" | sudo tee /etc/apt/apt.conf
git config --global --unset http.proxy
```

### DKMS installation

```bash
sudo apt update
sudo apt install -y dkms git bc
git clone https://github.com/fastoe/RTL8812BU.git
cd RTL8812BU
VER=$(sed -n 's/\PACKAGE_VERSION="\(.*\)"/\1/p' dkms.conf)
sudo rsync -rvhP ./ /usr/src/rtl88x2bu-${VER}
sudo dkms add -m rtl88x2bu -v ${VER}
sudo dkms build -m rtl88x2bu -v ${VER}
sudo dkms install -m rtl88x2bu -v ${VER}
sudo modprobe 88x2bu
sudo reboot
```

If the instruction *"sudo modprobe 88x2bu"* fails with *"Operation not permitted"* then disable secure boot before trying again:
```bash
sudo apt-get install mokutil
mokutil --sb-state
sudo mokutil --disable-validation
```

Choose length 8-16 password and reboot to change secure boot state and disable it: confirm the password and reboot, press any key when you see the blue screen (MOK management). Select *Change Secure Boot state* and enter what is asked about the password you chose and press Enter. Select *Yes* to disable Secure Boot in *shim-signed* and then press Enter key to finish.

For setting monitor mode:

```bash
# configure for monitor mode
sed -i 's/CONFIG_80211W = n/CONFIG_80211W = y/' Makefile
sed -i 's/CONFIG_WIFI_MONITOR = n/CONFIG_WIFI_MONITOR = y/' Makefile

make
sudo make install
sudo ip link set wlx1cbfcea97791 down
sudo iw wlx1cbfcea97791 set monitor none
sudo ip link set wlx1cbfcea97791 up
```

![image](https://www.fastoe.com/images/2020/05/8812bu-monitor-mode.png)

### For 5.10 kernel, please clone the v5.6.1 branch:
```bash
clone the new branch:
sudo apt update
sudo apt install -y dkms git bc
git clone -b v5.6.1 https://github.com/fastoe/RTL8812BU.git
cd RTL8812BU
make
sudo make install
sudo reboot
```

Enjoy!
