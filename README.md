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
*Anatomical Image*

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


## Siemens

### Directory structure

```
Siemens_demo/  
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

*Functional Data* - EPI sequence

* TE: 28ms
* TR: 1000ms
* Flip Angle: 90
* Slices/Vol: 18
* Slice dims: 64 x 64 x 18
* Voxel dims: 3 x 3 x 3.8mm
* Total Vols: 60


### 000015

*Functional Data* - Multiband EPI sequence

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