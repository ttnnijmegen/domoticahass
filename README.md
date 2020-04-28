# Domotica met Home Assistant en the Things Network

## Goal

We will install Home Assistant on a Raspberry Pi (which you bring with you), and configure it such that data from an (existing) TTN Kiss sensor is received in the dashboard and can be used to trigger actions.

This time we will use an exiting TTN sensor node, one of the next times we will again implement a sensor node that  would also work with Home Assistant.

## Devices to bring

- Your laptop.
- Raspberry Pi 3B or 4 
- Raspberry Pi matching power supply with a minimal power of 2A. 
-  Micro-SD card, we recommend at least a 32 GB SD card to avoid running out of space, and a SD card reader. The SD card needs to be of [Class A2](https://www.mymemory.co.uk/blog/a1-vs-a2-sandisk-microsd-card-whats-the-difference/). You can order such an SD card for example from [data.io](https://www.dataio.nl/sandisk-extreme-64gb-microsd-geheugenkaart-u3/?utm_campaign=SanDisk+Extreme+64GB+microSD+geheugenkaart+U3+A2+160MB/s&utm_content=&utm_source=googleshopping&utm_medium=cpc&utm_term=&gclid=EAIaIQobChMI4qTSmZWT6AIVEOJ3Ch0eegaFEAQYASABEgJh5vD_BwE).
- A USB stick is handy to exchange files between computers.(named CONFIG and FAT32 structured)
- (optional, see step 1b below) ESP8266 development board. We will use this as your personal network access point to access the Raspberry PI from, so that you can easily take your Raspberry Pi home without having to reconfigure the wireless. The ESP8266 device could e.g. be the WEMOS d1 mini that we have used in the previous workshop as location tracker. 

You should also bring your home WiFi name (SSID) and password, so that we can configure the temporary network just like you have it at home. That ensures that you can take the Raspberry Pi home, and that it will connect automatically to your home wifi.

We will commence this workshop according to the recommended installation for Home Assistant:

https://www.home-assistant.io/getting-started/

## STEP 1 is only applicable when doing this workshop at hackerspace Nijmegen. For the online version through Google Hangouts we start at step 2.
## Step 1a (simple) - configure the wifi like home

Use your mobile phone to make a WiFi hotspot. Configure it such that the SSID (network name) and password match that of your wifi network at home. 

## Step 1b (technical) - configure the wifi like home

We will use an EP8266 to make a WiFi access point that provides NAT and internet access through the hacker space wifi network.

You will have to install drivers on your computer for the ESP8266 to work with arduino.

The steps will help you to install arduino on your laptop and get an ESP8266 node to work with arduino.

For the ESP8266 follow the instructions on [this](https://randomnerdtutorials.com/how-to-install-esp8266-board-arduino-ide/) link 

### ESP8266

Download and install `esptool.py`. Assuming that you have Anaconda intalled, you can use:

    conda install -c conda-forge esptool

or you can use:

    pip install esptool

Download the repository <https://github.com/martin-ger/esp_wifi_repeater>. We specifically need the pre-compiled firmware; compiling the whole software is too complex for this workhop.


Using esptool to upload the firmware: 

    esptool.py --port /dev/ttyUSB0 write_flash -fs 4MB -ff 80m -fm dio 0x00000 firmware/0x00000.bin 0x02000 firmware/0x02000.bin
    
Connect your laptop to the newly created "MyAP" wifi network. Change the network name and password in the AP settings to match those of your home network. Change the network name and password in the STA setting to those of the hacker space: see the note on the wall near the coffee machine.

You can also use the serial interface, e.g. using the Arduino IDE serial monitor:

    set ssid        xxxx  # this is the available wifi network
    set password    xxxx
    set ap_ssid     xxxx  # this is the desired new wifi network
    set ap_password xxxx
    save
    reset
    

## Step 2 - Configure the network settings by USB
Flash usb and make a CONFIG partition (see step 2a for MacOS or 2b for Windows)

Make a folder on this disk called 'network' and within this folder a file called 'my-network' (without extension).

```
network/my-network
```

The content of the file should have the following structure:

```
[connection]
id= HassOS-network
uuid=<72111c67-4a5d-4d5c-925e-f8ee26efb3c3 ==> generate this at: https://www.uuidgenerator.net>
type=802-11-wireless

[802-11-wireless]
mode=infrastructure
ssid=<MY_SSID>
# Uncomment below if your SSID is not broadcasted
#hidden=true

[802-11-wireless-security]
auth-alg=open
key-mgmt=wpa-psk
psk=<MY_WLAN_SECRET_KEY>

[ipv4]
method=auto

[ipv6]
addr-gen-mode=stable-privacy
method=auto
```
Replace <MY_SSID> and <MY_WLAN_SECRET_KEY> with your own network settings.

Download the [my-network](https://github.com/ttnnijmegen/domoticahass/blob/master/src/my-network) file.

Additional info can be found [here](https://github.com/home-assistant/operating-system/blob/dev/Documentation/network.md)

## Step 2a - Configure the network settings by USB (MacOS)

On terminal on MacOS this can be done by typing in the following code:
diskutil eraseDisk FAT32 CONFIG <yourdisk>

You can also use the diskutility.

Make a folder on this disk called network and within this folder a file called my-network
Your data structure should look like the structure as above.

## Step 2b - Configure the network settings by USB (Windows)

Insert an USB-disk in the Windows PC. Open the USB-disk in the Windows Explorer. 
We assume the driveletter F: is assigned to the USB drive.

Format the USB disk as FAT-32 with label CONFIG (be aware that all data on this USD drive will be lost). Right-click on the assigned drive-letter (here F:\) and select format.

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/WindowsFormat.png)

Make a folder on this disk called network and within this folder a file called my-network
Your data structure should look like the structure as above.

## Step 3 Etching HASS.IO on the SD card

We will download and install the hass (home assistant) disk image on the micro-SD card according to the intructions on the home assistant website [link](https://www.home-assistant.io/hassio/installation/)

Please check the version u want to install and use the recommended version for your board (Raspberry PI 3+ or Raspberry PI 4)

Also download and install balena etcher from [this](https://www.balena.io/etcher/)  website. 

When done, use balena etcher to etch the downloaded image from HASS.IO on the SD card. 

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/screenshotbalenaetcherMacOS.png)

Because the SD card is not accessible on a MAC. You need to use the USB for your internet connection settings. The reason for this issue is explained on [this](https://github.com/home-assistant/operating-system/issues/110) link.

The workaround is accessing the SD card via Linux by installing [Oracle virtual box](https://www.virtualbox.org/wiki/Downloads) and using an Linux image.

Just like with the USB stick trick (which we will use in the step below) you can put a file named `my-network`in the folder:
```
\CONFIG\network\
```
The content of the file should have the following structure:

```
[connection]
id= HassOS-network
uuid=<72111c67-4a5d-4d5c-925e-f8ee26efb3c3 ==> deze zelf genereren op: https://www.uuidgenerator.net>
type=802-11-wireless

[802-11-wireless]
mode=infrastructure
ssid=<MY_SSID>
# Uncomment below if your SSID is not broadcasted
#hidden=true

[802-11-wireless-security]
auth-alg=open
key-mgmt=wpa-psk
psk=<MY_WLAN_SECRET_KEY>

[ipv4]
method=auto

[ipv6]
addr-gen-mode=stable-privacy
method=auto
```

Replace <MY_SSID> and <MY_WLAN_SECRET_KEY> with your own network settings

## Step 4 Starting up your Raspberry PI with HASS.IO

After you put both the SD card and the USB stick with the network specifications in you Raspberry PI. U can powerup your Raspberry PI.

At start the Raspberry PI will download some additional files. With a normal internet connection this will take approximately 20 minuts

After these 20 minuts you can you can follow [this](http://homeassistant.local:8123) link to upen up the Home Assistant window. The home assistant link is: http://homeassistant.local:8123

Here you fill in your prefered credentials for logging in. 

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantfirstuser.png)

You can name your house

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistanthouse.png)

And add integrations

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantaddintegrations.png)

Please add the integrations: 
- Coronavirus
- Luftdaten (number 21305)

Integrations can also be added at a later stage within the user interface of home assistant. 

Your Home Assistant is now active


## Step 5 Adding a The Things Sensor node

On basis of this tutorial a sensor can be added to HASS: [HASS and TTN](https://www.home-assistant.io/integrations/thethingsnetwork/)

Within home assistant you can add ADD-ONS. 

Go to Hass.io in the side panel and click on the tab "ADD-ONS" 

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/ADDAADDON.png)

The ADD-ON you will first install is Terminal. 

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/INSTALLTERMINAL.png)

Search for terminal or ssh in the search bar. You might find another SSH terminal as on the picture below. This is because HASS needs to load all it's add-ons in the background. It's ok. You can use another one. 

After installing ![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/INSTALLOPENSSH.png) OpenSSH and Terminal

You will add them to your sidebar and start this ADD-ON up in your home assistant system

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/OPENSSHINSIDEBARSTART.png)

When done you should see terminal appearing in your side bar. You can click on this

A larger window will open where you will fill in a few commands to adjust your configurations.yaml.

The configurations.yaml is used most settings regarding home assistant.

Use the old DOS command to go to the folder where the yaml is located by typing 

```
cd /config
```

Look at what files are in the folder by typing 
```
ls
```
After this you can open the YAML file with the Linux text editor "NANO" by typing in 
```
nano configuration.yaml
```

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/USETERMINALTOADJUSTYAML.png)

In the YAML file which looks like this in the beginning:
![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/YAMLfirst.png)

you should add the sensor by adding the following code:

```

# adding the TTN add-on
thethingsnetwork:
  app_id: lorakiss
  access_key: ttn-account-v2.Uo4q7SSN6m7I7PQMoomRSKwvOoWRUqahlqqOYEXwOdo

# adding the sensor entity and values
sensor ttn plantsensor:
  - platform: thethingsnetwork
    device_id: 'plantsensor'
    values:
      analog_out_3: Soilmoisture
      barometer_2: Barometric pressure
      humidity_1: Humidity
      temperature_0: Temperature
```
You can safe these changes to the YAML file by holding the ctrl button and clicking x. type yes to safe the changes to the configuration.yaml file. 


Now check your configuration with server control

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantservercontrol.png)

And validate your configuration

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantvalidateconfiguration.png)


After these changes reboot the system for them to take effect.
(hass.io==>system==>reboot)

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantreboot.png)

The rebooted system now has the sensors activated. 

You can add them to your dashboard by changing your dashboard configuration:

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/ConfiguringDashBoard.png)

And adding a card by clicking the + sign and adding a sensor card

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/AddingACardInTheDashBoard.png)

You can now add the different sensor entities to your dashboard

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/LoRaKISSSensorAsEntity.png)


## Step 7 automation with node red 

Install node-red 

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantnodered.png)

And check the box next to side panel to make the node red icon apear in the side panel

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantnoderedsidepanel.png)

Add credential_secret to the configuration and put in false with ssl.

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantnoderedconfig.png)

You may save these settings and start node-red. Check the log if everything goes according to plan without errors. 

Within node-red you can now add automations. You can for example retrieve more data from external resources like home-kit.

The more easy way to add your TTN nodes. And a way to add more application than one is to add the nodes with node-red. 
It is also possible to get the location of your TTN node on an Home Assistant app. This however is something for another workshop...



## Step 8 Installatie van HACS and installing the kickstarter gateway monitor 

# if you don't have the kickstarter gateway you can skip this step

Start up your terminal within HASS. Make a directory called custom_components in your config directory

```
cd config
mkdir custom_components

```
Download HACS in this directory with copying and pasting the following commands

```
git clone https://github.com/custom-components/hacs.git hacs_temp
cd hacs_temp
git checkout $(git describe --tags --always $(git rev-list --tags --max-count=1000) | grep -e "[0-9]\+\.[0-9]\+\.[0-9]\+$" | head -n 1)
cd ../
cp -r hacs_temp/custom_components/hacs hacs
rm -R hacs_temp
```
The folder structure within the config folder should look like following. It is important that the custom_components folder is within the same folder where the configuration.yaml resides.  

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantfolderstructurehacs.png)

The next step involves getting your personal tokens from your github account. So be sure to have one. 

Within the config folder adjust the configuration.yaml with
```
nano configuration.yaml
```

And add the following line
```
hacs:
  token: d73jds8f73jkr9d8sufv2br8sd9fy92nr9f80u23r97fhse (Don't copy+paste this token, create your own)
```
Use your own tokens you generated at your own github account. 

If you are unsure how to generate these tokens. [this](https://beta--hacs.netlify.com/installation/configuration) link will give you an explanation

Now restart your home assistant server

After restart (this might take some time)

You will see hacs installed in the side panel. If you click on hacks you see the following sequence of severall screens where you can click further. 


![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistenthacswelcome.png)
![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistenthacsintroductione.png)
![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistanthacsbackgroudtask.png)
![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistanthacswarning.png)

these are explanations of hacs including a warning

After clicking all the way. You can go to integrations and fill in thing in the search area. TTN Gateway configuration will be shown

homeassistanthacssearch.png
![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistanthacssearch.png)

Install this integration.
![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistanthacsinstallgateway.png)


As with all integrations you have to adjust you configuration.yaml file. How to do this is shown a little more down in the integration. 
![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistanthacsconfiguregateway.png)

so we adjust the configuration.yaml file by opening the yaml file in the terminal with the command;

```
nano configuration.yaml
```

And add the following text below
```
# Example configuration.yaml entry

sensor ttn gateway:
  - platform: ttn_gateway
    host: IP_ADDRESS
    scan_interval: 10
    resources:
      - gateway
      - hwversion
      - blversion
      - fwversion
      - uptime
      - connected
      - interface
      - ssid
      - activationlocked
      - configured
      - region
      - gwcard
      - brokerconnected
      - packetsup
      - packetsdown
      - estore
```

replace IP_ADRESS with the ip adress of the TTN gateway in your network.

To group the badges in your display. Adjust the goups.yaml by adding the following lines of code

```
# Example groups.yaml entry

TTN Gateway:
  - sensor.ttn_gw_hardware_version
  - sensor.ttn_gw_bootloader_version
  - sensor.ttn_gw_firmware_version
  - sensor.ttn_gw_uptime
  - sensor.ttn_gw_connected
  - sensor.ttn_gw_interface
  - sensor.ttn_gw_gateway
  - sensor.ttn_gw_ssid
  - sensor.ttn_gw_activation_locked
  - sensor.ttn_gw_configured
  - sensor.ttn_gw_region
  - sensor.ttn_gw_gateway_card
  - sensor.ttn_gw_broker_connected
  - sensor.ttn_gw_packets_up
  - sensor.ttn_gw_packets_down
  - sensor.ttn_gw_external_storage
```
## Step 9 adding information to the overview screen

Now we have added a lot of integrations and data sources it is time to add them to the overview screen. 

Home assistant has a variaty of available visualisations. You can look for them yourself. 

Here we give you just one example.

To add data to your overview. You use configure to change the overview page

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistanthacsconfiguregateway.png)

After this you can adjust existing pages and add new ones. 

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantaddnewscreen.png)

You can add icons to the different tabs by adding an icon short code

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantcoronadashboard.png)

The icons can be found icon short code can be found on [this](https://materialdesignicons.com) link.
Not all icons are included in home assistant. If the icon doesn't show it's eighter not included (yet) or you misspelt the shortcut. 

Clicking on the plus you get a screen which shows you visualisation possibilities
![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantcoronaadd1.png)

For example history charts. You can populate the history chart with severall entities

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantcoronaadd2.png)

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantcoronaadd3.png)

By clicking on the pensil in the tab you can adjust the viewing settings and switch this to panel settings for a full screen view.

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantcoronapanel.png)

Now you can track whether the lockdown will probably by extended or not... 
![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/homeassistantcoronahistory.png)


# The End



