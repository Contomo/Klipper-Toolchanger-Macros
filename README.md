# Klipper-Toolchanger-Macros

yes i use 4 space intendation. yes i think its more readable that way.
if you agree, wanna become a trans spacer, be smart. ctrl+a ctrl+c -> chat.openai.com -> ctrl+v -> please convert this to 4 space indent -> profit

### purge.cfg 
purge.cfg contains PURGE_TOOL as the main macro, callable while printing or idle.
logic built in for this:
assuming  current tool 1 is active
- PURGE_TOOL => purges active tool (tool 1)
- PURGE_TOOL TOOL=3,2,1,4,5,6 => purges all those tools. (will start with 1, then 3 2 4 5 6)
  
(while printing)
- PURGE_TOOL TOOL=3,1,4 => Purges tool 1, then 3 and 4, will return to tool1 once finished, getting it back to the heat it used to have. and heating/cooling to the tools previous temps when called.

The Preheating of the tools is managed in an intelligent way, where every next tool is preheated in the list to 50 less than the purging temps.
that way the time is handled properly of heating of tools. saving time between purges. and staying within a save 24V power draw (because instantly heating all 6 tools at once, may cause issues with some PSUs


### ShaperManagement.cfg 
manages your shapers based on the following logic:
load tool shapers if possible and avalible, if they arent, it uses the degfault shaper from the toolchanger config.
if both arent avalible. it uses no shaper.
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
