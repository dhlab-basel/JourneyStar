---
layout: home
---

 The Trip Ontology, `trip-onto`, is an [Rdf-star](https://www.w3.org/2022/08/rdf-star-wg-charter/) based ontology to represent complex metadata-oriented travel data (historical or modern). This ontology contains definition of classes and properties necessary to represent travel data as an RDF graph. It contains structures (explained with examples) to add metadata information to the edges of the graph using RDF-star technology to comprehensively represent the complexity of the travel data. The resulting RDF-star based knowledge graphs can be stored in triplestores which support RDF-star such as [GraphDB](https://graphdb.ontotext.com/) and the graph can be then efficiently queried using [SPARQL-star](https://w3c.github.io/rdf-star/cg-spec/editors_draft.html). To verify the data and ensure the consistency of the data with the ontology, SHACL node and property shapes are defined and made openly-accessible along with the ontology. You can find the ontology, and SHACL graphs, both serialized in Turtle format, with various usage examples [here](https://github.com/dhlab-basel/trip-ontology/).

# Description of the Ontology

 The Trip Ontology, `trip-onto`, is an OWL ontology that contains description of various RDF classes and predicates needed to represent travel information as RDF data. Some of the predicates are described so that they can be used to create RDF-star statements to attach information to the edges of the graph. To keep this ontology generic, we have employed the existing ontologies and extended the existing definitions of the classes and properties in other ontologies such as [schema](https://schema.org/) and [trip](http://ontology.eil.utoronto.ca/icity/Trip/Trip) through defining subclasses and adding more constructs necessary to comprehensively represent travel data with their associated metadata. This ontology serialized in Turtle can be found [here](https://github.com/dhlab-basel/trip-ontology/blob/main/tripOntology.ttl).

This ontology has the prefix `trip-onto` and the namespace `http://journeyStar.dhlab.ch/ontology/trip-onto#`.

```
<http://journeyStar.dhlab.ch/ontology/trip-onto> rdf:type owl:Ontology ;
                                       rdfs:comment "An RDF-star-based ontology to capture concepts reported in travel diaries." ;
                                       owl:versionInfo "v.1.0" .
```


Below we describe a few main classes and properties of this ontology with a few graphics.

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
@prefix trip: <http://ontology.eil.utoronto.ca/icity/Trip/> .
@prefix activity: <http://ontology.eil.utoronto.ca/icity/Activity/> .
@prefix trip-onto: <http://journeyStar.dhlab.ch/ontology/trip-onto#> .
@prefix trip-shacl: <http://journeyStar.dhlab.ch/shacl/trip-shacl#> .
@prefix : <http://journeyStar.dhlab.ch/resource/> .
@base <http://journeyStar.dhlab.ch/ontology/trip-onto#> .
```

# Classes
***
## Location
The `trip-onto:Location` is an OWL class representing a location/place such as a city, lake, mountain, village, etc. which have geo-coordinates and preferably a GeonameID (from [Geonames database](https://www.geonames.org/), retrievable also from Wikidata) so that they can be uniquely identified.

```
IRI: <http://journeyStar.dhlab.ch/ontology/trip-onto#Location>
has super class:
      dbo:Place   
      schema:Pace

is domain of:
      trip-onto:hasGeonameID  (max cardinality 1, range: xsd:string)
      trip-onto:hasWikiLink   (max cardinality 1, range: IRI)
      schema:name     (range: xsd:string, permitted language tags "en", "de", "fr", "es")

Corresponding SHACL node shape:
      trip-shacl:LocationShape
```
Below, you can see an example of an RDF representation of a location:

![Location Representation](images/location_example.png)

***
## PERSON
The `trip-onto:Person` is an OWL class representing a person who undertakes a journey or is involved in a journey (such as: hotel owner, waiter/waitress, travel companion, etc.).

```
IRI: <http://journeyStar.dhlab.ch/ontology/trip-onto#Person>
has super class:
      schema:Person

is domain of:
      schema:name   (min cardinality 1, range: xsd:string)
      schema:givenName (range: xsd:string)
      schema:familyName (range: xsd:string)
      schema:gender (restricted to schema:Male, schema:Female, and xsd:string)
      schema:birthDate (max cardinality 1, range: xsd:date)
      schema:birthPlace (max cardinality 1, range: IRI)
      schema:knows (range trip-onto:Person, trip-onto:Location)  
      trip-onto:hasJourney (range trip-onto:Journey)
      trip-onto:participatedIn (range trip-onto:Activity)
      trip-onto:hasGnd (max cardinality 1, range: xsd:string)

Corresponding SHACL node shape:
      trip-shacl:PersonShape
```

Below, you can see an example of an RDF representation of a person:

![Person Representation](images/person_example.png)


***
## Activity
The `trip-onto:Activity` is a general OWL class describing an activity a person undertakes, this can be a journey, sightseeing, excursion, dining, etc.

```
IRI: <http://journeyStar.dhlab.ch/ontology/trip-onto#Activity>
has super class:
      activity:Activity

is domain of:
      schema:name   (min cardinality 1, range: xsd:string)
      trip-onto:hasParticipant (range IRI)
      trip-onto:hasLocation (range: xsd:string, trip-onto:Location, xsd:anyURI, IRI)

Corresponding SHACL node shape:
      trip-shacl:ActivityShape
```

There are various classes that are defined as subclass of the `trip-onto:Activity` each with their specific predicates, for example:
- `trip-onto:Dining` that has predicates to describe the cusine and type of the meals.
- `trip-onto:Sightseeing` with properties to describe the building or monument.
- `trip-onto:Trip` representing a movement from one place to another.

***
## Dining
The `trip-onto:Dining` is a general OWL class describing the act of dining during a journey.

```
IRI: <http://journeyStar.dhlab.ch/ontology/trip-onto#Dining>
has super class:
      trip-onto:Activity

is domain of:
      trip-onto:mealType (range: xsd:string)
      trip-onto:cuisine (range: xsd:string)

Corresponding SHACL node shape:
      trip-shacl:DiningShape
```

Below, you can see an example of an RDF representation of a dining activity:

![Dining Representation](images/activity.png)

***
## SightSeeing
The `trip-onto:SightSeeing` is a general OWL class describing the act of sight-seeing during a journey.

```
IRI: <http://journeyStar.dhlab.ch/ontology/trip-onto#SightSeeing>
has super class:
      trip-onto:Activity

is domain of:
      trip-onto:sightingOf (min cardinality 1, range: xsd:string, IRI, or xsd:anyURI)

Corresponding SHACL node shape:
      trip-shacl:SightSeeingShape
```

The date and time of the sightseeing event are metadata information that can be added to the edges of the graph with `trip-onto:hasStartDate` and `trip-onto:hasEndDate`.

***
## Journey
The `trip-onto:Journey` is an OWL class that represents a journey a person undertakes from place A to place B. This journey can be a long one lasting for months or a short one for a few days.

```
IRI: <http://journeyStar.dhlab.ch/ontology/trip-onto#Journey>
has super class:
      trip:Trip
      trip-onto:Activity

different from:
      trip-onto:Journey
```

There are metadata information accompanying the travel information, for example when we say person X went from A to B, there is a metadata information about the time the person left location A (start date of the journey) and the time, person ends his journey (end date). Similarly, the exact time of departure and arrival are also metadata information about a journey, along with other information such as accommodation, mean of transportation, stages of the
journey, etc. The metadata statements are added to the edges of the graph as RDF-star triples; see example below:

<!-- ![Alt text](ontology_graphs/journey.png) -->

_**Note:** The predicates of the star (annotated) triples, do not have domain restriction, and can be used with subjects of any type, even a triple!_

***
## Excursion
The `trip-onto:Excursion` is an OWL class that represents a short (one day) round trip a person undertakes to and from the place A. This class is distinct from `trip-onto:Journey` and that the origin and destination of this kind of trip are the same without overnight stay.

```
IRI: <http://journeyStar.dhlab.ch/ontology/trip-onto#Excursion>
has super class:
      trip:Trip

different from:
      trip-onto:Journey

Corresponding SHACL node shape:
      trip-shacl:ExcursionShape
```

***
## Accommodation
The class `trip-onto:Accommodation` represents an accommodation. It can be any kind of shelter used to spend a night in during a journey; such as hotel, hostel, rented room, tent, cave, etc.

```
IRI: <http://journeyStar.dhlab.ch/ontology/trip-onto#Accommodation>
has super class:
      dbo:Building
      dbo:Shelter

is domain of:
      schema:name (min cardinality 1, range: xsd:string)
      schema:adress (range: xsd:string)
      trip-onto:hasLocation (range: xsd:string, trip-onto:Location, xsd:anyURI, IRI)

Corresponding SHACL node shape:
      trip-shacl:AccommodationShape
```

***
## Stay
The class `trip-onto:Stay` represents an overnight stay in a location in an accommodation which is represented by a resource of type `trip-onto:Accommodation` class.

```
IRI: <http://journeyStar.dhlab.ch/ontology/trip-onto#Stay>

is domain of:
      trip-onto:hasAccommodation (range:  trip-onto:Accommodation or any IRI)
      trip-onto:hasActivity (range: trip-onto:Activity)

Corresponding SHACL node shape:
      trip-shacl:StayShape
```

The cost of the accommodation can be added to the edge of the graph representing `:hasAccommodation` using RDF-star with the predicate `hasCost`. Additionally, the currency type can be added to the edge representing `hasCost` using RDF-star so that not only the cost of the accommodation, can be stored, the currency of the amount is also stored with it. The currency types can be found in the [currency data graph](https://spec.edmcouncil.org/fibo/ontology/FND/Accounting/CurrencyAmount/).

Below, you can see an example of the RDF-star representation of a stay:
![Stay Representation](images/stay_example.png)
