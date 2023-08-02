---
layout: default
title: Encounter data
nav_order: 99
---

# Admission & Discharge Times 

This code was designed to better identify the admit and discharge date times for individual inpatient encounters. Although patients are assigned an encounter-specific ID, it is not sufficient for accurately determining a patient's length of stay for 2 key reasons:

1. When patients move from one department to another they may temporarily get a new encounter id (CSN) even though they are not actually admitted/discharged
2. When patients move between hospitals they will also get a new encounter id, and new admit/discharge date time even though they are technically still considered "in patient" 

This not only effects hospital length of stay for patients, it also effects icu length of stay. 

To counter these effects we developed 2 algorithms:

1. Identify admit and discharge times for the entire hospital stay
2. Identify the admission and discharge times from the ICU

****

## Encounter Level Repair

This function takes raw encounter information and looks for hospital stays that have a minimum duration of 1 day. It will return a dataframe with the admit and discharge times, the age at the start of the encounter and the last disposition in that encounter. 

The encounter repair algorithm can be used by:

	repaired_encounters = repair_encounters(encounter_df)
	
The input (enconter_df above) should have the following format:

|PAT_MRN_ID    |ADMIT_TIME   |    DISCHARGE_TIME| DISCHARGE_DISPOSITION | AGE_AT_ENCOUNTER_START| VISIT_TYPE
|:---------------|:---------------|   :--------------|:------|:----------|:----------|
| 0000001        | 1985-10-26 23:59:00|     1985-10-28 14:14:00  | Inpatient rehab | 26 | Hospital Encounter | 

**Note:** Only "Hospital Encounter" visit types are used and these are identified in the algorithm.

***
## ICU admission and dicharge times
This algorithm performs similarly to the above, however, it uses a mapping file to identify ICUs, which is always subject to change, but can be modified as needed without changing the underlying function. 

The function takes the entire admission, discharge and transfer dataframe and returns a new dataframe with the following columns: PAT_MRN_ID,	CSN list, ICU_IN, ICU_OUT and DEPARTMENT_NAME list. 

The input dataframe should have the following data:

PAT_MRN_ID | CSN   | ADT_EVENT   | EFFECTIVE_TIME     | DEPARTMENT_NAME 
|:---------|:------|:------------|:-------------------|:----------|
| 0000001  | 34567 | Transfer In | 1885-9-7 14:12:00. | 7800 BMT ICU |

**NOTE:** The key ADT_EVENTs this function uses are: "Transfer In", "Census", "Admission" and "Discharge". The input dataframe must have these events for this function to work. 

The icu encounter repair function can be called as follows:

	icu_adt = geticudttms(adt_df)

