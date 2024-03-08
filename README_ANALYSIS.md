# Readme of the EMG4 project analysis procedure 

## Data handling and preparations

These are the EDF checker settings to check the triggers in the EDF+ file and compare them to the Presentation logfile.

EDF+Checker settings (standard*): 	
Remove illegal characters from the header of BioTrace exported EDF+ files:	check
Fix faulty bytes in the annotations of BioTrace exported EDF+ files:	check
First Flag:	Trigger:
Second Flag:	('
Replacement Char:	t
Prefix:	Trigger:
Postfix:	('
Maximum:	255
Minimum:	0
Specific triggers to ignore (delimitor = ";"):	100; 200; 202
Correct for Pauses in trigger onsets	check
9 triggers	
Error margin: 	50 ms
*Equal Timings but Unequal Numbers:	check Remove Recording Trigger
Equal Numbers but Unequal Timings:	-
No exact Match was ever found:	check Add Logged Trigger
Log file for progress logging of EDF+ Checker:	EDFChecker_log_standard.log
Save modified from MEMORY to new files on DRIVE:	create: "checked_oldfilename.edf"
output directory for new files:	create and map to folder named 'BVA_data_after_EDFchecker'

* EDF+Checker settings (alternative): deselect ‘Remove Recording Trigger’ at ‘Equal Timigs but Unequal Numbers’ in order to retain button press triggers that were recorded closer than 50ms to another trigger, e.g., ratings or answers given almost simultaneously with the following segment or screen.

## Preprocessing EMG data in BrainVision

This section contains the settings from the preprocessing steps in BrainVision Analyzer. This is the information that can be found under "Operations Info"

### Filter
Zero phase shift Butterworth filters.

Global filter settings:
Low cutoff: 20 Hz, time constant 0.0079577471545947669, order 8 (slope 48dB)
High cutoff: 500 Hz, order 8 
Notch filter: 50 Hz

### Rectify
The following channels have been selected for calculation:
Sensor-A:EMG (ra	Sensor-B:EMG (ra	
Remaining channels were removed.

### Edit Channels
Channel Properties:
				Labels:					Position:
Orig. Order	Orig. Label	Chn (+) -	Chn(-)	Radius	 Theta	   Phi	Data Unit		Color		User Properties
1		Sensor-A:EMG (ra	Corrugator			0.0	   0.0	   0.0		µV		Black
2		Sensor-B:EMG (ra	Zygomaticus			0.0	   0.0	   0.0		µV		Black	

Channel positions have been changed.
Channel order has been changed.

### Segmentation
Segmentation relative to reference marker positions
Reference markers:
    Comment    101
Advanced Boolean Expression:
    ---
Segment size and position relative to reference markers:
Start: 0.00 ms, End: 58000.00 ms, Length: 58000.00 ms
Allow overlapped segments? Yes
Skip bad intervals?        No

Data was cached to a temporary file.

*** Data node specific information ***

Number of segments: 66

### Matlab export
Solutions>Create Mat File

*** Create MAT File - Version 1.3 ***

Matlab MAT file name:   checked_1088_Segmentation.mat
Matlab MAT file path:   '../BrainVision/export_paper'

Matlab startup time:    4898 ms
Matlab detection time:  10 ms

Data was written to Matlab as a two-dimensional array.

## Baseline selection

For each trial check the baseline in BrainVision. Select at least 500ms continuous quiet baseline without artifacts. If the corrugator or zygomatic does not contain 500ms baseline then the trial will be rejected. For detailed criteria, see criteria.txt
We've created a new segmentation node for this purpose so that the baseline and trial number are clearly displayed in BrainVision.

*** Segmentation ***
Segmentation relative to reference marker positions
Reference markers:
    Comment    101
Advanced Boolean Expression:
    --- 
Segment size and position relative to reference markers:
Start: -50.00 ms, End: 4050.00 ms, Length: 4100.00 ms
Allow overlapped segments? Yes
Skip bad intervals?        No

The baseline is the same for both channels, so the same time points will be applied to both corrugator and zygomatic. However, a 2nd segment can be added: e.g. 500-1000 and 1500-2000ms. This gives  a more reliable estimate of the baseline. If you have only 1 segment fill in NaN at the 2nd segment. Make sure you always fill in all columns completely.

For each participant, create a txt file  and save it in the folder called baseline_paper. Important, the name of the txt file must match the name of the edf file.

The txt file contains the following tab-separated columns:
Triggers	Start Baseline1	End Baseline1	Corrugator Inclusion (1)/Exclusion (0)	Zygomaticus Inclusion (1)/Exclusion (0)	(Start Baseline2 when not 500ms in one segment)	(End Baseline2)
1	0	2000	1	1	NaN	NaN

## Preprocessing logfile data in Python

Using two Python scripts we analyzed the logfile of each participant and extracted the embedded ratings as well as the response to the comprehension questions. We saved the results to a csv-file for further analysis. The file EMG4_analysis_functions.py contained all custom made functions necessary to run the EMG4_analyses_logfiles.py script.

## Preprocessing questionnaire data from Qualtrics

For each of the 6 questionnaires in Qualtrics, go to ‘Data & Analysis’ and select relevant data
-	Filter Data Li (only responses collected between 2-6-22 and 17-9-22, finished surveys (excludes pilots and tests), excludes pp 4542 (no .edf) from Vragenlijst 1.4, Vragenlijst 2, and Exit)
-	Export & Import > Export Data > 
o	Download all fields 
o	Use numeric values
o	In ‘more options’, select ‘Use commas for decimals’!!

Continue data preprocessing in R.

### Exit questionnaire

“EMG4_exit_questionnaire_data.Rmd”
For the exit questionnaire, filter raw data and save as "subset_exit_data_EMG4.csv", this file only includes columns sjCode, Gender, Age, Diff, Concentration, Sleep.
-	barplot ages
-	barplot genders

### Story Fairness ratings

“EMG4_story_fairness_data.Rmd”
For the Story Fairness ratings, (for list #1, 2, 3, and 4), filter raw data, and center ratings 1-7. Pivot the data to a long format. Save the combined dataset as: "fairness_all_EMG4.csv"
-	boxplots/countplots rating variation per participant

### Justice Sensititvity Trait questionnaire

“EMG4_justice_trait_data.Rmd”
For the Justice Sensitivity Trait questions, filter the raw data. Compute Cronbach’s Alpha for the selected questions (see “grouping_Justice_traits.xlsx”)
For the selected questions center the ratings and compute a mean rating, i.e. Mean Justice Score, and save as "justice_means_EMG4.csv"


## Matlab computing proportional EMG data

The matlab script EMG4_matlab_100msbins.m was used to analyze the EMG data that was exported from BrainVision after preprocessing. It opens the matfile which contains corrugator and zygomatic data as well as timing info and markers. It stores the results in 100ms bins for the relevant segments, as described in the preregistration. The result is stored in a txt file. Open the txt file in Excel and save as xlsx for further processing. 

The script yields four different text files, one for each segment and the whole trial result:
- EMG4_Bin_AffectReason_60sjs.txt
- EMG4_Bin_Morality_60sjs.txt
- EMG4_Bin_Neutral_60sjs.txt
- EMG4_Bin_StateAdjective_60sjs.txt
- EMG4_Bin_whole_trial_60sjs.txt


## Statistical analysis in R

### EMG4_preprocessing_data.Rmd

The script prepares the EMG4 data for the Multilevel analysis. It starts with the output from Matlab (saved in xlsx) EMG4_Bin_whole_trial_60sjs.xlsx. First we performed a number of checks, e.g. number of trials, number of participants, outliers.

Then the results for each segment are loaded. Preprocessing is needed to transform the data from wide to long format based on the 100ms bins for the corrugator response. The relevant variables are computed for the different conditions. Another MoralityValence variable is computed for viualization purposes. Furthermore, we've computed linear, quadratic and cubic time trends for each condition (moral, immoral). Finally, the trials were filtered based on incCorr and incZygo, which was determined during the baseline selection phase. The preprocessed data is then saved for further analysis in the WORCS_EMG4/data/ folder:
- EMG4_morality_trial_data_27-Jun-2023_60pp_preprocessed.csv
- EMG4_state_adjective_trial_data_27-Jun-2023_60pp_preprocessed.csv
- EMG4_neutral_trial_data_08-Aug-2023_60pp_preprocessed.csv
- EMG4_affect_reason_trial_data_27-Jun-2023_60pp_preprocessed.csv

### EMG4_MLM_analysis.Rmd

The script runs the statistical models as described in the preregistration. It takes the preprocessed data from the “EMG4_preprocessing_data.Rmd” script and recodes relevant variables as factors and provides labels that are meaningful to the analysis. This data is now stored (in WORCS_EMG4/data/) so that the statistical analysis can be done on the fully prepared data (stored as RData and csv files):
- EMG4_morality_trial_data_for_MLM_analysis_60sjs.RData
- EMG4_state_adjective_trial_data_for_MLM_analysis_60sjs.RData
- EMG4_affect_reason_trial_data_for_MLM_analysis_60sjs.RData

Following the preregistration we built the multilevel growth curve models for each segment followed by post-hoc analyses. The best model is stored in WORCS_EMG4/results/:
- EMG4_Morality_final_model_60sjs.RData
- EMG4_StateAdjective_final_model_60sjs.RData
- EMG4_AffectReason_final_model_60sjs.RData

### EMG4_figures.Rmd

This script produces all the figures for the paper.

### EMG4_analysis_behavioral_data.Rmd

The script contains the analysis of the behavioral data. It uses the data from the previous steps. We check the distributions of the behavioral data.

### EMG4_MLM_exploratory_analysis.Rmd

The script runs additional exploratory analyses. We take the pre-processed EMG data from the morality segment and combine this with the behavioral data so all trials are included.

We compute the correlation between the peak of the morality segment response (3-4s after onset of the segment) and the embedded rating response.And we performed a multilevel analysis for the fairness responses to each story.

We also analyzed the justices scores in relation to the EMG data, this analysis was part of the supplementary materials as it wasn't the main focus of our paper.

### EMG4_post-hoc_neutral_segment_analysis.Rmd

The script preprocessess the data from the neutral segment and recodes relevant variables as factors and provides labels that are meaningful to the analysis. This data is then stored (in WORCS_EMG4/data/) so that the statistical analysis can be done on the fully prepared data (stored as RData and csv files):
- EMG4_neutral_trial_data_for_MLM_analysis_60sjs.RData

For a post-hoc analysis we build multilevel growth curve models for the segment followed by post-hoc analyses of possible interactions. The best model is stored in WORCS_EMG4/results/:
- EMG4_Neutral_final_model_60sjs.RData
