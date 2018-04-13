# Pyneal tutorial data

Example raw MRI data from 3 different scanner manufacturers, used for testing/demoing [**Pyneal**](https://github.com/jeffmacinnes/pyneal) real-time fMRI software. 

* [**Download Pyneal**](https://github.com/jeffmacinnes/pyneal)
* [**Pyneal Documentation**](https://jeffmacinnes.github.io/pyneal-docs/)

--

Within this repository you'll find example MRI data from **GE**, **Siemens**, and **Philips**  environments. The file formats and directory structures have been set up to mimic (as best as possible) how these data would appear during a real-time scan. The only exception is that the data have been de-identified by removing/altering some of the raw header fields. 

See below for specific details on each set of example data

## GE 

### Directory structure

```
GE_demo/  
└── p1  
    └── e123  
        ├── s1923  
        └── s1925 
``` 

**GE** scanners write new slices dicom files to a directory on the scanner console. The path to that directory can be broken apart like `[scannerBaseDir]/[sessionDir]/[seriesDir]`, where  

* **[scannerBaseDir]**: path that remains constant across all scans
* **[sessionDir]**: directories that can change from session to session, named like `p###/e###` where the specific `#` values are unknown in advance.
* **[seriesDir]**: series specific directory named like `s###` where the specific `#` values are unknown in advance. Each new scan during a given exam session will be assigned a unique `s###` dir.

The `GE_demo` folder contains two different series directories, `s1923` and `s1925`. These are nested in the session directory `p1/e123`

### s1923
*Anatomical Image* - corresponds to functional data: **s1925**

* FSPGR Bravo 
* 162 slices
* Slice dims: 256 x 256
* Voxel dims: 1 x 1 x 1mm

### s1925
*Functional Data* - EPI sequence

* TE: 28ms
* TR: 1000ms
* Flip Angle: 90
* Slices/Vol: 18
* Slice dims: 64 x 64 x 18
* Voxel dims: 3 x 3 x 3.8mm
* Total Vols: 60

