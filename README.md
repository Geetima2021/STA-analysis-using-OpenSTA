# Performance characterization for VSDBabySoC comprising of RISC-V core, PLL and DAC

STA timing analysis verifies that the functionality of a design(chip) is intact across various conditions. The analysis consists of three parts timing checks, constraints and libraries. Timing checks includes the setup/hold timing checks – combination of various types of valid timing checks, to ensure the design specifications/constrains are met. A number of STA analysis tools both commercial and open source  are available for analysis of the design. In this repository VSDBabySoC comprising of RISC-V core, PLL and DAC is analysed across different even PVT corners. Initially a design as per [VSD course](https://www.vlsisystemdesign.com/vsd-static-timing-analysis-ii/) is  done using the open source OpenSTA too along with the timing library provided by the Google/Skywater 130 nm PDK]. It also uses TCL command interpreter to read the design, specify timing onstraints and print timing report. Here, the STA analysis using even corners – TT, SS and FF is performed. OpenSTA tool being a standalone executable uses the standard design format for its analysis. 

## Some important terms

Arrival time: The time taken by a signal to reach the end point.

Required time: defines the system specification eg 1GHz frequency or 1ns time period.

Hold slack: It is the differnce between the data arrival time and data required time.

Setup slack: It is the differnce between the data required time and data arrival time.

![hold_setup_slack](https://user-images.githubusercontent.com/63381455/158803543-dcfd8986-a5e2-4440-9fd8-57cb2fc95a4a.png)


Types of hold/setup analysis

 - reg2reg
 - in2reg
 - reg2out
 - in2out
 - clock gating
 - recovery/removal
 - data2data
 - latch (time borrow/time given)

Slew transistion analysis
 - Data(max/min)
 - Clock(max/min)

Load analysis
 - Fanout(max/min)
 - Capacitance(max/min)

Clock analysis
 - Skew
 - Pulse width

## Tools required

- Linux operating system
- OpenSTA : a stand alone executable for sta analysis. [This](https://github.com/The-OpenROAD-Project/OpenSTA) repository contains the information on OpenSTA tool along with its installation process
- ngspice: An open source spice simulator for electronic circuits. For installation in Windows and Linux platform visit http://ngspice.sourceforge.net/download.html . 
 
All the necessary files and libraries is included in the repository.

## Delay table of CMOS inverter

CMOS inverter being the basic building block in designing its delay table for the even corners using skywater PDK is generated below. The details of the same can be obained in [CMOS-Circuit-Design-and-SPICE-Simulation-using-SKY130-Technology](https://github.com/Geetima2021/CMOS-Circuit-Design-and-SPICE-Simulation-using-SKY130-Technology) repository.

Table1: Delay table using sky130 tt corner

| **wp/lp** | **x.wn/ln** | **Rise delay (ps)** | **Fall delay(ps)** | **Switching threshold (V)** |
|-----------|-------------|---------------------|--------------------|----------------------------
| wp/lp     | 1(wn/ln)    |         149        |         73       |   0.831765   |  
| wp/lp     | 2(wn/ln)     |         88        |         75        |   0.889839   |  
| wp/lp     | 2.5(wn/ln)   |         73        |         75        |  0.895161    |
| wp/lp     | 3(wn/ln )    |         69         |         76       |   0.895161   |       
| wp/lp     | 4(wn/ln)     |         59         |         78       |    0.916129  |     
| wp/lp     | 5(wn/ln)     |         52         |         80        |   0.929032  |      

Table2: Delay table using sky130 ff corner

| **wp/lp** | **x.wn/ln** | **Rise delay (ps)** | **Fall delay(ps)** | **switching threshold (V)** |
|-----------|-------------|---------------------|--------------------|-----------------------------|
| wp/lp     | 1(wn/ln)    |         114         |         60         | 0.809677                    |
| wp/lp     | 2(wn/ln)    |         69          |         62         | 0.877419                    |
| wp/lp     | 2.5(wn/ln)  |          60         |         62         | 0.887097                    |
| wp/lp     | 3(wn/ln)    |         54          |         62         | 0.891935                    |
| wp/lp     | 4(wn/ln)    |          48         |         64         | 0.914516                    |
| wp/lp     | 5(wn/ln)    |          42         |         65         | 0.935484                    |

Table3: Delay table using sky130 ss corner

| **wp/lp** | **x.wn/ln** | **Rise delay (ps)** | **Fall delay(ps)** | **switching threshold (V)** |
|-----------|-------------|---------------------|--------------------|-----------------------------|
| wp/lp     | 1(wn/ln)    |         226         |         93         |             0.85            |
| wp/lp     | 2(wn/ln)    |         122         |         96         |           0.906452          |
| wp/lp     | 2.5(wn/ln)  |         103         |         97         |           0.908065          |
| wp/lp     | 3(wn/ln)    |          95         |         98         |           0.903226          |
| wp/lp     | 4(wn/ln)    |          76         |         100        |           0.922581          |
| wp/lp     | 5(wn/ln)    |          64         |         102        |           0.935484          

## Analysis of a basic design

Based on the design specification the given figure is analysed. The first step is to write a verilog program for the design and it is located [here](https://github.com/Geetima2021/vsdpcvrd/tree/main/resources/files). This  verilog file is linked with the skywater timing library for analysis.

![main_fig](https://user-images.githubusercontent.com/63381455/155880106-37238762-9551-4e05-9270-50b4c80167fa.JPG)

The analysis of the above design is done using a 15 different PVT corners and the trend on the cell delay and the input slew is observed. 

1. Inorder to invoke the openSTA tool - the terminal has to be at the path where the necessary files for the design are available. 
2. Type``sta <tcl>`` for report generation
3. The verilog file ```netlist.v``` is linked to the ```sky130_fd_sc_hd__tt_025C_1v80``` library using ``` link_design <module_name>```. For the initial analysis the clock period is define as mentioned [here](https://github.com/Geetima2021/vsdpcvrd/blob/main/resources/files/my_run.tcl). 

The following command shows the setup and hold report of the design

```bash
report_checks <switch1> <switch2> switch3>
```
- The worst slack both setup and hold report can be viewed by using a single switch `-path_delay <min_max>` with the `report_checks` command.
- The worst setup slack report can be generated by just the ``report_checks`` command or else using the following switches with ``-path_delay <max>``. 
- Worst hold slack report is generated by adding ``-path_delay <min>`` to the `report_checks` command.
-  Additional switches can be used for more detail description viz `digits` number of digits after the decimal point to report. The default value is the variable default_significant_digits. List of capacitance|slew|input_pins|nets|fanout can be viewed using`-fields` switch. 
<!---  To view the best hold slack either switch ``min_fall/min_rise`` is used with `report_checks` command depending on the worst slack end point edge
-  To view the best setup slack either switch ``max_fall/max_rise`` is used with `report_checks` command depending on the worst slack end point edge-->
-  Detail description of the command available in openSTA can be viwed by typing ```help``` within the sta environment.

The snapshot of the setup and hold report of a PVT corner (TT,1.8V,25℃) as obtained from the OpenSTA tool and its pictorial representation is included below. It shows the worst slack report for setup and hold condition.

![SH_horizontal](https://user-images.githubusercontent.com/63381455/158808585-51225aa8-2137-4e8e-8a8e-e3b9d8a07544.png)

![set_hold_worst](https://user-images.githubusercontent.com/63381455/158806716-c6ab740d-dc11-426c-98dd-22e9ed0d42fe.png)

STA analysis of different PVT corners is performed and the report generated for each corner is included [here](https://github.com/Geetima2021/vsdpcvrd/tree/main/resources/images/OpenSTA_setup_hold). Based on the setup and hold report generated a table is created containing all the information of each PVT corner as obatined from the OpenSTA tool and the concern library. The details of the same can be found [here](https://github.com/Geetima2021/vsdpcvrd/tree/main/resources/images). We have use the NAND gate report for our analysis and observe the trend on cell delay and input slew.

![CD_ip_setup_hold](https://user-images.githubusercontent.com/63381455/159007452-b8179f9f-2fac-4f9e-846a-1a0000798129.JPG)

The variation of the cell delay and input slew with respect to the PVT corner on NAND gate is plotted in the above graph. It shows that the input slew and the cell delay for the NAND gate follows similar trend. Both increases as we move from best case to the worst case scenario which should be the trend. The analysis holds true both for worst setup and hold NAND report as observe in the above graph.


