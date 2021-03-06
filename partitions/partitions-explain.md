# Partitions - Explain 

## Walkthrough 

```
#
# EXPLAIN PARTITIONS
#
DROP TABLE IF EXISTS audit_log;
CREATE TABLE audit_log (
  yr    YEAR NOT NULL,
  msg   VARCHAR(100) NOT NULL)
ENGINE=InnoDB 
PARTITION BY RANGE (yr) (
  PARTITION pless2010 VALUES LESS THAN (2010),
  PARTITION pless2011 VALUES LESS THAN (2011),
  PARTITION pless2012 VALUES LESS THAN (2012),
  PARTITION p2x VALUES LESS THAN MAXVALUE);
INSERT INTO audit_log(yr,msg) VALUES (2005,'2005'),(2006,'2006'),(2011,'2011'),(2020,'2020');
EXPLAIN PARTITIONS SELECT * from audit_log WHERE yr in (2011,2012)\G
```

## Example with years (Reorganizing Partitions) 

```
CREATE TABLE audit_log2 (   yr    YEAR NOT NULL,   msg   VARCHAR(100) NOT NULL) ENGINE=InnoDB PARTITION BY RANGE (yr) (   PARTITION p2009 VALUES LESS THAN (2010),   PARTITION p2010 VALUES LESS THAN (2011),   PARTITION p2011 VALUES LESS THAN (2012),   PARTITION p_current VALUES LESS THAN MAXVALUE);
INSERT INTO audit_log2(yr,msg) VALUES (2005,'2005'),(2006,'2006'),(2011,'2011'),(2012,'2012');

EXPLAIN PARTITIONS SELECT * from audit_log2 WHERE yr = 2012; 

ALTER TABLE audit_log2 REORGANIZE PARTITION p_current INTO ( 
   PARTITION p2012 VALUES LESS THAN (2013),
   PARTITION p_current VALUES LESS THAN MAXVALUE);
);

--  Where is data now saved 
EXPLAIN PARTITIONS SELECT * from audit_log2 WHERE yr = 2012;

```

## Eine Partition als ganzes löschen 

  * Vorteil: Schneller als ein Delete (delete from audit_log2 where yr <= 2009; (langsamer)  

```
ALTER TABLE audit_log2 DROP PARTITION p2009;

```

## Eine bestehende große Tabelle partitionieren (mariadb) 

```
Variante 1:
# Wichtig vorher Daten sichern 

ALTER TABLE `audit_log3` PARTITION BY RANGE (`yr`) ( PARTITION p2009 VALUES LESS THAN (2010) ENGINE=InnoDB, PARTITION p2010 VALUES LESS THAN (2011) ENGINE=InnoDB, PARTITION p2011 VALUES LESS THAN (2012) ENGINE=InnoDB, PARTITION p2012 VALUES LESS THAN (2013) ENGINE=InnoDB, PARTITION p_current VALUES LESS THAN MAXVALUE ENGINE=InnoDB )

Variante 2:
Daten ausspielen ohne create (dump)  + evtl zur sicherheit Struktur-Dump 
Tabelle löschen 
Daten ohne Struktur einspielen 
```

## Partitionierung (komplett) entfernen 

```
# Partitionierung entfernen, aber Daten sind noch da 
# Nur nicht mehr in einzelne Zellen partitioniert 
ALTER TABLE audit_log  REMOVE PARTITIONING;

```




## Ref:

  * https://mariadb.com/kb/en/partition-maintenance/
