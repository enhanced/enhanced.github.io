# n0pkt Bristol repeater buildout
The following components are those that will be used to build the allstar linked repeater on Bristol Head in Mineral County, CO.  The exact location can be seen at http://goo.gl/cLpKuq

## Base Radio Components
* MTR2000 40 Watt Radio
    - http://www.repeater-builder.com/motorola/mtr2k/pdf/mtr2000-vhf-uhf-catsheet.pdf
    - http://www.ebay.com/itm/Motorola-MTR2000-UHF-40-Watts-403-470-Mhz-HAM-GR1225-/191157286287?hash=item2c81dcb58f:g:ZysAAOSwCQNWfxIJ
    - Power Requirements
        + 8.5 AMP on TX
        + 1.7 AMP on Stby
        + 14.2 VDC
    - Cost $875
* Rasberry Pi II Model B (1GB)
    - 32GB Micro SD Card
    - Running DIAL for AllStarLink
        + https://goo.gl/zGZ8I8
    - Power Requirements
        + 2 AMP
        + 5 VDC
    - Cost $49
* RTL-SDR
    -  For APRS iGate
    -  $25
* URI (radio to pi interface and controller)
    - http://dmkeng.com/URI_Order_Page.htm
    - Cost $69
* Duplexer
    - http://www.ebay.com/itm/UHF-6-CAVITY-DUPLEXER-for-radio-repeater-N-connector-SQ-/221370208031?hash=item338ab14f1f:g:MZcAAOSw~bFWFabO
    - Cost $175
* Diamond X700HNA (UHF)
    - http://www.theantennafarm.com/catalog/commscope-db420-b-4271.html
    - http://www.theantennafarm.com/catalog/diamond-x700hna-7288.html
    - Cost $350
* Diamond X50NA Antenna (VHF)
    - Cost $100

## Supporting Hardware
* Power Control
    - Home Made
        + http://amzn.com/B0057OC66U
            * 16 Channel Relay Module
            * $22.99
        + http://amzn.com/B00M5WLZDW
            * Jumpers
            * $12.99
        + Information
            * http://www.instructables.com/id/Web-Controlled-8-Channel-Powerstrip/
            * http://forums.connectedly.com/raspberry-pi-f179/how-controlling-gpio-pins-via-internet-2884/
    -  Commercial
        +  http://www.westmountainradio.com/product_info.php?products_id=rr_4005i
        +  Cost: $300
    - Commercial DIN:
        + http://www.digital-loggers.com/din.html
        + Cost: $160
* Power Down Conversion 
    - 48v > 12v 
* Battery Bank
    - BWG 121000 x4
        + http://www.bwgllc.com/products/bwg-121000-group-27/
        + 

* Exide MC-31 MEGACYCLE AGM-200 Sealed Maintenance Free (AGM) Marine Battery
    - http://amzn.com/B003FFTFBW
    - 200Ah battery (x4)
* Rack
    - Unknown - waiting on other personnel
* Cable
    - http://www.repeater-builder.com/antenna/double-shielded-coax.html
    - 1/2" Heliax?
* Asterisk Manager:
    - More research being done

## Power Consumption
| Device | VDC | Tx Amps | Sty Amps |
|--------|-----|---------|----------|
| Radio  | 14.2| 8.5     | 1.7      |
| Raspi  | 12  | 2       | 2        |
| Power Control | 12 | 70mA | 70mA  |
| TOTALS | 12  |  10.6 | 3.8 |

## Price List
| Device | Price |
|--------|-------|
| Radio  | $875  |
| Duplexer | $175 |
| Raspi  | $49   | 
| RTL-STR| $25   |
| URI    | $69   |
| UHF Antenna|$350|
| VHF Antenna | $100|
| UHF Feedline | $150 |
| VHF Feedline | $150 |
| Power Control 4005i | $300 |
| Batteries | $860 |
| Rack Equipment | $200 |
| Total | $3303 |

## Misc Applications
* QRadioPredict
    - https://github.com/kantooon/qradiopredict
    - Used to simulate propogation 
* AllStarLink Dial
    - https://goo.gl/zGZ8I8
    - Runs on Rasberry Pi to create allstar controller/
* Propogation Modeling
    - http://www.ve2dbe.com/
    - Used to simulate propogation (online)

## Raspi APRS Buildout
* Some Directions
    - http://sq7mru.blogspot.co.nz/2013/08/aprs-igate-rx-z-tunera-dvb-t.html
* klaibrate-rtl
        git clone https://github.com/asdil12/kalibrate-rtl.git
* Generate a passcode:
    - http://apps.magicbug.co.uk/passcode/index.php
    - https://github.com/magicbug/PHP-APRS-Passcode
* It is CRITICAL to note that the GAIN needs to be adjusted to a value that the RTL-SDR supports, these values can be determined by running the rtl_test 

* TYT Radio Modification for Repeater for future use
    - http://crompton.com/hamradio/BeagleBoneBlackAllstar/TYT_mod.pdf


