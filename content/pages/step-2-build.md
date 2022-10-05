---
content_type: page
description: Building instructions for the bluetooth hide and seek experiment.
draft: false
title: 'Bluetooth Hide & Seek | Step 2: Build'
uid: 07c691eb-3eee-41dc-8c1a-14a2c9a52c18
---
Now let’s shift our focus to the ***Build***

phase. Going forward, we assume that you have the kit and are ready to continue.  

### Setting Up Your Software 

The first step of recreating the Adafruit experiment is to set up the **development environment**. A development environment usually includes a text editor to program in, such as an *Integrated Development Environment* or IDE for short, a required set of dependencies like libraries (bundled code for reuse), and a way of building and then imaging your code onto the final device (if there is hardware involved). Here, we will be using **CircuitPython** to help with these tasks.

This lab is pulled from Adafruit’s [Hide and Seek Bluefruit Ornament experiment](https://learn.adafruit.com/hide-n-seek-bluefruit-ornament/circuitpython-on-circuit-playground-bluefruit). The incorporated tutorial that follows will take you through downloading the Python environment/app to program your new board.   

Through this experiment, we will give you an initial look into programming as well as working with development boards. The code is provided for you for ease of use. This way, you can focus on the process of setting up a development environment and imaging code to run on your circuit board, not on the program language syntax. We also break down the code to describe how it works. 

### Install Circuit Python

Download the latest version of Circuit Python from [circuitpython.org](https://circuitpython.org/board/circuitplayground_bluefruit/) (Click on Download .UF2) and save it to your Desktop (or wherever is handy). 

Plug your Circuit Playground Bluefruit into your computer using a known-good data-capable USB cable. 

**NOTE: A lot of people end up using charge-only USB cables and it is very frustrating! Make sure you have a USB cable you know is good for data sync.**  

Double-click the small **Reset** button in the middle of the CPB (indicated by the red arrow in the image). The ten NeoPixel LEDs (pixels) will all turn red, and then will all turn green. If they turn all red and stay red, check the USB cable, try another USB port, etc. The little red LED next to the USB connector will pulse red - this is ok! 

{{< resource uuid="6c9a76b0-a9a5-417b-8650-9850f9cd31d6" >}}

Go to **Control Panel** and select **Devices and Printers.** You will see a new disk drive appear called **CPLAYBTBOOT**. 

Go to **File Manager**. Drag the **adafruit\_circuitpython\_etc.uf2** file to **CPLAYBTBOOT.** 

The LEDs will turn red. Then, the **CPLAYBTBOOT** drive will disappear and a new disk drive called **CIRCUITPY** will appear. That's it, you're done! :)

### Set Up Circuit Python Libraries and Circuit Playground Bluefruit

If you do not already have an empty **lib** folder on your **CIRCUITPY** drive, create one now.

Go to [https://circuitpython.org/libraries](https://circuitpython.org/libraries) and download the latest version of the adafruit-circuitpython-bundle. The bundle will download as a .zip file. Extract the file and open the resulting folder. Open the **lib** folder found within. 

Once inside, you'll find a lengthy list of folders and .mpy files, but we only need some of them. To install a CircuitPython library, you drag the file or folder from the **bundle lib folder** to **the lib folder on your CIRCUITPY drive**. 

Copy the following folders and files **from the bundle lib folder** to **the** **lib folder on your CIRCUITPY drive**: 

**Folders**

- adafruit\_ble 
- adafruit\_bluefruit\_connect 
- adafruit\_bus\_device 
- adafruit\_circuitplayground 
- adafruit\_gizmo 
- adafruit\_hid 

**Files**

- adafruit\_lis3dh.mpy 
- adafruit\_thermistor.mpy 
- neopixel.mpy  

Now you're all set to use CircuitPython with the features of the Circuit Playground Bluefruit!

### Code with Circuit Python

The **adafruit\_circuitplayground** library allows us to use essential commands and features of the board without a lot of setup in our code! Since we're using the **adafruit\_circuitplayground** library in this project, be sure you have all of the libraries installed as specified in the section above.

Let’s talk about what the code does and how it works! The code is written out below but is available for easy download [here](https://courses.llx.edly.io/assets/courseware/v1/42f035f7517399e0802ef4141b26e569/asset-v1:llx+MITLLx81+Self-paced-2022+type@asset+block/code.py). Once downloaded, copy the file into the **CIRCUITPY** drive and overwrite the default "code.py" file that is there. Before you run it though, let’s look at what it does. 

**Code.py Code Load:** 

```python
#&nbsp;This&nbsp;section is comments! Comments are needed so the engineer can remember why they 
# did what they did as well as let others who use the code to quickly understand.&nbsp; 

""" 
Circuit Playground&nbsp;Bluefruit&nbsp;Ornament Proximity 
This demo uses advertising to set the color of scanning devices depending on the strongest broadcast signal received. Circuit Playgrounds&nbsp;can be switched&nbsp;between advertising and scanning using the slide switch. The buttons change the color when advertising. 
""" 

# Import the libraries. Libraries hold complicated functions that&nbsp;are abstracted&nbsp;to make # writing code easier. 

import&nbsp;time 
from&nbsp;adafruit_circuitplayground.bluefruit&nbsp;import&nbsp;cpb 

from&nbsp;adafruit_ble&nbsp;import&nbsp;BLERadio 
from&nbsp;adafruit_ble.advertising.adafruit&nbsp;import&nbsp;AdafruitColor 

#&nbsp;This&nbsp;section selects the colors that will be displayed by the LED. 
#&nbsp;The&nbsp;color pickers will cycle through this list with buttons A and B. 
color_options&nbsp;= [0x110000, 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0x111100, 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0x001100, 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0x001111, 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0x000011, 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0x110011, 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0x111111, 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0x221111, 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0x112211, 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 0x111122] 

#&nbsp;Here, the Blue Tooth Low Energy (BLE) Radio is set up.&nbsp;
ble&nbsp;=&nbsp;BLERadio() 

#&nbsp;This&nbsp;section is a patch to keep the pixels from flickering. 
i&nbsp;= 0 
advertisement&nbsp;=&nbsp;AdafruitColor() 
advertisement.color&nbsp;=&nbsp;color_options[i] 
cpb.pixels.auto_write&nbsp;= False 
cpb.pixels.fill(color_options[i]) 

#&nbsp;Below&nbsp;is the main loop of code.&nbsp; 
#&nbsp;This&nbsp;first mode broadcasts signals from the BLE radio. 
while&nbsp;True: 
 &nbsp;&nbsp; #&nbsp;The&nbsp;first mode is the color selector which broadcasts its current color to other devices. 
 &nbsp;&nbsp; if&nbsp;cpb.switch: 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; print("Broadcasting color") 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ble.start_advertising(advertisement) 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; while&nbsp;cpb.switch: 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; last_i&nbsp;=&nbsp;i 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if&nbsp;cpb.button_a: 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; i&nbsp;+= 1 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if&nbsp;cpb.button_b: 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; i&nbsp;-= 1 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; i&nbsp;%=&nbsp;len(color_options) 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if&nbsp;last_i&nbsp;!= i: 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; color&nbsp;=&nbsp;color_options[i] 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; cpb.pixels.fill(color) 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; cpb.pixels.show() 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; print("New color {:06x}".format(color)) 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; advertisement.color&nbsp;= color 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ble.stop_advertising() 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ble.start_advertising(advertisement) 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; time.sleep(0.5) 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ble.stop_advertising() 

#&nbsp;This&nbsp;second mode listens for broadcasts from other&nbsp;Adafruit&nbsp;BLE radios that are seeking. The LEDs will light up if your detector hears from another unit. 
 &nbsp;  #&nbsp;The&nbsp;second mode listens for color broadcasts and shows the color of the strongest signal. 
 &nbsp;&nbsp; else: 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; closest&nbsp;= None 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; closest_rssi&nbsp;= -80 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; closest_last_time&nbsp;= 0 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; print("Scanning for colors") 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; while&nbsp;not&nbsp;cpb.switch: 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; for&nbsp;entry in&nbsp;ble.start_scan(AdafruitColor,&nbsp;minimum_rssi=-100, timeout=1): 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if&nbsp;cpb.switch: 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; break 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; now&nbsp;=&nbsp;time.monotonic() 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; new&nbsp;= False 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if&nbsp;entry.address&nbsp;== closest: 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; pass 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; elif&nbsp;entry.rssi&nbsp;>&nbsp;closest_rssi&nbsp;or now -&nbsp;closest_last_time&nbsp;> 0.4: 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; closest&nbsp;=&nbsp;entry.address 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; else: 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; continue 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; closest_rssi&nbsp;=&nbsp;entry.rssi 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; closest_last_time&nbsp;= now 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; discrete_strength&nbsp;=&nbsp;min((100 +&nbsp;entry.rssi) // 5, 10) 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; cpb.pixels.fill(0x000000) 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; for&nbsp;i&nbsp;in range(0,&nbsp;discrete_strength): 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; cpb.pixels[i] =&nbsp;entry.color 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; cpb.pixels.show() 


 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; # Clear the pixels if we&nbsp;haven't&nbsp;heard from anything recently. 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; now&nbsp;=&nbsp;time.monotonic() 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if&nbsp;now -&nbsp;closest_last_time&nbsp;> 1: 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; cpb.pixels.fill(0x000000) 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; cpb.pixels.show() 
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ble.stop_scan()
```

*And that’s the end of the code needed to run the device. *  

Now, let’s see how this newly programmed device works!

### Assembling Your Hardware 

This is a simple and portable build for your hide and seek adventures. 

{{< resource uuid="f6fb44d9-4f90-49ec-8cd1-6068c0fb3fee" >}}

Plug in the batteries into the battery ports. 

Once the boards turn on, move the selector switch to the left on the boards you will be seeking and to the right on the one you will be using as a detector.

If at any point your board stops functioning, make sure the battery is fully plugged in.