# Pyneal Tutorial


* [**Download Pyneal**](https://github.com/jeffmacinnes/pyneal)
* [**Pyneal Documentation**](https://jeffmacinnes.github.io/pyneal-docs/)

Within this repository you'll find example MRI data from **GE**, **Siemens**, and **Philips**  environments. The file formats and directory structures have been set up to mimic (as best as possible) how these data would appear during a real-time scan. The only exception is that the data have been de-identified by removing/altering some of the raw header fields. 

---

[toc]

# Setup

The goal of this section is to test the complete Pyneal Pipeline using conditions that are similar to what you'd find across one of the 3 major scanner manufacturers. 

To do so, we'll use the **Scanner Simulator** command line tool that comes with Pyneal. This tool will mock the behavior of an actual scanner by writing image data to an output directory in sequence and at a steady rate. Users can specify the rate at which new images appear by setting the `--TR` parameter in the tool. 

The source data, also included, is actual scan images from 3 different scanner platforms: GE, Philips, and Siemens. These data are meant to simulate the format and directory structure you might encounter while scanning on each of these platforms. This tutorial will allow users to test the complete Pyneal pipeline on any of these platforms prior to any real data collection.

There are instructions below for how to complete this tutorial using each of the platforms. But each platform follows the same general steps:  

* Set up **Scan Simulator**
* Set up **Pyneal Scanner**
* Set up **Pyneal**

Platform-specific instructions:  

* [**GE**](#ge)
* [**Philips**](#philips)
* [**Siemens**](#siemens)


# GE

## GE - Scan Simulator

Inside the `GE_demo` folder, there's a directory named `scanner`. This directory serves as our mock scanner for this tutorial, and follows a structure similar to what you might find on GE scanners. There's a single exam directory (`p1/e123`) that contains two series directories: `s1923` (anatomical image) and `s1925` (functional series) (for more source data detail, see **Appendix: GE source data**) 

We will use the **Scanner Simulator** tool to simulate a new functional series, using `s1925` as our source data. The new series will appear in the exam directory alongside the existing series directories, and will be named `s1926`. 

* open a new terminal and navigate to the **Scanner Simulator** tool:

	```
	cd /Users/jeff/pyneal/pyneal_scanner/simulation/scannerSimulators
	```
	
* launch `GE_sim.py`, specifying paths to the source data and output directory, and setting a TR of 1000ms:

	```
	python3 GE_sim.py /Users/jeff/pyneal-tutorial/GE_demo/scanner/p1/e123/s1925 -t 1000 -o /Users/jeff/pyneal-tutorial/GE_demo/scanner/p1/e123/s1926
	```
	
* You should see details about the current scan, and an option to press `ENTER` to begin the scan:

	```
	-------------------------
	Source slices: /Users/jeff/pyneal-tutorial/GE_demo/scanner/p1/e123/s1925
	Output dir: /Users/jeff/pyneal-tutorial/GE_demo/scanner/p1/e123/s1926
	Total Slices Found:  1080
	TR:  1000
	Vols:  60
	slices per vol: 18
	delay between slices: 0.05555555555555556
	Press ENTER to begin the "scan"
	```
	
* Before starting the simulator, complete the **Set up Pyneal Scanner** and **Set up Pyneal** steps.

## GE - Pyneal Scanner

Configure **Pyneal Scanner** to watch for new scan data in the exam directory for the GE mock scanner. 

* open a 2nd terminal window, and navigate to **Pyneal Scanner**:

	```
	cd /Users/jeff/pyneal/pyneal_scanner
	```
	
* create (or edit the existing) `scannerConfig.yaml` file in this directory to set the `scannerMake` to `GE` and the `scannerBaseDir` to the mock scanner folder. The contents of the `scannerConfig.yaml` file should look like:
	
	```
	pynealSocketHost: 127.0.0.1
	pynealSocketPort: '5555'
	scannerBaseDir: /Users/jeff/pyneal-tutorial/GE_demo/scanner
	scannerMake: GE
	```
	
* launch **Pyneal Scanner**:

	```
	python3 pynealScanner.py
	```
	
* You should see details about the current session, and an indication that **Pyneal Scanner** is attempting to connect to **Pyneal**:

	```
	===============
	SCANNER SETTINGS:
	pynealSocketHost: 127.0.0.1
	pynealSocketPort: 5555
	scannerBaseDir: /Users/jeff/pyneal-tutorial/GE_demo/scanner
	scannerMake: GE
	===============
	Session Dir:
	/Users/jeff/pyneal-tutorial/GE_demo/scanner/p1/e123
	Series Dirs:
	    s1925	 26.0 MB	748310 min, 8 s ago
	    s1923	 23.5 MB	748308 min, 36 s ago
	MainThread -  Connecting to pynealSocket...
	```
	
* There is nothing more to do in this terminal window. Once **Pyneal** is set up and the **Scan Simulator** tool starts, **Pyneal Scanner** will begin processing new images as they appear and sending the data to **Pyneal**. You can monitor the progress via the log messages that appear in this terminal. 

## GE - Pyneal

* Open a third terminal window, and navigate to **Pyneal**. Launch **Pyneal**

	```
	cd /Users/jeff/pyneal
	python3 pyneal.py
	```
	
* Configure **Pyneal** for the GE tutorial demo:
	* **Communication**: Make sure `Pyneal Host IP` is set to `127.0.0.1` and the `Pyneal-Scanner Port` is `5555`. 
	* **Mask**: In the `GE_demo` directory, there is a file named `dummyMask_64-64-18.nii.gz`. Set the mask value in **Pyneal** to use this file. This mask was pre-made to match the volume dimensions of the `GE_demo` scan data. This mask is simply a rectangle positioned in the middle slice of the 3D volume, and is for demonstration purposes only. 
	* **Preprocessing**: Set `# of timepts` to: `60`
	* **Analysis**: Select the `Average` option
	* **Output**: Set the output directory to `/Users/jeff/pyneal-tutorial/GE_demo/output`. Check `Launch Dashboard?`. 

* Start **Pyneal** by pressing `Submit`. 
	* In the **Pyneal Scanner** terminal, you will see messages indicating that **Pyneal Scanner** has succesfully set up a connection to **Pyneal** and that it is waiting for a new `seriesDir` (which will be created once the scan starts. 
	* In addition, you can open a browser window and enter `127.0.0.1:5558` in the URL bar to see the **Pyneal** dashboard. 

## GE - start demo

* In the first terminal window, where the **Scan Simulator** tool is running, press `ENTER` to begin the scan. 

* As the scan is progressing, each of the three terminal windows will update with new log messages. In addition, you can monitor the progress from dashboard in your browser at `127.0.0.1:5558`. 

* As soon at the scan finishes, you can find the **Pyneal** output at `/Users/jeff/pyneal-tutorial/GE_demo/output/pyneal_001`. This directory will have:
	* *pynealLog.log*: log file from the current scan
	* *receivedFunc.nii.gz*: 4D nifi file of the data, as received by **Pyneal**	* *results.json*: JSON file containing the analysis results from the current scan. 	
	
# Philips

## Philips - Scan Simulator

Inside the `Philips_demo` folder, there's a directory named `scanner`. This directory serves as our mock scanner for this tutorial, and follows a structure similar to what you might find using Philips scanners. There's a single exam directory (`Subject001`) that contains two functional series directories: `0001` and `0002` as well as an anatomical image `MPRAGE` (for more source data detail, see **Appendix: Philips source data**) 

We will use the **Scanner Simulator** tool to simulate a new functional series, using `0002` as our source data. The new series will appear in the exam directory alongside the existing series directories, and will be named `0003`. 

* open a new terminal and navigate to the **Scanner Simulator** tool:

	```
	cd /Users/jeff/pyneal/pyneal_scanner/simulation/scannerSimulators
	```
	
* launch `Philips_sim.py`, specifying paths to the source data and output directory, and setting a TR of 2000ms:

	```
	python3 Philips_sim.py /Users/jeff/pyneal-tutorial/Philips_demo/scanner/Subject001/0002 -t 2000 -o /Users/jeff/pyneal-tutorial/Philips_demo/scanner/Subject001/0003
	```
	
* You should see details about the current scan, and an option to press `ENTER` to begin the scan:

	```
	-------------------------
	Source slices: /Users/jeff/pyneal-tutorial/Philips_demo/scanner/Subject001/0002
	Output dir: /Users/jeff/pyneal-tutorial/Philips_demo/scanner/Subject001/0003
	Total Volumes:  93
	Image Dims:  (80, 80, 30)
	TR:  2000
	Press ENTER to begin the "scan"
	```
	
* Before starting the simulator, complete the **Set up Pyneal Scanner** and **Set up Pyneal** steps.

## Philips - Pyneal Scanner

Configure **Pyneal Scanner** to watch for new scan data in the exam directory for the Philips mock scanner. 

* open a 2nd terminal window, and navigate to **Pyneal Scanner**:

	```
	cd /Users/jeff/pyneal/pyneal_scanner
	```
	
* create (or edit the existing) `scannerConfig.yaml` file in this directory to set the `scannerMake` to `Philips` and the `scannerBaseDir` to the mock scanner folder. The contents of the `scannerConfig.yaml` file should look like:
	
	```
	pynealSocketHost: 127.0.0.1
	pynealSocketPort: '5555'
	scannerBaseDir: /Users/jeff/pyneal-tutorial/Philips_demo/scanner/Subject001
	scannerMake: Philips
	```
	
* launch **Pyneal Scanner**:

	```
	python3 pynealScanner.py
	```
	
* You should see details about the current session, and an indication that **Pyneal Scanner** is attempting to connect to **Pyneal**:

	```
	===============
	SCANNER SETTINGS:
	pynealSocketHost: 127.0.0.1
	pynealSocketPort: 5555
	scannerBaseDir: /Users/jeff/pyneal-tutorial/Philips_demo/scanner/Subject001
	scannerMake: Philips
	===============
	Session Dir:
	/Users/jeff/pyneal-tutorial/Philips_demo/scanner/Subject001
	Series Dirs:
	    0002	 73.4 MB	748534 min, 58 s ago
	    0001	 74.0 MB	748522 min, 55 s ago
	    MPRAGE	 23.1 MB	748521 min, 44 s ago
	MainThread -  Connecting to pynealSocket...
	```
	
* There is nothing more to do in this terminal window. Once **Pyneal** is set up and the **Scan Simulator** tool starts, **Pyneal Scanner** will begin processing new images as they appear and sending the data to **Pyneal**. You can monitor the progress via the log messages that appear in this terminal. 

## Philips - Pyneal

* Open a third terminal window, and navigate to **Pyneal**. Launch **Pyneal**

	```
	cd /Users/jeff/pyneal
	python3 pyneal.py
	```
	
* Configure **Pyneal** for the Philips tutorial demo:
	* **Communication**: Make sure `Pyneal Host IP` is set to `127.0.0.1` and the `Pyneal-Scanner Port` is `5555`. 
	* **Mask**: In the `Philips_demo` directory, there is a file named `dummyMask_80-80-30.nii.gz`. Set the mask value in **Pyneal** to use this file. This mask was pre-made to match the volume dimensions of the `Philips_demo` scan data. This mask is simply a rectangle positioned in the middle slice of the 3D volume, and is for demonstration purposes only. 
	* **Preprocessing**: Set `# of timepts` to: `93`
	* **Analysis**: Select the `Average` option
	* **Output**: Set the output directory to `/Users/jeff/pyneal-tutorial/Philips_demo/output`. Check `Launch Dashboard?`. 

* Start **Pyneal** by pressing `Submit`. 
	* In the **Pyneal Scanner** terminal, you will see messages indicating that **Pyneal Scanner** has succesfully set up a connection to **Pyneal** and that it is waiting for a new `seriesDir` (which will be created once the scan starts. 
	* In addition, you can open a browser window and enter `127.0.0.1:5558` in the URL bar to see the **Pyneal** dashboard. 

## Philips - start demo

* In the first terminal window, where the **Scan Simulator** tool is running, press `ENTER` to begin the scan. 

* As the scan is progressing, each of the three terminal windows will update with new log messages. In addition, you can monitor the progress from dashboard in your browser at `127.0.0.1:5558`. 

* As soon at the scan finishes, you can find the **Pyneal** output at `/Users/jeff/pyneal-tutorial/Philips_demo/output/pyneal_001`. This directory will have:
	* *pynealLog.log*: log file from the current scan
	* *receivedFunc.nii.gz*: 4D nifi file of the data, as received by **Pyneal**	* *results.json*: JSON file containing the analysis results from the current scan. 



# Siemens
## Siemens - Scan Simulator

Inside the `Siemens_demo` folder, there's a directory named `scanner`. This directory serves as our mock scanner for this tutorial, and follows a structure similar to what you might find using Siemens scanners. There's a single session directory (`data`) that contains all of the dicom files for two functional series (`000013`, `000015`) and an anatomical series (for more source data detail, see **Appendix: Siemens source data**) 

We will use the **Scanner Simulator** tool to simulate a new functional series, using `000013` as our source data. The new series will appear in the session directory alongside the existing series files, and dicom files will contain the series name `000014`. 

* open a new terminal and navigate to the **Scanner Simulator** tool:

	```
	cd /Users/jeff/pyneal/pyneal_scanner/simulation/scannerSimulators
	```
	
* launch `Siemens_sim.py`, specifying paths to the source directory, series numbers, output directory, and setting a TR of 1000ms:

	```
	python3 Siemens_sim.py /Users/jeff/pyneal-tutorial/Siemens_demo/scanner/data 000013 -t 1000 -n 000014
	```
	
* You should see details about the current scan, and an option to press `ENTER` to begin the scan:

	```
	-------------------------
	Source dir: /Users/jeff/pyneal-tutorial/Siemens_demo/scanner/data
	Total Mosaics Found: 60
	TR: 1000
	Press ENTER to begin the "scan"
	```
	
* Before starting the simulator, complete the **Set up Pyneal Scanner** and **Set up Pyneal** steps.

## Siemens - Pyneal Scanner

Configure **Pyneal Scanner** to watch for new scan data in the session directory for the Siemens mock scanner. 

* open a 2nd terminal window, and navigate to **Pyneal Scanner**:

	```
	cd /Users/jeff/pyneal/pyneal_scanner
	```
	
* create (or edit the existing) `scannerConfig.yaml` file in this directory to set the `scannerMake` to `Siemens` and the `scannerBaseDir` to the mock scanner folder. The contents of the `scannerConfig.yaml` file should look like:
	
	```
	pynealSocketHost: 127.0.0.1
	pynealSocketPort: '5555'
	scannerBaseDir: /Users/jeff/pyneal-tutorial/Siemens_demo/scanner/data
	scannerMake: Siemens
	```
	
* launch **Pyneal Scanner**:

	```
	python3 pynealScanner.py
	```
	
* You should see details about the current session, and an indication that **Pyneal Scanner** is attempting to connect to **Pyneal**:

	```
	===============
	Session Dir:
	/Users/jeff/pyneal-tutorial/Siemens_demo/scanner/data
	Unique Series:
	    000013	60 files 	1113170 min, 51 s ago
	    000015	60 files 	1113170 min, 51 s ago
	    000017	52 files 	1113170 min, 51 s ago
	MainThread -  Connecting to pynealSocket...
	```
	
* There is nothing more to do in this terminal window. Once **Pyneal** is set up and the **Scan Simulator** tool starts, **Pyneal Scanner** will begin processing new images as they appear and sending the data to **Pyneal**. You can monitor the progress via the log messages that appear in this terminal. 

## Siemens - Pyneal

* Open a third terminal window, and navigate to **Pyneal**. Launch **Pyneal**

	```
	cd /Users/jeff/pyneal
	python3 pyneal.py
	```
	
* Configure **Pyneal** for the Siemens tutorial demo:
	* **Communication**: Make sure `Pyneal Host IP` is set to `127.0.0.1` and the `Pyneal-Scanner Port` is `5555`. 
	* **Mask**: In the `Siemens_demo` directory, there is a file named `dummyMask_64-64-18.nii.gz`. Set the mask value in **Pyneal** to use this file. This mask was pre-made to match the volume dimensions of the `Siemens_demo` scan data. This mask is simply a rectangle positioned in the middle slice of the 3D volume, and is for demonstration purposes only. 
	* **Preprocessing**: Set `# of timepts` to: `60`
	* **Analysis**: Select the `Average` option
	* **Output**: Set the output directory to `/Users/jeff/pyneal-tutorial/Siemens_demo/output`. Check `Launch Dashboard?`. 

* Start **Pyneal** by pressing `Submit`. 
	* In the **Pyneal Scanner** terminal, you will see messages indicating that **Pyneal Scanner** has succesfully set up a connection to **Pyneal** and that it is waiting for a new `seriesDir` (which will be created once the scan starts. 
	* In addition, you can open a browser window and enter `127.0.0.1:5558` in the URL bar to see the **Pyneal** dashboard. 

## Siemens - start demo

* In the first terminal window, where the **Scan Simulator** tool is running, press `ENTER` to begin the scan. 

* As the scan is progressing, each of the three terminal windows will update with new log messages. In addition, you can monitor the progress from dashboard in your browser at `127.0.0.1:5558`. 

* As soon at the scan finishes, you can find the **Pyneal** output at `/Users/jeff/pyneal-tutorial/Siemens_demo/output/pyneal_001`. This directory will have:
	* *pynealLog.log*: log file from the current scan
	* *receivedFunc.nii.gz*: 4D nifi file of the data, as received by **Pyneal**	* *results.json*: JSON file containing the analysis results from the current scan. 


# Appendix

## GE source data

### Directory structure

```
GE_demo/
├── dummyMask_64-64-18.nii.gz
├── output
└── scanner  
	└── p1  
	    └── e123  
	        ├── s1923  
	        └── s1925 
``` 

**GE** scanners typically write new slices dicom files to a directory on the scanner console. The path to that directory can be broken apart like `[scannerBaseDir]/[sessionDir]/[seriesDir]`, where  

* **[scannerBaseDir]**: path that remains constant across all scans
* **[sessionDir]**: directories that can change from session to session, named like `p###/e###` where the specific `#` values are unknown in advance.
* **[seriesDir]**: series specific directory named like `s###` where the specific `#` values are unknown in advance. Each new scan during a given exam session will be assigned a unique `s###` dir.

The `GE_demo` folder contains two different series directories, `s1923` and `s1925`. These are nested in the session directory `scanner/p1/e123`

### s1923
*Anatomical Image*

* FSPGR Bravo 
* 162 slices
* Slice dims: 256 x 256
* Voxel dims: 1 x 1 x 1mm

### s1925
*Functional Series* - EPI sequence

* TE: 28ms
* TR: 1000ms
* Flip Angle: 90
* Slices/Vol: 18
* Slice dims: 64 x 64 x 18
* Voxel dims: 3 x 3 x 3.8mm
* Total Vols: 60


## Philips source data

### Directory structure
```
Philips_demo/
├── dummyMask_80-80-30.nii.gz
├── output
└── scanner 
	└── Subject001
		├── 0001
		│   ├── Dump-0000.par
		│   ├── Dump-0000.rec
		│   ├── Dump-0001.par
		│   ├── Dump-0001.rec
		│  ...
		│   ├── Dump-0185.par
		│   └── Dump-0185.rec
		├── 0002
		│   ├── Dump-0000.par
		│   ├── Dump-0000.rec
		│   ├── Dump-0001.par
		│   ├── Dump-0001.rec
		│  ...
		│   ├── Dump-0092.par
		│   └── Dump-0092.rec
		└── MPRAGE
		    ├── Subject001_WIP_MPRAGE_SENSE_2_1.PAR
		    └── Subject001_WIP_MPRAGE_SENSE_2_1.REC
```

In a **Phillips** environment, 3D volumes are typically exported from the scanner and saved to a remote directory during the scan. Each volume appears as a file pair: a `par` header file, and corresponding `rec` binary file. Each new series during the session will get its own series directory, where all of the `par`/`rec` files will appear. The series directories for functional series will be named sequentially, starting with `0000`. 


### 0001

*Functional Series* - EPI sequence

* TR: 1000ms
* Flip Angle: 79
* Slices/Vol: 15
* Slice dims: 80 x 80 x 15
* Voxel dims: 3 x 3 x 4mm
* Total Vols: 186

### 0002

*Functional Series* - EPI sequence

* TR: 2000ms
* Flip Angle: 79
* Slices/Vol: 30  
* Slice dims: 80 x 80 x 30
* Voxel dims: 3 x 3 x 4mm
* Total Vols: 93


### MPRAGE
*Anatomical Image* - EPI sequence

* Volume dims: 176 x 256 x 256
* Voxel dims: 1 x 1 x 1mm

## Siemens source data

### Directory structure

```
Siemens_demo/
├── dummyMask_64-46-18.nii.gz
├── output
└── scanner  
	└── data  
	    ├── 001_000013_000001.dcm  
	    ├── ...
	    ├── 001_000015_000001.dcm
	    ├── ...
	    ├── 001_000017_000001.dcm  
	    └── ...
```

In a **Siemens** environment, all dicom files for all series in a given session will be exported to the same directory. That directory is named `data` here, but may appear with a different name in an actual scan setting. 

Each dicom file represents a 3D volume, with all slices stored in a 2D mosaic format. File names contain both the `series number` and `volume number`, and follow the pattern:

`001_<seriesNumber>_<volumeNumber>.dcm`

The `Siemens_demo` directory contains dicom files for 3 different series: `000013`, `000015`, and `000017`.

### 000013

*Functional Series* - EPI sequence

* TE: 28ms
* TR: 1000ms
* Flip Angle: 90
* Slices/Vol: 18
* Slice dims: 64 x 64 x 18
* Voxel dims: 3 x 3 x 3.8mm
* Total Vols: 60


### 000015

*Functional Series* - Multiband EPI sequence

* TE: 37ms
* TR: 800ms
* Flip Angle: 52
* Slices/Vol: 72
* Slice dims: 104 x 104 x 72
* Voxel dims: 2 x 2 x 2mm
* Total Vols: 60


### 000017
*Anatomical Image* (Note: vitamin tablet placed on participant's left temple (left relative to the participant) in order to confirm orientation)

* Volume dims: 52 x 180 x 192
* Voxel dims: 3 x 1.33 x 1.33mm




