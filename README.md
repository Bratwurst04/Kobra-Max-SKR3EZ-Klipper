# Anycubic Kobra Max with BTT SKR3 EZ motherboard running Klipper

> [!WARNING]
> This repository is very much a work in progress!

> [!NOTE]
> Don't blindly trust any code or cfg files as there is a risk of damaging your equipment
> This is tested and verified on ***my*** Anycubic Kobra Max and BTT SKR3 EZ, *YMMV*

- Original LeviQ *(strain gauge)*
- Original Bed
- Original Hotend

---

<p><details>
  <summary><h2>Features</h2></summary>

#### Original
- Original LeviQ works
- Original strain gauge
- Original hotend
- Original Bowden
- Original fans
- Original LED
- Original bed MOSFET
- Original motors

#### BigTreeTech
- SKR 3 EZ
- 4x EZ2209 UART

#### Raspberry Pi
- Raspberry Pi Zero 2W
- Mellow fly ADXL345 USB-C accelerometer

#### Klipper
- Klipper
- Klipper Backup
- KIAUH
- KAMP
- Klipper TMC Autotune
- Mainsail
- Moonraker

<p><details>
  <summary><h3>In progress</h3></summary>

#### 3d printing
- Case for Rpi

#### Tuning
- Flow
- Temperature
- Input Shaper
- Speed (max and quality)

#### Accessories
- Filament runout sensor
- Accelerometer for *input shaper*
- Screen for *KlipperScreen*

</details></p>


<p><details>
  <summary><h3>Future upgrades</h3></summary>

#### Must-have
- Quieter powers upply fan
- Nozzle cleaner

#### Maybe
- Direct drive
- New hotend and/or printhead
- PEI bed
- LED-strip in chassis
- New motors for *X,Y,Z*
- Home-assistant connection
- Eddy current sensor

</details></p>

</details></p>

---

<p><details>
  <summary><h2>Modifications</h2></summary>

### Motherboard
- The motherboard is switched from the original to the BTT SKR3 EZ, with 4 EZ2209 UART motor drivers and all new JST connectors
- It's held in place in the printer chassis with a modified version of [this](https://www.thingiverse.com/thing:6247028) adapter. [My modified version](./docs/stl/Kobra%20Max%20SKR3%20Adapter%20plate.stl) moves the mounting holes around a bit and allows for normal 3mm screws instead of heated inserts.

### Chassis
- To access the sd-card and USB-B on the motherboard, I had to cut into the side of the printer chassis. With some electrical tape i covered the sharp edges of the cut. Functional but maybe not very pretty

### Wiring
- I had to do a lot of rewiring, recrimping and labeling. You can find it [here](./docs/Wiring.md).

### Printer head
- My original printer head cover broke pretty early, so I printed another one on my other printer. I used [this](https://www.thingiverse.com/thing:5380573) one, but pretty heavily modified. I [modified it](./docs/stl/Kobra%20max%20head%202.stl) in blender, so the quality of the flat faces aren't the greatest but it's functional. Among other things I moved stuff around, made the screw holes tighter and used modifiers in the slicer to create some nice texture on the front. Here as well I skipped the heat inserts and used original mixed with ordinary screws. 
- I also changed the whole hotend to a similar one from Amazon since I broke the original one

### Extruder
- I was clumsy and messed up the bowden tubes, so I had to order new ones.

### LCD screen
- Anycubic apparently uses a proprietary LCD screen so it doesn't work at all with klipper. I will remove it completely *eventually*

</details></p>

---

<p><details>
  <summary><h2>How to</h2></summary>

*Coming soon*

</details></p>

