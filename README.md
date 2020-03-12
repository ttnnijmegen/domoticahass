
Domotica workshop / maand


# Domotica met Home Assistant

## Goal

We will install Home Assistant on a Raspberry Pi (that you bring), and configure it such that data from an (existing) TTN Kiss sensor is received in the dashboard and can be used to trigger actions.

This time we will use an exiting TTN sensor node, one of the next times we will again implement a (plant sensor) node that would would also work with Home Assistant.

## Devices to bring

Your laptop.

Raspberry Pi 3B or 4 with a matching power supply of minimal 2A. 

Micro-SD card, we recommend at least a 32 GB SD card to avoid running out of space, and a SD card reader. The SD card needs to be of [Class A2](https://www.mymemory.co.uk/blog/a1-vs-a2-sandisk-microsd-card-whats-the-difference/). You can order such an SD card for example from [data.io](https://www.dataio.nl/sandisk-extreme-64gb-microsd-geheugenkaart-u3/?utm_campaign=SanDisk+Extreme+64GB+microSD+geheugenkaart+U3+A2+160MB/s&utm_content=&utm_source=googleshopping&utm_medium=cpc&utm_term=&gclid=EAIaIQobChMI4qTSmZWT6AIVEOJ3Ch0eegaFEAQYASABEgJh5vD_BwE).

A USB stick is handy to exchange files between computers.

An ESP8266 or ESP32 development board. We will use this as your personal network access point to access the Raspberry PI from, so that you can easily take your Raspberry Pi home without having to reconfigure the wireless. The ESP32 or ESP8266 device could e.g. be the WEMOS d1 mini that we have used in the previous workshop as location tracker. Or you could buy the plant sensor that we plan to use for one of the upcoming workshops: https://nl.aliexpress.com/item/32815782900.html

You should also bring your home WiFi name (SSID) and password, so that we can configure the temporary network just like you have it at home. That ensures that you can take the Raspberry Pi home, and that it will connect automatically to your home wifi.

We will commence this workshop according to the recommended installation for Home Assistant:

https://www.home-assistant.io/getting-started/

## Step 1a (simple) - configure the wifi like home

Use your mobile phone to make a WiFi hotspot. Configure it such that the SSID (network name) and password match that of your wifi network at home. 

## Step 1b (technical) - configure the wifi like home

We will use an EP8266 or ESP32 to make a WiFi access point that provides NAT and internet access through the hacker space wifi network.

You will have to install drivers on your computer for the ESP32 and ESP8266 to work with arduino.

The steps will help you to install arduino on your laptop and get an ESP32 and ESP8266 node to work with arduino.

For the ESP32 follow the instructions on [this](https://randomnerdtutorials.com/getting-started-with-esp32/) link

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
    
### ESP32

See <https://github.com/martin-ger/esp32_nat_router>. Further details to be added. 

## Step 2 - install the hass image

Download and install the hass (home assistant) disk image on the micro-SD card according to the intructions on <https://www.home-assistant.io/hassio/installation/>.


# Verdere Aantekeningen:

- Doorsturen LoraKiss
- P1 met FTDI
- ESPhome / Open mqtt gateway
- temperatuur humidity
- Speach opensource: https://mycroft.ai
- Thermostaten == > robert goedkoop? ==> intgratie thermostaat
- dmoticz => blockly beperkingen
- cloud vs dmz => 

Te testen: 

- ESP of hotspot
- USB mappen

## 1 ) aanmaken van router om vanuit hier Raspberry met HASS.IO te kunnen benaderen.
Installeer ESP32 op je laptop 

/// onderstaand nog niet getest. uiteindelijk eerst HASS.IO op eigen systeem gezet.

==> gewoon met arduino. Dit zodat ze later ook esp home kunnen installeren.
==> NAT voorbeeld. ==> nog testen. niet gelukt. 

Deze proberen. Webserver moet mDNS hebben:
https://github.com/espressif/arduino-esp32/blob/master/libraries/ESPmDNS/examples/mDNS_Web_Server/mDNS_Web_Server.ino

### tests
https://github.com/pfalcon/esp-open-sdk

```
esptool.py --chip esp32 --port /dev/tty.usbserial-0001 --baud 115200 --before default_reset --after hard_reset write_flash  -z --flash_mode dio --flash_freq 40m --flash_size detect 0x1000 build/bootloader/bootloader.bin 
0x10000 build/console.bin 
0x8000 build/partitions_example.bin
```

## 2 ) Aanmaken usb stick voor configuraties:

a ) configuratie van wifi network
Flash usb and make a CONFIG partition
diskutil eraseDisk FAT32 CONFIG <yourdisk>

maak file aan: 

network/system-connections

met dit als inhoud:

```
[connection]
id= HassOS-network
uuid=<72111c67-4a5d-4d5c-925e-f8ee26efb3c3 ==> deze zelf genereren op: https://www.uuidgenerator.net>
type=802-11-wireless

[802-11-wireless]
mode=infrastructure
ssid=MY_SSID
# Uncomment below if your SSID is not broadcasted
#hidden=true

[802-11-wireless-security]
auth-alg=open
key-mgmt=wpa-psk
psk=MY_WLAN_SECRET_KEY

[ipv4]
method=auto

[ipv6]
addr-gen-mode=stable-privacy
method=auto
```

b ) Configuratie voor benadering middels SSH (nog invullen of niet? terminal kan ook via zijpaneel worden benaderd. Veiliger?)

## 3 ) Etchen van de SD kaart met HASS.IO

Download en installeer balenaEtcher van https://www.balena.io/etcher/

Download de juiste image voor je Raspberry PI 3 of 4. Kies de voorgeschreven versie.: https://www.home-assistant.io/hassio/installation/

Etch de image met baleno image etcher op je SD kaart.

## 4 ) ADD-ONS

a) OpenSSH - Terminal

Installeer en klik op schuiver openen in zijkant

b) NODE-RED

Installeer en zet bij de config 
ssl:false   in plaats van true

Geef ook een credential secret in. Bijvoorbeeld:
credential_secret: 'HASS'

Save


monitor raspberry pi ==> bij test ligt TTN eruit

```
# Example configuration.yaml entry
sensor:
  - platform: systemmonitor
    resources:
      - type: disk_use_percent
        arg: /home
      - type: memory_free
```

## 5 ) Installatie van HACS ==> beter uitzoeken hoe goed het werkt

Adding a TTN node to Home Assistant

On basis of this tutorial a sensor can be added to HASS: [HASS and TTN](https://www.home-assistant.io/integrations/thethingsnetwork/)

We already made an sensor for you available to use: 

By using terminal in the side panel you can add the specifications to the configuration.yaml file:

(add printscreen here)

```
thethingsnetwork:
  app_id: lorakiss
  access_key: ttn-account-v2.Uo4q7SSN6m7I7PQMoomRSKwvOoWRUqahlqqOYEXwOdo

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

Now you can restart your home assistant system with supervisor and view your dashboard. Here you can add a card. 

At the entities you will seen the sensors.

(add printscreen here)

- Gateway toevoegen, zie https://github.com/cyberjunky/home-assistant-ttn_gateway
- copy via ssh van git in repository map
- pas yaml aan
- Check op deze link wat in te vullen in de configurations.yaml (verander IP adres) en in de group.yaml
- herstart hass

## 6 ) installatie HACS

For terminal installation via SSH see <https://beta--hacs.netlify.com/installation/terminal>

- https://www.youtube.com/channel/UC7G4tLa4Kt6A9e3hJ-HO8ng
- https://peyanski.com/home-assistant-themes-and-plugins-with-hacs/

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







