---
layout: default
title: Home
nav_order: 1
description: "WUSTL CCDM Conventions"
permalink: /
---

# WUSTL-CCDM

The WUSTL Critical Care Data Mart provides access to electronic health record (EHR) data from all patients who recieved critical care services at Washington University in St. Louis longitudinally starting after the implementation of the Epic EHR (6/2018). Since it is a logintudinal database, it will  include outpatient encounter information before and after any critical care stay. 

The ETL for this data mart was forked from the COVID-19 Data Mart and provides access to data through windows into the Research Data Core accessible through a SparkSQL and Spark Python Environment. 

 This repository will provide documentation on the development of knowledge layers built on top of the RAW (unmodified) EHR data.  

<ins>The Data Mart includes:</ins>
 * Flowsheet data (includes vital signs)
 * Laboratory results
 * Comorbidity ICD codes
 * Procedural ICD codes
 * Culture and susceptability data
 * Imaging results
 * Admission, Discharge, and Transfer information
 * Medication information
 
