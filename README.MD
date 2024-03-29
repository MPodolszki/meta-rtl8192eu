Tested with USB-Device EDUP EP-N1557 (equals to WISETIGER EP-N1557) (ID 0bda:818b Realtek Semiconductor Corp. RTL8192EU 802.11b/g/n WLAN Adapter)
on Phytec BSP-Yocto-AM335x-PD19.1.1 with machine:
phyboard-wega-am335x-2 (PHYTEC phyBOARD-WEGA AM335x 256MiB RAM, 128MiB NAND flash, PEB-AV-02)

1. How to add the 8192eu based Wifi-USB-Stick into a fresh Yocto BSP:


To build your own BSP choose a directory: (mkdir example) (cd example)

	wget ftp://ftp.phytec.de/pub/Software/Linux/Yocto/Tools/phyLinux
	chmod +x phyLinux 
	./phyLinux init
	. sources/poky/oe-init-build-env
	
choose a image to build (here for eg. Phytec-headless-image):
	
	bitbake phytec-headless-image

In a fresh BSP: 

1.1 go to:

        /sources/
	git clone https://github.com/MPodolszki/8192eu_wega.git

	
1.2 go to:

	/build/conf$  
	vi bblayers.conf

add below :BBLAYERS += "\
	
	${OEROOT}/../meta-rtl8192eu \

1.3 If you like to add Wlan-ID and passkey do the following steps else skip it:
	go to:
	sources/poky/meta/meta-rtl8192eu/recipes-bsp/drivers/files
	
	vi wpa_supplicant-wlan0.conf

add your desired wlan-ID and passkey:

	update_config=1
	country=DE
	network={
  	      ssid="MYSSID"
	      psk="MY-SECURE-PASSKEY"
	      proto=RSN
	      key_mgmt=WPA-PSK
              }
		      
1.4 sometimes before bitbake you got to source the sources with:
	
	. sources/poky/oe-init-build-env

build again:

	bitbake -c compile virtual/kernel -–force
	bitbake phytec-headless-image --force




2. How to change the Wireless Network Configuration on the running Board
      
Edit this file with the needed SSID and KEY

/etc/wpa_supplicant/wpa_supplicant-wlan0.conf:

	country=DE
	network={
    		ssid="SSID"
    		proto=WPA2
    		psk="KEY"
	}

Now a connection can be established with a reboot, or:

	target$ wpa_supplicant -Dnl80211 -c/etc/wpa_supplicant/wpa_supplicant-wlan0.conf -iwlan0 &

