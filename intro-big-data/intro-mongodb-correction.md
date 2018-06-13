---
title: Introduction à MongoDB - *correction*
---

## Recherche

### Lister les informations du restaurant “Cafe Henri”
```js
db.restaurants.find(
    { name: "Cafe Henri" }
).pretty()
```

### Lister les restaurants n’ayant pas de quartier connu (“Missing”)
```js
db.restaurants.find(
    { borough: "Missing" },
    { _id: 0, name: 1}
)
```

### Lister les restaurants ayant eu un score de 0
```js
db.restaurants.find(
    { "grades.score": 0 },
    { _id: 0, name: 1}
)
```

### Lister les restaurants ayant eu un score entre 0 et 10 (inclus)
```js
db.restaurants.find(
    { "grades.score": { $gte: 0, $lte: 10 } },
    { _id: 0, name: 1}
)
```

### Lister les restaurants qui ont le terme “Cafe” dans leur nom
```js
db.restaurants.find(
    { name: { $regex: "Cafe"} },
    { _id: 0, name: 1}
)
```

### Lister les restaurants faisant de la cuisine de type “Pizza” dans “Brooklyn”
```js
db.restaurants.find(
    { cuisine: "Pizza", borough: "Brooklyn" },
    { _id: 0, name: 1}
)
```


## Agrégat

### Quelles sont les 10 plus grandes chaines de restaurants (nom identique) ?
```js
db.restaurants.aggregate([
    { $match: { name: { $ne: "" }}},
    { $group: { _id: "$name", nb: { $sum: 1 }}},
    { $sort: { nb: -1 }},
    { $limit: 10 }
])
```

### Donner le Top 5 et le Flop 5 des types de cuisine, en terme de nombre de restaurants

#### TOP 5
```js
db.restaurants.aggregate([
    { $group: { _id: "$cuisine", nb: { $sum: 1 }}},
    { $sort: { nb: -1 }},
    { $limit: 5 }
])
```

#### FLOP 5
```js
db.restaurants.aggregate([
    { $group: { _id: "$cuisine", nb: { $sum: 1 }}},
    { $sort: { nb: 1 }},
    { $limit: 5 }
])
```

#### Tout en un
```js
db.restaurants.aggregate([
    { $group: { _id: "$cuisine", nb: { $sum: 1 }}},
    { $sort: { nb: -1 }},
    { $group: {
        _id: false,
        list: { $push: { cuisine: "$_id", nb: "$nb" } }
    }},
    { $unwind: {
        path: "$list",
        includeArrayIndex: "rankTOP"
    }},
    { $sort: { "list.nb": 1 }},
    { $group: {
        _id: false,
        list: { $push: { cuisine: "$list.cuisine", nb: "$list.nb", rankTOP: "$rankTOP" } }
    }},
    { $unwind: {
        path: "$list",
        includeArrayIndex: "rankFLOP"
    }},
    { $sort: { "list.nb": -1 }},
    { $match: { $or: [ 
        { "list.rankTOP": { $lt: 5 }}, 
        { rankFLOP: { $lt: 5 }}
    ]}},
    { $project: {
        _id: 0,
        cuisine: "$list.cuisine",
        nb: "$list.nb"
    }}
])
```


### Donner les dates de début et de fin des évaluations
```js
db.restaurants.aggregate([
    { $unwind: "$grades" },
    { $group: { 
        _id: null, 
        dateDeb: { $min: "$grades.date" },
        dateFin: { $max: "$grades.date" }
    }},
    { $project : {
        _id: 0,
        dateDeb: { $dateToString: { format: "%Y-%m-%d", date: "$dateDeb" }},
        dateFin: { $dateToString: { format: "%Y-%m-%d", date: "$dateFin" }}
    }}
])
```


### Quels sont les 10 restaurants (nom, quartier, addresse et score) avec le plus grand score moyen ?
```js
db.restaurants.aggregate([
    { $unwind: "$grades" },
    { $group: { 
        _id: { na: "$name", id: "$restaurant_id", 
               bo: "$borough", ad: "$address" },
        sc: { $avg: "$grades.score" }
    }},
    { $sort: { sc: -1 }},
    { $limit: 10 },
    { $project: {
        _id: 0,
        name: "$_id.na",
        address: { $concat: [ 
            "$_id.ad.building", " ", "$_id.ad.street", ", ", "$_id.bo"
        ]},
        score: "$sc"
    }}
]).pretty()
```

### Quels sont les restaurants (nom, quartier et addresse) avec uniquement des grades “A” ?
```js
db.restaurants.aggregate([     
    { $unwind: "$grades" },      
    { $group: {          
        _id: { na: "$name", id: "$restaurant_id", 
               bo: "$borough", ad: "$address" },
        valeur: { $addToSet: "$grades.grade" }
    }},      
    { $match: { valeur: [ "A" ]}},
    { $project: {
        _id: 0,
        name: "$_id.na",
        address: { $concat: [ 
            "$_id.ad.building", " ", "$_id.ad.street", ", ", "$_id.bo"
        ]}
    }}
]).pretty()
```


### Compter le nombre d’évaluation par jour de la semaine
```js
db.restaurants.aggregate([
    { $unwind: "$grades" },
    { $project: { jour: { $dayOfWeek: "$grades.date" }}},
    { $group: {
        _id: "$jour", nb: { $sum: 1 }
    }},
    { $sort: { _id: 1 }}
])
```
