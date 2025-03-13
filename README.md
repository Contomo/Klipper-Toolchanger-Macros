# Klipper-Toolchanger-Macros

yes i use 4 space intendation. yes i think its more readable that way.
if you agree, wanna become a trans spacer, be smart. ctrl+a ctrl+c -> chat.openai.com -> ctrl+v -> please convert this to 4 space indent -> profit

### purge.cfg 
purge.cfg contains PURGE_TOOL as the main macro, callable while printing or idle.
logic built in for this:
assuming  current tool 1 is active
PURGE_TOOL => purges active tool (tool 1)
PURGE_TOOL TOOL=3,2,1,4,5,6 => purges all those tools. (will start with 1, then 3 2 4 5 6)
(while printing)
PURGE_TOOL TOOL=3,1,4 => Purges tool 1, then 3 and 4, will return to tool1 once finished, getting it back to the heat it used to have. and heating/cooling to the tools previous temps when called.
