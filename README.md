# nasscom-vsd-soc-design-program
Repository for the workshop "Digital VLSI SoC Design and Planning"
## Lab Day 1 : Inception of open-source EDA, OpenLANE and Sky130 PDK
The objective is to perform the synthesis of the 'picorv32a' design and calculate the resulting flop ratio.

The first 5 steps are the following:
```bash
  # Step 1: Change directory to the OpenLANE flow directory within the OpenLANE working directory
  $ cd Desktop/work/tools/openlane_working_dir/openlane

  # Step 2: Execute the aliased 'docker' command to access the bash
  $ docker

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

1) Define the floorplan using the floorplan utility from OpenLANE
2) Review the floorplan output files and calculate the die area
3) Review the floorplan in Magic
4) Perform congestion-aware placement using RePlAcE 
5) Review the placement in Magic

#### 1  Define the floorplan using the floorplan utility from OpenLANE

We run the floorplan utility from OpenLANE to define the floorplan of the picorv32a design:

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

In the same location as in the previous point, we run the following command to execute Magic, providing the paths to the .tech, .lef and .def files:

```bash 
  $ magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

![floorplanmagicrun](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-29%20190140.png)

![magicfloorplanview](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-27%20004417.png)

Once Magic opens, hitting S allows to select and inspect cells at different zoom levels:

![magicfloorplandetails](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-27%20004739.png)

#### 4 Perform congestion-aware placement using RePlAcE

We run the placement utility from OpenLANE with the following command:

```bash
  #Perform congestion-aware placement
  %run_placement
```

![placementrun](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-28%20233317.png)

#### 5 Review the placement in Magic

To review the placement we navigate to the results folder of this specific run (28-01_20-56) and execute the command to launch Magic, 

```bash 
  $ magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

![magicplacementlaunch](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-29%20000730.png)

We observe how the tool has performed the placement in the previous step. The standard cells have been legally placed and we can observe also the power and GND lines.

![magicplacementview](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-29%20001808.png)

![magicplacementviewzoomed](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-29%20001412.png)
 
## Lab Day 3 : Design library cell using Magic Layout and ngspice characterization

The objectives of this day are the following:

1) Observe different placement modes with Magic
2) Clone CMOS inverter standard cell design from repository
3) Explore the inverter layout in Magic
4) Extraction of CMOS inverter SPICE files from Magic
5) Adjustment of CMOS inverter SPICE files for simulation
6) CMOS inverter characterization with ngspice
7) Sky130 Tech File Labs

#### 1 Observe different placement modes with Magic

The placement mode can be controlled through the value of FP_IO_MODE. We set it to 2, run floorplan again and observe it with Magic.

![runfloorplanagain](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-29%20235950.png)
![viewfloorplanmagicagain](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-30%20010513.png)

The IO pins have been placed around the bottom left-hand corner.

#### 2 Clone CMOS inverter standard cell design from repository

The necessary steps and commands are the following:

```bash
  #Step 1: Change directory to the OpenLANE flow directory within the OpenLANE working directory
  $ cd Desktop/work/tools/openlane_working_dir/openlane

  #Step 2: Clone the repository with the inverter cell design
  $ git clone https://github.com/nickson-jose/vsdstdcelldesign.git

  #Step 3: Check that the cloned files are in the intended destination
  $ ls -ltr
```

We observe a new folder 'vsdstdcelldesign' with the 'openlane' folder

![openlanevststdcelldesignfolder](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-30%20093159.png)

#### 3 Explore the inverter layout in Magic

To run Magic using a simpler command than the previous day, the Sky130 technology file needs to be copied into the cloned folder from the repository:

```bash
  #Navigate to the folder containing the Sky130 technology file from the 'openlane' folder
  $ cd ../../pdks/sky130/A/libs.tech/magic/

  #Copy the technology file into the cloned repository folder
  $ cp sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/
```

![copytechfile](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-30%20094253.png)

We navigate to the cloned repository folder, check that the technology file is successfully copied there and run Magic

```bash
  #Navigate to the cloned repository folder
  $ cd /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/

  #Explore the folder
  $ls -ltr

  #Run Magic
  $ magic -T sky130A.tech sky_inv.mag &
```
![magicrun](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-30%20095606.png)

We observe the standard CMOS inverter cell design and check the connectivity of the relevant design nodes.

![stdinvertercell](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-30%20095702.png)

Connectivity of the drains of both p-channel and n-channel MOSFETs to the output Y:

![drainsconnectivity](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-30%20123844.png)

Connectivity of the source of the p-channel MOSFET to VPWR:

![sourcePMOSVPWR](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-30%20123819.png)

Connectivity of the source of the n-channel MOSFET to VGND:

![sourceNMOSVGND](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-30%20123900.png)

#### 4 Extraction of CMOS inverter SPICE files from Magic

While in the cloned standard cell design folder, the following commands needs to be run from tkcon:
```bash
  %extract all

  %ext2spice cthresh 0 rthresh 0
```

![extractall](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-30%20125153.png)

Between the commands we check in another terminal that the file 'sky130_inv.ext' has been created:

![extfilecheck](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-30%20125552.png)

After the ext2spice command we check that the file 'sky130_inv.spice' has been created:

![spicefilecheck](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-30%20125622.png)

#### 5 Adjustment of CMOS inverter SPICE file for simulation

We inspect the initially created SPICE file:

![initialSPICE](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-01-30%20125653.png)

The initial file needs to be edited to perform the following changes:

- Correct the scale to 0.01u instead of 10m
- Include the library models for the pshort and nshort MOSFETs
- Define the pulse exciting the gate of the transistors
- Specify the transient analysis

![finalSPICE](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-01%20000718.png)

#### 6 CMOS inverter characterization with ngspice

To characterize the CMOS inverter we need to find out the rise time and the rise propagation delay.

We start by running the simulation with the following command:

```bash
  $ngspice sky130_inv.spice
```

From a running ngspice session we observe the time plot of the input and output signals with the following command:

```bash
  ngspice 1 -> plt y vs time a
```

![simulationrun](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-01%20001455.png)

Zooming in the time plot, it can be seen that the output signal (red trace) has ~100mV spikes at each pulse transition. 

![uglyspikes](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-01%20001527.png)

These spikes can be halved by changing the value of the output capacitor from 0.27fF to 2fF and re-running the simulation:

![lessuglyspikes](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-01%20001937.png)

**RISE TIME**

The rise time is defined as the difference between the instant where the output signal reaches the 80% of VPWR value (for VPWR=3.3V this is 2.64V) and the instant it reached 20% of VPWR value (0.66V).

After zooming in the plot and clicking on these points, the exact information is displayed in the simulation console:

![twentypercent](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-01%20003348.png)

![eightypercent](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-01%20003356.png)

The rise time is then 2.24522 ns - 2.18188 ns  =  0.06334 ns = **63.34 ps**

**RISE PROPAGATION DELAY**

The rise propagation delay is defined as the difference between the time instant the output signal reaches 50% of VPWR and the time instant the input signal reached the same value. For VPWR = 3.3V it is 1.65V.

By zoooming in the plot and clicking on these points, the exact information is displayed in the simulation console:

![zoom165](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-01%20005034.png)
![info165](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-01%20005113.png)

The rise propagation delay is then 2.21076 ns - 2.15 ns = 0.06076 ns = **60.76 ps**

#### 7 Sky130 Tech File Labs

This section starts by downloading the file 'drc_tests.tgz' and decompressing it in the home directory. The commands are the following:

```bash
  #Download the file
  $ wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz

  #Decompress the .tgz file
  $tar xfz drc_tests.tgz

  #Check that folder drc_tests has been created and explore it
  $ ls -ltr
  $ cd drc_tests/
  $ ls -ltr
```
![img1](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-01%20012105.png)

The next step is to observe the .magicrc file and start Magic to observe an example file: met3.mag 
```bash
  #Check the .magicrc file
  $ gvim .magicrc

  #Start Magic
  $ magic -d XR &
```
![rcfile](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-01%20012250.png)

In Magic we open 'met3.mag'

![magicstartmet3o](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-01%20012726.png)

Clicking on 'Drc-->DRC Update' a few DRC violations will show. To examine them it is possible to click on 'DRC find next error' or to graphically enclose a cell with the cursor and entering the following command in tkcon:

```bash
  %drc why
```

![magicstartmet3](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-01%20155227.png)

The displayed DRC violation involves met3.2. In the skywater documentation, in the 'Periphery Rules' we can see that the met 3.2 rules imposes a minimum spacing from metal3 to metal3 of 0.3 microns, which is violated in this case.

We will fix similar DRC errors in the four following examples

#### Incorrectly set poly.9 rule

The file 'poly.mag' is open and explored with Magic in the same way as the previous example. 

![polymagopen](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20103809.png)
![polymagzoomed](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20012938.png)

The distance between the vertical npolyres cell and the horizontal poly cell is 0.21 microns. This should be triggering a DRC violation on rule poly.9 (poly resistor spacing to poly or spacing (no overlap) to diff/tap > 0.48 microns) but it is not being displayed.

The file 'sky130A.tech' is explored and all occurrences of 'poly.9' are searched for. There are two defined rules: one on the spacing between poly.resistor and N-tap (line 4815) and another one on the spacing between poly.resistor and diffusion. There is no rule defined on the spacing between poly.resistor and poly. We add specific lines from poly.resistor to allpolynonres and save the file.

![norule](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20013427.png)
![polyruleadded1](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20110135.png)
![polyruleadded2](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20110201.png)

We reload the file in Magic without restarting it and run a DRC check. Zooming in the same spot we now receive a DRC violation warning for the poly.9 rule.

```bash
  # Load the tech file
  %tech load sky130A.tech

  # Perform new DRC check
  %drc check

  # DRC check of zoomed area
  %drc why
```
![drccheckpoly9ok](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20110502.png)

#### Implement poly resistor spacing to diff and tap

The rule for spacing between poly.resistor and poly has been fixed but the rule for spacing between poly.resistor and diffusion or tap seems to be for N only. 

![onlyndrc](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20124914.png)

This can be fixed by replacing '*.nsd' with 'alldiff' in the sky130A.tech file, loading it like in the previous step and re-running the DRC check:

![correctalldiff](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20130455.png)

The DRC warning appears now at the bottom of the leftmost poly resistor.

![correctalldiffgraph](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20130626.png)

Another example (to find on our own) is the DRC violation on rule li.7 in 'li.mag'. The width of the local interconnect resistor is 0.21 um, smaller than the required 0.29 um, yet there is no DRC warning.

![li7viol](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20140914.png)

This can be corrected by splitting line 4942 of the tech file and specifying rli in a separate line:

![correctionli7](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20141307.png)

After reloading the tech file and re-running the DRC check we now see the DRC violation warning for rule li.7:

![correctDRCwarningli7](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20141318.png)

#### Implement nwell.4 rule

Loading the 'nwell.mag' file in Magic and running a DRC check, we observe an nwell without any metal-contacted tap that should be triggering a DRC violation on rule nmwell.4 (All n-wells will contain metal-contacted tap). Yet there is no DRC warning displayed.

![nonwell4](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20162028.png)

This rule will be implemented on the basis of a geometric construct: from the nwell, all tapped nwells are subtracted. If the result of this operation is bigger than 0 there will be an error. Two temporary layers (nwell_tapped and nwell_untapped) are defined to perform this operation for the DRC but will not produce any GDS output.

![templayersnwell4](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20165433.png)
![rulenmwell4](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20170608.png)

Saving the tech file, reloading it in Magic and checking the DRC we now see the DRC warning for rule nwell.4 :

![drcwarningnwell4](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20170900.png)

We also check that the DRC warning for nwell.4 disappears when we add a metal-contacted tap to another nwell:

![drcwarningnwell4nomore](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20171122.png)

## Lab Day 4 : Pre-layout timing analysis and the importance of a good clock tree

The objectives of the day are the following:

1) Integrate customised inverter cell design in OpenLANE flow
2) Timing analysis using ideal clocks with openSTA

#### 1 Integrate customised inverter cell design in OpenLANE flow

#### Previous good practice verification

The first step is to verify the following conditions:

- Condition 1: The ports are placed at intersections of vertical and horizontal tracks
- Condition 2: The width and height of the place and route region (inner white contour) must be a multiple of grid cell width and height respectively.

We open the customised inverter cell layout in Magic with the following commands:

```bash
  #Change directory to vsdstdcelldesign
  $ cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign

  #Open custom inverter layout in Magic
  $ -T sky130A.tech sky130_inv.mag &
```

We inspect the tracks.info file of sky130_fd_sc_hd. The new grid in Magic should be defined acording to the spacing and the origin in each dimension of the li1 layer. 

![tracksinfo](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20174023.png)
![newgrid](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20174202.png)

Zooming in around the ports, it can be observed that the ports are at intersections of horizontal and vertical tracks. Condition 1 is verified.

![cond1yes](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20174308.png)

As can be visually checked, the width of the P&R region (internal white contour) is 3 times the grid cell width, and the P&R region height is 8 times the grid cell height. Condition 2 is verified.

![condition2yes](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20180306.png)

#### Saving and inspecting the finalized layout with customised name

The customised layout is saved in tkcon:

```bash
  #Command to save as .mag
  %save sky130_vsdinv.mag
```

 #### Generating LEF file from the customised inverter cell layout

 The lef file for the customised layout is generated from tkcon with the following command:

 ```bash
  #Create lef
  %lef write
```

![createlef](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20180855.png)

We inspect the newly created LEF file:

![leffile](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20181128.png)

#### Copying LEF and LIB files of the customised design to design source folder

The following commands are run to copy and check successful copying of the LEF and LIB files:

```bash
  # Copy lef file
  $ cp sky130_vsdinv.lef /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src

  # Copy lib files
  $ cp libs/sky130_fd_sc_hd__* /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
```
We check the successful copying of the files:

![copiedokleflib](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20182553.png)

#### Edit design configuration TCL file to include new LEF and LIB files

The 'config.tcl' is edited as follows to include the .lef and .lib files of the customised design:

![newconfigtcl](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20205430.png)

#### Run OpenLANE synthesis with customised inverter cell design

We start OpenLANE and prepare the customised design for synthesis with the following commands:

```bash
  #Change directory to openlane
  $ cd /Desktop/work/tools/openlane_working_dir/openlane
  $ docker
```
In the bash we execute this command to enter the OpenLANE flow in interactive mode:
```bash
 ./flow.tcl -interactive
```
In the OpenLANE flow we load the required packages and prepare the design:
```bash
  #load packages
  %package require openlane 0.9

  #Prepare the design
  %prep -design picorv32a

  #Include newly created lefs to OpenLANE flow
  %set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
  %add_lefs -src $lefs

  #Run synthesis
  %run_synthesis
```
![synthesiscustom](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20190047.png)
![synthesiscustom2](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20191627.png)

The synthesis is successfully performed, with a resulting chip area of 147712.9184. However, the slack condition is not being met with tns = -711.59 and wns = -23.89

#### Modify design synthesis parameters to reduce slack

To improve the timing situation we will change the 'SYNTH_SIZING' parameter from 0 to 1, the 'SYNTH_STRATEGY' parameter from 'AREA 0' to 'DELAY 3' and re-launch the synthesis.

```bash
  # Prepare the design indicating that there will be an update of variables of the specific run
  %prep -design picorv32a -tag 02-02_19-54 -overwrite

  # Include newly created lefs to OpenLANE flow
  %set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
  %add_lefs -src $lefs

  # Display current value of variable SYNTH_STRATEGY
  %echo $::env(SYNTH_STRATEGY)

  # Set new value for SYNTH_STRATEGY
  %set ::env(SYNTH_STRATEGY) "DELAY 3"

  # Display current value of variable SYNTH_BUFFERING
  %echo $::env(SYNTH_BUFFERING)

  # Display current value of variable SYNTH_SIZING
  %echo $::env(SYNTH_SIZING)

  # Set new value for SYNTH_SIZING
  %set ::env(SYNTH_SIZING) 1

  # Display current value of variable SYNTH_DRIVING_CELL 
  %echo $::env(SYNTH_DRIVING_CELL)

  # Run synthesis
  %run_synthesis
```

![changeparameterssynth](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20212403.png)

The new synthesis with the previous modifications allows to reduce the slack to 0 for both tns and wns, at the expense of a larger area: 1817300.544

![newsynthres1](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20212522.png)
![newsynthres2](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20212537.png)

#### Floorplan and placement of customised inverter design

Since the slack is reduced we proceed to run the floorplan of the customised design:

```bash
  %run_floorplan
```

![floorplanerror](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20212856.png)

The `run_floorplan` command is failing without apparent reason. Based on the information from `Desktop/work/tools/openlane_working_dir/openlane/scripts/tcl_commands/floorplan.tcl` and the `Floorplan Commands` section in `Desktop/work/tools/openlane_working_dir/openlane/docs/source/OpenLANE_commands.md`, it is possible to use an alternative sequence of the three following commands:

```tcl
%init_floorplan
%place_io
%tap_decap_or
```

![initfloorplan](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20212929.png)
![placeio](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20212943.png)
![tapdecapor](https://github.com/ABM15/nasscom-vsd-soc-design-program/blob/main/Screenshot%202025-02-02%20213004.png)

We observe the placement with Magic and expand the cell to observe the connections in a more detailed way.

#### 2 Timing analysis using ideal clocks with openSTA

-------- Since slack has been removed, to illustrate the timing troubleshooting process we will depart from the initial synthesis with (-711, -23.89)

Now we create pre_sta.conf in openlane directory and my_base.sdc in src directory of the design

We run STA and no improvement is observed.

We limit the maximum fanout number to 4 and try again. No improvement (-23.90)

We examine the elements with the largest slew and delay values.



















  





