---
layout: default
title: SOFA Score
parent: Derived Data
nav_order: 4
has_toc: true
---

# SOFA Score Implementation 

This code is based on the work done by Mackenzie Hofford [OpenSep](https://github.com/mhofford/I2_Sepsis) and is required for this function to work. 

Important information to note about this function: 

* Since UOP is not available, serum Cr is used to assess the renal SOFA score. 
* Outliar exclusion is performed as they are for the [vital signs] (https://andrewmichelson.github.io/WUSTL-CCDM/data_extraction/vitals.html)
	

**To calculate the SOFA score, the algorithm needs 4 tables of information:**

1. Vital signs/Mechanical ventilation Information (flowsheet data; "flow")
2. Medication information (specifically vasopressors; "meds")
3. Laboratory results ("labs")
4. ADT information to indicate when the patient was in an ICU (indicates when to use qSOFA vs. SOFA; "pats")

To call the function:

	# Generate the required dataframes
	lvdf, mvdf, adtdf, vasodf = sofa_tables(flow, meds, labs, pats, med_name_col='med_name', med_admin_route="med_admin_route", lab_val_col='lab_rslt_value')
	
	# Use those dataframes to call the SOFA calculation function
	sofascore = score_SOFA(lvdf=lvdf, adt=adtdf, mvdf=mvdf.loc[mvdf.vent_start.notnull()], dxdf= None, vasodf = vasodf, uodf= None, SF_dict = {1:512,2:357,3:214,4:89}, calc_FiO2 = True, calc_PF = True, calc_SF= True, max_flow_convert= 15, calc_interval_mins = 60, LOCF_hours =24, include_SF_RATIO = True, mech_vent_def = 'PEEP', debug = True, cutoff = 2)
	
	#Reset the timezone
	sofascore.score_time=sofascore.score_time.dt.tz_localize(None)

## Generating the Input Data Frames

### Flowsheet Information ("flow")
This includes heart rate, respiratory rate, temperature, spo2, blood pressure and peep. 

The input flowsheets dataframe can have more data than is required. To identify the relavent flowsheet elements, the function looks for a combination of specific flowsheet_names and measure_names, which are contained in a dictionary within the script. It can be updated in the code for individual use cases and modified to account for new information over time.


The input dataframe should have the following columns and structure:

| pat_mrn_id     | flowsheet_name | measure_name  | value | performed|
|:---------------|:---------------|:--------------|:------|:----------|
| 0000001        | vitals|           HR				    | 60  | 2023-01-01 14:01:00
| 0000002        |vitals|           RR				    | 18  |2023-01-01 14:01:00
| 0000003        | vitals|            SpO2               | 99  |2023-01-01 14:01:00
| 0000004        | vitals|            Temp		           | 37  |2023-01-01 14:01:00

***Important notes:***

1. This assumes the BP is stored as a string in the format of SBP/DBP (ex 120/80)
2. The use of positive pressure either through MV or NPPV is identified by capturing PEEP from the appropriate flowsheets
3. Null values are removed
4. Assumes O2 Flow <15 LPM for nasal cannula
5. Nasal cannula flow is converted into Fio2 through the equation: FiO2 = (O2 Flow x 3.5)+21

### Medication information ("meds")
This contains solely vasopressors/ionotropes and accounts for:

* Angiotensin II
* Dobutamine
* Dopamine
* Epinephrine
* Milrinone
* Norepinephrine
* Phenylephrine 
* Vasopressin

Like the flowsheet elements, the script contains a dictionary of items are mapped to one type of vasopressor. Vasopressors are also converted into norepi equivalents as specified in the original OpenSep manuscript published by Hofford, et al. This can be updated and modified in the code to suite specific use cases. This function also will only look at medications that were "Administered" and will not consider push dose pressors. 


The input dataframe should have the following columns and structure:

| pat_mrn_id     | med_name | med_admin_route | med_admin_status |med_admin_amt|'med_admin_dose_start_date'|'med_admin_dose_stop_date'|
|:---------------|:------------------|:------|:------|:------|:------|:------|
| 0000001        | epi				    | intravenous  | Administered   |  0.05 mcg/kg/min   | 2023-01-01 14:01:00  | 2023-01-01 15:01:00
| 0000002        | norepi				 | intraosseus  |  Administered  | 0.05 mcg/kg/min   | 2023-01-01 14:01:00 | 2023-01-01 15:01:00
| 0000003        | vaso              |   intravenous| Administered   |  0.04 Units/min    | 2023-01-01 14:01:00   |2023-01-01 15:01:00 
| 0000004        | dobutamine		    |  intravenous | Administered   |  5 mcg/kg/min      | 2023-01-01 14:01:00   |2023-01-01 15:01:00 

***Important notes:***

1. Only vasopressors administered through the following routes are captured: 
	* 'intravenous', 'intraosseous','contin. intravenous infusion', 'central venous line infusion'
2. Only continuous vasopressors are included in the sofa calculation
	*  Note that there is still a vasopressor identifation and mapping function available if you dont want only continuous vasopressor infusions. Below, if the "med_admin_route" parameter is blank, it will return push dose pressors 	as well as continuous infusions. If the med_admin_route is specified, it will return only continuous infusions.

			df = getpressorsionotrops(meds, med_name, med_admin_route)


### Laboratory Information ("labs")
The labs included here expand slightly beyond what we use for strictly calculating SOFA and include:

* WBC
* HgB
* PLT count
* Cr
* D-Dimer
* AST
* ALT
* Bilirubin
* Albumin
* CRP
* PaO2

The input labs dataframe can have more data than is required. To identify the relavent labs, like the flowsheet elements, the function looks for a combination of specific specimen_types and lab_tests, which are contained in a list within the script. These too can be updated in the code for individual use cases and modified to account for new information over time.



The input dataframe should have the following columns and structure:

| pat_mrn_id     | lab_rslt_dttm|   specimen_type         | lab_test | lab_val_col | 
|:-------------------|:------------------|:------|:------|:------|
| 0000001        | 23-01-01 14:01:00	| Blood |  WBC  | 14
| 0000002        | 23-01-01 14:01:00	| Blood | HgB | 9
| 0000003        | 23-01-01 14:01:00 | Blood | PLT   | 105       
| 0000004        | 23-01-01 14:01:00	 | Blood | Bili | 1.2

***Important notes:***

1. This function will also clean the laboratory results by removing string characters using:

		labs.values = pd.to_numeric(labs.lab_rslt_value.str.strip("<>|+-# "), errors = "coerce")
	This effectively bins data at the extremes (eg, lactate >15 becomes 15). 
	
2. This function does not include outlier removal, so data cleaning prior to using this function may be required
3. This function only looks at blood tests
4. This function does not do outliar exclusion
5. Like the vasopressors identification, the laboratory extraction function can be run indepdendently as follows:
	
		df = sofa_labs_df(labs, lab_val_col)
		
### Admission, Discharge, Transfer (ADT) Information ("pats")
The function uses the patients location to determine if SOFA or qSOFA should be used. There is no screening of filtering done here and this has to be done independently for the moment. 


The input dataframe should have the following columns and structure:

| pat_mrn_id     | icu_in|   icu_out        
|:---------------|:------|:---------|
| 0000001        | 23-01-01 14:01:00	| 23-01-04 14:01:00
| 0000002        | 23-01-01 14:01:00	| 23-01-04 14:01:00
| 0000003        | 23-01-01 14:01:00 | 23-01-04 14:01:00
| 0000004        | 23-01-01 14:01:00 | 23-01-04 14:01:00

***Important notes:***

1. Independent identification of ICU times is still currently required.
