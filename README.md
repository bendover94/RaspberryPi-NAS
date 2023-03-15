# RaspberryPi - NAS

[![Raspberry Pi - NAS](https://res.cloudinary.com/marcomontalbano/image/upload/v1678908248/video_to_markdown/images/youtube--Wfi1DpxG3Zc-c05b58ac6eb4c4700831b2b3070cd403.jpg)](https://youtu.be/Wfi1DpxG3Zc "Raspberry Pi - NAS")

![NasPic](/PICs/NAS_1.jpg)

Create your own Network Attached Storage (NAS) setup using a Raspberry Pi and OpenMediaVault.

![Inside](/PICs/inside.jpg)

<br>

Here is a [List of the components](#components) I used.

<br>

## Setup you RaspberryPi
First you´ll need a flashing program (e.g. Raspberry Pi Imager).
![RPI_OS](/PICs/RPI_OS.png)

Click on "Raspberry PI OS (other)" and flash `Raspberry PI OS Lite` onto your SD-card. Before flashing you can also hit "ctrl + shift + x" to specify further settings. 

<br>

## Connecting to PI
Check your router to get the RPI's IP-Address. Then log into your PI using:
```bash
ssh <uname>@<IP-Address>
```

After logging into your PI it is recommended to update your system:
```bash
sudo apt update && sudo apt upgrade -y
```
<br>

## Tweaking hardware and software of your PI for safety
For safety reasons I recommend to consider the following steps. If you are more the "risky" type of person continue by [Installing OpenMediaVault](#install-openmediavault)

1. Wire up a power switch to your system
2. Wire up a status LED 
3. Establish a secure shutdown

see details below.

<br>

### **1. Use a power switch to turn your PI ON/OFF**

You need a momentary switch and jumper cables.

Connect one cable of the switch to the `GND (6)` Pin of your RPI.

Connect the other cable to the `SCL (5)` Pin of your RPI.

Polarity does not matter.

For reference you can look up the pinout at:
https://pinout.xyz/


Than you need to install the "power-button-script" by typing:
```bash
git clone https://github.com/Howchoo/pi-power-button.git
./pi-power-button/script/install
```

Now your power button is ready to use.


Credits to "Tyler":
https://howchoo.com/g/mwnlytk3zmm/how-to-add-a-power-button-to-your-raspberry-pi

<br>

### **2. Adding a status LED**
To add a status LED you could write some code to switch a GPIO Pin but we can also make use of the `TX (8)` Pin of the Raspberry which is HIGH when the PI is running, is LOW when the PI is off / in standby and flickers during boot up.

To establish this we just need to modify a existing file by typing:
```bash
sudo nano /boot/config.txt
```

At the bottom we just add the following line:
```script
enable_uart=1
```


You need a LED, a resistor and jumper cables.

Connect the positive side of the LED to `TX (8)` and the negative side of the LED with the resistor in series to any `GND` Pin.

Credits to "Zach" https://howchoo.com/g/ytzjyzy4m2e/build-a-simple-raspberry-pi-led-power-status-indicator

<br>

For my project I used a different approach. Instead of using `TX (8)` and `GND` to power a LED I used it as signal for a MOSFET. As power source I used th 5V rail of the PI. Then I connected a step up converter to get 12V and powered a little LED stripe. Using this technique I pull <100mA from the 5V rail which would not be possible via the 3V rail.



<br>

### **3. For unmounting the Drive safely**

Go to your PI's system-shutdown folder by typing:
```bash
cd /lib/systemd/system-shutdown
```
Then create a script by typing:
```bash
sudo nano safe_spindown.sh
```

Fill the script with the following content:
```script
#!/bin/bash

# Sync cached data
sync

# unmount drive
umount /dev/sda1
sleep 1

# Spin down
hdparm -y /dev/sda
sleep 3
```

Make your script executable by typing:
```bash
sudo chmod +x safe_spindown.sh
```

You can now use the switch to turn off your PI and your Drive safely.

Credits to "LostInCompilation" https://forum.openmediavault.org/index.php?thread/43657-guide-raspberry-pi-safely-turn-off-external-hard-drive-on-shutdown/&postID=316709

<br>

## Install OpenMediaVault
To download the script and install OMV on your PI just type the following command:
```bash
sudo wget -O - https://raw.githubusercontent.com/OpenMediaVault-Plugin-Developers/installScript/master/install | sudo bash
```

If the installation is complete your PI will boot and because of that, close the ssh connection. After the boot up check your router again for RPI's IP-Address (it may have changed).

<br>

## Setup OpenMediaVault
Go to your web browser and type in the PI's IP-Address.

***Note: If you cannot log into your PI via ssh or via the web browser but your router tells you "yeah man, this is the right IP", don't believe him and reset the router, then you'll see the right IP-Address of your PI.***

<br>

If you can access via web browser:

default uname: `admin`

default pw: `openmediavault`

Now you can plug in your Drive into the RPI and setup your NAS inside the web browser.
<br>

For a complete walkthrough / detailed insight I highly recommend watching NetworkChuck's awesome video on this topic.

https://www.youtube.com/watch?v=gyMpI8csWis

<br>


## Components
Type-C connector: https://www.amazon.de/dp/B0BF9HDN89?psc=1&ref=ppx_yo2ov_dt_b_product_details

Type-C cable: https://www.amazon.de/dp/B09DSGMNRY?psc=1&ref=ppx_yo2ov_dt_b_product_details

Cat-6 connector: https://www.amazon.de/dp/B09TR1GPLN?psc=1&ref=ppx_yo2ov_dt_b_product_details

Cat-6 cable: https://www.amazon.de/dp/B07HKRP4WL?psc=1&ref=ppx_yo2ov_dt_b_product_details

Momentary switch: https://www.amazon.de/gp/product/B093GTW46P/ref=ppx_yo_dt_b_asin_title_o06_s00?ie=UTF8&th=1

Step-up-converter: https://www.amazon.de/gp/product/B08HQQ32M2/ref=ppx_yo_dt_b_asin_title_o07_s00?ie=UTF8&psc=1

CF-PLA: https://www.amazon.de/gp/product/B01IICFS4Y/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1
