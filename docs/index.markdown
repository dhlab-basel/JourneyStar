---
layout: home
---

 The Trip Ontology, `trip-onto`, is an [Rdf-star](https://www.w3.org/2022/08/rdf-star-wg-charter/) based ontology to represent complex metadata-oriented travel data (historical or modern). This ontology contains definition of classes and properties necessary to represent travel data as an RDF graph. It contains structures (explained with examples) to add metadata information to the edges of the graph using RDF-star technology to comprehensively represent the complexity of the travel data. The resulting RDF-star based knowledge graphs can be stored in triplestores which support RDF-star such as [GraphDB](https://graphdb.ontotext.com/) and the graph can be then efficiently queried using [SPARQL-star](https://w3c.github.io/rdf-star/cg-spec/editors_draft.html). To verify the data and ensure the consistency of the data with the ontology, SHACL node and property shapes are defined and made openly-accessible along with the ontology. You can find the ontology, and SHACL graphs, both serialized in Turtle format, with various usage examples [here](https://github.com/dhlab-basel/trip-ontology/).

# Description of the Ontology

 The Trip Ontology, `trip-onto`, is an OWL ontology that contains description of various RDF classes and predicates needed to represent travel information as RDF data. Some of the predicates are described so that they can be used to create RDF-star statements to attach information to the edges of the graph. To keep this ontology generic, we have employed the existing ontologies and extended the existing definitions of the classes and properties in other ontologies such as [schema](https://schema.org/) and [trip](http://ontology.eil.utoronto.ca/icity/Trip/Trip) through defining subclasses and adding more constructs necessary to comprehensively represent travel data with their associated metadata. This ontology serialized in Turtle can be found [here](https://github.com/dhlab-basel/trip-ontology/blob/main/tripOntology.ttl).

This ontology has the prefix `trip-onto` and the namespace `http://journeyStar.dhlab.ch/ontology/trip-onto#`. Below we describe a few main classes and properties of this ontology with a few graphics.

**Note:** in all of the graphics below the following prefixes are used:
```
@prefix owl: <http://www.w3.org/2002/07/owl#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix currencyA: <https://spec.edmcouncil.org/fibo/ontology/FND/Accounting/CurrencyAmount/> .
@prefix currency: <https://spec.edmcouncil.org/fibo/ontology/FND/Accounting/ISO4217-CurrencyCodes/>.
@prefix schema: <https://schema.org/> .
@prefix dbr: <http://dbpedia.org/resource/> .
@prefix dbo: <http://dbpedia.org/ontology/> .
@prefix trip: <http://ontology.eil.utoronto.ca/icity/Trip/Trip> .
@prefix trip-onto: <http://journeyStar.dhlab.ch/ontology/trip-onto#> .
@prefix : <http://journeyStar.dhlab.ch/ontology/trip-onto#> .
```

# Classes
***
## Location
The `trip-onto:Location` is an OWL class representing a location/place such as a city, lake, mountain, village, etc which have geo-coordinates and preferably a GeonameID (from [Geonames database](https://www.geonames.org/), retrievable also from Wikidata) so that they can be uniquely identified. This class is a sub-class of `dbo:Place` and is domain of various predicates some of which are shown below:

![Alt text](ontology_graphs/location.png)

The ranges of the properties of this class are defined in the corresponding SHACL node shape: `trip-shacl:LocationShape`.

***
## PERSON
The `trip-onto:Person` is an OWL class (as a subclass of `schema:Person`) representing a person who undertakes a journey. Some of the OWL object and datatype properties that have this class as their `rdfs:domain` are shown in this picture with their corresponding `rdfs:range`.

![Alt text](ontology_graphs/person.png)

The ranges of the properties of this class are defined in the corresponding SHACL node shape: `trip-shacl:PersonShape`.

***
## Activity
The `trip-onto:Activity` (subclass of `trip:activity`) is a general OWL class describing an activity a person undertakes, this can be a journey, sightseeing, excursion, dining, etc. In the picture below, you see its predicates together with their expected object ranges.

![Alt text](ontology_graphs/activity.png)

There are various classes that are defined as subclass of the `trip-onto:Activity` each with their specific predicates, for example:
- `trip-onto:Dining` that has predicates to describe the cousine and type of the meals.
- `trip-onto:Sightseeing` with properties to describe the building or monument.

The most important subclass is the `trip-onto:Journey`.

***
## Journey
The `trip-onto:Journey` is an OWL class that represents a journey a person undertakes from place A to place B of type `trip-onto:Location`. This journey can be a long one lasting for months or a short one for a few days. There are metadata information accompanying the travel information, for example when we say person X went from A to B, there is a metadata information about the time the person left location A (start date of the journey) and the time, person ends his journey (end date). Similarly, the exact time of departure and arrival are also metadata information about a journey, along with other information such as accommodation, mean of transportation, stages of the
journey, etc. The statements representing the metadata (such as start and end date of the journey, as well as arrival and departure times) are added to the edges of the graph as RDF-star triples, as shown below:

![Alt text](ontology_graphs/journey.png)

The class `trip-onto:Journey` is a subclass of the basic `trip-onto:Trip` that overarches journeys from location A to B represented with class `trip-onto:Journey`, as well as short round trip from location A and back to it represented with the class `trip-onto:Excursion`.

***
## Excursion
The `trip-onto:Excursion` is an OWL class that represents a short round trip a person undertakes to and from place A of type `trip-onto:Location`. This class is distinct from `trip-onto:Journey` and that the origin and destination of this kind of trip are the same is ensured through a property shape.

```
trip-onto:Journey owl:differentFrom trip-onto:Excursion
```
`trip-onto:Excursion` is used to represent short round trip that does not contain overnight stays.

***
## Stay
The class `trip-onto:Stay` represents an overnight stay in an accommodation which is represented using `trip-onto:Accommodation` class. The cost of the accommodation can be added to the edge representing `:hasAccommodation` using RDF-star and the predicate `hasCost`. There is a SHACL property shape defined to ensure that the object value of this predicate is a numerical value. Additionally, the currency type can be added to the edge representing `hasCost` using RDF-star so that not only the cost of the accommodation, can be stored, the currency of the amount is also stored with it. The currency types can be found in the [currency data graph](https://spec.edmcouncil.org/fibo/ontology/FND/Accounting/CurrencyAmount/).

Below, you can see an excerpt of the ontology graph representing the `trip-onto:Stay` class and its accompanying metadata, and expected object values for each predicate.

![Alt text](ontology_graphs/onto8.png)

***
## Accommodation
An accommodation can mean anything [Unterkunft bis Unterschlupf]@de from tent to hotel.
![Alt text](ontology_graphs/onto9.png)

## Mode of Transport
the trip-onto:ModeOfTransport is a rdfs:subClass of schema:ModeOfTransport
domain: hasTransport  
* Ferry
* Train
* Ship

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


# SPARQL*

```

prefix onto:<http://www.ontotext.com/>

select ?subject ?predicate ?object

{
    ?subject a <http://www.mysemantics.com/ontology/trip/SightSeeing> .
    ?subject ?predicate ?object.
}

```
![Alt text](<Bildschirmfoto 2023-11-03 um 11.32.34.png>)



```

### Trip ###
:Trip rdf:type owl:Class ;
        rdfs:label "Reisen"@de ,
                "Trip"@en ;
        rdfs:comment "Any type of movement between two locations."@en .

```
