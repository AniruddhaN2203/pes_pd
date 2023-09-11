# pes_pd





# Contents
## Day 1
- [SoC Design and OpenLANE](#soc-design-and-openlane)
- [Getiing Familiar with the Open Source EDA Tools](#getting-familiar-with-the-open-source-eda-tools)

## Day 2
- [Chip Floor Planning Considerations](#chip-floor-planning-considerations)




# Day 1
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
