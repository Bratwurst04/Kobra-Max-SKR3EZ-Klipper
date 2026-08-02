# Config Files
> [!NOTE]
> Some/all *.cfg* files have comments in Swedish.
> I Might change that in the future

## [printer.cfg](./printer.cfg)
>[!IMPORTANT]
>You need to change ***/dev/serial/by-id/*** to match your MCU!

## [steppers.cfg](./steppers.cfg)
- I may have flipped some of the motor-wires, so you might need to add or remove a **!** from your stepper pin to reverse that specific motor.
- The PID settings are probably different too for your device. You can comment out the PID settings to use the default klipper ones.

## [tmc.cfg](./tmc.cfg)

## [bed.cfg](./bed.cfg)
- The Max and Min temp are klipper *STOP* commands, which is why they are put a bit above and below the normal operating temp

## [fans.cfg](./fans.cfg)

## [leviq_probe.cfg](./leviq_probe.cfg)
- It's set up for using KAMP. It probably works without but it's recommended för QoL

## [macros.cfg](./macros.cfg)
- Start macro in here instead of slicer for ease of access
> [!IMPORTANT]
> For the start macro here to work you need to have
> 
> `M117`\
> `PRINT_START BED=[bed_temperature_initial_layer_single] EXTRUDER=[nozzle_temperature_initial_layer]`
> 
> as *Start g-code* in your slicer
