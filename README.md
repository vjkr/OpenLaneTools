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

![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/c3c0d719-4c90-4c0e-b1b9-ccb59778fcf3)
Exit yosys and analyze synth.v file
![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/947c3825-8f26-4b3b-915c-fda50159628a)
5. Check with json file, whether there is any differnece.
![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/b7ea6e56-90b5-402d-bd7c-5e574b9ac9ae)
It is evident that yosys has generated exact same files for both the conditions. Hence config.json is not related to yosys. Care should be taken that yosys maybe different than synthesis part in Openlane.
6. Next we will analyze by writing different modelling style. Following is the conclusion
![image](https://github.com/vjkr/OpenLaneTools/assets/16399079/d3c90e12-06f0-49f0-bd52-32121b4439de)
Somehow we ended with same synthesis!
