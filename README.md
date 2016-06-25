# R-Pi Relay + RGB Controller

![alt tag](/pcb-animated.gif)

Allows use of fully addressable RGB LEDs as mood lighting. Ideal for anyone who wants to implement light effects / light shows / sequences, but still retain some basic lighting control. The project uses the WiringPi library to listen for a GPIO input, allowing for sequential color cycling / color fading / dimming of the LEDs; from a single button.

Also offers a blind motor controller script; to control hard-wired home automation type blind / shutter motors from a single button. Can also be used with motorised projector screens for use in home cinema applications.

# Motivation

This project was created for use in my own home cinema build. I wanted a single-room home automation solution that would offer relay control of mains AC lighting, blind motor control and addressable RGB control in a single unit.

Home cinema build log: https://www.avforums.com/threads/ongoing-plasmadans-living-room-cinema-office-build.1992617/

## TODO

Finish the web GUI

## Prerequisites

Raspberry Pi with clean Raspian install:
https://www.raspberrypi.org/downloads/raspbian/

## Dependencies

https://github.com/WiringPi/WiringPi-Python

https://github.com/adafruit/Adafruit_DotStar_Pi

## Build Your Own!

The hardware for this controller is quite simple, all the components are readily available. If you decide to build one for yourself, I have provided the necessary Gerber files for the PCB. These can either be sent to a PCB manufacturer like [PCBway](http://www.pcbway.com), or you can etch the board yourself (see included transfer pdf). The PCB design is single-sided to make it easier to re-create yourself. The PCB was designed to fit into a small case ([CAMDENBOSS 7200-269C](http://camdenboss.com/enclosures/heavy-duty-enclosures/polycarbonate-clear-lid-cases#7200-series-grey-clear200x120x75)), so there wasn't any room for redundant channels on the controller. If you require additional channels, or want to make any other changes; you may want to create your own PCB instead.

![alt tag](/pcb-black-transfer.png)

Parts list: https://goo.gl/5SdG7h

## Stackable

The PCBs can easily be stacked using standoffs. The [CAMDENBOSS 7200-269C](http://camdenboss.com/enclosures/heavy-duty-enclosures/polycarbonate-clear-lid-cases#7200-series-grey-clear200x120x75) enclosure is tall enough accommodate two stacked boards. So if you require more channels; this is a simple solution.

## Raspberry Pi Compatibility

Since the code is just basic Python, it will work on any version of Raspberry Pi, including the Pi Zero. The PCB design uses a 26-way header (same as the Raspberry Pi model B), so all you need is a ribbon cable to suit your Pi (you can make these yourself easily).

## Wiring

The controller is designed to work with 4-wire type addressable LED strips; such as APA-102C or WS2801. Everything else on the controller is pretty-much universal in terms of wiring options. I have provided an example wiring diagram:

![alt tag](/example-wiring-diagram.png)

In this example, the LED driver and halogen lighting circuits are linked to the changeover relay channels. This allows for standard 2-way / intermediate (3-way if you're outside the UK) light switches to be used in conjunction with the controller. This means that if the controller went offline for whatever reason, your lights will still work!

Notice in the example that the switched-line is looped back into the AC detect circuits. This is to allow the Raspberry Pi to sense when the lights / LED driver are powered, regardless of relay / switch positions. This should make for a much nicer looking web GUI (when I get around to finishing that).

## Installation

```
sudo bash
```

Update Raspian

```
apt-get update
apt-get upgrade
```

Install Apache components

```
apt-get install apache2 php5 libapache2-mod-php5 mysql-server php5-mysql
```

Install vsftpd

```
apt-get install vsftpd -y
```

Change user for vsftpd

```
chown -R pi /var/www
```

Edit vsftpd.conf

```
nano /etc/vsftpd.conf
```

Uncomment the following line:

```
write_enable=YES
```

Add the following line:

```
force_dot_files=YES
```

Restart vsftpd

```
service vsftpd restart
```

## Installing WiringPi

Either use the instructions below or follow their guide here: https://github.com/WiringPi/WiringPi-Python

Install git

```
apt-get install git-core -y
```

Get repo

```
git clone git://git.drogon.net/wiringPi
```

Build WiringPi

```
cd wiringPi
git pull origin
./build
```

Install dependencies

```
apt-get install python-dev python-pip -y
```

Install WiringPi

```
pip install wiringpi2
```

## Usage

Edit the config options in color.py / motor.py as required.

FTP into the Pi and transfer your modified scripts to ```/var/www/html/python```, or choose your own location.

Add the dotstar.c file (from [here](https://github.com/adafruit/Adafruit_DotStar_Pi)) to the same directory; this is required for the RGB LEDs to work.

Be sure to set file permissions to 755 in the web directory.

```
chmod -R 755 /var/www
```

To make the scripts run at startup, edit rc.local:

```
nano /etc/rc.local
```

Add before exit 0

```
python /var/www/html/python/motor.py&
python /var/www/html/python/color.py&
```

Alter the paths as required. The scripts are independent from each other to allow you to just use what you need.

## License

MIT © [Dan Jones](https://www.danielkeithjones.com) - [PlasmaDan.com](https://plasmadan.com)
