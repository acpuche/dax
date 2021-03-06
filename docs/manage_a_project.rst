Manage a Project
================

Table of Contents
~~~~~~~~~~~~~~~~~

1.  `Check Why an Assessor Failed <#check-why-an-assessor-failed>`__
2.  `Set/Reset Assessors to Run <#set/reset-assessor-to-run>`__
3.  `Run an XnatCheck on Your Project <#run-an-xnatcheck-on-your-project>`__
4.  `Reset Sessions to Force DAX to Update Again <#reset-sessions-to-force-dax-to-update-again>`__
5.  `Run dax_update Manually on a Project (Advanced Users) <#run-dax_update-manually-on-a-project-(advanced-users)>`__
6.  `Run dax_launch Manually on a Project (Advanced Users) <#run-dax_launch-manually-on-a-project-(advanced-users)>`__
7.  `Common and Spurious Errors You May Encounter <#common-and-spurious-errors-you-may-encounter>`__
8.  `Unable to Read Experiments for Project: XXXXXXXX <#unable-to-read-experiments-for-project:-xxxxxxxx>`__
9.  `Restarting a Job <#restarting-a-job>`__
10. `Project Settings Files <#project-settings-files>`__
11. `Adding Directories Caused by OSError <#adding-directories-caused-by-oserror>`__
12. `Settings Directory is Missing from tmp Folder <#settings-directory-is-missing-from-tmp-folder>`__
13. `Verifying the Spider is Waiting to get Uploaded to XNAT <#verifying-the-spider-is-waiting-to-get-uploaded-to-xnat>`__

----------------------------
Check Why an Assessor Failed
----------------------------

Each assessor has a procstatus. If you look at a session view and specifically at the assessor list, you can see the column Procstatus.png (see below):

	.. image:: images/manage_project/assessor_list.png

An assessor with the status JOB_FAILED means that the script failed to run on the cluster. To understand why, the user can look at the OUTLOG file under the assessor. If the file is not present, you can check the Uploading queue on your gateway running dax in the OUTLOG folder. When you have located the file, you can see the error generated by the script and try to solve them.

--------------------------
Set/Reset Assessors to Run
--------------------------

If you need to set an assessor to run or reset a large number of assessors to run because they failed, you can use XnatSwitchProcessStatus. We are going to reset all the dtiQA_v2 assessors on our test project VUSTP to NEED_TO_RUN because we want them to rerun:

- XnatSwitchProcessStatus -p VUSTP -s NEED_TO_RUN -t dtiQA_v2 -d

-d means that we want to delete the previous resources. In an other example, we want to run again all the fMRIQA that failed because we fixed the problem:

- XnatSwitchProcessStatus -p VUSTP -s NEED_TO_RUN -t fMRIQA -f JOB_FAILED -d

Sometimes, an assessor is used as an input for an other assessor (TRACULA uses FreeSurfer outputs). If you rerun a FreeSurfer for example on the subject number 1, you might want to set the TRACULA to NEED_INPUTS to wait for FreeSurfer to have the valid inputs to rerun as well. To do so, you can use the options -n following by the proctype:

- XnatSwitchProcessStatus -p VUSTP --subj VUSTP1 -s NEED_TO_RUN -t FS -d -n TRACULA_v1

You should be able now to restart all the jobs you want/need on XNAT.

--------------------------------
Run an XnatCheck on Your Project
--------------------------------

Xnatcheck is useful to get a list of assessors from XNAT that fit specific criteria. For example, you want to get the list of all the assessors that failed to restart, you can use the following command:

- Xnatcheck -p VUSTP --filters procstatus=JOB_FAILED

The result is the following:

::

	################################################################
	# XNATCHECK #
	# #
	# Usage: #
	# Check XNAT data (subject/session/scan/assessor/resource) #
	# Parameters : #
	# Project(s) -> VUSTP #
	# Resource Delimiter -> -- #
	# filters String -> ['procstatus=JOB_FAILED'] #
	################################################################
	===================================================================
	INFO: Creating your filters from the options.
	* regular filter: procstatus = JOB_FAILED
	
	INFO: extracting information from XNAT:
	WARNING: extracting information from XNAT for a full project might take some time. 
	Please be patient.
	
	- VUSTP
	INFO: Number of XNAT object found after filters:
	-------------------------------------------
	| Project ID | Number of Objects |
	-------------------------------------------
	| VUSTP | 18 |
	-------------------------------------------
		
	object_type,project_id,subject_label,session_type,session_label,as_label,as_type,
	      as_description,as_quality
	assessor,VUSTP,VUSTP1,MR,VUSTP1a,
	      VUSTP-x-VUSTP1-x-VUSTP1a-x-nonrigid_reg_to_ATLAS,nonrigid_reg_to_ATLAS,
	      JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP3,MR,VUSTP3a,
	      VUSTP-x-VUSTP3-x-VUSTP3a-x-T1-x-FSL_First,FSL_First,JOB_FAILED,
	      Job Pending
	assessor,VUSTP,VUSTP3,MR,VUSTP3a,
	      VUSTP-x-VUSTP3-x-VUSTP3a-x-nonrigid_reg_to_ATLAS,nonrigid_reg_to_ATLAS,
	      JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP4,MR,VUSTP4a,
	      VUSTP-x-VUSTP4-x-VUSTP4a-x-MPRAGE-x-VBMQA,VBMQA,JOB_FAILED,
	      Job Pending
	assessor,VUSTP,VUSTP4,MR,VUSTP4a,
	      VUSTP-x-VUSTP4-x-VUSTP4a-x-nonrigid_reg_to_ATLAS,nonrigid_reg_to_ATLAS,
	      JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP5,MR,VUSTP5a,
	      VUSTP-x-VUSTP5-x-VUSTP5a-x-nonrigid_reg_to_ATLAS,nonrigid_reg_to_ATLAS,
	      JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP6,MR,VUSTP6a,
	      VUSTP-x-VUSTP6-x-VUSTP6a-x-nonrigid_reg_to_ATLAS,nonrigid_reg_to_ATLAS,
	      JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP7,MR,VUSTP7a,
	      VUSTP-x-VUSTP7-x-VUSTP7a-x-nonrigid_reg_to_ATLAS,nonrigid_reg_to_ATLAS,
	      JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP8,MR,VUSTP8a,
	      VUSTP-x-VUSTP8-x-VUSTP8a-x-nonrigid_reg_to_ATLAS,nonrigid_reg_to_ATLAS,
	      JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP8,MR,VUSTP8b,
	      VUSTP-x-VUSTP8-x-VUSTP8b-x-nonrigid_reg_to_ATLAS,nonrigid_reg_to_ATLAS,
	      JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP9,MR,VUSTP9a,
	      VUSTP-x-VUSTP9-x-VUSTP9a-x-LST_v1,LST_v1,JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP9,MR,VUSTP9a,
	      VUSTP-x-VUSTP9-x-VUSTP9a-x-LST_vDEV0,LST_vDEV0,JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP9,MR,VUSTP9a,
	      VUSTP-x-VUSTP9-x-VUSTP9a-x-MPRAGE-x-VBMQA,VBMQA,JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP9,MR,VUSTP9a,
	      VUSTP-x-VUSTP9-x-VUSTP9a-x-nonrigid_reg_to_ATLAS,nonrigid_reg_to_ATLAS,
	      JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP9,MR,VUSTP9b,
	      VUSTP-x-VUSTP9-x-VUSTP9b-x-LST_v1,LST_v1,JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP9,MR,VUSTP9b,
	      VUSTP-x-VUSTP9-x-VUSTP9b-x-LST_vDEV0,LST_vDEV0,JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP9,MR,VUSTP9b,
	      VUSTP-x-VUSTP9-x-VUSTP9b-x-MPRAGE-x-VBMQA,VBMQA,JOB_FAILED,Job Pending
	assessor,VUSTP,VUSTP9,MR,VUSTP9b,
	      VUSTP-x-VUSTP9-x-VUSTP9b-x-nonrigid_reg_to_ATLAS,nonrigid_reg_to_ATLAS,
	      JOB_FAILED,Job Pending
	===================================================================

You can then check the different errors for each assessor and restart the assessors using XnatSwitchProcessStatus as we saw earlier. You can also modify the header of the output to have more information (see available header name with -printformat). For example, to see the walltime and memory used as well as the starting date for the jobs that are COMPLETE for the session VUSTP1a:

- Xnatcheck -p VUSTP --filters procstatus=COMPLETE session_label=VUSTP1a --format assessor_label,proctype,procstatus,walltimeused,memused,jobstartdate

The output now for the csv is:

::

	object_type,assessor_label,proctype,procstatus,walltimeused,memused,jobstartdate
	assessor,VUSTP-x-VUSTP1-x-VUSTP1a-x-1001-x-dtiQA_v2,dtiQA_v2,COMPLETE,
	      17:02:43,3127140,2015-02-04
	assessor,VUSTP-x-VUSTP1-x-VUSTP1a-x-1001-x-dtiQA_v3,dtiQA_v3,COMPLETE,
	      16:43:45,3135972,2015-02-04
	assessor,VUSTP-x-VUSTP1-x-VUSTP1a-x-301-x-FSL_First,FSL_First,COMPLETE,
	      00:22:17,1613624,2015-02-04
	assessor,VUSTP-x-VUSTP1-x-VUSTP1a-x-301-x-Multi_Atlas,Multi_Atlas,COMPLETE,
	      1-10:40:20,5585220,2015-02-04
	assessor,VUSTP-x-VUSTP1-x-VUSTP1a-x-301-x-VBMQA,VBMQA,COMPLETE,
	      00:20:13,1380344,2015-02-19
	assessor,VUSTP-x-VUSTP1-x-VUSTP1a-x-FS,FreeSurfer,COMPLETE, , ,2014-09-22
	assessor,VUSTP-x-VUSTP1-x-VUSTP1a-x-White_Matter_Stamper,White_Matter_Stamper,
	      COMPLETE,01:57:14,2254504,2015-02-16
	assessor,VUSTP-x-VUSTP1-x-VUSTP1a-x-dtiQA_Multi,dtiQA_Multi,COMPLETE,
	      16:35:51,3109260,2015-02-04
	assessor,VUSTP-x-VUSTP1-x-VUSTP1a-x-intra_sess_reg,intra_sess_reg,COMPLETE,
	      00:03:34,318328,2015-02-04

-----------------------------------------------------
Run dax_update Manually on a Project (Advanced Users)
-----------------------------------------------------

You can run manually dax_update on a project if you want to update directly a session and not wait for the next time it will run. To do so, you will need to use this command line:

- dax_update ProjectSettings.yaml --project PID --sessions S_ID1,S_ID2

-----------------------------------------------------
Run dax_launch Manually on a Project (Advanced Users)
-----------------------------------------------------

You can run manually a dax_launch on a project if you want to submit jobs (assessors with the status NEED_TO_RUN) to the cluster and not wait for the next time it automatically runs. To do so, you will need to use this command line:

- dax_launch ProjectSettings.py --project PID --sessions S_ID1,S_ID2


--------------------------------------------
Common and Spurious Errors You May Encounter
--------------------------------------------

PyXNAT is still a work in progress. As such, you may encounter errors that make little to no sense. A common one that you may get is this:

DatabaseError:

Unable to Read Experiments for Project: XXXXXXXX
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can get technical details here. Please continue your visit at our home page. Where XXXXXXX will be your XNAT Project ID (like VUSTP). Chances are likely that users don't have access to your project. It's a quick fix.

Restarting a Job
~~~~~~~~~~~~~~~~

Jobs can be restarted using XnatSwitchProcessStatus: 

- XnatSwitchProcessStatus -s NEED_INPUTS -d --select

Note that you can also switch the process status to NEED_INPUTS in the GUI but the associated data is NOT deleted. Thus, the preferred way is to use XnatSwitchProcessStatus.

Project Settings Files
~~~~~~~~~~~~~~~~~~~~~~

The dax_project_settings need to specify an attribute change in the processor variables from the project_settings file. Consider the yaml script from the snapshot. To change scan types in a project settings file, we do:

::

	- name: multi_atlas_v3_0_0_VUIIS_ABCD
	  filepath: Multi_Atlas_v3.0.0_processor.yaml
	  arguments:
	    inputs.xnat.scans.scan_t1.types: "ABCD_T1W3D"

To change the attributes from the "resources" section from the processor, the arguments would be passed thus:

- inputs.xnat.scans.resource.t1_file_fmatch:"\*.nii.gz"

and not as

- inputs.xnat.scans.resource.NIFTI.fmatch

Adding Directories Caused by OSError (only relevant to LDAX)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

[Errno 2] No such file or directory from CRITICAL messages in past 24 hours email

Usually check /scratch/$USER/Modules_tmp, which is based on the project name, not the file name. For instance, this ginko file may have something like the following:

- OSError: [Errno 2] No such file or directory: '/scratch/vuiisccidev/Modules_tmp/MSSeg2016/MSSeg2016_preview_nifti_ginko_settings'
- The MSSeg2016 and MSSeg2016/MSSeg2016_preview_nifti_ginko_settings directories would need to be created

Settings Directory is Missing from tmp Folder (only relevant to LDAX)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We need to check REDCap. Settings files should not be in the /tmp/ folder. Normally, they would be somewhere like: 

:: 

	'/scratch/vuiisccidev/Modules_tmp/MSSeg2016/MSSeg2016_preview_nifti_ginko_settings'

Verifying the Spider is Waiting to get Uploaded to XNAT
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- The upload queue is different from the ACCRE queue
- The ACCRE cluster is not involved in the upload process
- Upload happens from the following directory:

::

	/scratch/$USER/Spider_upload_dir
