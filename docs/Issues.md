# Issues I found while doing the conversion

## LeviQ

- The LeviQ needed a reset before probing, otherwise it would trigger at completely different Z-values every probe, and a lot of times it would simply trigger before touching the build plate. <br> I fixed it by giving it a reset command right before every probe attempt, by putting pin [**PB14**](./config/leviq_probe.cfg) to *LOW* then *HIGH*.
> [!TIP]
> You can find my ***.cfg*** files in *[~/config!](./config)*
- I had a new 3d printed head cover since my original one broke. The version I found online had a problem that haunted me for a long time. After screwing it to the head, apparently it pushed slightly on the screws of the LeviQ loadcell. It might be that i had put the wrong screws or something there, but it resulted in probe triggers at random times. In the end I modified the design of the head so it had a lot more clearance.
## Motors

### Z-motor
- I made a mistake and flipped one of the motor wires on one of the Z-motors, so they went opposite ways. The fix was to take one pair of motor wires on one of the motors and simply switch them. Since they are parallel and connected, they skipped teeth on the belt so the whole gantry was scewed by multiple *mm*. I wasn't able to fix it 100%, only because I didn't want to disassemble the top of the printer, but I managed to get the difference to levels that the bed mesh could compensate for.

### X and Y motor
- When trying to figure out the motor wire connections to the new JST connectors, I flipped the old one around in my head accidentally and mixed up my wires. For both the X, Y and E connectors I had mixed motor wires and non-motor wires on a single new JST connector and was so confused why it didn't work at first. After fixing that mistake everything worked again tho.

- 
