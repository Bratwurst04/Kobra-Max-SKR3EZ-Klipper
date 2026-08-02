# Config Files
> [!NOTE]
> Some/all *.cfg* files have comments in Swedish.
> I Might change that in the future

## [printer.cfg](./printer.cfg)
>[!IMPORTANT]
>You need to change ***/dev/serial/by-id/*** to match your MCU!
- You will want to comment out the *[include ...]* lines of the features that you haven't enabled yet.
- When you have done bed meshing, or PID tuning or something like that, a *don't touch* paragraph will appear. I removed mine from this repository so you have to generate your own. 

## [steppers.cfg](./steppers.cfg)
- I may have flipped some of the motor-wires, so you might need to add or remove a `!` from your stepper pin to reverse that specific motor.
- The PID settings are probably different too for your device. You can comment out the PID settings to use the default klipper ones.
- The extruder is rotation tuned, but yours might be different. Though it's probably a good baseline.
- The pressure advance should be tuned. It's different for *every printer*, *every temperature* and *every filament*...

## [tmc.cfg](./tmc.cfg)
- Nothing is tuned here right now, but it's a conservative *(i think)* baseline.

## [bed.cfg](./bed.cfg)
- The Max and Min temp are klipper *STOP* commands, which is why they are put a bit above and below the normal operating temp

## [fans.cfg](./fans.cfg)
- You can change the *kick-on* time, I just guessed something and it seems to work :shrug:
- Same with the *off_below*

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
- The LED macro is just for a visual representation of when the Klipper firmware on the Rpi Zero 2W is started and I can connect to it. Since i don't have a screen connected *yet*, that's tho only way except retrying constantly.

## [accessories.cfg](./accessories.cfg)
- Currently just a button for the print-head LED

---

## Extensions

### [KAMP_SETTINGS.cfg](./KAMP_SETTINGS.cfg)
- Not super tuned but works currently for me.
