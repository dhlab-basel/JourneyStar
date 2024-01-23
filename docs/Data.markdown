---
layout: page
title: Example Data
permalink: /Data/
---

# Different levels of rdf* application

## Low rdf star application
The in the lowest amount via rdf* added metadata is
![Alt text](other/rdf_LEVELS_v4_2310302.png)
</br>
</br>

## Medium rdf star application
![Alt text](other/rdf_LEVELS_v4_2310303.png)
</br>
</br>

````
:SofiaM :hasJourney :Journey_2018_03 .
<< :SofiaM :hasJourney :Journey_2018_03 >> :hasStartDate "2018-03-10"^^xsd:date .
<< :SofiaM :hasJourney :Journey_2018_03 >> :hasEndDate "2018-03-22"^^xsd:date.

:Journey_2018_03 a :Journey ;
    :hasStartLocation :Zurich;
    :hasDestination :Marrakech .

<< :Journey_2018_03 :hasStartLocation :Zurich >> :hasDeparture "2018-03-10T08:00:00"^^xsd:dateTime .
<< :Journey_2018_03 :hasStartLocation :Marrakech >> :hasArrival "2018-03-10T22:00:00"^^xsd:dateTime .

````


## High amount of rdf star application
![Alt text](other/rdf_LEVELS_v4_2310304.png)
