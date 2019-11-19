# PR_Test

Working example of a single instance of ProjectionRouter. The instance of PR that is used in this example is PR_L3PHIC.

### How to open the project for the first time

This is how you open the project for the first time (this could someday be automated maybe). This will open the vivado GUI. After cloning the firmware-hls repository...

* Navigate to firmware-hls/IntegrationTests/PR_Test
* cd sourceFiles/PR
* unzip xilinx_com_hls_ProjectionRouterTop_1_0.zip
* cd ../../emData
* . download.sh
* cd ../../
* vivado -source generate_project.tcl

### How to open the project after it has been built once

This will also open the vivado GUI.

* Navigate to firmware-hls/IntegrationTests/PR_Test
* vivado PR_Test/PR_Test.xpr

### How to run the behavioral simulation once vivado is open

Flow -> Run Simulation -> Run Behavioral Simulation

There is currently a waveform saved in the repo called CurrentWaveform.wcfg, which will open automatially. It shows the bx in & out of the PR, the registers driving the number of entries in the input memories, many of the interesting ports for input & output memories, and the contents of the output memory BRAMs.

### How to incorporate a new version of an IP into the project

Suppose, for example, you want to make some changes to the PR IP and incorporate them into this test project. Here's what you need to do:

* Make the changes you want to make in
	* firmware-hls/TrackletAlgorithm/ProjectionRouter.hh
	* firmware-hls/TrackletAlgorithm/ProjectionRouterTop.h
	* firmware-hls/TrackletAlgorithm/ProjectionRouterTop.cpp
	* firmware-hls/TestBenches/ProjectionRouter_test.cpp
* Navigate to firmware-hls/project
* vivado_hls -f script_PR.tcl  
This will create a zip file at  
firmware-hls/project/projrouter/solution1/impl/ip/xilinx_com_hls_ProjectionRouterTop_1_0.zip
* Navigate to firmware-hls/IntegrationTests/PR_Test/sourceFiles/PR
* delete everything
* copy the above zip file to this directory
* unzip it  
Next, in the vivado GUI...
* Window -> IP Catalog
* Right-click on User Repository -> Refresh All Repositories
* In the sources tab, right-click on PR_L3PHIC -> Upgrade IP
* (if asked, continue with core-container disabled)
* In the sources tab, right-click on PR_L3PHIC -> Generate output products
* Click Generate

### How to generate a new .tcl file after making changes to the project

In the vivado GUI...  
* File -> Project -> Write tcl...
* Uncheck everything, including "copy sources to new project"
* Choose a name for the new tcl (probably just keep generate_project.tcl)
* Click OK

### Brief description of the contents of this part of the repo

* **generate_project.tcl:** tlc script generated by vivado, used to generate the project for the first time.
* **emData/download.sh:** Bash script to download and untar the data files that are needed to populate the input BRAMs in the test bench.
* **sourceFiles/PR:** Directory containing the HLS-generated PR IP, generated by running script_PR.tcl in the firmware-hls/project directory. This is where vivado looks to add the PR to its IP catalog.
* **sourceFiles/PR_0:** A particular instance of the PR IP that is used in this project.
* **sourceFiles/Memory.v:** Verilog memory module used to store the contents of the memories between the processing steps, as well as the input & output memories in the test bench. Also stores the number of entries in the memories.
* **sourceFiles/SectorProcessor_mod.v:** Verilog top-level module which instantiates the many instances (in this test project, just one) of the HLS-generated processing block IPs.
* **sourceFiles/SectorProcessor_test_mod.v:** Verilog test bench to test the verilog top-level module. Also instantiates the needed input & output memories and control signals.
