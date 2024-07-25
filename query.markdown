---
layout: page
title: SPARQL* Queries
permalink: /query/
---


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
