# Klipper-Toolchanger-Macros


## macro_debugging.cfg 

| Macro               | Description                                   | Example                          |
|---------------------|-----------------------------------------------|----------------------------------|
| **[DOC_DUMP](./macro_debugging.cfg#L7)**         | SEARCH DOCS FOR .FUNCTION()                  | <img src="images/doc_dump_example.png" width="240"> |
| **[MEGA_DUMP](./macro_debugging.cfg#L100)**        | N LEVEL BRUTE FORCE SEARCH (PRINTER OBJECT)   | <img src="images/mega_dump_example.png" width="240"> |
| **[PRINT_PRINTER_PATH](./macro_debugging.cfg#L386)**| PRINTS PRINTER.FOO.BAR (CONTENTS)            | <img src="images/print_printer_path.png" width="240"> |
---


## toolchanger.cfg
a custom toolchanger config to handle custom tool pickup failure stuff. very much WIP
`_TC_FAILURE`
inside there define what you want to happen at pickup failure(nothing would mean, printer would just sit there)
`TC_RECOVER_FAILURE`
inside there define how you want to recover from it.
currently checks if its detected now. and if it is. continue whatever it was doing

## tc_offset_calibration_extras.cfg
edit 
```
variable_edge_offset: 15
variable_grid_count: 3
```
Run ```TC_ENDSTOP_AND_NUDGE_ACCURACY``` to start running a test for the nudge std deviation and endstop deviation.
doesnt really need changing of anything, default will grab 3x3 so 9 probes and get the std deviations of them.

since there is no native option to stop the console spam from the toolchanger plugin i can HIGHLY recommend adding this into your mainsaiils interface (console) settings.

```^Probe made contact at [-+]?\d+\.\d+,\s*[-+]?\d+\.\d+,\s*[-+]?\d+\.\d+$``` 
as a regex filter

as always with these macros, ```params_debug_toolchanger: True``` may be added to the ```[toolchanger]``` section

### Interpreting the output
```
Std  X=0.03316, Y=0.03261, Z=0.00209
Mean X=219.6993, Y=7.9583, Z=4.3401

Std  X=0.00242, Y=0.00604, Z=0.00177
Mean X=219.691, Y=7.9644, Z=4.3414
```
lower one is the probe itself, so an std of about 2um in the x 6um in y and 1umm in the z
thats pretty good. homing is 33um X (im using sensorless) 32um in the Y (regular microswitch) and 2um in the z (tap)
the difference of those would be the actual (well close enough) but just looking at the values you can tell its not bad at all! 

## purge.cfg 
purge.cfg contains PURGE_TOOL as the main macro, callable while printing or idle.
logic built in for this:
assuming  current tool 1 is active
- PURGE_TOOL => purges active tool (tool 1)
- PURGE_TOOL TOOL=3,2,1,4,5,6 => purges all those tools. (will start with 1, then 3 2 4 5 6)
  
(while printing)
- PURGE_TOOL TOOL=3,1,4 => Purges tool 1, then 3 and 4, will return to tool1 once finished, getting it back to the heat it used to have. and heating/cooling to the tools previous temps when called.

The Preheating of the tools is managed in an intelligent way, where every next tool is preheated in the list to 50 less than the purging temps.
that way the time is handled properly of heating of tools. saving time between purges. and staying within a save 24V power draw (because instantly heating all 6 tools at once, may cause issues with some PSUs


## ShaperManagement.cfg 
manages your shapers based on the following logic:

[input_shaper]
shaper_type_x: mzv
shaper_type_Y: mzv
shaper_freq_x: 55.4
shaper_freq_y: 39.4
damping_ratio_x: 0.06
damping_ratio_y: 0.13
#rest is defined in tools and or the macro. these set here are default fallback vals

usage:
_UPDATE_SHAPER T={tool.tool_number}
example:
```
after_change_gcode:
    {% set tn = "T"+(tool.tool_number|string) %}
    {% if printer["gcode_macro " + tn ] %}
        SET_GCODE_VARIABLE MACRO={tn} VARIABLE=color VALUE="'c44'"
    {% endif %}
    _UPDATE_SHAPER T={tool.tool_number}
```
includes inbuilt errorproofing. You will get warnings on whats missing if it is. 

[toolchanger]
params_supress_warnings_low: False

Set to True to supress those warnings if they annoy you.

Features: everything is optional. if you didnt define shapers in a single tool. the whole thing is not just gonna error or crash, its just gonna revert back to default.


## **WIP** Toolchanger dock debugging
a script to debug your docks and get them perfectly aligned (fine tuned)
requqires mainsails guis. and broadly set dock positions (broadly being for example. you manually calibrated T0 position, and you know T1 T2 T3 T4 T5 are spaced equally apart from that, just shift the X direction by that spacing. thats broad enough. just needs to go in without breaking stuff.)

Run the macro, it opens a gui, from there select the tool you want to tinker on and select the action
- Drop: (drop it in to the point its in (LED would just turn on), from there you can adjust the little butt lifter, screws, finetune XYZ positions)
- Run Docking Test: Tests the docking 10x to check for errors. be able to observe if its smooth, will increment speed from 0.1x to 2x to see for rattles/max speed

