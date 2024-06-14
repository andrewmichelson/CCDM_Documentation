---
layout: default
title: Labs
parent: Data Extraction
nav_order: 3
has_toc: true
---

# Laboratory Data
As of December 2023, the electornic health record contains data on 37,297 unique lab tests, some of which likely measure the same clinical entity. To facilitate usability of the electronic health record, the data core has started to create mappings that group many similar lab tests into 1 entity. For example, a hemoglobin result may be obtained from a point of care hemoglobin, a CBC with differential cell count, a CBC without differential cell count or an arterial blood gas. While these are all unique lab tests in the electronic health record, they all measure the same entity and as a result are grouped together into 1 entity ("bld_hemoglobin"). These mappings are provided alongside all of the requested lab items and may be disregarded if not useful, but are also used for automated score calculations (ex, APACHE II and SOFA).

Since it was not feasible to map all lab tests at the establishment of the datacore, data item mapping was prioritized through 2 means:
1. Exhaustive mapping for high yield lab tests (Covers labs for APACHE II and SOFA Scores)
  
2. Lab tests were arranged in priority of frequency and mapped until the frequency count was apromixately <10,000 (except for culture based data)

Please see [Data types](https://andrewmichelson.github.io/WUSTL-CCDM/datatypes/datatypes.html) for more information. The list of exhaustively mapped items may also be found [here](https://andrewmichelson.github.io/WUSTL-CCDM/datatypes/12-2023-exhaustivelymappeditems.xlsx).

Additional lab mapping will occur based on frequency, priroity or to meet individual investigator needs. 
