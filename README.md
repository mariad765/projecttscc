# projecttscc
date: 2025

# Proiect: OpenBook
Acest proiect utilizează Autodesk Fusion 360 pentru realizarea designului carcasei și a plăcii PCB pentru OpenBook, având la bază microcontrollerul ESP32-C6. Include senzori, module de alimentare și componente conectate prin diverse interfețe.

# Diagrama Bloc

### General:

<prev>
+------------------------+     +------------------------+     +------------------------+
| USB-C + ESD Protection | --> | LDO Voltage Regulator  | --> | Li-Po Charging Ctrl.   |
+------------------------+     +------------------------+     +------------------------+
                                                            |
                                                            v
                                                      +----------------+
                                                      |   Battery      |
                                                      | (3.7V Li-Po)   |
                                                      +----------------+
                                                            |
                                                            v
+------------------+     +------------------+         +-------------------+
| RTC DS3231SN     |<--->|   ESP32-C6       |<------->| E-Paper Display   |
+------------------+     +------------------+         | (Header + Driver) |
                           ^    ^   ^   ^              +-------------------+
                           |    |   |   |
       +------------------+    |   |   +--------------------+
       |                       |   |                        |
       v                       v   v                        v
+--------------+       +--------------+           +----------------------+
| SD Card Slot |       | BME688 Sensor|           | External Flash 64MB  |
+--------------+       +--------------+           +----------------------+
</prev>
<prev>
                           ^   |
                           |   v
               +-----------------------------+
               | Voltage Supervisor + Reset  |
               | Boot / IO Button Module     |
               +-----------------------------+

                           ^
                           |
                    +-------------+
                    | EPD Power   |
                    +-------------+

                           ^
                           |
              +-----------------------+
              | SPI ESD Protection    |
              +-----------------------+

                           ^
                           |
            +--------------------------+
            | Qwic / Stemma QT Header |
            +--------------------------+

                           ^
                           |
                     +-----------+
                     | Test Pads |
                     +-----------+

</prev>

### Organizarea componentelor mici:
<prev>

                        +------------------------+
                        | USB-C + ESD Protection |
                        | R1, F1, D1             |
                        +-----------+------------+
                                    |
                                    v
                      +-----------------------------+
                      | LDO Voltage Regulator       |
                      | U4 (XC6220)                 |
                      | D2 (Schottky)               |
                      | C1, C2 (Capacitors)         |
                      +-----------+-----------------+
                                  |
                                  v
          +----------------------------------------------+
          | Li-Po Charging Controller (MCP73831)         |
          | RPROG, C1_BAT, C2_BAT                        |
          +--------------------+-------------------------+
                               |
                               v
                          +---------+
                          | Battery |
                          +---------+

          +------------------------------------------+
          |                ESP32-C6                  |
          | U2, conectat cu GPIO la toate modulele   |
          +--+--------+----------+----------+--------+
             |        |          |          |
             |        |          |          |
             |        |          |          |
             v        v          v          v
+------------------+ +------------------+ +-------------------+ +-------------------+
| RTC DS3231SN     | | BME688 Sensor    | | E-Paper Display   | | External NOR Flash|
| U3, C1, C2       | | Ux, Cx (Filter)  | | Header + Drive    | | U1, Cx (Decouple) |
| + CRYSTAL        | | R (Pull-ups)     | | Q3 (MOSFET),      | |                   |
+------------------+ +------------------+ | L1 (Inductor),    | +-------------------+
                                          | D3, D4 (Diodes)   |
                                          | C3, C4, C5, C6    |
                                          +-------------------+

                      +------------------------------------+
                      | SD Card Module (SPI)              |
                      | + Pull-up Resistors               |
                      +------------------------------------+
                      +--------------------------------------------+
                      | Voltage Supervisor + Boot + Reset Buttons  |
                      | Ux (BD52), R1, R2, C1, C2                   |
                      +--------------------------------------------+
                      +------------------+ +--------------------+
                      | EPD Power        | | SPI ESD Protection |
                      | Zener Diode      | | TVS Diodes         |
                      +------------------+ +--------------------+
                      +---------------------------+
                      | Qwiic / Stemma QT Header  |
                      | 4-Pin Connector           |
                      +---------------------------+

                      +---------------------------+
                      | Test Pads (TP1 - TP17)    |
                      +---------------------------+

</prev>

#### Legenda componente:
R = Rezistențe (pull-up, pull-down, programare)

C = Condensatori (filtrare, decuplare, bypass)

D = Diode (Schottky, Zener, TVS)

Q = Tranzistori (MOSFET, BJT)

L = Bobine (inductoare pentru EPD)

U = Integrate (ESP32-C6, MCP73831, etc.)

TP = Test Pads

CRYSTAL = Oscilator 32.768kHz pentru RTC



<prev>

| **Device**                             | **Datasheet**                                                                                       | **Purchase URL**                                                                                                                                               | **Notes**                                                                                                                                                                                                                                                                                                                |
|----------------------------------------|----------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ADAFRUIT_LEDCHIP-LED0603               | [Datasheet](https://dammedia.osram.info/media/resource/hires/osram-dam-5178061/LH%20R974_EN.pdf)   | [Purchase](https://eu.mouser.com/ProductDetail/ams-OSRAM/LH-R974?qs=rUek3o0zKEvmwAoLLbCfCQ%3D%3D)                                                              |                                                                                                                                                                                                                                                                                                                          |
| ESP32_WROVER_EAGLE-LTSPICE_RR0402      | [Datasheet](https://eu.mouser.com/datasheet/2/447/PYu_RT_1_to_0_01_RoHS_L_15-3461507.pdf)           | [Purchase](https://eu.mouser.com/ProductDetail/YAGEO/RT0402BRE072KL?qs=gY0y7AQI9SOEMRqvTCljCA%3D%3D)                                                           |                                                                                                                                                                                                                                                                                                                          |
| ESP32_WROVER_EAGLE-LTSPICE_RR0402      | [Datasheet](https://eu.mouser.com/datasheet/2/447/PYu_SR_20105_RoHS_L_12-3461462.pdf)               | [Purchase](https://eu.mouser.com/ProductDetail/YAGEO/SR0402FR-0710KL?qs=TyBUSTk9pD%2F02BJ2PplVgA%3D%3D)                                                       |                                                                                                                                                                                                                                                                                                                          |
| EAGLE-LTSPICE_CC0402                   | [Datasheet](https://eu.mouser.com/datasheet/2/447/KEM_C1003_C0G_SMD-3316496.pdf)                    | [Purchase](https://eu.mouser.com/ProductDetail/KEMET/C0402C100J4GACTU?qs=mjekseImwbbdX0mMb15eGA%3D%3D)                                                          |                                                                                                                                                                                                                                                                                                                          |
| SJ                                     | [Datasheet](https://www.snapeda.com/parts/5112/Keystone%20Electronics/datasheet/)                    | [Purchase](https://eu.mouser.com/ProductDetail/Keystone-Electronics/5112?utm_campaign=mouser&qs=u16ybLDytRZh2kbiE9WIqQ==&utm_medium=online&utm_source=snapedaonline&utm_content=datasheet) |                                                                                                                                                                                                                                                                                                                          |
| ESP32_WROVER_EAGLE-LTSPICE_RR0402      | [Datasheet](https://eu.mouser.com/datasheet/2/447/PYu_RC_Group_51_RoHS_L_12-3368608.pdf)            | [Purchase](https://eu.mouser.com/ProductDetail/YAGEO/RC0402FR-07226RL?qs=ZELcmBmapfyYDZKSOq20kw%3D%3D)                                                           |                                                                                                                                                                                                                                                                                                                          |
| EAGLE-LTSPICE_CC0402                   | [Datasheet](https://eu.mouser.com/datasheet/2/447/KEM_C1018_X7R_SnPb_SMD-3316276.pdf)               | [Purchase](https://eu.mouser.com/ProductDetail/KEMET/C0402C104K4RAL?qs=pIZUS6sqCWCaMuP2QVR%2Fog%3D%3D&srsltid=AfmBOorFGwDGmQ6lWmC_Jiog7gZnCk6L84apRuEeDYw4gmpryDUgmfwW)              |                                                                                                                                                                                                                                                                                                                          |
| ESP32_WROVER_EAGLE-LTSPICE_CC0402      | [Datasheet](https://eu.mouser.com/datasheet/2/447/KEM_C1002_X7R_SMD-3316098.pdf)                    | [Purchase](https://eu.mouser.com/ProductDetail/KEMET/C0402C104K4RAC?qs=sykUljcfnH1GXndxGT8AHQ%3D%3D)                                                           |                                                                                                                                                                                                                                                                                                                          |
| RCL_CPOL-EUCT3528                      | [Datasheet](https://www.snapeda.com/parts/OF-SMD3528WC/Optoflash/datasheet/)                          | [Purchase](https://www.tme.eu/en/details/of-smd3528wc/smd-white-leds/optoflash/)                                                                                  |                                                                                                                                                                                                                                                                                                                          |
| ESP32_WROVER_EAGLE-LTSPICE_CC0402      | [Datasheet](https://eu.mouser.com/datasheet/2/447/KEM_C1018_X7R_SnPb_SMD-3316276.pdf)               | [Purchase](https://eu.mouser.com/ProductDetail/KEMET/C0402C104K4RAL?qs=pIZUS6sqCWCaMuP2QVR%2Fog%3D%3D)                                                           |                                                                                                                                                                                                                                                                                                                          |
| 112A-TAAR-R03_ATTEND                   | [Datasheet](https://www.snapeda.com/parts/112A-TAAR-R03/Attend/datasheet/)                            | [Purchase](https://www.tme.eu/ro/details/mcc-sdmicro/conectori-pentru-cartele/attend/112a-taar-r03/)                                                              |                                                                                                                                                                                                                                                                                                                          |
| EAGLE-LTSPICE_CC0402                   | [Datasheet](https://eu.mouser.com/datasheet/2/447/KEM_C1006_X5R_SMD-3316465.pdf)                    | [Purchase](https://eu.mouser.com/ProductDetail/KEMET/C0402C475M9PAC7411?qs=cpo3%2FpBou2jRzZxyxJnMGg%3D%3D)                                                          |                                                                                                                                                                                                                                                                                                                          |
| ESP32_WROVER_SPARKFUN-DISCRETESEMI_MOSFET_PCH-DMG2305UX-7 | [Datasheet](https://www.diodes.com/assets/Datasheets/DMG2305UX.pdf) | [Purchase](https://eu.mouser.com/ProductDetail/Diodes-Incorporated/DMG2305UX-7?qs=L1DZKBg7t5F%2FNBHrjfxC%252Bg%3D%3D) |                                                                                                                                                                                                                                                                                                                          |
| 744043680IND_4828-WE-TPC_WRE           | [Datasheet](https://www.we-online.com/components/products/datasheet/744043680.pdf)                  | [Purchase](https://eu.mouser.com/ProductDetail/Wurth-Elektronik/744043680?qs=PGXP4M47uW6VkZq%252BkzjrHA%3D%3D)                                                    |                                                                                                                                                                                                                                                                                                                          |
| BD5229G-TR                             | [Datasheet](https://fscdn.rohm.com/en/products/databook/datasheet/ic/power/voltage_detector/bd52xxg-e.pdf) | [Purchase](https://eu.mouser.com/ProductDetail/ROHM-Semiconductor/BD5229G-TR?qs=4kLU8WoGk0vvnhrrYwdszw%3D%3D) |                                                                                                                                                                                                                                                                                                                          |
| ESP32_WROVER_BME680_BME680             | [Datasheet](https://eu.mouser.com/datasheet/2/783/bst_bme688_fl000-2307034.pdf)                      | [Purchase](https://eu.mouser.com/ProductDetail/Bosch-Sensortec/BME688?qs=IS%252B4QmGtzzqQoVDscqwx3A%3D%3D)                                                       |                                                                                                                                                                                                                                                                                                                          |
| BUTTON_CUSYOMV1                         | [Datasheet](https://www.ckswitches.com/media/1482/kms.pdf)                                           | [Purchase](https://eu.mouser.com/ProductDetail/CK/KMS231G?qs=cygERTaPE9Aulmq4rq4WXQ%3D%3D)                     | ✅ Side-Actuated Tactile Switches (Compatible with KMR2 footprint)                                                                                                                                                                                                                                                                                                    |
| CPH3225A                               | [Datasheet](https://eu.mouser.com/datasheet/2/360/Seiko_Instruments_MicroBattery_E_20230330_2024Jan_-3561061.pdf) | [Purchase](https://eu.mouser.com/ProductDetail/Seiko-Semiconductors/CPH3225A?qs=3etwrb1wR%252BhUOph6lAO7eg%3D%3D&utm_id=6470900573&utm_source=google&utm_medium=cpc&utm_marketing_tactic=emeacorp&gad_source=1&gclid=CjwKCAjwzMi_BhACEiwAX4YZUD6usznTzJWXm4Z9u8rBBFy2NakSyAr25YMlMpxMyBTQ3bVXFg2nFhoCpx0QAvD_BwE) |                                                                                                                                                                                                                                                                                                                          |
| DS3231SN#                              | [Datasheet](https://eu.mouser.com/datasheet/2/609/DS3231-3421123.pdf)                                | [Purchase](https://www.snapeda.com/api/url_track_click_mouser/?unipart_id=562593&manufacturer=Seiko%20Instruments&part_name=CPH3225A&search_term=None)          |                                                                                                                                                                                                                                                                                                                          |
| ESP32-C6-WROOM-1-N8                    | [Datasheet](https://eu.mouser.com/datasheet/2/891/Espressif_ESP32_C6_WROOM_1__Datasheet_V0_1_PRELIMI-3239987.pdf) | [Purchase](https://eu.mouser.com/ProductDetail/Espressif-Systems/ESP32-C6-WROOM-1-N8?qs=8Wlm6%252BaMh8ST02Gmwp74cw%3D%3D)                                            |                                                                                                                                                                                                                                                                                                                          |
| ESP32C6_VARISTORCN1812                 | [Datasheet](https://www.snapeda.com/parts/PFMF.050.2/Schurter/datasheet/)                             | [Purchase](https://www.digikey.com/en/products/detail/schurter-inc/PFMF-050-2/1731348?utm_campaign=buynow&utm_medium=aggregator&utm_source=snapeda)               |                                                                                                                                                                                                                                                                                                                          |
| ESP32_WROVER_AVX---SD0805S020S1R0_AVX_SD0805S020S1R0_0_0AVX_SD0805S020S1R0_0_0 | [Datasheet](http://datasheets.avx.com/schottky.pdf) | [Purchase](https://www.kyocera-avx.com/products/diodes/lead-less-chip-schottky-barrier-diodes/)               |                                                                                                                                                                                                                                                                                                                          |
| ESP32_WROVER_SPARKFUN-IC-POWER_MCP73831 | [Datasheet](https://eu.mouser.com/datasheet/2/268/MCP73831_Family_Data_Sheet_DS20001984H-3441711.pdf) | [Purchase](https://eu.mouser.com/ProductDetail/Microchip-Technology/MCP73831T-2ACI-OT?qs=yUQqVecv4qvbBQBGbHx0Mw%3D%3D)               |                                                                                                                                                                                                                                                                                                                          |
| FH34SRJ-24S-0.5SH_99_                  | [Datasheet](https://www.snapeda.com/parts/FH34SRJ-24S-0.5SH(99)/Hirose%20Electric/datasheet/)         | [Purchase](https://www.digikey.com/en/products/detail/hirose-electric-co-ltd/FH34SRJ-24S-0-5SH-99/5132529?utm_campaign=buynow&utm_medium=aggregator&utm_source=snapeda) |                                                                                                                                                                                                                                                                                                                          |
| MAX17048G+T10                          | [Datasheet](https://eu.mouser.com/datasheet/2/609/MAX17048_MAX17049-3469099.pdf)                     | [Purchase](https://www.snapeda.com/api/url_track_click_mouser/?unipart_id=329239&manufacturer=Analog%20Devices&part_name=MAX17048G%2BT10&search_term=None)     |                                                                                                                                                                                                                                                                                                                          |
| MBR0530                                | [Datasheet](https://www.onsemi.com/products/discrete-power-modules/schottky-diodes-schottky-rectifiers/mbr0530t3g) | [Purchase](https://www.snapeda.com/api/url_track_click_mouser/?unipart_id=179458&manufacturer=ON%20Semiconductor&part_name=MBR0530&search_term=None)            |                                                                                                                                                                                                                                                                                                                          |
| PGB1010603MR                           | [Datasheet](https://www.littelfuse.com/assetdocs/pulseguard-esd-suppressors-pgb1-datasheet?assetguid=8a337998-d54d-466b-be4b-c4736c63c12e) | [Purchase](https://www.snapeda.com/api/url_track_click_mouser/?unipart_id=39658&manufacturer=Littelfuse&part_name=PGB1010603MR&search_term=None)        |                                                                                                                                                                                                                                                                                                                          |
Here's the table formatted as requested:

| **Device**                   | **DATASHEET**                                                                 | **PURCHASE-URL**                                                                                                      | **Notes**                                                                                                                                                                 |
|------------------------------|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| QWIIC_CONNECTORJS-1MM         | [Qwiic Connector Datasheet](https://eu.mouser.com/datasheet/2/813/Qwiic_Connector_Datasheet-1223982.pdf) | [SparkFun Product](https://eu.mouser.com/ProductDetail/SparkFun/PRT-14417?qs=wd5RIQLrsJhgdz%2FpmZ%2F3GQ%3D%3D)    |                                                                                                                                                                           |
| SAMACSYS_PARTS_USB4110-GF-A   | [USB4110 Product Drawing](https://eu.mouser.com/datasheet/2/837/GCT_USB4110_Product_Drawing___20k_cycles-3455479.pdf) | [GCT Product](https://eu.mouser.com/ProductDetail/GCT/USB4110-GF-A?qs=KUoIvG%2F9IlYiZvIXQjyJeA%3D%3D)                    |                                                                                                                                                                           |
| SI1308EDL-T1-GE3              | [SI1308EDL Datasheet](https://www.vishay.com/docs/63399/si1308edl.pdf)          | [Vishay Product](https://eu.mouser.com/ProductDetail/Vishay-Semiconductors/SI1308EDL-T1-GE3?qs=bX1%252BNvsK%2FBramh9tgpOaEw%3D%3D) |                                                                                                                                                                           |
| TPTP20R                       | [TPTP20R Datasheet](https://eu.mouser.com/datasheet/2/215/000-5004-741320.pdf)  | [Keystone Product](https://eu.mouser.com/ProductDetail/Keystone-Electronics/5000?qs=q0tsjPZWdm%252B9SiiTAbEwUw%3D%3D) | TP20R is often associated with a round, 2.0 mm diameter test pad.
Here’s a commonly used and widely available match:

✅ Keystone Electronics 5000 Series
Model: Keystone 5000

Type: SMT Test Point

Material: Phosphor bronze

Plating: Tin over Nickel

Pad Diameter: Fits 2.0 mm pad

Mounting: Surface Mount

Application: General purpose test point for probes

✅ Harwin S1751-46R
Type: SMT Test Point

For Pad: ~2.0 mm diameter

Profile: Low

Finish: Tin

Common in: Test pads designed for probe contact

✅ Alternative: Mill-Max 2501-2-00-80-00-00-07-0
Type: Test Point/Contact Pad

Style: Coined terminal

Footprint Compatibility: Suitable with TP20R

Mount: SMT

Useful for: Precise contact with spring-loaded probes
                                                                                                |
| USBLC6-2SC6Y                  | [USBLC6-2SC6Y Datasheet](https://eu.mouser.com/datasheet/2/389/usblc6_2sc6y-1852505.pdf) | [STMicroelectronics Product](https://eu.mouser.com/ProductDetail/STMicroelectronics/USBLC6-2SC6Y?qs=gNDSiZmRJS%2FOgDexvXkdow%3D%3D) |                                                                                                                                                                           |
| W25Q512JVEIQ                  | [W25Q512JVEIQ Datasheet](https://eu.mouser.com/datasheet/2/949/Winbond_W25Q512JV_Datasheet-3240039.pdf) | [Winbond Product](https://eu.mouser.com/ProductDetail/Winbond/W25Q512JVEIQ?qs=l7cgNqFNU1jw6svr3at6tA%3D%3D)         |                                                                                                                                                                           |
| XC6220A331MR-G                | [XC6220A331MR-G Datasheet](https://eu.mouser.com/datasheet/2/760/xc6220-3371556.pdf) | [Torex Semiconductor Product](https://eu.mouser.com/ProductDetail/Torex-Semiconductor/XC6220A331MR-G?qs=AsjdqWjXhJ8ZSWznL1J0gg%3D%3D) |                                                                                                                                                                           |




</prev>