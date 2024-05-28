---
layout: default
title: Data Cleaning
nav_order: 4
---

# Data cleaning
While data may be extracted in an unedited manner, the core can preprocess data to remove outliers. In general this is performed only on vital signs (lab tests are presumed real since the equipment and labs undergo routine certification and maintenane).

In general, we remove outliers based on quantile (below), but other mechanisms are possible if desired. Please note, that any derived data, such as APACHE or SOFA scores will by necessity require outliar exclusion, which is also normally done by quantile.

|Item|Inclusion Criteria|
|:--:|---|
|Temp| ≥ 15C and ≤ 0.999 quantile|
|RR| ≥ 0 and and ≤ 0.999 quantile|
|HR| ≥ 0 and and ≤ 0.999 quantile|
|BP| ≥ 0 and and ≤ 0.999 quantile|
|SpO2| ≥ 0 and ≤ 100|
|FiO2| ≥ 21 and ≤ 100|
|GCS| ≥ 3 and ≤ 15

