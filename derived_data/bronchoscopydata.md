---
layout: default
title: Bronchoscopy Data
parent: Data extraction
nav_order: 3
has_toc: true
---

# Bronchoscopy Data Extraction 

While the data from bronchoscopies are discrete, it is particularly difficult to analyze because lab tests performed on bronchoscopy fluid are not directly associated with procedure of performing a bronchoscopy and not all lab tests have a specimen type that directly links back to fluid obtained from a bronschopscopy. As a result, an algorithm was created to identify and extract labs run on bronschoscopy fluid. This algorithm should capture ~80% of all inpatient bronchoscopies performed. Those missing were likely ordered incorrectly and cannot be directly identified as from the EHR data. 

This algorithm was developed using a 2 step approach and allows the user extract all bronchoscopy labs, with the option to extract additional dataframes that contain formatted cell counts and culture results. 

To use this algoithm, the data must be formated as follows:

|PAT_MRN_ID|CSN|LAB_RSLT_COLLECTION_DTTM|LAB_ORDER_ID|SPECIMEN_TYPE|LAB_PROCEDURE|LAB_TEST|LAB_RSLT_VALUE|LAB_RSLT_UOM
|:--|---|---|---|---|---|---|---|---|
|123456789|111111|1955-11-05 21:04|99999|Bronchial|Pneumonia PCR|METAPNEUMOVIRUS PCR|POSITIVE|NaN|

The relevant mapping files can be found in the mapping files directory and modified for individual use.

**Please note:** The data returned from this algorithm are all unmodified and further data cleaning will be needed depending on the objective at hand.

***

## Bronchoscopy identifidication method:

**Step 1:**

1. Lab results were searched for strings that could be associated with a bronschoscopy (ex, 'BAL', 'Bronch Wash', etc..) and manually reviewed for relevance. 
2. LAB_ORDER_ID's associated with the above lab results were extracted and used to find all related lab tests performed on the fluid obtained from the bronchoscopy

**NOTE:** This identified mostly cell counts.

**Step 2:**

1. All lab result SPECIMEN_TYPE and LAB_PROCEDURE combinations were reviewed those associated with a bronchoscopy were used to extract all related data from the labs. 
2. Then these data were then merged together. 

**NOTE:** If the specimens were not ordered such that the specimen_type identified it as from a bronchoscopy in some way, (ex, "Aspirate") then it will NOT be included here.**

## A note about extracting bronchoscopy cell differential and count:
Here a mapping file was used to identify cell counts and differential and then group like items with different names together. This can be modified in the mapping file and codebase if needed. The function also calcualtes absolute cell counts as needed.

## A note about extracting bronchoscopy culture data (including bacterial, mycology, viral, AFB, and PJP):
Here a mapping file was used to identify the type of microbiology exam performed on the bronchoscopy fluid (direct exam, culture, or report) and group them together to make table columns. The data within the columns was then harmoninzed to be True, False or None, if positive, negative, or not performed, respectively. Since this aggregates multiple tests that report outcomes in different ways, a regular expression was used to search individual results for specific strings and label them as positive if identified. For example, a CMV pcr test may be reported as "DETECTED" or "POSITIVE", in which case this would return "True" for that lab test. 

For other data where the outcome is expected to be a list of strings (ex, cultured organisms) the algorithm will return a comma separated string (ex, E. Coli, Staph Aureus). Lastly, the unmodified microbiology reports and direct specimen exams are returned unmodified. While the data contains both the final report and a listed organism (if one id identified), they do not correlate perfectly, though they are close. In internal review, 107 of 2094 (5%) mycology reports and 28 or 2805 (1%) bacterial cultures that isolated an organism did not have an organism directly available. These reports are available in the output data if the end used wishes to parse them further. 

***

## How to use the function:
		# To extract bronchoscopy data simply call the function, with the desired flags:
		all_bronch_data = newgetbronchs(labs, allbronchdata = True, cellcountseparately=True, culturedataseparately = True)

**Plese note:** This function returns a dynamic dictionary with the following keys that vary based on the set flags: "all_bronch_results", "bronch_cell_count" and "bronch_cultures"
		

		
