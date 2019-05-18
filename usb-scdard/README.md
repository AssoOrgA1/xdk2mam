# XDK2MAM USB WITH SD CARD
USB, short for Universal Serial Bus, is an industrial standard which was developed in the mid-1990s. It is an uniform, user-friendly interface for all peripheral devices which can be connected to a computer for both data
transfer and power supply purposes. The XDK110 supports connectivity via USB allowing the sensors node to send data to a machine without the need of being connected to the internet. 

The following repository has either files for the Bosch XDK 110 and for the data receiver in Node.js where the publish to Tangle via MAM happens.

- xdk2mam-c (C Code to build and flash to your XDK)
- xdk2mam-nodejs (Node code to start a listener server)

**The Node code provided here was tested in Linux (Debian and Raspberry). If you want to fetch USB data on other OS you can extend the solution by visiting the [Tessel Repository](https://github.com/tessel/node-usb).**

**This package uses a config file on a micro sd card, which makes possible to alter some values such as the data relay interval and the sensors used without need to recompile (you just change values in the config file and you are ready to go)**


# Instructions

## Requirements
In order to be able to run the code on this repo you will need to [download XDK Workbench](https://xdk.bosch-connectivity.com/software-downloads), have a XDK110 and install [Nodejs](https://nodejs.org/en/download/) on the computer you are going to use as listener server.

Tessel Node-USB requires the install of build-essential and libudev-dev packages on Linux, so proceed to install them if you don't have those packages. For this, run the following commands. 

```
sudo apt-get install build-essential
sudo apt-get install libusb-1.0-0-dev
sudo apt-get install libudev-dev
```

## Setting up your Node listener
Because as soon as you flash the C program to your XDK it starts sending the sensor's data, it might be a good idea to start first the Node server that will be listening. Download and install Node.js and be sure to include npm package manager.

Navigate to your xdk2mam-nodejs folder and run the following command

```
npm i
```
Once the installation finishes, open and edit the xdk2mam-usb.js file to add a Full Node (be sure to use one with PoW enabled). You browse public Full Nodes at [IOTA Dance](https://iota.dance)

```
let iota = new IOTA({
  'provider': 'http://you-pow-enabled-node:14265' //(Check https://iota.dance/)  
});
```
You will also need to check that the USB Device vid (idVendor) and pid (idProduct) parameters at **usb.findByIds(vid, pid)** matches your system USB port.

Default values provided on our code (usb.findByIds(4236, 379)) seem to work for Linux. If they don't you can explore your devices buy loggin into the console the results of **usb.getDeviceList()** as follows. 

Unplug the the XDK110 from the USB port, print usb.getDeviceList() on console and check the Devices listed. Plug the XDK110 back and run the usb.getDeviceList() again. The USB port in which your XDK110 is connected should now be added to the previous devices list.

![USB Device ids](https://xdk2mam.io/assets/images/XDK-USB-DEVICES.jpg)

Once this is done, start the node server (notice that you need to be rooted or use sudo)

```
sudo node xdk2mam-usb.js
```
Now we are ready to start with the XDK software.


## Flashing your XDK: sensors configuration
Open XDK Workbench and go to File -> Import. Choose General > Projects from Folder or Archive and select the folder ***xdk2mam-c***. Accept to import project. 


### Clear, Build and Flash
Open XDK Workbench and go to File -> Import. Choose General > Projects from Folder or Archive and select the folder **xdk2mam-c**. Accept to import project. Once project is imported, right click on **xdk2mam** folder in your Workbench Project Explorer and select **Clean project**. When the clean is done, repat and select **Build Project**. This process can take some minutes depending on your hardware and you should see any problems at the Workbench Console.

Finally, once the project has been built, connect your XDK 110 via USB and click the ***Flash*** button to install the software on the board. If everything went fine, you should be able to see the sensor data on your console.

### Editing config data

Open the **config.cfg** file on your computer and change the values the define an id, and the sensors you want to use.

```
DEVICE_NAME=enter-your-device-id
INTER_REQUEST_INTERVAL=30000
ENVIROMENTAL=YES
ACCELEROMETER=YES
GYROSCOPE=YES
INERTIAL=YES
LIGHT=YES
MAGNETOMETER=YES
ACOUSTIC=YES
```

Save the values, extract the micro SD card and carefully insert it into the XDK SD slot (contacts up). 
Turn on the XDK and you are good to go! 
If everything went fine you should see data on your console. 


![Sensors data on listening server](https://xdk2mam.io/assets/images/XDK-USB-DATA.jpg)

You can verify that the sensors data has been published to the Tangle by entering the root at [thetangle.org/mam](https://thetangle.org/mam) MAM Decoder. 
For instance, this example dataset is at [YNXLOUYHPLGHQUPGQZFOOQYFYUDCUMMZKKELGIXWGQWINHAG9XDNXWBXBISCNGBHPXMVLVPNGLQDRTYBK](https://thetangle.org/mam/YNXLOUYHPLGHQUPGQZFOOQYFYUDCUMMZKKELGIXWGQWINHAG9XDNXWBXBISCNGBHPXMVLVPNGLQDRTYBK
)


