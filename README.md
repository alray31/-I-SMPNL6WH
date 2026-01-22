# Convert Illume / Dals Connect I-SMPNL6WH recessed light to ESPHome 
This yaml is provided as a base to convert your Illume lights to control them using ESPHome insteand of the Dals Connect app.

<img width="2000" height="2000" alt="image" src="https://github.com/user-attachments/assets/cd533dff-c74f-45dc-8b2d-02964f540a63" />


## Summary:

Flashing the firmware requires access to the Tuya WB2L module inside of the light assemgbly, dissembly is required but easily done.
There is no USB interface on the WB2L module, TTL-UART flashing is then required.
The WB2L module is equipped with a Beken BK7231t MCU. The BK7231t can be flashed using the LibreTiny library from ESPHome

## BOM:
* A computer with USB port.
* A USB TTL-UART dongle with 3.3V logic level, e.g. HiLetGo CP2102, with the correct drivers installed on the computer.
* A BDM Frame with at least 4 probe pens, a 5th one (optional) if using Reset/CEN method.
* At least 4 Dupont cable extension (female to female), 5 if using the Reset/CEN method.
* The Itchiptool software https://github.com/libretiny-eu/ltchiptool.
* Optional, a good 3.3Vdc power supply. The 3.3 power from your USB TTL-UART dongle might no be stable/powerful enough. I can confirm the HiLetGo dongle was able to power and flash the module without using any other power supply.

<img width="894" height="697" alt="image" src="https://github.com/user-attachments/assets/919ae0fd-eb67-44d5-9685-a3ccfb3d312c" />
<img width="604" height="634" alt="image" src="https://github.com/user-attachments/assets/30b3f6b8-2b2a-494b-aa92-7b29e7d61e1d" />
<img width="570" height="313" alt="image" src="https://github.com/user-attachments/assets/95afec02-4a47-499e-8d2a-1a22c9153d03" />

## Preparation:
 
* Remove the 6 philips screws from the back metal housing
<img width="528" height="557" alt="image" src="https://github.com/user-attachments/assets/a8c92990-8808-4c61-ac7b-e3bd60156621" />

* Remove the 2 spring assembly. Use a pick or small screw driver inside the spring hook tab and push it toward the opposite side to compress the spring enough so one end of the spring will disengage from the plastic spring retainer. The spring will then be loose enough to be pulled out.
* Carefully lift the back metal housing taking care not to damage any wire. Gently pry the PCB so its removed from the doublesided tape sticked to the metal housing.
<img width="484" height="864" alt="image" src="https://github.com/user-attachments/assets/eed5009a-8fbf-41aa-8afd-e932cd434ebe" />

* Carefully cut the shrink tube over the pcb in order to access the WB2L Tuya module. Be careful not to damage any component.
<img width="490" height="484" alt="image" src="https://github.com/user-attachments/assets/99bd2d07-5812-4cfb-bb74-0dfa12430421" />

* Locate the white WB2L module. Fortunately enough, it is soldered in place in a way that we can easily access the connections needed for flashing without de-soldering the module from the PCB:
<img width="344" height="388" alt="image" src="https://github.com/user-attachments/assets/bae4e4b0-66a5-4e89-bcd0-ece2caee9923" />

* Position the PCB under the BDM frame and connects pens probes as follow: 

USB TTL/UART Dongle                   WB2L module
     
     3.3V        ----------------      3.3v
     
     GND         ----------------      GND (either the GND next to the 3.3V or the GND located under U2_TXD are ok to use)
     
     RX          ----------------      U1_TXD
     
     TX          ----------------      U1_RXD
     
     RST         ----optional----      leave it free for now 

     

<img width="821" height="495" alt="image" src="https://github.com/user-attachments/assets/ae9796eb-d1fd-4018-b06b-dff39881461d" />
<img width="537" height="675" alt="image" src="https://github.com/user-attachments/assets/74f71135-6d62-43c9-8213-8de180b5ae8a" />


## Flashing instructions:
* Using ESPHome and the provided yaml file in this repo, crate a UF2 firmware to be flashed on the module (In ESPHome, create a new device, edit it with the provided yaml making sure to edit to required info (e.g wifi related info), select "install" then "manual download" then UF2 file for Itchiptool. Download this file, it will be required by Itchiptool)
* Launch Itchiptool
* Connect the USB TTL-UART dongle to the probes pens and next to the computer. If using the RST/CEN method, do not it to anything yet, leave the other end of the wire free
* Optional - recommended: Do a dump of the orignal firmware from the module if you want to restore it later. The dump is made using the same procudre as flashing but with selecting "read flash" instead of "write flash" in itchiptool
* In Itchiptool, select Baud rate to Auto
* In Itchiptool, select input file (the UF2 file create at step 1 using ESPHome)
* In Itchiptool, click Start
* If using the RESET/CEN method, momentarly short the RST pin to the ground to put the module in bootloader mode. If not using the RESET/CEN method, unplug and replug the 3.3V cable from the TTL-UART dongle. Both methods will requires a few attempts to get the correct timing so Itchiptool tries to connect to the module at the same time that the module boots in bootloader mode. If you're confident your connections are good, keep trying until you see ITchiptool progress bar showing activities.
<img width="693" height="431" alt="image" src="https://github.com/user-attachments/assets/68388340-8715-442a-a752-e08a402d4183" />

* Wait for the progress bar to show a sucessful flash
* Disconnect the USB TTL-UART dongle from the computer
* Remove all probes
* Install a new shrink tube over the PCB (or if still in a good condition, use the old one and wrap some electrical tape over it).
* Reassemble the light.
* Reconnect the light to its AC-DC power supply
* Wait for the light to boot and connect to wifi (take about 2 minutes). The light will stays off by default since restore_mode is set to RESTORE_DEFAULT_OFF. You can change this if wanted in your yaml.
* Subsequent flash/ota can now be done OTA using esphome builder


## Ressources:
<img width="752" height="480" alt="image" src="https://github.com/user-attachments/assets/3bb4944e-e2eb-450f-a618-32471177487e" />

WB2L specs:
https://fcc.report/FCC-ID/2ANDL-WB2L/4589224.pdf

LibreTiny WB2L support page:
https://docs.libretiny.eu/boards/wb2l/

ESPHome / Libretiny for BK72xx family MCU:
https://esphome.io/components/libretiny/



[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.me/AlainRaymond564)

