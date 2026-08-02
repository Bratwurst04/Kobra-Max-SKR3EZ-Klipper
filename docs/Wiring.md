
## Wiring diagram of the Kobra Max connections to the BTT SKR3 EZ board

![Excel Wiring Diagram](./pics/Anycubic_Kobra_Max_Klipper.png)

## Re-crimping

All wires need to be moved to new connectors, while some needs to be re-crimped aswell. I used the connectors that came with my BTT board, and some simple round nose pliers to "crimp" the wires to the new JST connectors. It was some fine work but I got used to it pretty quick.

## Labeling of wires

Every time I removed a wire from the original connector, I printed a label with a NIIMBOT D110 printer. The labels aren't the greatest but hey, if it works.

## 24V & HEAT

For the 24V and HEAT wires, i just bundled them up to one respectively and put in the HE0 connector on the board.

## LeviQ

The LeviQ wires needs a bidirectional logic converter. I used a cheap one from AliExpress. It needs a reference 3V3 and 5V and GND, which i took from the board and the 5V wire going to the LeviQ 5V.

According to [this](https://github.com/smartwareio/SWIO-Kobra-Max-Adapter-Kit/tree/main) person, you need a logic converter:
> The level shifter is REQUIRED to be used with the factory print head. The factory head sends 5V out into the GPIO upon reset. The shifter brings this to safe levels.
