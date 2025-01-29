# nasscom-vsd-soc-design-program
Repository for the workshop "Digital VLSI SoC Design and Planning"
## Lab Day 1 : Inception of open-source EDA, OpenLANE and Sky130 PDK
The objective is to perform the synthesis of the 'picorv32a' design and calculate the resulting flop ratio.

The first 5 steps are the following:
```bash
  # Step 1: Change directory to the OpenLANE flow directory within the OpenLANE working directory
  $cd Desktop/work/tools/openlane_working_dir/openlane

  # Step 2: Execute the aliased 'docker' command to access the bash
  $docker

  # Step 3: Start OpenLANE in interactive mode
  %./flow.tcl -interactive

  # Step 4: Load the required packages
  %package require openlane 0.9

  # Step 5: Prepare the 'picorv32a' design for synthesis
  %prep -design picorv32a
```
![steps1to5](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/950eb4a67a2848ad0721ae1366ef8e7f135faf8c/Screenshot%202025-01-26%20001924.png)

At this point the synthesis can be started:
```bash
  # Step 6: Run the synthesis
  %run_synthesis
```
![step6](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/950eb4a67a2848ad0721ae1366ef8e7f135faf8c/Screenshot%202025-01-26%20002117.png)

After successful completion of the synthesis we navigate to the reports folder of this specific run (25-01_23-16) and inspect the Yosys stats report file:

![step7](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/a9d8208600c40e73eb06927a184dc04b06d12e45/Screenshot%202025-01-26%20011032.png)

The count of flip-flops is given by 'sky130_fd_sc_hd__dfxtp_2' :

![step8](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/a9d8208600c40e73eb06927a184dc04b06d12e45/Screenshot%202025-01-26%20002632.png)

Count of flip-flops: 1613

Number of cells: 14876

Flop ratio = 1613/14876 = **0.1084 [10.84%]**

## Lab Day 2 : Good floorplan VS bad floorplan and introduction to library cells

The objectives are the following:

1) Run the floorplan utility from OpenLANE
2) Review the floorplan output files and calculate the die area
3) Review the floorplan in Magic
4) Congestion-aware placement using RePlAcE Run congestion-aware placement from OpenLANE to perform the placement
5) Review the placement in Magic

#### 1  Run the floorplan utility from OpenLANE

We run the floorplan utility from OpenLANE to define the floorplan:

```bash
  #Define the floorplan
  %run_floorplan
```
![runfloorplan>](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-26%20132729.png)

#### 2  Review the floorplan output files and calculate the die area

After successful completion of the command we navigate to the results folder of this specific run (26-01_11-54) and inspect the file 'picorv32a.floorplan.def'.

![floorplanresult1](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-26%20132856.png)

![floorplanresult2](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-26%20132916.png)

The width and height of the die are given (in distance units) by the 'DIEAREA' line. The number of distance units per micron is given by the 'UNITS DISTANCE MICRONS'. The current setting is 1000 distance units per micron.

Die width: 660685 d.u. ---> 660.685 microns

Die height: 671405 d.u. ---> 671.405 microns

Die area = 660.685x671.405 = **443587.212425 square microns** 

#### 3 Review the floorplan in Magic







 
