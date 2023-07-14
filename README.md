## [openlane architecture and tools](https://openlane.readthedocs.io/en/latest/flow_overview.html)
# OpenLaneTools
Documenting individual open lane tools
This repository is a continuation from another repo where OpenLane is used for running inverter
The purpose is to learn the maximum possible parts of OpenLane
# Initializing the OpenLane
 1. Open Terminal on Desktop
 2. In this case, browse to make file which is provided by VSDFLOW ```cd /home/vijaykumar/Desktop/openlane/vsdflow/openlane_working_dir/OpenLane ```
 3. check the setup by running ```make mount``` followed by ```./flow.tcl -design spm```
 4. The flow should be complete without any errors.

# Learning Synthesis using Yosys
1. Referring to this URL to understand the intricacies of [Yosys](https://yosyshq.readthedocs.io/projects/yosys/en/latest/CHAPTER_Approach.html#chapter-approach) - Synthesis
2. It seems we do not compulsorily need config.json file for synthesis. This can be inferred from steps 3 and 4. We will analyze the synthesized files with and without config.json.
3. Yosys can be invoked in openlane by typing yosys. Browse to directory containing verilog file ```/openlane/designs/inverter_testing2/src```
   then run ```yosys```
   
![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/8838e909-e668-4a30-a826-1caf1dbab28c)

4. run steps ```read_verilog inverter.v``` followed by ```proc``` then ```opt``` ending with ```write_verilog synth.v```
5. 
![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/c3c0d719-4c90-4c0e-b1b9-ccb59778fcf3)

Exit yosys and analyze synth.v file

![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/947c3825-8f26-4b3b-915c-fda50159628a)

<details>
 <summary> Analysing : This did not make sense. I will keep this for future.</summary>

6. Check with json file, whether there is any differnece.  It is evident that yosys has generated exact same files for both the conditions. Hence config.json is not related to yosys. Care should be taken that yosys maybe different than synthesis part in Openlane.
7. Next we will analyze by writing different modelling style. Following is the conclusion

![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/d3c90e12-06f0-49f0-bd52-32121b4439de)

Somehow we ended with same synthesis! 
I had forgot techmap step. But putting techmap part didnt make any difference
I will keep this pending for now.
</details>

# Learning Synthesis in OpenLane
1. I ran ```./flow.tcl -design spm -from synthesis -to synthesis``` without config.json file. I got an error!

![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/af58817d-b23c-4eb5-ab77-43d958f3e65e)

 2. After adding config.json, flow completes with results in ```/openlane/designs/inverter3/runs/RUN_2023.07.11_10.41.45/results/synthesis```

![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/9c3b589f-415e-4cbd-894a-ee30e37d18b0)


 4. There are two files .sdf and .v as below. these are interesting and are to be understood.

.sdf is standard delay format and .v is the synthesis file. the types of standard cells used from skywater can be read at [naming](https://skywater-pdk.readthedocs.io/en/main/contents/libraries.html#third-party-provided-io-and-periphery-libraries) 

![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/d7c4513f-e672-4e30-ac13-1fbb16a3724d)

5. Let us try editing of config.json file
   diode_insertion_strategy is a mandatory requirement for flow to start.
   With only this prompt, STA fails with

   ![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/b168a38d-4a2d-497f-b37e-2c47eaa5767b)

   Adding clock_port , i.e total 4 prompts in config, the flow completes. The run results are same in terms of sdf and v files!!
   Same with changing diode insertion strategy from 3 to 1.
LEF files are generated in tmp folder. I will check these in future study.

    # Floorplanning
   There is no tool name for performing this other than 'Floor Planning: init_fp, ioPlacer, pdn and tapcell' as mentioned in readthedocs
1. The floorplan folder is empty as of now.
2. without proper config file following error occurs.

   ![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/5bc22a31-29c7-42da-b428-f62993ed6227)

3. Too many errors were encountered , hence skipped to running  ```./flow.tcl -design inverter3 -from synthesis -to floorplan```

   ![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/ca5de7ad-a495-45d3-a38c-978d47348e3d)

4. All folders are empty, seems only planning is done. Results/reports are unavialble.
   i checked log files which are mentioned in above figure. Interesting data are there!!! Endcaps, tie cells, PDN, BUMP Pitches etc................. Let us study these later.

   ![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/8e6d6248-e445-4a8b-b111-c5a392ab5f23)

# Placement

1. ```./flow.tcl -design inverter3 -from synthesis -to placement```
generates

![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/a07135c2-ab62-4a1b-9b8a-f18bd6180fa4)

2. After running placement, results folder for floorplan contains 2 files, def file and odb file. let us study later.

   
3. BAck to placement. Four files as seen in below figure are seen in the results of placement. def files is more detailed version of def file in floorplan.
   nl.v file seems like a netlist file.

   ![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/8a53389e-ac65-4da4-b590-0d437d241477)

pnl.v seems like power netlist file. As it includes power lines too.

4. Some logs observed are
   ```cat designs/inverter3/runs/RUN_2023.07.12_11.04.39/logs/placement/7-global.log```
   gives
![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/fe4ff166-78ce-4c8c-a343-9fa5c39c8f2a)

```cat designs/inverter3/runs/RUN_2023.07.12_11.04.39/logs/placement/8-resizer.log``` 
gives slack reports for everythig, setup, hold etc etc

![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/93f04d87-163c-422d-8bea-2b362b86a194)


   ```designs/inverter3/runs/RUN_2023.07.12_11.04.39/logs/placement/9-detailed.log```

# CLOCK TREE SYNTHESIS CTS
1. ```./flow.tcl -design inverter3 -from synthesis -to cts```
doesnot provide any results on terminal :(

![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/d1299e23-d512-4988-a7c4-f3e475c19ed3)

2. No files in results too. Fortunately few reports are available as seen in below figure.

   ![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/4090abf5-6c39-493e-9850-2c4160e48880)

3. Very similar reports were seen during placement. Not sure what to make of these.

   # Routing
   1. Verified that routing folder under results is empty. run ```./flow.tcl -design inverter3 -from synthesis -to routing```
   2. Many reports are generated and few are in results of routing.
  
      ![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/dd42c1f2-8044-41a6-b5c4-b69c90e5b670)

3. Went through all reports. too many things are in process!. Let us keep this for research.
4.  Studied all files under results. Again interesting. Let us compare all def files using magic!!

   #
