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


