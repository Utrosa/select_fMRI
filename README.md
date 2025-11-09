# Sequence Selection
We developed 13 functional MRI sequences that we are comparing in their ability to record brain signals from subcortical auditory areas.

## Project Goals
1. Select functional EPI sequence with parameters optimzed for data collection from subcortical brain regions.
2. Implement and evaluate use of denosing methods: NORDIC & TAPAS

## Summary of Data Analysis Steps 

All data is stored in 3 different folders, depending on the source (MRI scanner, behavioral software, BIOPAC).

All scripts ran by main_xxx.sh are located in .../scripts except for fMRIprep preprocessing, which is in data_MRI/code (BIDS standard requirement).

### 01 Curation

1. Get the data from the source and save :
	.dcm files from MRI scanner in data_MRI/sourcedata/dicoms
	.acq files from BIOPAC in data_physio/sourcedata
	.txt files from Expyriment in BIDS data_logs/bids
	--> These folders are untouched by next steps to ensure replicable pipeline.
2. Check that data is complete and correctly named.
3. Run pre_import.py to create sidecar files, needed for config file.
4. Set up the config file for BIDSifying MRI data with `dcm2bids`. The configuration doesn't have to include the headscouts and the phoenix ZIP report.
5. Add "dataset_description.json" file to data_MRI/sourcedata/raw
6. Remove any duplicate data.
7. Run [BIDS Validator](http://bids.neuroimaging.io/tools/validator.html) on the dataset to ensure compliance to [the BIDS specification](https://bids-specification.readthedocs.io/en/stable/).

### 02 Importing & Preprocessing

1. Run: `bash import_localizer.sh ; spd-say done`
   
   Outputs:
   - raw MRI data in BIDS with background-corrected T1 images
   - text files are moved to the appropriate MRI data folder (sub-00/ses-00/func/)
   - tmp folder (deleted) with phyio files in .mat and .txt format, and preprocessed physio files
   - tapas regressors and physio data structure per session, subject, and functional sequence.
   - denoised BOLD images (with NORDIC)

2. Exclude incomplete data (e.g.: a functional scan that was interrupted) and remove any duplicate images. Refer to the laboratory log to guide the decision (the log contains info on MRI protocol flow - tracks any changes, errors, modifications, ...).
3. Visually inspect T1 image and denoised images.
4. Add dataset_description to data_MRI/sourcedata/raw/

5. Run: `bash fMRIprep_localizer.sh ; spd-say done`
   - note warnings, bugs, ...

   Outputs:
   - preprocessed fMRI data
   - use sloppy tag for testing the pipeline (faster)

 6. Delete temporary cache directories once preprocessing is successful.

### 03 Analysis

1. Run analyze_localizer.py
	Outputs:
	- filtered artifacts
	- Bunch