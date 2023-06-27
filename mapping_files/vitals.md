---
layout: default
title: Vital signs
parent: Data extraction
nav_order: 2
has_toc: true
---

# Vital Sign Extraction 

Vitals include, heart rate (HR), respiratory rate (RR), temperature (temp), peripheral oxygen satuation (spo2), systolic blood pressure (SBP), diastolic blood pressure (DBP, mean arterial pressure (MAP; measured in mmHg), glasgow coma scale score, FiO2 and nasal cannula flow rates. 

Flowsheet data are categorized by a flowsheet_name and a measure_name. The flowsheet_name provides a context through which a measurement was observed and recorded. For instance, a blood pressure could be recorded as routine part of care or during a procedure. For most purposes, we wouldnt want the later influencing the data, so great care was taken to identify those vital signs who's context supports accurate patient phsyiology. 

As a result, this functions calls from a hand-curated list of vital signs captured in the ideal context, which can be found in the mapping_files directory. This mapping can be updated for individual project needs. 

By default, this function also removes outlier data as seen below and converts temperatures from F to C. Note: these function can be turned off by passing "False" boolean as a parameters. 

**NOTE:** Null values were removed, so if someone had their nasal cannula O2 weaned to 0 and it was was charted as null, that has been removed.

|Item|Inclusion Criteria|
|:--:|---|
|Temp| ≥ 15C and ≤ 0.999 quartile|
|RR| ≥ 0 and and ≤ 0.999 quartile|
|HR| ≥ 0 and and ≤ 0.999 quartile|
|BP| ≥ 0 and and ≤ 0.999 quartile|
|SpO2| ≥ 0 and ≤ 100|
|FiO2| ≥ 0 and ≤ 100|
|GCS| ≥ 3 and ≤ 15


Sample code:

		# Get vital signs and return a singular dataframe:
		vitals = get_vitals(flowsheets_dataframe,  clean_data_bool=True, o2_flow_conversion=True, temp_conversion=True)



