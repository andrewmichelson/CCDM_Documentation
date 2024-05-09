---
layout: default
title: Apache II
parent: Data extraction
nav_order: 3
has_toc: true
---

# Apache Score Implementation 


Important information to note about this function: 

* Since UOP is not available, serum Cr is used to assess the creatinine score. 
* Outlier exclusion is performed as they are for the [vital signs] (https://andrewmichelson.github.io/WUSTL-CCDM/data_extraction/vitals.html)

To calculate the Apache score, the algorithm needs 3 tables of information:**

1. Vital signs/Mechanical ventilation Information (flowsheet data; "flow")
3. Laboratory results ("labs")
4. ADT information to indicate when the patient was in an ICU ("pats")


To call the function:

apache_scores= apachescore_process(flow, labs, pats, vital_mapping_file="mapping_files/2023_06_flowsheet_items_mapped.csv", labs_mapping_file="labsmode_mapping_am_v2.csv",clean_outlier=True)


* flow : flowsheet data
* labs : Labs data
* vital_mappin_file  : csv with mapped labels
* pats  : includes ICU_in , Icu_out  
* labs_mapping_file : csv with mapped labels

Output: dataframe with worst scores within 24 hours of icu admission


### Flowsheet Information ("flow")
This includes heart rate, respiratory rate, temperature, spo2,fio2,blood pressure[sbp, dbp, meanbp],gcs and peep.

***Important notes:***

1. This assumes the BP is stored as a string in the format of SBP/DBP (ex 120/80)
2. The use of positive pressure either through MV or NPPV is identified by capturing PEEP from the appropriate flowsheets
3. Null values are removed
4. Assumes O2 Flow <15 LPM for nasal cannula
5. Nasal cannula flow is converted into Fio2 through the equation: FiO2 = (O2 Flow x 3.5)+21
6. The dataframe should have following columns : pat_mrn_id,flowsheet_name,measure_name,value,performed

| pat_mrn_id     | flowsheet_name | measure_name  | value | performed|
|:---------------|:---------------|:--------------|:------|:----------|
| 0000001        | vitals|           HR				    | 60  | 2023-01-01 14:01:00
| 0000002        |vitals|           RR				    | 18  |2023-01-01 14:01:00
| 0000003        | vitals|            SpO2               | 99  |2023-01-01 14:01:00
| 0000004        | vitals|            Temp		           | 37  |2023-01-01 14:01:00


7. Flowsheet data extraction fuction :

               flow_data=process_vitals(flow_df, flowlist_file, clean_outliers=True)


### Laboratory Information ("labs")
This includes wbc, sod, pot, hct, ph, pco2, po2, cr.

The labs for calculating Apache includes:
* wbc
* sodium
* potassium
* hct
* ph
* pco2
* po2
* cr

***Important notes:***

1. This function will also clean the laboratory results by removing string characters using:

		 labs.values = pd.to_numeric(labs.lab_rslt_value.str.strip("<>|+-# "), errors = "coerce")
2. This function does not do outliar exclusion.
3. The dataframe should have following columns : pat_mrn_id,specimen_type,lab_rslt_dttm,lab_procedure,'lab_test', 'lab_rslt_value'.


| pat_mrn_id     | lab_rslt_dttm|   specimen_type         | lab_test | lab_val_col | 
|:-------------------|:------------------|:------|:------|:------|
| 0000001        | 23-01-01 14:01:00	| Blood |  WBC  | 14
| 0000002        | 23-01-01 14:01:00	| Blood | HgB | 9
| 0000003        | 23-01-01 14:01:00 | Blood | PLT   | 105       
| 0000004        | 23-01-01 14:01:00	 | Blood | Bili | 1.2

4. laboratory extraction function:
          
                labs = process_labs(labs_df, lab_map_file)


### Admission, Discharge, Transfer (ADT) Information ("pats")

The dataframe should have following columns : 'pat_mrn_id','icu_in', 'icu_out'.


| pat_mrn_id     | icu_in|   icu_out        
|:---------------|:------|:---------|
| 0000001        | 23-01-01 14:01:00	| 23-01-04 14:01:00
| 0000002        | 23-01-01 14:01:00	| 23-01-04 14:01:00
| 0000003        | 23-01-01 14:01:00 | 23-01-04 14:01:00
| 0000004        | 23-01-01 14:01:00 | 23-01-04 14:01:00


### oxygenation scores (pao2,AAgradient scores):

1. used arterial blood gas values [pao2,fio2,pco2] within 24 hrs of icu admission to calculate aa_gradient scores and pao2 scores based on following conditions:

     if fio2 >=50% , aa_score calculated
     if fio2 <50% , pao2_scores calculated

2. If within 24 hrs of icu admission has missing oxygenation values :

     Calculated the estimated pao2 using equation : P/F =['spo2_value'] / ['fio2_value'] * 1.19) - 76.19 


### if with AKI used check_kdigo_criteria fuction

1.Doubled cr _score if:Increase in serum creatinine of ≥0.3 mg/dL within 48 hours or ≥50% within 7 days(168hrs), time made modular.

2.check_kdigo_criteria fuction:

         check_kdigo_criteria(crdf, time_window_diff=48, time_window_percent=7):


* crdf= dataframe with Creatinine values with lookforward = 165(7days)
* time_window_diff = time range to check for increase in creatinine >= 0.3 mg/dl, set 48hrs (modular)
* time_window_percent = days to check increase in creatinine ≥50%, set 7days (modular)




    


