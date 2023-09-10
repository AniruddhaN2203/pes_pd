# pes_pd





# Contents
## Day 1
- [SoC Design and OpenLANE](#soc-design-and-openlane)
- [Getiing Familiar with the Open Source EDA Tools](#getting-familiar-with-the-open-source-eda-tools)






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

