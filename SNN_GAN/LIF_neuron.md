# [T0.3] LIF neuron
## Pre-prepare
glone Torch2HW
```
git clone -b t1-3_dev https://github.com/klab-aizu/Torch2HW.git
git submodule update --init --recursive
```
go to folder
```
cd Torch2HW/HW/SIM
```
## simulation and coverage
Source the environment variables  
```
source /home/share/cad_sh/cad.sh
source ~/ModelSim_tutorial/env.sh
```
open vsim
```
vsim
```
Create a working library by using the transcript box
```
vlib work
vmap work work
```
Compile the RTL design and testbench
```
vlog +cover ../RTL/LIF_neuron.v
vlog ../TB/TB_LIF_neuron.v
```
start simulation
```
vsim -coverage -voptargs="+acc" work.TB_LIF_neuron
add wave /*
run -all
```
the result of  
simulation  
<img width="1920" height="1050" alt="image" src="https://github.com/user-attachments/assets/f7c3363b-cd41-474d-b4eb-85e2ccae0167" />  
  
coverage
<img width="1051" height="101" alt="image" src="https://github.com/user-attachments/assets/200262dd-1523-4425-8506-57e45273ec07" />  
the coveraged source code  
<img width="1486" height="763" alt="image" src="https://github.com/user-attachments/assets/ab06c12f-ae25-40be-a0fc-175875599ecb" />  
<img width="886" height="808" alt="image" src="https://github.com/user-attachments/assets/8a21a056-718e-4cc7-8b7b-9bede538e47e" />  
<img width="656" height="257" alt="image" src="https://github.com/user-attachments/assets/d9a7711e-71c6-4697-994e-5ee80d485b9b" />  
<img width="880" height="622" alt="image" src="https://github.com/user-attachments/assets/b8d7180c-1409-4a20-9c46-ed73faa9528b" />  

The coverage report
```
Coverage Report by instance with details

================================================================================                                                                                                             =
=== Instance: /TB_LIF_neuron/LIF0
=== Design Unit: work.LIF_neuron
================================================================================                                                                                                             =
Branch Coverage:
    Enabled Coverage              Bins      Hits    Misses  Coverage
    ----------------              ----      ----    ------  --------
    Branches                        32        30         2    93.75%

================================Branch Details================================

Branch Coverage for instance /TB_LIF_neuron/LIF0

    Line         Item                      Count     Source
    ----         ----                      -----     ------
  File ../RTL/LIF_neuron.v
------------------------------------IF Branch-----------------------------------                                                                                                             -
    150                                      372     Count coming in to IF
    150             1                        359
    151             1                         13
Branch totals: 2 hits of 2 branches = 100.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    156                                      347     Count coming in to IF
    156             1                         20
    157             1                        327
Branch totals: 2 hits of 2 branches = 100.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    157                                      327     Count coming in to IF
    157             2                         22
    157             3                        305
Branch totals: 2 hits of 2 branches = 100.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    158                                      332     Count coming in to IF
    158             1                          1
    159             1                        331
Branch totals: 2 hits of 2 branches = 100.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    159                                      331     Count coming in to IF
    159             2                        318
    159             3                         13
Branch totals: 2 hits of 2 branches = 100.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    160                                      331     Count coming in to IF
    160             1                    ***0***
    161             1                        331
Branch totals: 1 hit of 2 branches = 50.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    161                                      331     Count coming in to IF
    161             2                    ***0***
    161             3                        331
Branch totals: 1 hit of 2 branches = 50.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    185                                       58     Count coming in to IF
    185             1                          2
    186             1                         56
Branch totals: 2 hits of 2 branches = 100.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    186                                       55     Count coming in to IF
    186             2                         31
    187             1                         24
Branch totals: 2 hits of 2 branches = 100.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    187                                       26     Count coming in to IF
    187             2                          5
    187             3                         21
Branch totals: 2 hits of 2 branches = 100.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    202                                       54     Count coming in to IF
    202             1                         12
    202             2                         42
Branch totals: 2 hits of 2 branches = 100.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    214                                      299     Count coming in to IF
    214             1                          2
    216             1                        297
Branch totals: 2 hits of 2 branches = 100.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    217                                      297     Count coming in to IF
    217             1                        172
    219             1                        109
                                              16     All False Count
Branch totals: 3 hits of 3 branches = 100.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    232                                       16     Count coming in to IF
    232             1                          2
    234             1                         14
Branch totals: 2 hits of 2 branches = 100.00%

------------------------------------IF Branch-----------------------------------                                                                                                             -
    249                                        7     Count coming in to IF
    249             1                          2
    251             1                          2
    258             1                          3
Branch totals: 3 hits of 3 branches = 100.00%


Condition Coverage:
    Enabled Coverage              Bins   Covered    Misses  Coverage
    ----------------              ----      ----    ------  --------
    Conditions                      16        11         5    68.75%

================================Condition Details===============================                                                                                                             =

Condition Coverage for instance /TB_LIF_neuron/LIF0 --

  File ../RTL/LIF_neuron.v
----------------Focused Condition View-------------------
Line       156 Item    1  (~w_acc_V_sum[6] && w_acc_V_sum[5])
Condition totals: 2 of 2 input terms covered = 100.00%

----------------Focused Condition View-------------------
Line       157 Item    1  (w_acc_V_sum[6] && ~w_acc_V_sum[5])
Condition totals: 2 of 2 input terms covered = 100.00%

----------------Focused Condition View-------------------
Line       160 Item    1  (~w_acc_V_leak[6] && w_acc_V_leak[5])
Condition totals: 0 of 2 input terms covered = 0.00%

       Input Term   Covered  Reason for no coverage   Hint
      -----------  --------  -----------------------  --------------
  w_acc_V_leak[6]         N  '_0' not hit             Hit '_0'
  w_acc_V_leak[5]         N  '_1' not hit             Hit '_1'

     Rows:       Hits  FEC Target            Non-masking condition(s)
 ---------  ---------  --------------------  -------------------------
  Row   1:    ***0***  w_acc_V_leak[6]_0     w_acc_V_leak[5]
  Row   2:          1  w_acc_V_leak[6]_1     -
  Row   3:          1  w_acc_V_leak[5]_0     ~w_acc_V_leak[6]
  Row   4:    ***0***  w_acc_V_leak[5]_1     ~w_acc_V_leak[6]

----------------Focused Condition View-------------------
Line       161 Item    1  (w_acc_V_leak[6] && ~w_acc_V_leak[5])
Condition totals: 0 of 2 input terms covered = 0.00%

       Input Term   Covered  Reason for no coverage   Hint
      -----------  --------  -----------------------  --------------
  w_acc_V_leak[6]         N  '_1' not hit             Hit '_1'
  w_acc_V_leak[5]         N  '_0' not hit             Hit '_0'

     Rows:       Hits  FEC Target            Non-masking condition(s)
 ---------  ---------  --------------------  -------------------------
  Row   1:          1  w_acc_V_leak[6]_0     -
  Row   2:    ***0***  w_acc_V_leak[6]_1     ~w_acc_V_leak[5]
  Row   3:    ***0***  w_acc_V_leak[5]_0     w_acc_V_leak[6]
  Row   4:          1  w_acc_V_leak[5]_1     w_acc_V_leak[6]

----------------Focused Condition View-------------------
Line       186 Item    1  (r_refrac == 0)
Condition totals: 1 of 1 input term covered = 100.00%

----------------Focused Condition View-------------------
Line       187 Item    1  ((r_refrac != 0) && (i_State == 4))
Condition totals: 1 of 2 input terms covered = 50.00%

       Input Term   Covered  Reason for no coverage   Hint
      -----------  --------  -----------------------  --------------
  (r_refrac != 0)         N  '_0' not hit             Hit '_0'
   (i_State == 4)         Y

     Rows:       Hits  FEC Target            Non-masking condition(s)
 ---------  ---------  --------------------  -------------------------
  Row   1:    ***0***  (r_refrac != 0)_0     -
  Row   2:          1  (r_refrac != 0)_1     (i_State == 4)
  Row   3:          1  (i_State == 4)_0      (r_refrac != 0)
  Row   4:          1  (i_State == 4)_1      (r_refrac != 0)

----------------Focused Condition View-------------------
Line       217 Item    1  (w_spike | (r_refrac != 0))
Condition totals: 2 of 2 input terms covered = 100.00%

----------------Focused Condition View-------------------
Line       219 Item    1  ((w_leak_en | i_svalid) | i_recc)
Condition totals: 3 of 3 input terms covered = 100.00%


Expression Coverage:
    Enabled Coverage              Bins   Covered    Misses  Coverage
    ----------------              ----      ----    ------  --------
    Expressions                      5         5         0   100.00%

================================Expression Details==============================                                                                                                             ==

Expression Coverage for instance /TB_LIF_neuron/LIF0 --

  File ../RTL/LIF_neuron.v
----------------Focused Expression View-----------------
Line       178 Item    1  ((i_State == 4) && (r_acc_V >= w_threshold))
Expression totals: 2 of 2 input terms covered = 100.00%

----------------Focused Expression View-----------------
Line       180 Item    1  (i_State == 3)
Expression totals: 1 of 1 input term covered = 100.00%

----------------Focused Expression View-----------------
Line       202 Item    1  ((i_State == 6)? i_recc: 1'b0)
Expression totals: 2 of 2 input terms covered = 100.00%


Statement Coverage:
    Enabled Coverage              Bins      Hits    Misses  Coverage
    ----------------              ----      ----    ------  --------
    Statements                      18        18         0   100.00%

================================Statement Details===============================                                                                                                             =

Statement Coverage for instance /TB_LIF_neuron/LIF0 --

    Line         Item                      Count     Source
    ----         ----                      -----     ------
  File ../RTL/LIF_neuron.v
    150             1                        374
    156             1                        348
    158             1                        334
    160             1                        332
    178             1                        150
    180             1                         50
    185             1                         60
    202             1                         55
    213             1                        299
    215             1                          2
    218             1                        172
    220             1                        109
    231             1                         16
    233             1                          2
    235             1                         14
    248             1                          7
    250             1                          2
    252             1                          2
Toggle Coverage:
    Enabled Coverage              Bins      Hits    Misses  Coverage
    ----------------              ----      ----    ------  --------
    Toggles                        176       146        30    82.95%

================================Toggle Details================================

Toggle Coverage for instance /TB_LIF_neuron/LIF0 --

                                              Node      1H->0L      0L->1H  "Cov                                                                                                             erage"
                                              ----------------------------------                                                                                                             -----
                                        i_State[3]           0           0                                                                                                                     0.00
                                        i_Thres[0]           0           1                                                                                                                    50.00
                                        i_Thres[1]           0           0                                                                                                                     0.00
                                        i_Thres[2]           0           1                                                                                                                    50.00
                                        i_Thres[3]           1           0                                                                                                                    50.00
                                        i_Thres[4]           0           1                                                                                                                    50.00
                                        i_Thres[5]           0           0                                                                                                                     0.00
                                       r_refrac[3]           0           0                                                                                                                     0.00
                                    r_threshold[0]           0           1                                                                                                                    50.00
                                    r_threshold[1]           0           0                                                                                                                     0.00
                                    r_threshold[2]           0           1                                                                                                                    50.00
                                    r_threshold[3]           1           0                                                                                                                    50.00
                                    r_threshold[4]           0           1                                                                                                                    50.00
                                    r_threshold[5]           0           0                                                                                                                     0.00
                                   w_nxt_refrac[3]           0           0                                                                                                                     0.00
                                    w_threshold[0]           0           1                                                                                                                    50.00
                                    w_threshold[1]           0           0                                                                                                                     0.00
                                    w_threshold[2]           0           1                                                                                                                    50.00
                                    w_threshold[3]           1           0                                                                                                                    50.00
                                    w_threshold[4]           0           1                                                                                                                    50.00
                                    w_threshold[5]           0           0                                                                                                                     0.00

Total Node Count     =         88
Toggled Node Count   =         67
Untoggled Node Count =         21

Toggle Coverage      =      82.95% (146 of 176 bins)


Total Coverage By Instance (filtered view): 89.09%
```


## perform synthesis
The commands to use Synopsys Design Compiler  
```
source /home/share/cad_sh/cad.sh
```
  
Copy to ./Troch25HW/dc_syn/decoder110.tcl from ./EDAS-Tool-Examples/design-compiler-synthesis/decoder110.tcl.
decoder110.tcl file was changed for lif_neuron.v.
```
set search_path [concat  $search_path $TSV_PATH]
->
set search_path [concat $search_path $TSV_PATH "../PARAM" "../RTL"]
```
```
set base_name "detector110"
->
set base_name "LIF_neuron"
```
```
analyze -format verilog $src_folder/detector110.v
->
analyze -format verilog $src_folder/LIF_neuron.v
```
```
define_design_lib WORK -path ./WORK
->
define_design_lib WORK -path ../SIM/work
```
  
Create the needed folders(output_files, reports).
the commad to run synopes design complier
```
dc_shell
```
  
Then, run "source decoder110.tcl".
The log is shown after running the command.
```
dc_shell> source decoder110.tcl
Running PRESTO HDLC
Compiling source file ../RTL/LIF_neuron.v
Opening include file ../PARAM/param_common.v
Opening include file ../PARAM/param_LIF.v
Opening include file ../PARAM/param_LIF.v
Presto compilation completed successfully.
Loading db file '/home/lib/typical.db'
Loading db file '/home/apps/vdec/Synopsys/syn/latest/libraries/syn/dw_foundation.sldb'
Loading db file '/home/lib/TSV_lib/TSV.db'
Loading db file '/home/apps/vdec/Synopsys/syn/latest/libraries/syn/gtech.db'
Loading db file '/home/apps/vdec/Synopsys/syn/latest/libraries/syn/standard.sldb'
  Loading link library 'NangateOpenCellLibrary'
  Loading link library 'TSV'
  Loading link library 'gtech'
Running PRESTO HDLC
Warning:  ../RTL/LIF_neuron.v:150: unsigned to signed assignment occurs. (VER-318)
Warning:  ../RTL/LIF_neuron.v:156: signed to unsigned conversion occurs. (VER-318)
Warning:  ../RTL/LIF_neuron.v:158: signed to unsigned conversion occurs. (VER-318)
Warning:  ../RTL/LIF_neuron.v:160: signed to unsigned conversion occurs. (VER-318)
Warning:  ../RTL/LIF_neuron.v:162: signed to unsigned part selection occurs. (VER-318)
Warning:  ../RTL/LIF_neuron.v:181: signed to unsigned assignment occurs. (VER-318)
Warning:  ../RTL/LIF_neuron.v:252: unsigned to signed assignment occurs. (VER-318)
Warning:  ../RTL/LIF_neuron.v:283: signed to unsigned assignment occurs. (VER-318)
Warning:  ../RTL/LIF_neuron.v:259: Statement unreachable (Branch condition impossible to meet).  (VER-61)

Inferred memory devices in process
        in routine LIF_neuron line 213 in file
                '../RTL/LIF_neuron.v'.
===============================================================================
|    Register Name    |   Type    | Width | Bus | MB | AR | AS | SR | SS | ST |
===============================================================================
|     r_acc_V_reg     | Flip-flop |   6   |  Y  | N  | N  | N  | N  | N  | N  |
===============================================================================

Inferred memory devices in process
        in routine LIF_neuron line 231 in file
                '../RTL/LIF_neuron.v'.
===============================================================================
|    Register Name    |   Type    | Width | Bus | MB | AR | AS | SR | SS | ST |
===============================================================================
|    r_refrac_reg     | Flip-flop |   4   |  Y  | N  | N  | N  | N  | N  | N  |
===============================================================================

Inferred memory devices in process
        in routine LIF_neuron line 248 in file
                '../RTL/LIF_neuron.v'.
===============================================================================
|    Register Name    |   Type    | Width | Bus | MB | AR | AS | SR | SS | ST |
===============================================================================
|   r_threshold_reg   | Flip-flop |   6   |  Y  | N  | N  | N  | N  | N  | N  |
===============================================================================
Presto compilation completed successfully. (LIF_neuron)
Elaborated 1 design.
Current design is now 'LIF_neuron'.
Current design is 'LIF_neuron'.

  Linking design 'LIF_neuron'
  Using the following designs and libraries:
  --------------------------------------------------------------------------
  LIF_neuron                  /home/s1310244/Torch2HW/HW/dc_syn/LIF_neuron.db
  NangateOpenCellLibrary (library) /home/lib/typical.db
  dw_foundation.sldb (library) /home/apps/vdec/Synopsys/syn/latest/libraries/syn/dw_foundation.sldb
  TSV (library)               /home/lib/TSV_lib/TSV.db

Information: Checking out the license 'DesignWare'. (SEC-104)
Information: Evaluating DesignWare library utilization. (UISN-27)

============================================================================
| DesignWare Building Block Library  |         Version         | Available |
============================================================================
| Basic DW Building Blocks           | Q-2019.12-DWBB_201912.3 |     *     |
| Licensed DW Building Blocks        | Q-2019.12-DWBB_201912.3 |     *     |
============================================================================


Information: There are 4 potential problems in your design. Please run 'check_design' for more information. (LINT-99)



  Beginning Pass 1 Mapping
  ------------------------
  Processing 'LIF_neuron'

  Updating timing information
Information: Updating design information... (UID-85)

  Beginning Implementation Selection
  ----------------------------------
  Mapping 'LIF_neuron_DW_cmp_0'
  Processing 'LIF_neuron_DW01_add_0'
  Processing 'LIF_neuron_DW01_sub_0_DW01_sub_1'
  Processing 'LIF_neuron_DW01_add_1'
  Mapping 'LIF_neuron_DW_mult_uns_0'

  Beginning Mapping Optimizations  (High effort)
  -------------------------------
Information: Added key list 'DesignWare' to design 'LIF_neuron'. (DDB-72)
Information: compile falsified 4 infeasible paths. (OPT-1720)
  Mapping Optimization (Phase 1)
  Mapping Optimization (Phase 2)
  Mapping Optimization (Phase 3)
  Mapping Optimization (Phase 4)
  Mapping Optimization (Phase 5)
  Mapping Optimization (Phase 6)

                                  TOTAL
   ELAPSED            WORST NEG   SETUP    DESIGN
    TIME      AREA      SLACK     COST    RULE COST         ENDPOINT
  --------- --------- --------- --------- --------- -------------------------
    0:00:18     312.8      0.66      13.5       0.0
    0:00:18     306.2      0.66      13.1       0.0
    0:00:18     306.2      0.66      13.1       0.0
    0:00:18     306.2      0.66      13.1       0.0
    0:00:18     306.2      0.66      13.1       0.0
    0:00:19     299.0      0.66      13.0       0.0
    0:00:19     299.0      0.66      13.0       0.0
    0:00:19     299.0      0.66      13.0       0.0
    0:00:19     299.0      0.66      13.0       0.0
    0:00:19     299.0      0.66      13.0       0.0
    0:00:19     299.0      0.66      13.0       0.0
    0:00:19     299.0      0.66      13.0       0.0
    0:00:19     299.0      0.66      13.0       0.0



  Beginning Delay Optimization Phase
  ----------------------------------

                                  TOTAL
   ELAPSED            WORST NEG   SETUP    DESIGN
    TIME      AREA      SLACK     COST    RULE COST         ENDPOINT
  --------- --------- --------- --------- --------- -------------------------
    0:00:19     299.0      0.66      13.0       0.0
    0:00:19     301.9      0.65      12.8       0.0 r_acc_V_reg[3]/D
    0:00:19     303.5      0.64      12.7       0.0 r_acc_V_reg[3]/D
    0:00:19     307.5      0.63      12.6       0.0 r_acc_V_reg[3]/D
    0:00:19     309.4      0.62      12.6       0.0 r_acc_V_reg[4]/D
    0:00:19     312.5      0.61      12.5       0.0 r_acc_V_reg[2]/D
    0:00:19     313.6      0.61      12.5       0.0 r_acc_V_reg[3]/D
    0:00:19     318.1      0.60      12.5       0.0 r_acc_V_reg[3]/D
    0:00:19     319.2      0.59      12.4       0.0 r_acc_V_reg[3]/D
    0:00:19     322.7      0.59      12.3       0.0 r_acc_V_reg[4]/D
    0:00:19     325.6      0.58      12.2       0.0 r_acc_V_reg[4]/D
    0:00:19     329.8      0.58      12.2       0.0 r_acc_V_reg[4]/D
    0:00:19     331.7      0.57      12.2       0.0 r_acc_V_reg[4]/D
    0:00:20     333.0      0.57      12.2       0.0 r_acc_V_reg[4]/D
    0:00:20     333.8      0.57      12.2       0.0 r_acc_V_reg[4]/D
    0:00:20     334.1      0.57      12.2       0.0 r_acc_V_reg[2]/D
    0:00:20     336.2      0.57      12.2       0.0 r_acc_V_reg[4]/D
    0:00:20     337.3      0.57      12.2       0.0 r_acc_V_reg[4]/D
    0:00:20     336.5      0.56      12.2       0.0 r_acc_V_reg[2]/D
    0:00:21     337.0      0.56      12.1       0.0
    0:00:21     333.3      0.56      12.1       0.0
    0:00:21     332.8      0.56      12.1       0.0
    0:00:21     333.8      0.56      12.1       0.0
    0:00:21     334.1      0.56      12.0       0.0
    0:00:21     335.7      0.56      12.0       0.0
    0:00:21     335.7      0.56      12.0       0.0
    0:00:21     336.0      0.56      12.0       0.0


  Beginning Area-Recovery Phase  (cleanup)
  -----------------------------

                                  TOTAL
   ELAPSED            WORST NEG   SETUP    DESIGN
    TIME      AREA      SLACK     COST    RULE COST         ENDPOINT
  --------- --------- --------- --------- --------- -------------------------
    0:00:21     336.0      0.56      12.0       0.0
    0:00:21     336.0      0.56      12.0       0.0
    0:00:21     332.2      0.56      11.9       0.0
    0:00:21     332.2      0.56      11.9       0.0
    0:00:21     332.2      0.56      11.9       0.0
    0:00:21     332.2      0.56      11.9       0.0
    0:00:21     332.2      0.56      11.9       0.0
    0:00:21     332.0      0.56      11.9       0.0
    0:00:21     332.0      0.56      11.9       0.0
    0:00:21     332.0      0.56      11.9       0.0
    0:00:21     332.0      0.56      11.9       0.0
    0:00:21     332.0      0.56      11.9       0.0
    0:00:21     332.0      0.56      11.9       0.0
    0:00:22     332.0      0.56      11.9       0.0 r_acc_V_reg[4]/D
    0:00:22     331.2      0.56      11.9       0.0
    0:00:22     331.4      0.56      11.9       0.0
    0:00:22     333.0      0.56      11.9       0.0
    0:00:22     334.9      0.56      11.9       0.0
    0:00:22     337.3      0.56      11.9       0.0
    0:00:22     337.3      0.56      11.9       0.0
    0:00:22     337.8      0.56      11.9       0.0 r_acc_V_reg[4]/D
    0:00:22     341.0      0.56      11.9       0.0 r_acc_V_reg[4]/D
    0:00:22     341.0      0.56      11.9       0.0
Loading db file '/home/lib/typical.db'
Loading db file '/home/lib/TSV_lib/TSV.db'


Note: Symbol # after min delay cost means estimated hold TNS across all active scenarios


  Optimization Complete
  ---------------------
Warning: "The variable 'compile_high_effort_area_in_incremental' is supported in DC NXT only. Ignoring this setting." (OPT-1726)


  Beginning Pass 1 Mapping  (Incremental)
  ------------------------

  Updating timing information
Information: Updating design information... (UID-85)

  Beginning Mapping Optimizations  (High effort)  (Incremental)
  -------------------------------

  Beginning Incremental Implementation Selection
  ----------------------------------------------
Information: compile falsified 4 infeasible paths. (OPT-1720)

  Beginning Delay Optimization Phase
  ----------------------------------

                                  TOTAL
   ELAPSED            WORST NEG   SETUP    DESIGN
    TIME      AREA      SLACK     COST    RULE COST         ENDPOINT
  --------- --------- --------- --------- --------- -------------------------
    0:00:23     341.0      0.56      11.9       0.0
    0:00:24     342.6      0.56      11.9       0.0 r_acc_V_reg[4]/D
    0:00:25     343.7      0.56      11.9       0.0 r_acc_V_reg[4]/D
    0:00:25     344.7      0.56      11.8       0.0 r_acc_V_reg[4]/D
    0:00:25     343.7      0.56      11.8       0.0 r_acc_V_reg[2]/D
    0:00:25     345.8      0.56      11.8       0.0 r_acc_V_reg[2]/D
    0:00:25     346.3      0.55      11.8       0.0 r_acc_V_reg[4]/D
    0:00:25     347.7      0.55      11.8       0.0 r_acc_V_reg[2]/D
    0:00:25     349.3      0.55      11.8       0.0 r_acc_V_reg[1]/D
    0:00:26     349.3      0.55      11.8       0.0
    0:00:26     349.3      0.55      11.8       0.0
    0:00:26     350.6      0.55      11.8       0.0
    0:00:26     352.4      0.55      11.8       0.0
    0:00:26     353.0      0.55      11.8       0.0
    0:00:26     354.0      0.55      11.8       0.0
    0:00:26     353.5      0.55      11.8       0.0
    0:00:26     352.4      0.55      11.8       0.0
    0:00:26     354.6      0.55      11.7       0.0
    0:00:26     355.6      0.55      11.7       0.0
    0:00:26     356.7      0.55      11.7       0.0
    0:00:26     356.7      0.55      11.7       0.0
Loading db file '/home/lib/typical.db'
Loading db file '/home/lib/TSV_lib/TSV.db'


Note: Symbol # after min delay cost means estimated hold TNS across all active scenarios


  Optimization Complete
  ---------------------
Writing verilog file '/home/s1310244/Torch2HW/HW/dc_syn/output_files/LIF_neuron_net.v'.
Warning: Verilog 'assign' or 'tran' statements are written out. (VO-4)
Information: Annotated 'cell' delays are assumed to include load delay. (UID-282)
Information: Writing timing information to file '/home/s1310244/Torch2HW/HW/dc_syn/output_files/LIF_neuron_net.sdf'. (WT-3)
Information: Writing parasitics to file '/home/s1310244/Torch2HW/HW/dc_syn/output_files/LIF_neuron_net.spef'. (WP-3)
Writing ddc file './output_files/LIF_neuron_net.ddc'.

Memory usage for this session 99 Mbytes.
Memory usage for this session including child processes 99 Mbytes.
CPU usage for this session 14 seconds ( 0.00 hours ).
Elapsed time for this session 28 seconds ( 0.01 hours ).

Thank you...
```  
files in reports folder.
```
$ ls reports/
check_design_LIF_neuron_net.txt  report_timing_LIF_neuron_net.txt
report_area_LIF_neuron_net.txt   summary_report_LIF_neuron_net.txt
report_power_LIF_neuron_net.txt
```
files in output_files folder.
```
$ ls output_files/
LIF_neuron_net.ddc  LIF_neuron_net.sdf   LIF_neuron_net.v
LIF_neuron_net.sdc  LIF_neuron_net.spef
```

## perform layout
Copy to ./Troch25HW/layout/ from ./EDAS-Tool-Examples/design-compiler-synthesis/.
```
cp ../../../EDAS-Tool-Examples/place-and-route/detector110.tcl ./
cp ../../../EDAS-Tool-Examples/place-and-route/detector110.globals ./
cp ../../../EDAS-Tool-Examples/place-and-route/detector110.view  ./
```
Change a name of the files.
```
mv detector110.tcl lif_neuron.tcl
mv detector110.globals lif_neuron.globals
mv detector110.view lif_neuron.view
```
  
lif_neuron.tcl file was changed for lif_neuron.v.
```
set model_name "detector110.v"
->
set model_name "LIF_neuron"
```

lif_neuron.globals file was changed for lif_neuron.v.  
```
set init_top_cell {detector110}
set init_verilog {../synthesis/output_files/detector110_net.v}
->
set init_top_cell {LIF_neuron}
set init_verilog {../dc_syn/output_files/LIF_neuron_net.v}
```

lif_neuron.view file was changed for lif_neuron.v.
```
create_constraint_mode -name timing_cons -sdc_files {../synthesis/output_files/detector110_net.sdc}
->
create_constraint_mode -name timing_cons -sdc_files {../dc_syn/output_files/LIF_neuron_net.sdc}
```
  
the commad to run innovus.
```
innovus
```
  
Then, run "source LIF_neuron_layout.tcl".
the result of layout  
<img width="1920" height="1050" alt="image" src="https://github.com/user-attachments/assets/d6e46817-536d-450a-bc56-680114c248a5" />  
<img width="1920" height="1050" alt="image" src="https://github.com/user-attachments/assets/5901d9af-b7b6-4738-a132-855812822328" />  
<img width="1920" height="1050" alt="image" src="https://github.com/user-attachments/assets/2746f175-9660-4c0d-b617-402a85e9f22f" />  
