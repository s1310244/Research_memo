#
# Timing
#
create_clock -name $clock_name -period $clock_period [find port $clock_name]
set_clock_uncertainty $clkUncertainty [get_clocks $clock_name]
set_input_delay $InDelay_ns -clock clk [remove_from_collection [all_inputs] {clk rst_n}]
set_output_delay $OutDelay_ns -clock clk [all_outputs]



#
# Clock gating
#
# set_clock_gating_style -sequential latch
# insert_clock_gating


#
# Set wire load model
#
#set_wire_load_model -name 5K_hvratio_1_1 -library NangateOpenCellLibrary


#
# Design synthesis
#

## If you want to ungroup, uncomment this>>
#ungroup -all -flatten

compile -map_effort high
compile -incremental_mapping -map_effort high

#
# Design report
#
check_design > ./report/check_design_${base_name}_${runname}.txt 





Warning: Cannot find the design 'spike_rom' in the library 'WORK'. (LBR-1)
Warning: Cannot find the design 'bias_l1_rom' in the library 'WORK'. (LBR-1)
Warning: Cannot find the design 'bias_l2_rom' in the library 'WORK'. (LBR-1)
Warning: Cannot find the design 'bias_l3_rom' in the library 'WORK'. (LBR-1)
Warning: Cannot find the design 'weight_l1_rom' in the library 'WORK'. (LBR-1)
Warning: Cannot find the design 'weight_l2_rom' in the library 'WORK'. (LBR-1)
Warning: Cannot find the design 'weight_l3_rom' in the library 'WORK'. (LBR-1)
-> this cause : If "analyze -format verilog ../RTL/weights_l2_rom.v" are not executed, the above warning message is appeared, That cannot find the designs the library 'WORK' is indicated. 
-> the synthesis is not used the data, because the synthesis is to create the gate level netlist. So the above files need not.

Warning:  ../RTL/sram_sp_w8_b64_freepdk45.v:58: Intraassignment delays for nonblocking assignments are ignored. (VER-130)
-> in synthesis, data is not loaded, but in RTL-simulation data is loaded.  So The warning can be ignored.






Warning: Can't find objects matching '*/R0' in design 'SNPC'. (UID-95)
Error: Value for list 'object_list' must have 1 elements. (CMD-036)
-> First, xbar.v is compiled as first checkpoint 


Error: The alib file '/home/share/FreePDK45/FreePDK45/osu_soc/lib/files/alib-52/gscl45nm.db.alib' is not writable. (OPT-1313)



Warning: Cannot find the design 'SNN_Slave_If_v1_1_S00_AXI' in the library 'WORK'. (LBR-1)


Warning: Can't find port 'clk' in design 'SNN_top'. (UID-95)
Error: Value for list 'source_objects' must have 1 elements. (CMD-036)
Warning: Design 'SNN_top' has '1' unresolved references. For more detailed information, use the "link" command. (UID-341)
Warning: Can't find clock 'clk' in design 'SNN_top'. (UID-95)
Error: Value for list 'object_list' must have 1 elements. (CMD-036)
Warning: Nothing implicitly matched 'clk' (SEL-003)
Warning: Nothing implicitly matched 'rst_n' (SEL-003)
Warning: Can't find clock 'clk' in design 'SNN_top'. (UID-95)
Error: Value for list '-clock' must have 1 elements. (CMD-036)
Warning: Can't find clock 'clk' in design 'SNN_top'. (UID-95)
Error: Value for list '-clock' must have 1 elements. (CMD-036)



finaly, I created new tcl files. The above error messages and comments to modifying correctly was waste.

