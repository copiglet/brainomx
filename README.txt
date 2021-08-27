##############################################################################################################################################################
##############################################################################################################################################################
################################################################## BRAINOMX PIPELINE README ##################################################################
##############################################################################################################################################################
##############################################################################################################################################################

The current BrainoMX pipeline consists of Structural Segmentation(Tissue and Brain Region Segmentation) and Resting State Functional Network Connectivity 
analysis, with an option of Head Fat Segmentation. There's also a mode to perform low resolution brain tissue segmentation. All the modes yield a quantitative 
subject and physician-friendly report for each input subject. The pipeline currently supports only T1w structural MRI and T2*w resting state fMRI DICOM images 
as input.

##############################################################################################################################################################
####################################################################### PRE-REQUISITES #######################################################################
##############################################################################################################################################################

1) FreeSurfer needs to be installed beforehand
2) FreeFurfer license text file
3) Docker engine with at least 8GB(16GB ideal) memory usage space
4) Python 3.8 or higher

##############################################################################################################################################################
###################################################################### ONE-TIME SETUP ######################################################################
##############################################################################################################################################################

If the BrainoMX solution is going to be run on the system for the first time, the following commands need to be executed to install relevant python packages 
and load necessary docker images:
1) pip3 install -r requirements.txt
2) docker load < parcellations_v2.tar.gz
3) docker load < make_html_v2.2.tar.gz

##############################################################################################################################################################
#################################################################### RUNNING THE PIPELINE ####################################################################
##############################################################################################################################################################

The base directory contains the 'input' folder which consists of subject directories. Each subject directory should begin with the word 'sub-' followed by the 
subject ID without any special characters or space(alphanumeric is tolerated). Each subject folder should contain 'anat' folder which holds sMRI dicom images 
and/or 'func' folder which holds the rs-fMRI dicom images. The input directory and all of its contents should follow the directory structure and naming 
conventions as shown below:

<base directory>
---------------<input*>
-----------------------<sub-XXXXX>
-----------------------------------<anat*>
-----------------------------------------<dicom images>
-----------------------------------<func*>
-----------------------------------------<dicom images>

-----------------------<sub-YYYYY>
-----------------------------------<anat*>
-----------------------------------------<dicom images>

* Directory names should be exactly as shown above for successful processing 

STEP 1: Go to the directory containing the downloaded scripts
cd ~/folder/containing/brainomx/pipeline/files/

STEP 2: Run the BrainOMX Pipeline
The BrainoMX pipeline takes three required arguments namely '--mode', '--base_dir' & '--fs_license_path', and one optional argument i.e. '--head_fat_segmentation'
The path to a log file location also needs to be provided. The process can be run either in the foreground or in the background.

Running in the foreground:
python3 BrainoMX_pipeline.py -m <mode> -hfs -b /path/to/base/directory/ -fsl /path/to/freesurfer/license/file/

Running in the background:
nohup python3 -u BrainoMX_pipeline.py -m <mode> -hfs -b /path/to/base/directory/ -fsl /path/to/freesurfer/license/file/ > <directory/of/logs/log_file.log> &

-m, --mode ============================> Mode of processing: 
							'struc' for high resolution structural processing, 
							'lowres_struc' for low resolution structural processing(only brain tissue segmentation)
							'func' for resting-state functional processing pipeline
-hfs, --head_fat_segmentation =========> Optional argument to perform head fat segmentation
-b, --base_dir ========================> Absolute path to the base directory containing the 'input' directory of subject dicoms
-fsl, --fs_license_path ===============> Absolute path to the freesurfer license text file

Few sample commands:
-> Structural processing(high res): 
	nohup python3 -u BrainoMX_pipeline.py -m struc -b /home/pmx/base_dir -fsl /home/pmx/freesurfer_license.txt > brainomx/logs/struc_logs_file.log &
-> Functional processing: 
	nohup python3 -u BrainoMX_pipeline.py -m func -b /home/pmx/base_dir -fsl /home/pmx/freesurfer_license.txt > brainomx/logs/func_logs_file.log &
-> Structural processing(high res) with head fat segmentation: 
	nohup python3 -u BrainoMX_pipeline.py -m struc -hfs -b /home/pmx/base_dir -fsl /home/pmx/freesurfer_license.txt > brainomx/logs/struc_hfs_logs_file.log &
-> Functional processing with head fat segmentation: 
	nohup python3 -u BrainoMX_pipeline.py -m func -hfs -b /home/pmx/base_dir -fsl /home/pmx/freesurfer_license.txt > brainomx/logs/func_hfs_logs_file.log &
-> Structural processing(low res): 
	nohup python3 -u BrainoMX_pipeline.py -m lowres_struc -b /home/pmx/base_dir -fsl /home/pmx/freesurfer_license.txt > brainomx/logs/struc_lowres_logs_file.log &

On successful completion of the pipeline, a HTML report will be generated in the 'Reports' directory in the specified base directory for each subject.

Ignore any messages like "nohup: ignoring input and redirecting stderr to stdout". You may press ENTER to get back to normal command line. You can check the log 
file in the specified location to monitor the progress of the process.

NOTE: 
1) You cannot run head fat segmentation as a standalone process i.e. without specifying any mode. It has to be run with mode as high res structural(struc) 
or functional(func). Currently, head fat segmentation is not possible with low field images and therefore, cannot be accompanied with mode as lowres_struc.
2) Both functional and structural MRI images should be present for a subject to run the functional processing pipeline.

##############################################################################################################################################################
##############################################################################################################################################################
##############################################################################################################################################################