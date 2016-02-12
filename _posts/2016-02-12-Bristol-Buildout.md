---
layout: post
title: "Initial Bristol Head Buildout Post"
date: 2016-02-12 00:00:00
tags: 
 - "amateaur radio"
 - repeater
 - n0pkt
 - "rasberry pi"
 - APRS
 - electronics
description: Initial post about the Bristol Head repeater buildout.  This is the basic hardware list and links to more information.
---

## n0pkt Bristol repeater buildout

The following components are those that will be used to build the allstar linked repeater on Bristol Head in Mineral County, CO.  The exact location can be seen on google earth at: http://goo.gl/cLpKuq

I have been working closely with the Mineral County Sherriffs office to build a system that can be used by the public in a highly reliable way (this covers some back country locations and a substantial amount of coverage along the colorado trail) and for Search and Rescue (SAR) operations.

I have not included details about the feedlines in this post, suffice it to say that I am hoping to get feedline for both the UHF and VHF side donated from the Cheyenne Mountain Repeater Group http://www.cmrg.org in the form of 1/2" and 7/8" heliax.

### Core Radio Components

#### MTR2000 40 Watt Radio
* http://www.repeater-builder.com/motorola/mtr2k/pdf/mtr2000-vhf-uhf-catsheet.pdf
* http://www.ebay.com/itm/Motorola-MTR2000-UHF-40-Watts-403-470-Mhz-HAM-GR1225-/191157286287?hash=item2c81dcb58f:g:ZysAAOSwCQNWfxIJ
* Power Requirements
    - 8.5 AMP on TX
    - 1.7 AMP on Stby
    - 14.2 VDC
* Cost: $875

#### Rasberry Pi II Model B (1GB)
This will be the primary control device for everything.  It will be responsible for not only APRS information relay but also control the repeater and allow for linking into the http://www.k0jsc.com repeater system while also providing auto patch and reverse auto patch access.

* 32GB Micro SD Card
* Running DIAL for AllStarLink
    - https://goo.gl/zGZ8I8
* Power Requirements
   - 2 AMP
   - 5 VDC
* Cost: $49
 
#### RTL-SDR
* For APRS iGate
* Cost: $25
>note The RTL-SDR will interface directly with the Rasberry Pi, see notes below for APRS iGate.

#### URI (radio to pi interface and controller)
* http://dmkeng.com/URI_Order_Page.htm
* Cost: $69

#### Duplexer
* http://www.ebay.com/itm/UHF-6-CAVITY-DUPLEXER-for-radio-repeater-N-connector-SQ-/221370208031?hash=item338ab14f1f:g:MZcAAOSw~bFWFabO
* Cost: $175

#### Commscope DB420 (or equivalent for TX antenna)
* http://www.theantennafarm.com/catalog/commscope-db420-b-4271.html
* Cost: $1625.00

#### Diamond X50NA Antenna (VHF APRS RX antenna)
* Cost: $100


### Miscelanious Supporting Hardware

#### Power Control
The Power Controller will be controlled by the GPIO pins on the Rasberry Pi.  it will consist of the following components, I will detail more in a followon posting.

* 4 Channel Relay Module with both NC and NO poles
    - http://amzn.com/B00KTEN3TM
    - Cost: $6.99
* RasPi GPIO T-Cobbler
    - http://amzn.com/B00Q1T7728
    - Cost: $12.30
* High capacity relays
    - http://amzn.com/B017VDI0GY
    - Cost: $13.00
* Breadboard Jumpers
    - http://amzn.com/B00M5WLZDW
    - Cost: $14.99
* Misc Informational Links to Similar Projects
    - http://www.instructables.com/id/Web-Controlled-8-Channel-Powerstrip/
    - http://forums.connectedly.com/raspberry-pi-f179/how-controlling-gpio-pins-via-internet-2884/
 
#### Power StepDown Conversion and Source
This is a solar/battery/generator site, where the main feed will be a 48v feed.  This feed will need to be down-converted to a 12v feed to power the Rasberry Pi and repeater.

* 48 > 12v StepDown Converter
    - http://amzn.com/B015VYE546
    - Cost: $50
* Battery Bank
    - BWG 121000 x4
        + http://www.bwgllc.com/products/bwg-121000-group-27/
        + $150
        + $150 shipping

#### Mounting Hardware
The system will be mounted in a rack, assuming we will need minimal mounting items.

### Power Consumption
| Device | VDC | Tx Amps | Sty Amps |
|--------|-----|---------|----------|
| Radio  | 14.2| 8.5     | 1.7      |
| Raspi  | 12  | 2       | 2        |
| Power Control | 12 | 70mA | 70mA  |
| TOTALS | 12  |  10.6 | 3.8 |

### Price List
| Device | Price |
|--------|-------|
| Repeater  | $875  |
| Duplexer | $175 |
| Raspi  | $49   | 
| RTL-STR| $25   |
| URI    | $69   |
| UHF Antenna|$1625|
| VHF Antenna | $100|
| UHF Feedline | $150 |
| VHF Feedline | $150 |
| Relay Box | $6.99 |
| Relays | $13 |
| Jumpers | $14.99 |
| T-Cobbler| $12.30 |
| Converter | $50 | 
| Batteries | $750 |
| Rack Equipment | $200 |
| Total | $4265.00 |

### Misc Applications
* QRadioPredict
    - https://github.com/kantooon/qradiopredict
    - Used to simulate propogation 
* AllStarLink Dial
    - https://goo.gl/zGZ8I8
    - Runs on Rasberry Pi to create allstar controller and add other critical features
* Propogation Modeling
    - http://www.ve2dbe.com/
    - Used to simulate propogation (online)

### Raspi APRS Buildout
* Some Directions
    - http://sq7mru.blogspot.co.nz/2013/08/aprs-igate-rx-z-tunera-dvb-t.html
* klaibrate-rtl
    - git https://github.com/asdil12/kalibrate-rtl.git
* Generate a passcode:
    - http://apps.magicbug.co.uk/passcode/index.php
    - https://github.com/magicbug/PHP-APRS-Passcode
    - Code is included with pymultimonaprs also
>note It is CRITICAL to note that the GAIN needs to be adjusted to a value that the RTL-SDR supports, these values can be determined by running the rtl_test and then sampling to see what you get 