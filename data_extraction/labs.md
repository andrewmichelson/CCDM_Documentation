---
layout: default
title: Labs
parent: Data extraction
nav_order: 2
has_toc: true
---

# Laboratory Data
As of January 2024, the electornic health record contains data on 37,297 unique lab tests, some of which likely measure the same clinical entity. To facilitate usability of the electronic health record, the data core has started to create mappings that group many similar lab tests into 1 entity. For example, a hemoglobin result may be obtained from a point of care hemoglobin, a CBC with differential cell count, a CBC without differential cell count or an arterial blood gas. While these are all unique lab tests in the electronic health record, they all measure the same entity and as a result are grouped together into 1 entity ("bld_hemoglobin"). These mappings are provided alongside all of the requested lab items and may be disregarded if not useful, but are also used for automated score calculations (ex, APACHE II and SOFA).

Since it was not feasible to map all 37,297 lab tests at the establishment of the datacore, data items were prioritized in 2 ways:
1. Lab tests used for APACHE II and SOFA scores were thoroughly mapped to very low frequency counts (<100).
   This prioritized mapping included the following items:
   * Arterial pH and PaO2
   * Serum sodium, potassium, creatinine, AST, ALT, bilirubin (total and direct), albumin, WBC, hemoglobin, hematocrit, platelet count, and c-reactive protein.

3. Lab tests were arranged in priority of frequency and mapped until the frequency count was apromixately <10,000 (except for culture based data)

To use the mappings, one can simple search the "MAPPING" colums of the output data for the desired data. As of 3/1/2024, 1,065 unique lab tests have been mapped to 201 unique lab concepts (example, bld_hemoglobin above). Additional lab mapping will occur based on frequency, priroity or to meet individual investigator needs. 
