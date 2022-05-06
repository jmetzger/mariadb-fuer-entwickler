# Views mit Beispielen 

## Einfaches Beispiel 

```
use sakila;
CREATE VIEW actor_vorname AS SELECT first_name AS vorname FROM actor;
select * from actor_vorname;
```

## Über View updaten wenn Updatable 

```
use sakila;
UPDATE actor_vorname SET vorname='BRANGELINA' WHERE vorname = 'ANGELINA';
```

## Kann algorithm MERGE verwendet werden ? 

```
CREATE ALGORITHM=MERGE VIEW actor_b AS SELECT * FROM actor WHERE last_name LIKE 'B%';
```

## Neue Felder, sind automatisch im View: 

```


alter table actor add column city varchar(45) default 'Hildesheim';
# city is not nicht im view
select * from actor_b; 

# erst wenn ich das create oder alter statement ausführen 
alter view actor_b as select * from actor where last_name like 'B%';
select * from actor_b; 

```
