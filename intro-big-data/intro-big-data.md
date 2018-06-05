class: middle, center, inverse, title

# Introduction au Big Data
## FX Jollois
### LP Data-Mining

Ce cours est une introduction aux nouveaux outils de gestion de bases de données massives,
utilisés dans des environnements **Big Data**.

---
# Plan à suivre

1. Stockage de données : fichiers plats, BD, ...
1. Limite des systèmes classiques
1. vers le Big Data et NoSQL
    - distribution
    - réplication
    - ...
1. Théorême CAP
1. Type de BD NoSQL
1. Map-Reduce
    1. Données vers processeur de calcul : trop gourmant en I/O
    1. calcul vers données : moins gourmant
1. MongoDB
1. HADOOP

---
# Nouveau contexte

- Applications basées sur le Web (Google, Yahoo, Amazon, LinkedIn, ...)
	- Utilisation de ressources non ou faiblement structurées (page web, textes, images, ...)
	- Explosion de la taille mémoire des données
- BD relationnelles classiques montrant leurs limites
	- Recherche dans des documents pas optimale
	- Capacités de stockage limités

--

Nécessité de développer de nouveaux outils avec les caractéristiques suivantes :

- Distribution des données pour résister à la montée en charge
- Gestion des données non structurées, complexes et hétérogènes (sans déclaration préalable du format des données - *structure implicite*)

---
class: inverse, middle, center
# Accès aux données

---
# Accès aux données

- Fichiers : CSV, XML, JSON, HTML, Logs, Texte brut, Email, binaires
- Bases de données opérationnelles, Datawarehouse et datalake
- API, Web scraping

---
# Limites actuelles



---
# Passage à l'échelle

On distingue deux grands types de passage à l'échelle

**Vertical scaling** (ou *scaling up*)

- on augmente la capacité de l'unique serveur

**Horizontal scaling** (ou *scaling out*)

- on ajoute des petits serveurs

---
# Passage à l'échelle

La première coûtant rapidement très chère, la deuxième solution est celle privilégiée, selon deux possibilités :

- Paradigme *Maître/Esclave*
	- Ecritures par le maître, lecture par les esclaves
	- Réplication directe des écritures aux esclaves (donc lecture éventuellement fausse car avant fin de la réplication)
	- temps de réplication pouvant être très long


- *Partitionnement*
	- Répartition des données dans les différents noeuds
	- Pas transparent : la répartition doit potentiellement être connue des applications
	- Problème sur les contraintes d'intégrité à prévoir


---
# Bases de données NoSQL

On parle maintenant de systèmes **NoSQL** (pour *Not Only SQL*)

- Classe de BD non relationnelles
	- Rien de nouveau réellement, existent depuis plus longtemps que les SBGDR


- Pas forcément de schéma fixe des données
- Pas forcément d'utilisation du concept de **jointure**


- Relaxation d'au moins une des propriétés **ACID** :
	- **A**tomicity
	- **C**onsistancy
	- **I**solation
	- **D**urability

---
# Distribution et réplication

---
# Concepts essentiels

---
# Théorème CAP

Proposé par Brewer (2000), puis amélioré par la suite par Gilbert et Lynch.

Il existe trois propriétés essentielles d'un système :

- **Consistency** (cohérence) : les données sont cohérentes entre tous les noeuds
- **Availability** (disponibilité) : les données sont disponibles à n'importe quel moment
- **Partition Tolerance** (resistance au partitionnement) : le système continue de fonctionner même si un des noeuds est inopérant

**Problème** : Aucun système distribué ne peut respecter ces trois propriétés.

---
# Théorème CAP (suite)

On a donc le choix entre :

- **C + A** : un problème sur un des noeuds fait stopper le système (les SGBDR classiques sont plutôt dans cette catégorie)


- **C + P** : les données ne sont pas forcément disponibles au moment de la requête


- **A + P** : les données renvoyées ne sont pas toujours cohérentes

---
# Conséquence du passage à l'échelle

- Le passage à l'échelle implique (presque obligatoirement) le *partitionnement* des données
- Il faut donc faire le choix entre *cohérence* et *disponibilité*
- Dans pratiquement tous les systèmes, la disponibilité est préférée, et donc la cohérence stricte est abandonnée (d'où le non-respect de *ACID*)
- Heureusement, une réponse existe - **BASE** :
	- **Basically Available** : il y aura une réponse à toute requête, même si c'est du genre *failure* ou *inconsistent data*
	- **Soft State** : le système n'est pas consistent à tout instant
	- **Eventually consistent** : le système deviendra finalement consistent, lorsqu'il ne recevra plus d'entrées

Tous les systèmes actuels des géants du web sont dans cette configuration **BASE**

---
# Bases NoSQL

---
# Inconvénients

---
# Avantages

---
class: middle, center, inverse

# Typologie NoSQL

Il existe quatre principaux types de bases de données dites NoSQL (voir [ce site web](http://nosql-database.org/), d'autres existent mais nous n'en parlerons pas ici).



---
# Key-Value Store

Principe :

- Système à base de couples *clé / valeur*
- Tableau associant des clés à un espace mémoire où sont stockées les valeurs
- Une valeur peut être n'importe quel objet (chaîne, numérique, objet, ...)
- On ne peut requêter le système que sur la clé, et pas sur le contenu de la valeur
- Structure de la valeur inconnue par le système (c'est l'applicatif qui gère)
- Modèle assimilé à une table de hashage

Exemples :

- [DynamoDB](http://aws.amazon.com/dynamodb/)
- [Redis](http://redis.io/)
- [Voldemort](http://project-voldemort.com/)

---
# Key-Value Store (suite)

4 opérations possibles (**CRUD**) :

- `create(key, value)` : on associe une valeur à la clé
- `read(key)` : on renvoie la valeur qui correspond à la clé
- `update(key, value)` : on met à jour la valeur correspondant à la clé
- `delete(key)` : on supprime la valeur correspondant à la clé

---
# Key-Value Store (suite)

**Pour** :

- Interface de requêtage très simple et souvent accessible facilement, par n'importe quel langage
- Performances très élevées en lecture et en écriture

**Contre** :

- Modèle très (trop ?) simple
- Traitements complexes à faire du côté de l'application

---
# Document-Oriented Store

Principe :

- Documents stockés dans un format clé/valeur, où la valeur est structuré
- Structure d'un document sous forme arborescente
- Pas de schéma pré-définis des documents, d'où une grande adaptabilité
- Interface de requêtage simple
- Contrairement au modèle *key-value*, on peut requêter sur les valeurs

Exemples :

- [MongoDB](http://www.mongodb.org/)
- [CouchDB](http://couchdb.apache.org/)
- [Elasticsearch](http://www.elasticsearch.org/)

---
# Document-Oriented Store (suite)

Un document est composé de champs associés à des valeurs (entier, numérique, chaîne, liste, tableau), dans un format type JSON ou XML

Grande hétérogénéité permise entre les documents, puisque la structure de ceux-ci n'est jamais pré-supposée

Pas de prototypage ou de modélisation des données en amont nécessaires

---
# Document-Oriented Store (suite)

**Pour**

- Modèle de données simple et puissant
- Mise à l'échelle aisée
- Requêtage sur le contenu des documents possible

**Contre**

- pas fait pour des données liées
- lenteur éventuelle sur des requêtes complexes

---
# Column-Oriented Store

Principe :

- Données stockées par colonnes, et non par lignes
- A chaque valeur possible d'un attribut, on indique l'objet ayant cette valeur
(compression possible de la colonne si regroupement des valeurs identiques)
- Ajout d'une colonne simple, mais ajout d'une ligne complexe
- Modèle très efficace pour l'analyse de données

Exemples :

- [HBase](http://hbase.apache.org/)
- [Cassandra](https://cassandra.apache.org/)
- [Hypertable](http://hypertable.org/)

---
# Column-Oriented Store (suite)

Il existe deux sous-types de ce genre de système :

- Stockage des colonnes sans compression et de manière séparée
- Regroupement de plusieurs colonnes dans des familles de colonnes

Bien qu'ils soient étiquetés dans le même groupe pour les BD NoSQL, ces deux types ne répondent pas aux mêmes besoins et il est important de savoir ce qu'on veut faire pour choisir entre les deux

---
# Column-Oriented Store (suite)

**Pour**

- Prise en compte très facile de données *sparse* (type **B**) (pas de valeur `NULL` présente dans les données)
- Parfait pour les datawarehouses (type **A**) et pour les opérations de type agrégation
- Très grande flexibilité (type **B**)

**Contre**

- Pas adapté aux données reliées ou complexes
- Maintenance lourde

---
# Graph Database

Principe :

- Modèle basé sur la théorie des graphes
- Adapté à la manipulation d'objets structuré en réseau : cartographie, réseaux sociaux, ...
- 2 composants principaux :
	- Base de stockage des objets (chaque objet est un noeud du graphe)
	- Description des relations entre objets (avec possiblement des propriétés)
- Capacité à manipuler des relations non-triviales et/ou variables

Exemples :

- [OrientDB](http://www.orientechnologies.com/)
- [Neo4J](http://www.neo4j.org/)
- [Infinite Graph](http://www.infinitegraph.com/)

---
# Graph Database (suite)

Typiquement adapté aux traitements des problématiques de type réseaux sociaux ou de cartographie, et donc beaucoup plus rapide qu'un SGBDR dans un tel cadre

Particulièrement adapté à ce qu'on appelle le *Web sémantique* et les moteurs de recommandation

**Pour**

- Très efficace pour les données liées
- Modèles d'interrogation établis et performant
- Capacité de gérer des (très) grosses quantités de données

**Contre**

- Partitionnement (de type *sharding*)


---
class: middle, center, inverse
# Map-Reduce

---
# Map-Reduce

Données vers processeur de calcul : trop gourmant en I/O

Calcul vers données : moins gourmant

---
# Présentation de MapReduce

- Framework développé par Google
- Permet l'écriture simple de programmes sur des clusters informatiques (possiblement très gros)
- Idée de base de la parallélisation des tâches : diviser pour régner
- 2 étapes donc :
	1. Etape 1 (**Map**) :
		- Diviser le travail à faire en plusieurs tâches 
		- Réaliser les tâches en parallèle 
	2. Etape 2 (**Reduce**) :
		- Récupérer les différents résultats
		- Regrouper ceux-ci pour obtenir le résultat final

---
# Paradigme de MapReduce

Le framework MapReduce est constitué de :

- un seul *JobTracker*, qui sera le chef d'orchestre :
	- programmation (*scheduling*) des jobs aux musiciens
	- gestion des défaillances de ceux-ci
- un *TaskTracker* par noeud du cluster, qui sera un musicien :
	- exécution des tâches demandés par le chef

Le travail se fait exclusivement sur des paires $<key, value>$

- Entrées : ensemble de paires $<key, value>$
- Sorties d'un job : paires $<key, value>$

---
# Schéma de MapReduce 

<img src="Mapreduce.png" style="margin: 0 auto;" width="673">

<div class="footnote">Source : <a href="http://commons.wikimedia.org/wiki/File:Mapreduce.png" target="_blank">http://commons.wikimedia.org/wiki/File:Mapreduce.png</a></div>

---
# Etapes Map et Reduce

Comme indiqué, cela s'articule autour de deux grandes étapes (**Map** et **Reduce**)  :

- Etape **Map** :
	- réalisé dans chaque noeud du cluster
	- souvent un seul des deux paramètres intéressant 
	- calcule une liste de couples $<key, value>$
- Etape **Reduce** :
	- traitement sur les valeurs ($value$) pour chaque $key$
	- travail possible en parallèle
	- tous les couples avec le même $key$ arrivent au même *worker*

---
# Exemple basique : comptage de mots

Deux fonctions à écrire : `map(key, value)` et `reduce(key, value)`

```{js}
map(string key, string value) {
	// key: document name
	// value: document contents
	for each word w in value 
		emit <w, 1>
}

reduce(string key, list value) {
	// key: word
	// value: list of each word appareance
	sum = 0
	for each v in value
		sum = sum + v
	emit <key, sum>
}
```

<div class="footnote">Ceci n'est pas un exemple littéral, mais une adaptation pour illustration</div>

---
# Exemple basique : comptage de mots

<img src="example-mapreduce-wordcount.png" width=100%>

<div class="footnote">Source : <a href="http://blog.trifork.com/wp-content/uploads/2009/08/" target="_blank">http://blog.trifork.com/wp-content/uploads/2009/08/</a></div>

---
# Algorithme plus détaillé de MapReduce

- Lecture des entrées dans le système de fichier distribué, découpages en blocs de taille identique, et assignation de chaque bloc à un *worker*
- Application de la fonction `map()` dans chaque *worker*
- Distribution des résultats de `map()` (étape **Shuffle**) en fonction des clés
- Application de la fonction `reduce()` (en parallèle ou non, selon les besoins)
- Ecriture de la sortie dans le système de fichier distribué (généralement)

---
# Caractéristiques 

- Modèle de programmation simple : 
	- deux fonctions à écrire (`map()` et `reduce()`)
	- indépendant du système de stockage
	- adaptatif à tout type de données
- Ajout possible d'une fonction `combine()` des résultats de `map()` pour les couples avec même clé
- Système gérant seul le découpage, l'allocation et l'exécution
- Tolérance aux défaillances (redémarrage de tâches, réaffectation)
- Parallélisation invisible à l'utilisateur

---
# Quelques critiques

- Pas de garantie d'être rapide : attention à l'étape *shuffle* qui peut prendre du temps, et qui n'est pas adaptable par l'utilisateur
- Coût de communication pouvant être important
- Pas adapté à des problèmes où les données peuvent tenir en mémoire ou à un petit cluster
- Pas de support de langage haut niveau, tel que SQL
- Est une réelle nouveauté ?
	- Proche d'autres implémentations, tel que *Clusterpoint* ou *MongoDB* 
	- Facilement applicable avec PL/SQL sous *Oracle*
- Pas optimisé au niveau des entres/sorties, et donc pas forcément adapté à un problème de *Machine Learning* dans lequel on doit régulièrement lire le même jeu de données plusieurs fois



---
class: middle, center, inverse
# Open data

---


---

---
# Retour sur les BD relationnelles

A l'heure actuelle, les BD classiquement utilisées sont les BD relationnelles :

- ensemble structuré d'informations (*structure explicite*) :
	- tables décrites par des attributs
	- reliées entre elles par des relations
- stockage persistant

Celles-ci sont gérées dans un SGBD permettant :

- l'accès aux fichiers sur le disque
- la création, la suppression, l'insertion, la modification et la recherche de valeurs (via **SQL**)
- la sécurisation et la gestion des accès concurrents

---
# Retour sur les BD relationnelles (suite)

Utilisées dans quasiment toutes les applications, elles ont pour avantages :

- Maturité importante des logiciels

- Documentation souvent très complète

- Performances globalement satisfaisantes

