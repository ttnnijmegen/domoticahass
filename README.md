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
- ESP8266 development board. We will use this as your personal network access point to access the Raspberry PI from, so that you can easily take your Raspberry Pi home without having to reconfigure the wireless. The ESP8266 device could e.g. be the WEMOS d1 mini that we have used in the previous workshop as location tracker. 

You should also bring your home WiFi name (SSID) and password, so that we can configure the temporary network just like you have it at home. That ensures that you can take the Raspberry Pi home, and that it will connect automatically to your home wifi.

We will commence this workshop according to the recommended installation for Home Assistant:

https://www.home-assistant.io/getting-started/

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
    

## Step 3a - Configure the network settings by USB

Flash usb and make a CONFIG partition

On terminal on MacOS this can be done by typing in the following code:
diskutil eraseDisk FAT32 CONFIG <yourdisk>


On Windows this van be done via Putty by tying in the following code: 
...... {E}

You can also use the diskutility (MacOS) or .... {E} on Windows


Make a folder on this disk called network en within this folder a file called my-network
Your data structure should look like:


network/my-network

(? network/system-connections ?)

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


## Step 3b - Configure network settings by adding files to the USB (has to be done on Windows) {E}


## Step 4 Etching HASS.IO on the SD card

We will download and install the hass (home assistant) disk image on the micro-SD card according to the intructions on the home assistant website [link](https://www.home-assistant.io/hassio/installation/)

Please check the version u want to install and use the recommended version for your board (Raspberry PI 3+ or Raspberry PI 4)

Also download and install balena etcher from [this](https://www.balena.io/etcher/)  website. 

When done, use balena etcher to etch the downloaded image from HASS.IO on the SD card. 

## Step 5 Starting up your Raspberry PI with HASS.IO

After you put both the SD card and the USB stick with the network specifications in you Raspberry PI. U can powerup your Raspberry PI.

At start the Raspberry PI will download some additional files. With a normal internet connection this will take approximately 20 minuts

After these 20 minuts you can you can follow [this](http://homeassistant.local:8123) link to upen up the Home Assistant window.

Here you fill in your prefered credentials for logging in. 

Your Home Assistant is now active



## Step 6 Adding a The Things Sensor node
On basis of this tutorial a sensor can be added to HASS: [HASS and TTN](https://www.home-assistant.io/integrations/thethingsnetwork/)

Within home assistant you can add ADD-ONS. 

Go to Hass.io in the side panel and click on the tab "ADD-ONS" 

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/ADDAADDON.png)

The ADD-ON you will first install is Terminal. 

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/INSTALLTERMINAL.png)

Search for terminal or ssh in the search bar. 

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
nano configurations.yaml
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
sensor:
  - platform: thethingsnetwork
    device_id: 05
    values:
      accelerometer_6.x: X movement
      accelerometer_6.y: Y movement
      accelerometer_6.z: Z movement
      analog_in_2: Potentio meter?
      digital_in_1:
      luminosity_5: Light intensity
      relative_humidity_3: Relative humidity
      temperature_4: Temperature
```


You can safe these changes to the YAML file by holding the ctrl button and clicking x. type yes to safe the changes to the configurations.yaml file. 

After these changes reboot the system for them to take effect.
(hass.io==>system==>reboot)

The rebooted system now has the sensors activated. 

You can add them to your dashboard by changing your dashboard configuration:

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/ConfiguringDashBoard.png)

And adding a card by clicking the + sign and adding a sensor card

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/AddingACardInTheDashBoard.png)

You can now add the different sensor entities to your dashboard

![image](https://github.com/ttnnijmegen/domoticahass/blob/master/img/LoRaKISSSensorAsEntity.png)


## Step 6 automation with node red ==> verder uitwerken met een leuke automation die relevant is (e.g. licht levels na zonsondergang omhoog geven aan dat er iemand in HS is. of temperatuur controle voor 3D printen)

b) NODE-RED

Installeer en zet bij de config 
ssl:false   in plaats van true

Geef ook een credential secret in. Bijvoorbeeld:
credential_secret: 'HASS'

Save


## Step 7 Installatie van HACS and installing the kickstarter gateway monitor ==> beter uitzoeken hoe goed het werkt

For terminal installation via SSH see <https://beta--hacs.netlify.com/installation/terminal>

- https://www.youtube.com/channel/UC7G4tLa4Kt6A9e3hJ-HO8ng
- https://peyanski.com/home-assistant-themes-and-plugins-with-hacs/


## add the kickstarter gateway you your Home Assistant Dashboard
- Gateway toevoegen, zie https://github.com/cyberjunky/home-assistant-ttn_gateway

wanne have hacs

- https://github.com/cyberjunky/home-assistant-ttn_gateway
- https://github.com/davidmstraub/homeassistant-homeconnect
- https://github.com/gcobb321/icloud3
- https://github.com/gieljnssns/buienalarm-sensor-homeassistant
- https://github.com/limych/ha-gismeteo
- https://github.com/r-renato/hass-xiaomi-mi-flora-and-flower-care
- https://github.com/vinteo/hass-opensprinkler
- https://github.com/tybritten/ical-sensor-homeassistant
- https://github.com/r-renato/hass-xiaomi-mi-flora-and-flower-care
- https://github.com/rodbr/miflora-card
- https://github.com/cyberjunky/home-assistant-ttn_gateway







