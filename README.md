# VSD_SoC_24
Repository containing information and instructions provided during VSD-IAT

## Glossary
### Floorplanning
Step in which all blocks or macros are placed in the chip (core). It also involves creating the iopin placement, power and ground grids.

Define locations of preplaced cells
	1. Combinational logic can be split up.  

	2. The circuit can be split into "blocks".  

	3. First step is extending io pins.  

	4. The biggest advantage is single implementation and reusing the same implementation  

	5. termed as preplaced as every cells functionality is being implemented 	and placed only once and must be done before placement and routing. Once fixed, they cannot be moved.  

	6. Come up with a design scenario suitable for the functionality.  
    
Some important terms are:
```math 
Aspect\;Ratio = \frac{Height\;of\;the\;core}{Width\;of\;the\;core}
```
**Core** is defined as the area within which the netlist is placed. (The logic)  

**Die** is the area containing the core. It can be thought of as an outline.   

**Core Utilization** defines the area occupied by the cells and macros. 100% means no space for addition  
of logic/circuits (say buffers, amplifiers etc) or the routing wires.   
Hence standard practice is to aim for ~50% to 60% core utilization, leaving enough to make additions and connections  

**Design Exchange File** called DEF and contains definitions of rows, standard cells, nets etc. It may also contain physical design information.

**Library Exchange File** called LEF and contains information regarding cells, metal layers, vias and design rules.



## Lab-1 Inception of Open Source EDA, OPENLANE and the sky130 PDK
### Let the flow begin! 
```
docker
```
Starts the OpenLane container using docker (alias for a larger invocation)
It is made apparent by the shell name changing to BASH.
```
./flow.tcl -interactive     //required step to initialize the full design flow
```
![Running the openlane container & Initializing design flow](vsdimages/invokingopenlane.png)

Following which, it is required to define the packages required, in our case openlane 0.9
```
package require openlane 0.9
```
Before preparing the design, let's go through the file structure
### File Structure
In the directory from which flow.tcl was initialized, use 
```
ls -ltr
```
or 
```
ls
```
To obtain a list of all files and folders.
change directories using ```cd /nameofdirectory```, in this case the designs directory.
![design files directories](vsdimages/designfilesdirectory.png)
The designs Directory is used to store files related to the device, parameters and implementations of aforementioned devices. Here we see multiple designs already created such as a synth_ram, APU etc. Delving further into the filesystem,
```
cd picorv32a
```
The picorv32a is an open source risc-V CPU, available on the yosys github. 
![picoRV32 files and sources](vsdimages/picorv32andsrcfiles.png)
The verilog code is provided inside the src file directory.
It also contains a config.tcl file, a user provided file describing the parameters that the openlane flow tries to adhere to.
![tcl config](vsdimages/configtcl.png)
### Synthesis
Now that we are familiar with the file structure, lets proceed with the synthesis of the design. However, before synthesis we must prepare the design in question. The picoRV32a will be our chosen design. So, to prepare the design, use
```
prep -design picorv32a //Remember to have the same naming between this and the design directory present in the designs/ directory!
```
Once finished preparing, the output will look something like this
![designprep](vsdimages/designprep.png)

Running this made some changes to our files. Again go to the design directory for picorv32, and cd into runs. a new folder with the current date and time will appear, cd into it. This displays all the directories that will be used in the flow. (which currently are empty) Descending into the tmp directory shows us all the temporary design files.
![post design prep](vsdimages/prepdesignrunfile.png)
currently, the information contained within isn't super valuable. 
```
run_synthesis  //runs the synthesis step for whatever design was prepared (picoRV32a)
```
After a short wait, a whole slew of text is displayed, showing the synthesis process. This includes all devices used, the area of the design etc. 
![dffratio](vsdimages/dffratio.png)
In particular, we are interested in the flip-flop ratio. This can be calculated by a simple formula,
```math
Flop\;Ratio = \frac{Total\;Number\;of\;D\;Flip-flops}{Total\;Number\;of\;cells}
```
```math
Flop\;Ratio = \frac{1613}{14876} = 0.1084296854
```
```math
\%Flop\;Ratio = 10.84296854
```
## Lab-2 Floorplanning and Intro to library cells
### 