# README: Documentation of the QPR Verify Tool 2020

Date: 09. Juli 2020
For questions or comments please contact: marko.kleinebuening@kit.edu

---
The publication of the source code of QPR Verify is an ongoing process. 

Currently, there are two options to run and test QPR Verify.

1) We provide a Virtual Maschine with QPR-Verify installed. You can either run QPR Verify yourself or look at provided verification results.

Name of VM:     QPR_Verify-2020.ova --
Link to the VM: https://baldur.iti.kit.edu/qpr/QPR_Verify_2020.ova

	QPR_Verify-2020.ova was exported from VirtualBox
	System parameters:
	OS: 	   Ubuntu 18.04.3 LTS
	RAM: 	   4096 KB
	CPU MHz:   2902.482
	CPU Cores: 1
	username: qpr
	password: there is no password (if you need one for example for an easier sudo setup: enter in terminal: passwd, otherwise try password:qprverify)

2) For Ubuntu 18, we provide the binary files together with libariy files for llvm and other dependencies (will soon be pushed). If you clone these files onto your system and add then to your PATH and LD_LIBRARY_PATH variable you can run QPR Verify on your linux maschine. The viewer for verification results is provided in the folder qpr-report-with-angular-4.
Examples for the variables: 

 	PATH=$YOUR-PATH-TO-QPR/QPR-Verify/install/reldeb-gcc/lib/:$PATH
	
	export LD_LIBRARY_PATH=$YOUR-PATH-TO-QPR/QPR-Verify/install/reldeb-gcc/lib

---

QPR Verify was designed to verify larger projects. The best way to familiarize yourself is to use the VM and follow the instructions to verify the bmi160 project. If you want to test it on a single file "test.c" you should create an empty repository (mkdir QPR) and navigate to that directory (cd QPR). Then you can do as follows: 

	qpr init .. ; qpr add-source-files ../test.c ; qpr murphy ; qpr compile ; qpr check-all-functions-locally 

For more commands and configuration possiblities you should first perform the initalization of an empty folder:

	qpr init ..
Then you get use the help command to get more information:

	qpr help
	qpr help configure-project

We give a description how to start working with QPR Verify based on the Virtual Maschine.

##Quick start (review analysis results):
1) Open QPR-Report (over double click on link on Desktop or qpr2020-artefact folder)
2) Click "Open Folder" and navigate to "~/qpr2020-artefact/Benchmark/BMI160_driver/QPR/01_AnalyzeWithChecksCombined-bound5
3) Click on the tab "Check" and have a look at check details and more

### Overall strucutre
1. Folder: Benchmark
	-BMI160_driver: holds the source files of the bmi160 sensor driver
		-Already finished Analysis results can be found in the subfolder QPR
	-Scripts:holds the scripts helpfull to run different analysis with QPR Verify	

2. Folder: QPR-Verify: holds the executables and libraries necessary to execute QPR Verify. Currently provided are LLBMC, STP, Minisat and some standard-libraries

3. Executable: Reference to the executable for the QPR-Report, which is a GUI to display verification results. Can be opend by a double click or via the terminal 
		 

### Performing a static analysis on the BMI160 project
We give three different ways to perform analysis with QPR-Verify. In general QPR-Verify has to be set up in an empty folder (qpr init <dir>) which we often create inside a project folder. Furthermore, you can always execute "qpr -help" and "qpr help configure-project" for further information about commands.

	-To run an analysis with QPR Verfy run-script setting
	1) open a terminal go into the folder BMI160_driver/QPR/
	2) create a new empty folder for the analysis (mkdir <Name>)
	3) run: qpr init ../.. (the directory ../.. gives the root directory of the source files)
	4) in the folder: Bechmark/Scripts there are a number of possible scripts available
	5) run: qpr run-script ~/qpr2020-artefact/Benchmark/Scripts/AnalyseWithChecksCombined-bound5.qpr
	6) the check results will be placed in the created folder
	7) The results can be viewed with the QPR-Report

	-Different configurations and manual step for step description
	0) the descripted steps in the Paper can be run manually
	1) go into the folder BMI160_driver/QPR/
	2) create a new empty folder for the analysis (mkdir AnalysisReadMe)
	3) run: qpr init ../..
	4) run: qpr find-source-files
	5) run: qpr add-compiler-option "-DSTUB_FIFO_SIZE_BYTES=8"  (this is specific to the BMI160 project)
	6) run: qpr murphy
	7) run: qpr compile
	8) you can configure the project to your liking and run diffrent analysis techniques, the best overview is gained by looking through the .qpr scripts given in the Scripts folder 
		qpr configure-porject LoopUnrollBound:5
		qpr configure-project AnalyzeChecksIndividually:true
		qpr configure-project EnableSlicing:false
	9) run: qpr check-all-functions-locally 			or

		qpr configure-project HavocLimit:1
		qpr check-all-functions-with-call-abstractions		or

		qpr configure-project EntryPoint:bmi160_set_regs	(you can choose any function in the bmi160 project to be an entryPoint for the verification task)
		qpr analyze-globally  					(all checks not reachable from the entryPoint are set to safe)

	-To run an analysis over a given .sh script:
		1) navigate into the Script folder
		2) execute BMI160-LocalAnalysis.sh
		3) The results can be viewed with the QPR-Report, either run: QPR-Report or double klick on the executable in the qpr20-artefact folder

	-Understanding the files and folders in the created directory
		a) Best to use the QPR-Report to have a GUI Overview over all information
		b) All information about the verification task are saved in the directory: files, traces, configuration, checks with results, etc. 
		c) The folder jobs contains the verification tasks created for either a single check or for combined checks. Detailed LLBMC logs can also be found there.

### Use the QPR-Report to understand the verification results
	a) Open the QPR-Report 
	b) Navigate to your verification folder via the "Open Folder" button
	c) A summary of the results and configurations are shown.
	d) If there were compiler messages they are shown in the tab "Compiler"
	e) The "check" tab hols all information about the checks. You can filter them by different options.
	f) By clicking on the check, you move to the file view of the check.
	g) You can also view the error traces of unsafe or cond. unsafe checks.

## Troubleshooting
-If you have trouble with the QPR-Report, it can be that you have to increase the screen resolution so that you can see all checks.
-Please do not move the QPR Verify install or Library files. 
-If you did so anyways you have to adjust the PATH to the qpr executable and eventually also the Path to the libraries needed to run QPR

	PATH=~/qpr2020-artefact/QPR-Verify/install/reldeb-gcc/lib/:$PATH (or your now new location)
	export LD_LIBRARY_PATH=/home/cav/qpr2020-artefact/QPR-Verify/install/reldeb-gcc/lib (or your now new location)

