# Pyneal tutorial data


Example raw MRI data from 3 different scanner manufacturers, used for testing/demoing [**Pyneal**](https://github.com/jeffmacinnes/pyneal) real-time fMRI software. 

* [**Download Pyneal**](https://github.com/jeffmacinnes/pyneal)
* [**Pyneal Documentation**](https://jeffmacinnes.github.io/pyneal-docs/)

--

Within this repository you'll find example MRI data from **GE**, **Siemens**, and **Philips**  environments. The file formats and directory structures have been set up to mimic (as best as possible) how these data would appear during a real-time scan. The only exception is that the data have been de-identified by removing/altering some of the raw header fields. 

See below for specific details on each set of example data

* [**GE**](#ge)
* [**Philips**](#philips)
* [**Siemens**](#siemens)

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
*Functional Series* - EPI sequence

* TE: 28ms
* TR: 1000ms
* Flip Angle: 90
* Slices/Vol: 18
* Slice dims: 64 x 64 x 18
* Voxel dims: 3 x 3 x 3.8mm
* Total Vols: 60


## Philips

### Directory structure
```
Philips_demo/
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

In a **Phillips** environment,  3D volumes are exported from the scanner and saved to a remote directory during the scan. Each volume appears as a file pair: a `par` header file, and corresponding `rec` binary file. Each new series during the session will get its own series directory, where all of the `par`/`rec` files will appear. The series directories for functional series will be named sequentially, starting with `0000`. For structural data 


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