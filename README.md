# Klipper-Toolchanger-Macros


## macro_debugging.cfg 

| Macro               | Description                                   | Example                          |
|---------------------|-----------------------------------------------|----------------------------------|
| **[DOC_DUMP](./macro_debugging.cfg#L7)**         | search the jinja function docs with keywords.                  | <img src="images/doc_dump_example.png" width="240"> |
| **[MEGA_DUMP](./macro_debugging.cfg#L100)**        | brute force search for finding what executes what, finding objects and their type.   | <img src="images/mega_dump_example.png" width="240"> |
| **[PRINT_PRINTER_PATH](./macro_debugging.cfg#L386)**| can print out specific paths, useful for debugging, easy to drop into macros to dump entire objects during runtime.| <img src="images/print_printer_path.png" width="240"> |
---


| Macro               | Description                                   | Example                          |
|---------------------|-----------------------------------------------|----------------------------------|
| **[_UNIFIED_RESPOND](./_unified_respond.cfg#L3)** (_unified_respond.cfg) | Rich, color-coded status messages with formatting, tooltips, and multi-line support for detailed macro feedback. easily formattable, supporting foldable summaries, hover text, and even clickable commands.  | <img src="images/unified_respond_example.png" width="240"> |

---

# toolchanger specific stuff
| Macro               | Description                                   | Example                          |
|---------------------|-----------------------------------------------|----------------------------------|
| **[PLOT_PATH](./plot_path.cfg#L3)** (plot_path.cfg) | a macro to generate SVGs to help you understand the docking path, provide with PATH or none to show the path from your toolchanger configs. | <img src="images/plot_path_example.png" width="240"> |


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




