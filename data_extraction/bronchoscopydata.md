---
layout: default
title: Bronchoscopy Data
parent: Data extraction
nav_order: 3
has_toc: true
---

# Bronchoscopy Data Extraction 

Bronchoscopy data is particularly difficult to extract because lab tests performed on bronchoscopy fluid are not directly associated with procedure of performing a bronchoscopy and not all lab tests have a specimen type that directly links back to fluid obtained from a bronschopscopy. As a result, an algorithm was created to identify and extract labs run on bronschoscopy fluid. This algorithm should capture ~80% of all inpatient bronchoscopies performed. Thosem missing were likely ordered incorrectly and cannot be directly identified as from a bronchoscopy. 

This algorithm was developed using a 2 step approach and allows the user extract all bronch labs, all bronch cultures (aerobic/anaerobiuc/legionella/pneumocystis), all bronch cell counts with diff and all viral tests off of bronchoscopies. 

To use this algoithm, the data must be formated as follows:

|PAT_MRN_ID|CSN|LAB_RSLT_COLLECTION_DTTM|LAB_ORDER_ID|SPECIMEN_TYPE|LAB_PROCEDURE|LAB_TEST|LAB_RSLT_VALUE|LAB_RSLT_UOM
|:--|---|---|---|---|---|---|---|---|
|123456789|111111|1955-11-05 21:04|99999|Bronchial|Pneumonia PCR|METAPNEUMOVIRUS PCR|POSITIVE|NaN|

The relevant mapping files can be found in the mapping files directory and modified for individual use.

**Please note:** The data returned from this algorithm are all unmodified and further data cleaning will be needed depending on the objective at hand.

***

## Bronchoscopy identifidication method:

**Step 1:**

1. Lab results were searched for strings that could be associated with a bronschoscopy (ex, 'BAL', 'BW', etc..) and manually reviewed for relevance. 
2. LAB_ORDER_ID's associated with the above lab results were extracted and used to find all related lab tests performed on the fluid obtained from the bronchoscopy

**NOTE:** This identified mostly cell counts.

**Step 2:**

1. All lab result SPECIMEN_TYPE and LAB_PROCEDURE combinations were reviewed those associated with a bronchoscopy were used to extract all related data from the labs. 
2. Then these data were then merged together. 

**NOTE:*** If the specimens were not ordered such that the specimen_type identified it as from a bronchoscopy in some way, (ex, "Aspirate") then it will NOT be included here.**

***


## Extract all bronchoscopy-related data

		# To extract all bronchoscopy related data simple call the function:
		all_bronch_data = get_bronchs(labs, allbronchdata = True, cellcountseparately=False, returnculturesseparately = False, returnvirusesseparately=False)
		
		
## Extract bronchoscopy cell differential and cell count:
Here a mapping file was used to identify cell counts and differential and then group like items with different names together. This can be modified in the mapping file and codebase if needed. 

		# To get these data back in a pivoted dataframe, simply set the cellcountseparately flag to True:
		all_bronch_data, all_cellcounts = get_bronchs(labs, allbronchdata = True, cellcountseparately=True, returnculturesseparately = False, returnvirusesseparately=False) 

## Extract bronchoscopy culture data:
Here a mapping file was used to identify aerobic and anaerobic, legionella and pneumocystis tests. While data points are included can be modified in the mapping file. 

		# To get these data back set the returnculturesseparately flag to True:
		all_bronch_data, all_cellcounts, all_cx =get_bronchs(labs, allbronchdata = True, cellcountseparately=True, returnculturesseparately = True, returnvirusesseparately=False) 

## Extract bronchoscopy viral data:
Here a mapping file was used to identify viral tests. While data points are included can be modified in the mapping file. 

		# To get these data back set the returnvirusesseparately flag to True:
		all_bronch_data, all_cellcounts, all_cx, all_viruses =get_bronchs(labs, allbronchdata = True, cellcountseparately=True, returnculturesseparately = True, returnvirusesseparately=True) 
		
