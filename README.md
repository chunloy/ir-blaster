# WiFi Web and Voice Remote using an ESP8266

![tvremote_final-1170x658 2](https://user-images.githubusercontent.com/101907461/198857975-40d67592-6b41-48d0-960b-b086a7230496.jpg)

My journey into web development started with a need to create a device with the ability to control a TV with modern technology. The device functions like a physical remote, but instead, infrared signals (IR) are transmitted using a web remote from a smartphone or by voice commands using a Google Home.

## Libraries

- assert
- IRrecv
- IRsend
- ESP8266WebServer
- IRremoteESP8266
- IRtext
- IRutils

## Hardware

- Adafruit Feather HUZZAH with ESP8266 WIFI and header pins
- Featherwing protoboard and breadboard
- IR receiver sensor (TSOP38238)
- IR transmitter (Super-bright 5mm IR LED - 940nm)
- NPN Bipolar transistor (PN2222)
- 470 Ohm Resistor

## Roadmap

The project has four parts:

1. Capture IR codes
2. Transmit IR codes
3. Build the web remote
4. Integrate voice with Google Home

## Part 1 - Capture IR codes

### The Receiver Circuit

<img width="776" alt="image" src="https://user-images.githubusercontent.com/101907461/198855893-67ce064f-225c-432f-b17c-688781d5cd40.png">

The receiver circuit above captures the codes from the physical remote. The `IR_Reader_Huzzah.ino` sketch displays the raw codes in the serial monitor, which get copied to the ircodes.h header file.

## Part 2 - Transmit IR codes

### The Transmitter Circuit

<img width="776" alt="image" src="https://user-images.githubusercontent.com/101907461/198855837-bcc1c20d-afcd-474d-af65-09f463bee74d.png">

With the codes stored in the `ircodes.h` header file, the next step is building the transmitter circuit (see above) to send them out to the TV. The web remote discussed in Part 3 sends the appropriate codes to control the TV.

## Part 3 - Build the web remote

The `esp8266wifiremote.ino` sketch is responsible for serving up the web remote using basic HTML and transmitting codes to the TV. The web remote is accessible from a web browser by entering the device's IP address (the IP address is displayed on the serial monitor when running the sketch). The web remote looks something like this:

![IMG_2667](https://user-images.githubusercontent.com/101907461/198856811-1a261688-14b2-40c9-8834-0568b014ce19.png)

_This was my first time writing HTML, so please don't laugh._

## Part 4 - Integrate voice with Google Home

The web remote worked great, but with a little more effort, I was able to link it up to my Google Home and use voice commands such as "_Hey Google, turn off the TV_", or "_Hey Google, mute the TV._"

Integrating the voice feature required two steps:

1. Configuring my router to allow access to the web remote on the internet
2. Mapping voice commands to the buttons on the web remote

For my Google Home to send commands to the web remote, I needed to find the web remote's public IP and configure my router to allow port forwarding by designating a port number between 1024 - 65535. Using the public IP and designated port, I could access the web remote outside of my home network by entering `http://[public IP address]:[designated port]`.

**CAUTION: I don't recommend doing this as it is not very secure and can be defeated using brute force methods. I no longer use this device!**

Configuring the voice commands was perhaps the easiest part of this project, thanks to the IFTTT platform, which supports Google Home. The idea was to create a series of applets by mapping URLs with voice commands to make GET requests using the Webhooks service. Below is a table listing the voice commands and corresponding URLs.

| Command "_Hey Google..._" | URL                                                        |
| ------------------------- | ---------------------------------------------------------- |
| Turn on/off the TV        | http://1.2.3.4:5/button?task=power                         |
| Mute/unmute the TV        | http://1.2.3.4:5/button?task=mute                          |
| Turn up the volume        | http://1.2.3.4:5/button?task=volup&value=5                 |
| Turn down the volume      | http://1.2.3.4:5/button?task=voldn&value=5                 |
| Switch to channel #       | http://1.2.3.4:5/button?task=channel&value={{NumberField}} |
| Switch to input #         | http://1.2.3.4:5/button?task=input&value=x                 |
| Go up # channels          | http://1.2.3.4:5/button?task=chanup&value=x                |
| Go down # channels        | http://1.2.3.4:5/button?task=chandn&value=x                |

## Caveats

Mapping voice commands to GET requests is challenging due to reserved phrases on Google Home. For example, "Turn up the volume" turns the volume up on Google Home instead of the TV. One limitation of the IFTTT platform is that it limits free users to three applets. I could only toggle the TV power and change the volume for this project.
