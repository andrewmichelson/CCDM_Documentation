---
layout: default
title: Comorbidity Scores
parent: Derived Data
nav_order: 4
has_toc: true
---


# Comorbidity Score Implementation


assign_comorbidities(): This function takes a DataFrame with diagnosis codes and assigns Charlson and Elixhauser comorbidities and comorbidity scores based on the provided dictionaries.
It returns both a long-form DataFrame (df_long) showing the mapping from ICD codes to comorbidity systems and a wide-form DataFrame (df_wide) showing comorbidities and scores per identifier.


      df_diagnosis_long,df_diagnosis_wide = assign_comorbidities(df,column_code,column_version,columns_id,verbose=False)

* df: pandas.DataFrame containing diagnosis codes
* column_code: str, name of column containing diagnosis code.
* column_version: int or str 
        if int, 9 or 10 indicating ICD version
        if str, name of column containing ICD version (9 or 10)
* columns_id: list of str ["pat_mrn_id"]
        list of names of columns to be used as identifier
  
Output:

* df_long: pandas.DataFrame
         long-form dataframe showing the mapping from icd code to comorbidity systems
* df_wide: pandas.DataFrame
        wide-form dataframe showing comorbidities and comorbidity score per identifier



## Dictioneries _charlson() and _elixhauser():


1. These functions define dictionaries that map comorbidity conditions to corresponding ICD-9 and ICD-10 codes.


2. The dictionaries also include information about the original, Quan11 scores (Elixhauser) and Moore17, vanWalraven09 (Charlson) for each condition. 


## Comorbidity Scores using Spark_df with lookback period :

This fuction reads comorbidity data from a Spark SQL query. It then merges the comorbidity data with the provided Spark DataFrame and filters the data within a specified lookback period.
       
             df,df_scores= get_comorb(spark_df,look_start,lookback_window)

* look_start  = to include diagnosis data +/- from the admission time (set to 0 days, look_start= admit_time) (modular) #(if look_start set -2 then admit_time minus 2days, if set to 10 then admit_time plus 10days)
* lookback_window = lookback untill how many days.(modular)

  
