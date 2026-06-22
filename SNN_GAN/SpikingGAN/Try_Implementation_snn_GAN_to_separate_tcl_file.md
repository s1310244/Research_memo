```
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
```




```
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
```


finaly, I created new tcl files. The above error messages and comments to modifying correctly was waste.  

finaly tcl files  
```GAN-SNN_synthesis_part1.tcl```:  
```
# ==========================================
# PART 1: ELABORATE PARAMETERS & CHECKPOINT 1
# ==========================================
set OSU_FREEPDK [format "%s%s"  [getenv "FREEPDK45"] "/osu_soc/lib/files"]
set MEM_GEN_256 [format "%s%s"  [getenv "PWD"] "/../output_sp_w8_b256_freepdk45"] 
set MEM_GEN_512 [format "%s%s"  [getenv "PWD"] "/../output_sp_w8_b512_freepdk45"] 
set TSV_PATH  "/home/lib/TSV_lib"
set search_path [concat  $search_path $OSU_FREEPDK $MEM_GEN_256 $MEM_GEN_512 $TSV_PATH ../PARAM ../RTL]
set target_library "gscl45nm.db"
set link_library [concat [list TSV.db gscl45nm.db dw_foundation.sldb sram_sp_w8_b256_freepdk45_TT_1p0V_25C.db sram_sp_w8_b512_freepdk45_TT_1p0V_25C.db]]

define_design_lib WORK -path ./work

# 1. Analyze of verilog HDL
analyze -format verilog ../PARAM/LIF.v
analyze -format verilog ../PARAM/common.v
analyze -format verilog ../RTL/LIF_neuron.v
analyze -format verilog ../RTL/xbar.v
analyze -format verilog ../RTL/SNPC.v
analyze -format verilog ../RTL/SNPC_cntrl.v
analyze -format verilog ../RTL/SNPC_top.v

# 2. construction of top module (Elaborate)
elaborate SNPC_top
current_design SNPC_top
link > link_report.txt

# 3. パラメータに基づく3つのレイヤーの自動固有化
uniquify

# 4. タイミング制約の適用
create_clock -name clk -period 2 [find port clk]
set_clock_uncertainty 0.004 [get_clocks clk]
set_input_delay 0.1 -clock clk [remove_from_collection [all_inputs] {clk rst_n}]
set_output_delay 0.1 -clock clk [all_outputs]

# 5. 未マッピングDDCの保存（チェックポイント1）
write_file -format ddc -hierarchy -output ./checkpoints/chk1_unmapped.ddc

echo ">>> PART 1 SUCCESSFUL: Saved chk1_unmapped.ddc"
exit
```
  
```GAN-SNN_synthesis_part2.tcl```:  
```
# ==========================================
# PART 2: INITIAL MAPPING & CHECKPOINT 2
# ==========================================
set OSU_FREEPDK [format "%s%s"  [getenv "FREEPDK45"] "/osu_soc/lib/files"]
set MEM_GEN_256 [format "%s%s"  [getenv "PWD"] "/../output_sp_w8_b256_freepdk45"] 
set MEM_GEN_512 [format "%s%s"  [getenv "PWD"] "/../output_sp_w8_b512_freepdk45"] 
set TSV_PATH  "/home/lib/TSV_lib"
set search_path [concat  $search_path $OSU_FREEPDK $MEM_GEN_256 $MEM_GEN_512 $TSV_PATH]
set target_library "gscl45nm.db"
set link_library [concat [list TSV.db gscl45nm.db dw_foundation.sldb sram_sp_w8_b256_freepdk45_TT_1p0V_25C.db sram_sp_w8_b512_freepdk45_TT_1p0V_25C.db]]

# 1. チェックポイント1の復元
read_file -format ddc ./checkpoints/chk1_unmapped.ddc
current_design SNPC_top

# 2. 初期マッピングの実行（中レベルの最適化努力）
compile -map_effort medium

# 3. 初期マッピング済みDDCの保存（チェックポイント2）
write_file -format ddc -hierarchy -output ./checkpoints/chk2_initial_mapped.ddc

echo ">>> PART 2 SUCCESSFUL: Saved chk2_initial_mapped.ddc"
exit
```
  
```GAN-SNN_synthesis_part3.tcl```:  
```
# ==========================================
# PART 3: HIGH OPTIMIZATION & CHECKPOINT 3
# ==========================================
set OSU_FREEPDK [format "%s%s"  [getenv "FREEPDK45"] "/osu_soc/lib/files"]
set MEM_GEN_256 [format "%s%s"  [getenv "PWD"] "/../output_sp_w8_b256_freepdk45"] 
set MEM_GEN_512 [format "%s%s"  [getenv "PWD"] "/../output_sp_w8_b512_freepdk45"] 
set TSV_PATH  "/home/lib/TSV_lib"
set search_path [concat  $search_path $OSU_FREEPDK $MEM_GEN_256 $MEM_GEN_512 $TSV_PATH]
set target_library "gscl45nm.db"
set link_library [concat [list TSV.db gscl45nm.db dw_foundation.sldb sram_sp_w8_b256_freepdk45_TT_1p0V_25C.db sram_sp_w8_b512_freepdk45_TT_1p0V_25C.db]]

# 1. チェックポイント2の復元
read_file -format ddc ./checkpoints/chk2_initial_mapped.ddc
current_design SNPC_top

# 2. インクリメンタル最適化の実行（高レベルの最適化努力）
compile -incremental_mapping -map_effort high

# 3. 高度最適化済みDDCの保存（チェックポイント3）
write_file -format ddc -hierarchy -output ./checkpoints/chk3_high_optimized.ddc

echo ">>> PART 3 SUCCESSFUL: Saved chk3_high_optimized.ddc"
exit
```
  
```GAN-SNN_synthesis_part4.tcl```:  
```
# ==========================================
# PART 4: FINAL CLEANUP & REPORT EXPORT
# ==========================================
set OSU_FREEPDK [format "%s%s"  [getenv "FREEPDK45"] "/osu_soc/lib/files"]
set MEM_GEN_256 [format "%s%s"  [getenv "PWD"] "/../output_sp_w8_b256_freepdk45"] 
set MEM_GEN_512 [format "%s%s"  [getenv "PWD"] "/../output_sp_w8_b512_freepdk45"] 
set TSV_PATH  "/home/lib/TSV_lib"
set search_path [concat  $search_path $OSU_FREEPDK $MEM_GEN_256 $MEM_GEN_512 $TSV_PATH]
set target_library "gscl45nm.db"
set link_library [concat [list TSV.db gscl45nm.db dw_foundation.sldb sram_sp_w8_b256_freepdk45_TT_1p0V_25C.db sram_sp_w8_b512_freepdk45_TT_1p0V_25C.db]]

set base_name "SNN-GAN"
set runname "net"

# 1. チェックポイント3の復元
read_file -format ddc ./checkpoints/chk3_high_optimized.ddc
current_design SNPC_top

# 2. 最終クリーンアップパス
compile -incremental_mapping -map_effort low

# 3. デザインレポートの出力
check_design > ./report/check_design_${base_name}_${runname}.txt 
report_qor > ./report/summary_report_${base_name}_${runname}.txt 
report_area -hierarchy > ./report/report_area_${base_name}_${runname}.txt 
report_timing > ./report/report_timing_${base_name}_${runname}.txt 
report_power -verbose > ./report/report_power_${base_name}_${runname}.txt 
report_area > ./output_files/SNN-GAN_ra.txt

# 4. 最終成果物（サインオフファイル）の書き出し
write -format verilog -hierarchy -output ./output_files/${base_name}_${runname}.v 
write_sdc ./output_files/${base_name}_${runname}.sdc 
write_sdf ./output_files/${base_name}_${runname}.sdf 
write_parasitics -output ./output_files/${base_name}_${runname}.spef 
write_file -format ddc -hierarchy -output ./output_files/${base_name}_${runname}.ddc 

echo ">>> PART 4 SUCCESSFUL: Final netlist generated successfully!"
exit
```


### in addition
a other idea:  
A code block To maintain the structure according to the code as much as possible
```
#
# IMPORTANT LARGE-DESIGN SETTINGS ()
#

# DO NOT FLATTEN
# ungroup -all -flatten

# Prevent massive restructuring
set_structure false

# Preserve hierarchy
set compile_preserve_subdesign_interfaces true

# Keep SRAMs untouched
set_dont_touch [get_cells */R0]
```


Since it is compiled from the lower level, if you only operate the paremeters from the upper level, the bit width of the interface of he lower level port will mismatch, so adjust the parameters from the lower level.  
