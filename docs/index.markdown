---
layout: home
---

 `JourneyStar`, is an [Rdf-star](https://www.w3.org/2022/08/rdf-star-wg-charter/)-based
  ontology to represent complex metadata-oriented travel data (historical or modern).
   This ontology contains definition of classes and properties necessary to represent
   travel data with all its associated metadata as a knowledge graph.
   The meta-level information can be added to the the edges of the graph using
   RDF-star technology to comprehensively represent the complexity of the travel data.
   We have illustrated use-case of the ontology constructs through multiple examples.
   The resulting RDF-star based knowledge graphs can be stored in triplestores
   which support RDF-star such as [GraphDB](https://graphdb.ontotext.com/) and
   the graph can be then efficiently queried using [SPARQL-star](https://w3c.github.io/rdf-star/cg-spec/editors_draft.html).
   To verify the data and ensure the consistency of the data with the ontology,
   SHACL node and property shapes are defined and made openly-accessible along
   with the ontology. You can find the ontology, and SHACL graphs, both
   serialized in Turtle format, with various usage examples [here](https://github.com/dhlab-basel/JourneyStar).

# Description of the Ontology

`JourneyStar`, is an OWL ontology that contains description of various classes
and predicates needed to represent travel accounts as linked open data. Since
RDF-star makes embedding triples directly within other triples possible, we can
represent the core statements with their metadata as an annotated triples where
the core triples take the subject or object positions. In the ontology, we have
defined properties for this that can be used for annotated triples.

To keep this ontology generic, we have employed the existing ontologies and
extended the existing definitions of the classes and properties in other
ontologies such as [schema](https://schema.org/) and [Trip](http://ontology.eil.utoronto.ca/icity/Trip/Trip)
through defining subclasses. The ontology serialized in Turtle can be found [here](https://github.com/dhlab-basel/trip-ontology/blob/main/tripOntology.ttl).

This ontology has the prefix `js` and the namespace `http://journey-star.dhlab.unibas.ch/ontology/JourneyStar#`.

```
<http://journey-star.dhlab.unibas.ch/ontology/JourneyStar> rdf:type owl:Ontology ;
                                       rdfs:comment "An RDF-star-based ontology to represent travel data." ;
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
@prefix js: <http://journey-star.dhlab.unibas.ch/ontology/JourneyStar#> .
@prefix : <http://journey-star.dhlab.unibas.ch/data/JourneyStar#> .
```

# Classes
***
## Location
The `js:Location` is an OWL class representing a location/place such as a city,
lake, mountain, village, etc. which have geo-coordinates and preferably a
GeonameID (from [Geonames database](https://www.geonames.org/), retrievable also
 from Wikidata) so that they can be uniquely identified.

```
IRI: <http://journey-star.dhlab.unibas.ch/ontology/JourneyStar#Location>
has super class:
      dbo:Place   
      schema:Pace

is domain of:
      js:hasGeonameID  (max cardinality 1, range: xsd:string)
      js:hasWikiLink   (max cardinality 1, range: IRI)
      schema:name     (range: xsd:string, permitted language tags "en", "de", "fr", "es")

Corresponding SHACL node shape:
      js-shacl:LocationShape
```
Below, you can see an example of an RDF representation of a location:

![Location Representation](images/location_example.png)

***
## PERSON
The `js:Person` is an OWL class representing a person who undertakes a journey or
 is involved in a journey (such as: hotel owner, waiter/waitress, travel companion, etc.).

```
IRI: <http://journey-star.dhlab.unibas.ch/ontology/JourneyStar#Person>
has super class:
      schema:Person

is domain of:
      schema:name   (min cardinality 1, range: xsd:string)
      schema:givenName (range: xsd:string)
      schema:familyName (range: xsd:string)
      schema:gender (restricted to schema:Male, schema:Female, and xsd:string)
      schema:birthDate (max cardinality 1, range: xsd:date)
      schema:birthPlace (max cardinality 1, range: IRI)
      schema:knows (range js:Person, js:Location)  
      js:participatedIn (range js:Event)
      js:hasGnd (max cardinality 1, range: xsd:string)

Corresponding SHACL node shape:
      js-shacl:PersonShape
```

Below, you can see an example of an RDF representation of a person:

![Person Representation](images/person_example.png)

***
## Event
The `js:Event` is a general OWL class describing an an event in a real world with
spatiotemporal data and participants. An event can be an activity such as a journey,
 sightseeing, excursion, dining, etc, or an occurrence such as encounter with a person,
 or even a natural phenomena.

```
IRI: <http://journey-star.dhlab.unibas.ch/ontology/JourneyStar#Event>

is domain of:
      js:hasParticipant (range IRI)
      js:hasLocation (range: xsd:string, js:Location, xsd:anyURI, IRI)
      js:hasDate (range: xsd:date, xsd:dateTime)


Corresponding SHACL node shape:
      js-shacl:EventShape
```

There are various classes that are defined as subclass of the `js:Event` each with their specific predicates, for example:
- `js:Activity` that has predicates to describe the a physical activity such as a movement.
- `js:Occurrence` with properties to describe an occurrence.
- `js:NaturalPhenomena` representing a natural phenomena.


***
## Activity
The `js:Activity` is a general OWL class describing an activity a person undertakes, this can be a journey, sightseeing, excursion, dining, etc.

```
IRI: <http://journey-star.dhlab.unibas.ch/ontology/JourneyStar#Activity>
has super class:
      activity:Activity, js:Event

is domain of:
      schema:name   (min cardinality 1, range: xsd:string)
      js:hasCost (max cardinality 1, range: xsd:decimal)

Corresponding SHACL node shape:
      js-shacl:ActivityShape
```

There are various classes that are defined as subclass of the `js:Activity` each with their specific predicates, for example:
- `js:Dining` that has predicates to describe the cuisine and type of the meals.
- `js:Sightseeing` with properties to describe the building or monument.
- `js:Trip` representing a movement from one place to another.

***
## Dining
The `js:Dining` is a general OWL class describing the act of dining during a journey.

```
IRI: <http://journey-star.dhlab.unibas.ch/ontology/JourneyStar#Dining>
has super class:
      js:Activity

is domain of:
      js:mealType (range: xsd:string)
      js:cuisine (range: xsd:string)

Corresponding SHACL node shape:
      js-shacl:DiningShape
```

<!-- Below, you can see an example of an RDF representation of a dining activity: -->

<!-- ![Dining Representation](images/activity.png) -->

***
## SightSeeing
The `js:SightSeeing` is a general OWL class describing the act of sight-seeing during a journey.

```
IRI: <http://journey-star.dhlab.unibas.ch/ontology/JourneyStar#SightSeeing>
has super class:
      js:Activity

is domain of:
      js:sightingOf (min cardinality 1, range: xsd:string, IRI, or xsd:anyURI)

Corresponding SHACL node shape:
      js-shacl:SightSeeingShape
```

***
## Journey
The `js:Journey` is an OWL class that represents a journey a person undertakes
from place A to place B. This journey can be a long one lasting for months or a
short one for a few days.

```
IRI: <http://journey-star.dhlab.unibas.ch/ontology/JourneyStar#Journey>
has super class:
      trip:Trip
      js:Activity

is domain of:
      js:origin (min cardinality 1, range: IRI or xsd:anyURI)
      js:destination (min cardinality 1, range: IRI or xsd:anyURI)
      js:startDate (max cardinality 1, range: xsd:date , xsd:dateTime)
      js:endDate (max cardinality 1, range: xsd:date , xsd:dateTime)
      js:hasActivity (range: js:Activity)
      js:hasStay (range: js:Stay)
      js:meanOfTransportation (range: range: IRI or xsd:anyURI)
      js:transitThrough (range: range: IRI or xsd:anyURI)

Corresponding SHACL node shape:
      js-shacl:JourneyShape

```

There are metadata information accompanying the travel information, for example
when we say person X went from A to B, there is a metadata information about the
time the person left location A (departure date) and the time, person arrives to the destination (arrival date).
These meta-level information can be added to the edges of the graph representing
the origin and destination, respectively through predicates `js:arrivalDate` and `js:departureDate`.

<!-- ![Alt text](ontology_graphs/journey.png) -->

_**Note:** The predicates of the star (annotated) triples, such as `js:arrivalDate` and `js:departureDate`, do not have domain restriction, and can be used with subjects of any type, even a triple!_

***
## Excursion
The `js:Excursion` is an OWL class that represents a short (one day) round trip
a person undertakes without overnight stay. This class is distinct from `js:Journey` in that the
origin and destination of this kind of trip is the same.

```
IRI: <http://journey-star.dhlab.unibas.ch/ontology/JourneyStar#Excursion>
has super class:
      trip:Trip

different from:
      js:Journey

Corresponding SHACL node shape:
      js-shacl:ExcursionShape
```

***
## Accommodation
The class `js:Accommodation` represents an accommodation. It can be any kind of
shelter used to spend a night in during a journey; such as hotel, hostel, rented
 room, tent, cave, etc.

```
IRI: <http://journey-star.dhlab.unibas.ch/ontology/JourneyStar#Accommodation>
has super class:
      dbo:Building
      dbo:Shelter

is domain of:
      schema:name (min cardinality 1, range: xsd:string)
      schema:adress (range: xsd:string)
      js:hasLocation (range: xsd:string, js:Location, xsd:anyURI, IRI)

Corresponding SHACL node shape:
      js-shacl:AccommodationShape
```

***
## Stay
The class `js:Stay` represents an overnight stay in a location in an accommodation
which is represented by a resource of type `js:Accommodation` class.

```
IRI: <http://journey-star.dhlab.unibas.ch/ontology/JourneyStar#Stay>
has super class:
    js:Activity

is domain of:
      js:hasAccommodation (range:  js:Accommodation or any IRI)
      js:startDate (max cardinality 1, range: xsd:date or xsd:dateTime)
      js:endDate (max cardinality 1, range: xsd:date or xsd:dateTime)

Corresponding SHACL node shape:
      js-shacl:StayShape
```

Additionally, the currency type can be added to the edge representing `js:hasCost`
 using RDF-star so that not only the cost of the accommodation, can be stored,
 the currency of the amount is also stored with it. The currency types can be
 found in the [currency data graph](https://spec.edmcouncil.org/fibo/ontology/FND/Accounting/CurrencyAmount/).

<!-- Below, you can see an example of the RDF-star representation of a stay:
![Stay Representation](images/stay_example.png) -->
