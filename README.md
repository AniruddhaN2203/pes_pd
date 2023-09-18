# pes_pd





# Contents
## Day 1
- [How to Talk to Computers](#how-to-talk-to-computers)
- [SoC Design and OpenLANE](#soc-design-and-openlane)
- [Getiing Familiar with the Open Source EDA Tools](#getting-familiar-with-the-open-source-eda-tools)

## Day 2
- [Chip Floor Planning Considerations](#chip-floor-planning-considerations)
- [Library Binding and Placement](#library-binding-and-placement)
- [Cell Design and Characterization Flow](#cell-design-and-characterization-flow)

## Day 3
- [Labs for CMOS inverter ngspice simulations](#labs-for-cmos-inverter-ngspice-simulations)
- [Inception of Layout and CMOS Fabrication Process](#inception-of-layout-and-cmos-fabrication-process)
- [Sky130 Tech File Labs](#sky130-tech-file-labs)

## Day 4
- [Timing Modelling using Delay Tables](#timing-modelling-using-delay-tables)
- [Timing Analysis with Ideal Clocks using OpenSTA](#timing-analysis-with-ideal-clocks-using-opensta)
- [Clock Tree Synthesis TritonCTS and Signal Integrity](#clock-tree-synthesis-tritoncts-and-signal-integrity)

## Day 5
- [Power Distribution Network and Routing](#power-distribution-network-and-routing) 

# Day 1

## How to Talk to Computers
- First we look at the introduction to the RISC-V ISA(Instructiion Set Architecture). Supposing we need to execute a C program on a particular hardware. First the C-program is converted into Assembly Code( here for RISC-V processor). Then the assembly code is converted into binary. An RTL implements this code for the particular layout of the RISC-V processor and the output is visible.
- An application running on a system is usually written with the help of a high level language such as C,C++,Python etc. The code of these applications are compiled with the help of compilers running on a system software(OS). The compiler converts the high level code into assembly intructions for the particular processor. The assembler then converts the instructions into binary which is fed into the layout of the chip that processes every pattern of bits and the program is hence run.

## SoC Design and OpenLANE

**What is a PDK?**
- PDK stands for Process Design Kit.
- It is a collection of files used to model a fabrication process for the EDA tools used to design an IC
  - Process Design Rules.
  - Device Models
  - Digital Standard Cell Libraries
  - I/O Libraries
 
A simplified RTL to GDSII Flow is :
- Synthesis -> Floor/Power Planning -> Placement -> Clock Tree Synthesis -> Routing -> Signoff

- Synthesis - Converts RTL to a ciruit, out of compomments from the standard cell library.
- Floor and Power Planning - Obejctive here is to plan the silicon area and create robust power distribution network to power the chip.
  - Chip Floor Planning - Partition the chip die between different system building blocks and place the I/O pads.
  - Macro Floor Planning - We define the macro dimensions, pin locations and rows are defined.
  - Power Planning - The power distribution network is contructed.
- Placement - Placing the cells on the floorplan rows, aligned with the sites. There are 2 steps: Global and Detailed.
- Clock Tree Synthesis - To deliver the clock to all sequential elements.
- Routing - Implement the interconnect using the available metal layers.
- Sign Off - Perform physical verification such as DRC(Design Rule Check) and LVS(Layout vs Synthesis). Also perform STA(Static Timiing Analysis).

**OpenLANE ASIC Flow**

![Screenshot from 2023-09-10 23-56-09](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/b1bbef29-0748-4fd7-acf8-8c421d599aca)

## Getting Familiar with the Open Source EDA Tools

### Design Preparation Step

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/765e24e4-31bc-454c-8c2f-419978b74e08)
- Let us first go the the working directory using the following commands
```
cd Desktop/work/tools/
```

```
cd openlane_working_dir/openlane/
```
- We now type the command ```docker```.
- This will open the shell as shown in the figure above
- Now we type
```
./flow.tcl -interactive
```
- If the 'interactive' keyword is not present, then the entire flow of the tool is run.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/7c205c9e-58f2-43e1-835e-c73ca47c0ff4)

- Now we must import all the packages required to run the flow, we use the command:
```
package require openlane 0.9
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/c7a07b4a-f833-4696-a2cd-c19cfe4a6b77)
- We will be working with the 'picorv32a' design.
- The src folder has the verilog file and the sdc file of the design
- Now we do the design setup stage using the command:
```
prep -design picorv32a
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/e268e6aa-28dc-465e-8a44-5c7c49b7eec1)
- After preparing the design, we can see that a new 'runs' folder is created.

- To synthesize the design we type
```
run_synthesis
```
- This command invokes yosys, runs the synthesis and the abc commands.
- A long process is observed after typing this command, which for a little over two mintues.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/7a3d0687-8a0e-4754-bf53-7bbdf6bd3a71)
- A synthesis successful message must be displayed.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/c0747e9d-b9ef-4609-ac75-005bb8fc46b6)
- The flop ratio can be calculated by using:
```
No. of flops/No. of cells = 1613/14876 = 0.108
```
- In percentage there is 10.8% of the total number of cells are Flops

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/fa830470-1b1d-4074-a710-8398db096aab)
- Under the runs folder we can check out the netlist file generated after synthesis

# Day 2
## Chip Floor Planning Considerations

**Utilization Factor and Aspect Ratio**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/a1812369-af71-48c4-860c-f76af506400e)
- We consider a simple netlist with a Launch and Capture Flop. It also has an AND and OR gate.
- We then convert it into squares since we need appropriate dimensions

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/f7148664-ea48-420e-92a6-b5ef5ebc30fd)
- Let us consider the areas of the gates and Flops as 1 sq unit

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/6b3f5692-c22e-4c21-857d-d689adf834f0)
- Clubbing them together we get an area of 4 sq units

- The 'core' section of a chip is where the fundamental logic design is placed.
- The 'die' area contains the core and is a small semiconductor are on which the fundamental circuit is fabricated.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/b2c4dbb6-2b0c-477c-9696-a5fe90c282ef)
- Now we put the netlist in the 'core' area and check the utilization.
- Here
```
Utilization Factor = Area Occupied by the Netlist/Total Area of the Core
```
- As we can see here, there is 100% utilization and ```Utilization Factor = 1```.
- In practical scenarios we don't go for such a high utilization factor.
- The 'Aspect Ratio = Height/Width = 1'.

**Concept of Pre Placed Cells**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/ce3ce1b6-578f-4910-924c-d712f174e809)
- We take the above combinational logic as an example

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/a9cd2668-26b2-41ec-9c04-cb5b1d85f2a5)
- We split the circuit into two parts, block 1 and block 2 as shown above

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/6a468eae-b0f1-4eb7-ad74-0ba8ba42626d)
- We extend the I/O pins and black box the boxes.
- Now we separate the boxes and the get their respective I/O ports.
- The use of doing this is that the users can use the blocks multiple times and form the required final circuit with ease.
- They only need to implement the design once and it can be reused.
- These kind of IPs have user defined locations and are placed in the chip before automated placement and routing takes place. These are called pre-placed cells.

**Surrounding Pre-Placed Cells with Decoupling Capacitors**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/10b42d05-a8a2-4a44-bd0c-08102b608e38)

- Huge capacitor filled with charge. The equivalent voltage across the capacitor is similar to what the power supply produces.
- We add the capacitor in parallel to the circuit.
- Everytime the circuit switches it draws current from the decoupling capacitor, whereas the outer network with the power supply and other componets is used to re-charge the capacitor

**Pin Placement**
- In pin placemnt step we use the HDL netlist to determine where a specific pin should be placed in the circuit.
- We join the common pins and try to keep the connections as effecient as possible.
- Pins are placed in the Die area.

**Steps to run FLoorplan using OpenLANE**
- To view floorplan we type
```
run_floorplan
```
in the OpenLANE shell.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/4263c922-01d5-4806-afab-2d81ebbb73e8)

- To open the Floorplan we go to the required directory that is
```
vsduser@vsdsquadron:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/11-09_15-36/results/floorplan
```
using the ```cd``` command.

- Then we type the command:
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

- The following layout is displayed

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/c3eeadc7-821f-45ce-b077-7702623e25bf)
- We can press 's' and then 'v' to align the design to the center of the screen.

- We can right click on the mouse and pess 'z' to zoom into a desired part.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/3d251ffe-c125-41be-a96e-00f2391b89fb)
- We can see here that the I/O ports are equidistant

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/2b79ae12-8c15-44e1-b800-48981f3fc442)
- We can check the details of the ports as follows
  - Hover over a port with your crosshair and press 's' on your keyboard
  - Now open the tkcon command window and type ```what```.
  - This will show you the details of the selected port.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/2ee6d6b3-a7e9-415b-b2dd-1271d16dac2c)
- If we zoom in a little more, we can see the tap cells.
- They are present to prevent latch up conditions which occur in the CMOS devices

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/7e726389-8a96-4572-b6a7-59d5eb0d821a)
- These are the standard cells that are used in the design

## Library Binding and Placement

**Netlist Binding and Initial Place Design**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/9fbd1dd6-34b3-4b38-b92a-c56efe08311f)
- In real life, the logic gates and cells do not have shapes, but are present in the form of rectangles and squares.
- Hence they have dimensions to them and the space where they are placed must be utilized carefully
- The above picture shows an example of a library.
- Library consists of various kinds of cells which have different shapes and sizes, flavours and different timing information.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/adc000d3-4076-4c74-b6d3-96e3e10f311f)
- The components of the netlist are placed in the core area.
- They are placed according to the convenience of distance from the pins.
- When sending signal from FF1 to FF2, according to the circuit requirements, there has to be a very fast propogation of signals. Hence, they are placed very close and buffers are added since there is a small delay for the signal from the pin to reach FF1. The buffers maintain signal integrity

**Viewing the Placement**
- To view the placement we type
```
run_placement
```
in the OpenLANE shell.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/56832e08-c84e-4c78-8a24-73e1b9bdb05f)
- This is the result displayed. As we can see the '/picorv32a.placement.def' file is read.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/0adbe3c9-fe26-4770-9eb0-1ed9ca581402)
- We move one directory up from the 'floorplan' folder using
```
cd ../placement/
```
- To view the placement design we use the command
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/313098df-673a-466d-8cce-169a9932fcce)
- The above is displayed.
- All these standard cells were present at the initial layout of the floorplan.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/2a7cce9b-9550-4eaf-922e-8a64306f05ac)
- If we zoom in we can see the placement of the standard cells in the standard cell rows.

## Cell Design and Characterization Flow

**Cell Design Flow**
- Inputs -> Process design kits(PDKs) : DRC and LVS rules, SPICE models, library and user-defined specs.
- Design Steps -> Circuit Design, Layout Design(Euler Path and Stick Diagram), Characterization.
- Outputs -> CDL(Circuit Description Language), GDSII, LEF, extracted spice netlist(.cir)

**Characterization Flow**
- This is for an inverter.
1) Read the model files.
2) Read the extracted SPICE netlist.
3) Recognize the behaviour of the buffer.
4) Attaching the necessary power sources
5) Apply the stimulus, which is the input signal to the circuit.
6) Read the sub-circuit of the inverter.
7) Provide necessary output capacitances.
8) Provide the necessary simulation commands

**Timing Characterization**
- slew_low_rise_thr = 20%
- slew_high_rise_thr = 80%
- slew_low_fall_thr = 20%
- slew_high_fall_thr = 80%
- in_rise_thr = 50%
- in_fall_thr = 50%
- out_rise_thr = 50%
- out_fall_thr = 50%

- Propogation delay = time(out_fall_thr) - time(in_rise_thr)

- Transition Time
  - On rise: time(slew_high_rise_thr) - time(slew_low_rise_thr)
  - On fall : time(slew_high_fall_thr) - time(slew_low_fall_thr)

# Day 3
## Labs for CMOS inverter ngspice simulations
**IO Placer Revision**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/fabe5ca4-7dde-43f9-a8e4-260ed11ed820)
- The following command can be typed to change the I/O pins placemnt configuration.

## Inception of Layout and CMOS Fabrication Process
**SPICE Deck Creation for CMOS Inverter**
- SPICE Deck is a netlist that has information on:
  - component connectivity 
  - component values
  - identifying the nodes
  - giving a designation to the nodes

**SPICE Simulation and Switching Threshold**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/29e6f5c4-d166-4283-85c2-81947d29f165)
- The CMOS on the right side has a bigger size than the one on the left.
- These waveforms tell us that the CMOS is a very robust device. The characteristics of the CMOS are maintained across a variety of sizes.
- The arrow is pointing to the point where 'Vin = Vout'.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/247e37b7-b3b3-4036-9eaf-2c5380a6c71a)
- Above graph gives details on each point and its significance

**A Git Clone and some other Steps**

- We need to perform a git clone here from a repository that we require, to do the future labs.
- We can type the following command
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```

- Now we need to copy the 'sky130A.tech' file into the directory we just cloned
- We can do this by using
```
cp sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
```
in the follwoing directory shown in the figure

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/c0cefbbc-dfd8-40b0-859e-3603e5589416)

**16 Mask CMOS Process**
1) Selecting a Substrate - Selecting the appropriate substrate to synthsize the design on.
2) Creating active reagion for transistors - Adding layers of SiO2(40nm), Si3N4(80nm) and photoresist(1um). On top of the photoresist we put a mask layer. Pass UV light and remove the mask. Resist is removed. LOCOS(Local Oxidation of Silicon) is performed. Si3N4 is etched.
3) N-Well and P-Well formation - The next masks are used to create the source and drain regions of the MOSFETs. Boron is used to make P-Well using ion implantation. Phosphorus is used to create N-Well. Put the MOSFET in a Drive In furnace.
4) Formation of Gate - Gate formation involves depositing a gate oxide, defining gate patterns using photolithography, depositing gate material, etching to create gates, doping the substrate and insulating the gates.
5) Lightly Doped Drain Formation(LDD) - Lightly doped drain (LDD) formation involves implanting the drain and source regions of a MOSFET transistor with a lighter concentration of dopants to reduce hot electron effect and short channel effect and enhance device performance.
6) Source and Drain Formation - Source and drain formation in a MOSFET transistor typically involves doping the silicon substrate with chemicals such as arsenic or phosphorous for n-type regions (source and drain) and boron for p-type regions (source and drain). High temperature annealing is performed.
7) Steps to form Contacts and Interconnects(local) - Titanium is deposited with a process known as sputtering. Wafer is heated to about 650 - 700 C in an N2 ambient furnace for 60 seconds. TiSi2 contacts are formed.  TiN is also formed used for local communication. TiN is etched using RCA cleaning.
8) Higher Level Metal Formation - Forming contacts and interconnects locally involves depositing a dielectric material like silicon dioxide, patterning it using photolithography, etching contact holes, depositing a barrier metal (e.g., titanium or titanium nitride), filling with a conductor (e.g., aluminum or copper) using chemical vapor deposition (CVD), and then planarizing through chemical-mechanical polishing (CMP).

**Sky130 Basic Layers Layout and LEF using Inverter**
- Now let us look at the layout of a CMOS inverter. To open this we type the command

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/f488e2ef-1afe-42a0-9028-835ae446a797)
```
 magic -T sky130A.tech sky130_inv.mag &
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/4544d764-7b49-4b1f-b3d7-0ae61961bd81)
- The following layout is displayed.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/f10146f9-71a2-4d15-b377-43a72034b9fc)
- We can get to know the details of the inverter by hovering the mouse cursor over it and pressing 's' on the keyboard. Then we can type ```what``` in the tkcon.
- Pressing 's' three times will show what parts are connected to the selected part.

- We shall look at the difference between LEF and Layout. The above image is a Layout.
- LEF represents abstract component data in a machine-readable format for IC libraries, while layout is the physical geometric arrangement of these components on a semiconductor chip.

**Steps to Create Standard Cell Layout and Extract Spice Netlist**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/aa5b0d26-1711-4fb3-84ff-817ca929110f)
![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/7a2bca47-28d8-4c2e-96cd-4fdcb983fcf0)
- DRC errors can be viewed in the tkcon.

To extract Spice Netlist we perform the following steps in the tkcon window:

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/deb99366-4eb3-40b6-8172-ecce6962db33)
- We use the commands
```
ext2spice cthresh 0 rthresh 0 -> this is done to copy the parasitic capacitances
```
- The next command is
```
ext2spice
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/443ea6b4-b82e-4db6-b2d5-5192904507bd)
- We can see that a sky130_inv.spice file is created

## Sky130 Tech File Labs

**Create Final SPICE Deck**
- To start off we look at the minimum value of the layout window.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/6a5d5d43-94d8-4248-8681-5d8d94e924e4)
- We can use 'g' on the keyboard to activate the grid and after selecting a grid by right clicking on the mouse, we type ```box``` in tkcon window to check the minimum value of the layout window.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/fcadbb47-849c-4d5f-a15d-4612fb015be6)
- Next we need to open the spice file using the command
```
gedit sky130_inv.spice
```
- We need to configure it to the above specifications.

**Characterize Inverter using Sky130 Models**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/548d6ace-9d74-47f4-906f-4614c1babaca)
- We now plot the graph for output vs input sweeping the time.
- We first use the command
```
ngspice sky130_inv.spice
```
- In the ngspice shell we use the command
```
plot y vs time a
```
![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/b810d9fe-11e4-44f7-863c-ed19593e0b3c)
- The following graph is displayed.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/400506ec-61c3-458c-9130-7147ec496c6a)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/55c43b65-5c02-451a-bb11-391de8d87571)
- Rise Time -> time taken to rise from 20% to 80% of the max value -> 2.25075e-09 - 2.184e-09 = 0.006675e-09 s.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/93f95ac2-43e2-48de-93e1-07399e20b4b1)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/fc3b2887-d910-4864-ae33-ef02fdb8035f)
- Propogation Delay/Cell Rise Delay -> 2.21379e-09 - 2.15e-09 = 0.06379e-09 s.

**Sky130 PDKS and Steps to Download Magic Tool**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/9f99bcfc-e79c-4c8b-afcf-27f3502b477f)
- Enter the command
```
 wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
```

- Move the file to desktop using
```
mv drc_tests.tgz Desktop/
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/dd984ca9-3692-43f1-b111-f90e5e7a4c08)
- Extract the file using
```
tar xfz drc_tests.tgz 
```
- Do ```ls``` to view all the files in it.

To open the software we type
```
magic -d XR
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/8571a471-4937-4b89-8197-99c2648dfa66)
- We click 'file' and open the 'met3.mag' file.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/cbec69ce-fedb-41cd-af31-0dd9750c1ed9)
![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/b9551c50-a44b-472b-bf63-d4979c2f8caa)
- If we select an area and type ```drc why``` in the tkcon wndow, it will show us the DRC error.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/53fdfab7-7d24-4638-9fb3-855589c89b83)
- To add contact cuts to metal3, first select an area using left and right click. Then hovering over the m3contact we click middle mouse button.

**Fixing DRC Errors**
- There is a DRC error in the poly.mag file in 'poly.9'.
- Open the sky130A.tech file in the editor and make the following changes

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/d786e946-95e0-42c8-a0bd-a83a57697e04)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/4274217a-ba1b-499d-b81a-fa26b4262301)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/efafb2d3-520d-4405-b1ae-731b0308ac99)
- Now open the tkcon window and type
```
load tech sky130A.tech
drc check
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/ebd7630c-128d-46ba-adb7-a30169ba7fe6)
- As we can see the error is fixed.

**DRC Error as Geometrical Construct**
- We open the nwell.mag file.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/4c49e5c5-4738-449c-9df0-048d2034825d)
- We type the above commands

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/40097614-0dfe-4479-b85c-4418e7b43787)
- The following is displayed

**Find Missing or Incorrect Rules and Fix Them**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/338b998a-9a6b-4485-8d2b-095fbbcf3d83)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/cc00bc2f-31ed-45b3-96d8-a12b28697bd4)
- As we can see this is an incorrect implementation and the above rule is violated.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/d689590d-b9a7-43b2-b1da-72e95a9f4dcb)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/71d62451-245b-4010-8246-234727062c8b)
- We make the following changes

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/08900ffa-8f91-4550-be8c-375b4cb42866)
- Now we select the nwell.4 and type the following commands
```
tech load sky130A.tech
drc check
drc style drc(full)
drc check
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/dbb58cd3-4845-4d28-a22b-0228b8260cf6)
- As we can see the error still persists
- We can fix it by the following method.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/4bc70446-9a20-47cd-95b9-d850e170887a)
- Select the existing nwell.4 and make a copy of it by selecting it and clicking 'c'.
- Now select a small area on the nwell.4 and add an 'nsubstratecontact' by hovering over it and clicking middle mouse button.

# Day 4
## Timing Modelling using Delay Tables

**Convert Grid Info to Track Info**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/0f4f84e4-2465-4275-b548-8c5f4fe3020a)
- We must go to the following directory and type
```
less tracks.info
```
![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/3ff966c6-0567-4a44-8639-e128d040b188)
- The 'tracks.info' file is used during the routing stage.
- Routes are the metal traces.
- Since the PNR is an automated flow, we need to specify where all we want the routes to go.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/15744b5a-0fc9-4ad5-b4ce-dfefd26e860d)
- Now we converge the grid definition in the layout to track definition, by typing the following command

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/dbc80960-e5cb-4b75-9f8d-d30c0e48febb)
- The following is the result.
- This shows that the routing of 'li1' layer can happen only along this grid

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/026c5733-e48a-4454-8bed-82a3dd6e1697)
- Having the ports at the intersection of horizontal and vertical tracks ensure that the route can reach that port from the 'y' as well as 'x' direction.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/a1022ba3-b42d-4fed-987f-d4823c0e5550)
- The next requirement is that the width of the cell should be the odd multiple of xpitch which is '0.46' as seen in the 'tracks.info' file.
- As we can see it encloses two full boxes and two halves of one box, totally making three boxes as indicated by the white rectangle.

**Convert Magic Layout to Standard Cell LEF**
- In the tkcon window of the 'sky130_inv.mag' file we type
```
save sky130_vsdinv.mag
```
to make our own .mag file

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/c5cbd3be-e520-4548-b863-51c211664688)
- To make the .lef file we type
```
lef write
```
to make our own lef file.

- Type ```less sky130_vsdinv.lef```.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/4da168e6-7999-4a15-a973-03a767694c51)
- The .lef file is as follows

**Steps to Include New Cell in Synthesis**
![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/a737393f-5802-4fcc-ac89-8fdbccec7307)
- We copy the .mag file that we created to the 'src' folder of picorv32a folder.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/7d6338d2-212a-40c7-853a-3054453f8d82)
- We then perform this copy command.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/acb2c280-ba86-4f84-947e-450fc242eee8)
- Next we modify the 'config.tcl' file in the picorv32a folder as follows.
- Open the OpenLANE interactive window and retrieve the 0.9 package.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/2d3fb8cd-43e6-46c2-b2de-69a338320fe4)
- Type the following
```
prep -design picorv32a -tag 16-09_19-58 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs 
```
- Next we type ```run_synthesis```.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/e3fdfe4f-0139-4bd1-8617-b9aa6c54d0e2)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/527db989-1cd9-403a-8b02-3028400befc0)
- The following results are displayed.

- To run floorplan and placement we type
```
init_floorplan
run_placement
```
- Now to view the design we type the command
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/84fad4e0-bda4-4026-a9a4-4dc9bc9edec6)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/7fd1b736-d6dd-4a98-8567-1ee6d0cc5813)
- The following is displayed.
- Zooming into the design using 'z' we can see the sky130_vsdinv than we defined.
- We have plugged in our custon cell in the OpenLANE flow.

## Timing Analysis with Ideal Clocks using OpenSTA

**Configure OpenSTA for Post-Synth Timing Analysis**
- We must create two files

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/c9867e29-810f-4aca-87cb-c71032f823e4)
- The first one must be in the openlane directory
- This file is known as the 'pre_sta.conf' file.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/9e849dac-a4ff-4420-9a48-bf759f6c82e4)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/92bd662a-e176-41c3-afb5-a808630a1af8)
- The second is the my_base.sdc file.
- This should be in the 'src/sky130' directory under the picorv32a directory.

- To run the timing analysis we type
```
sta pre_sta.conf
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/130dfd6a-d980-4cd8-99e0-4ca26d7cd32b)
- Following result is displayed
- There is a slack violation

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/a033e771-31e5-4b36-b783-d37e0fc52001)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/48528ec3-a353-4809-8d67-4238ad7880fc)
- Settinf MAX_FANOUT value to 4 reduces the slack violation.

## Clock Tree Synthesis TritonCTS and Signal Integrity
**Run CTS**
- To run CTS we need to type the command
```
run_cts
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/29f40c8f-71de-43cb-9829-b188b7ba0c97)
- New .v is created

**Timing Analysis with Real CLocks using OpenSTA**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/9946a62e-f301-4eab-afe7-f3127d4e56b6)
- First we type the command ```openroad```.
- Then we read the .lef file using the command
```
read_lef /openLANE_flow/designs/picorv32a/runs/16-09_19-58/tmp/merged.lef
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/168b1183-65aa-4e51-ab9f-8c6e227e188c)
- Then we read the .def file.
```
read_def /openLANE_flow/designs/picorv32a/runs/16-09_19-58/results/cts/picorv32a.cts.def
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/0e3199ee-c1dc-43fb-8097-0c688b685c48)
- We then do
```
write_db pico_cts.db
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/16-09_19-58/results/synthesis/picorv32a.synthesis_cts.v
read_liberty -max $::env(LIB_SLOWEST)
read_liberty -max $::env(LIB_FASTEST)
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/4b35830f-9822-48c7-ab41-5bcfb1eea4f6)
- We read the .src file.
```
read_sdc /openLANE_flow/designs/picorv32a/src/sky130/my_base.sdc
```
- We set the clock
```
set_propagated_clock [all_clocks]
```
- Checking the report
```
report_checks -path_delay min_max -format full_clock_expanded -digits 4
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/f637e66a-db20-4cb6-8b84-75c11267c0eb)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/184e7412-dc84-453c-b2f0-76b40e3588f4)
- Above results are displayed

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/7fd17b40-dfe1-4b1f-b597-fa5b5c911f74)
- We perform it again for a more accurate result

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/a563b159-c6bb-44ec-a44a-e406c16f5ed3)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/19435f73-e4c7-4663-9c21-0780cdd3f2a6)
- Above results are displayed

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/0415b653-7c0b-478d-91c3-e70d0e94d654)
```
report_clock_skew -hold
report clock_skew -setup
```

# Day 5
## Power Distribution Network and Routing

**Build Power Distribution Network**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/02713233-6b98-4280-b70b-c54aea1d9ddb)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/965b7342-ccfa-41ee-9798-4180265a6119)
- To do this first we type
```
gen_pdn
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/8467354c-875a-44e9-8ef1-f5be38bd0cc4)
- We see that there is a change in the DEF.
- To run the rounting we type ```run_routing```.
- To check for DRC errors we need to check the 'tritonRoute.drc' folder
- To extract the parasitics we need to use an extractor engine.
- We use the SPEF Extraction.

**SPEF Extraction**
- To use this engine we need to go to
```
cd Desktop/work/tools/SPEF_Extractor
```
- Next we need to use this command
```
python3 /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/16-09_19-58/tmp/merged.lef /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/16-09_19-58/results/routing/picorv32a.def
```
