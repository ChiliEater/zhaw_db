# Praktikum 11

|Format|Constraints|Beschreibung|
|-|-|-|
|**L** (LNr, LName, Status, Stadt)|{LNr} Primary Key <br> {LName} Unique|Lieferant|
|**T** (TNr, TName, Farbe, Gewicht, Stadt)|{TNr} Primary Key <br> {TName} Unique|Teil|
|**P** (PNr, PName, Stadt)|{PNr} Primary Key <br> {PName} Unique|Projekt|
|**LTP** (LNr, TNr, PNr, Menge)|{LNr} Foreign Key auf L <br> {TNr} Foreign Key auf T <br> {PNr} Foreign Key auf P <br> {LNr, TNr, PNr} Unique|"Welcher Lieferant liefert welche Teile für welche Projekte in welcher Menge" Eine Lieferung entspricht einer Zeile in LTP|

## Aufgabe 1

Finden Sie alle verschiedenen Teilefarben/Teilestädte-Kombinationen. (Mit "alle" ist gemeint "alle, die zur Zeit in der DB gespeichert sind" und nicht alle überhaupt möglichen)  

Lösungen:  

```sql
SELECT DISTINCT Farbe, Stadt FROM T;
```

## Aufgabe 2

Finden Sie alle Lieferantennummern/Teilenummern/Projektnummern-Kombinationen, bei denen Lieferant, Teil und Projekt alle aus derselben Stadt kommen.  

Lösungen:  

```sql
SELECT L.LNr, T.TNr, P.PNr
FROM (L JOIN T ON L.Stadt = T.Stadt) JOIN P
ON T.Stadt = P.Stadt;
```

## Aufgabe 3

Finden Sie die Teilenummern von allen Teilen, welche von einem Lieferanten aus Winterthur für ein Projekt in Winterthur geliefert werden.  

Lösungen:  

```sql
SELECT
    DISTINCT LTP.TNr
FROM
    LTP,
    L,
    P
WHERE
    LTP.LNr = L.LNr
    AND LTP.PNr = P.PNr
    AND P.Stadt = 'Winterthur'
    AND L.Stadt = 'Winterthur';
```

## Aufgabe 4

Definieren Sie eine Sicht mit Namen BStadtTeile, die alle Angaben über alle Teile liefert die aus einer Stadt stammen deren Namen mit "B" beginnt. Fragen Sie die Sicht ab nach blauen Teilen (nur die Attribute TName und Gewicht).  

Lösungen:  

```sql
DROP VIEW IF EXISTS BStadtTeile;

CREATE VIEW BStadtTeile AS
SELECT
    TNr,
    TName,
    Farbe,
    Gewicht,
    Stadt
FROM
    T
WHERE
    Stadt LIKE 'B%';

SELECT
    TName,
    Gewicht
FROM
    BStadtTeile
WHERE
    Farbe = 'Blau';
```

## Aufgabe 5

Finden Sie die Lieferantennummern aller Lieferanten, die ein Teil liefern das aus einer Stadt stammt, deren Namen mit "B" beginnt (verwenden Sie obige Sicht).  

Lösungen:  

```sql
SELECT DISTINCT LNr
FROM LTP JOIN BStadtTeile
ON LTP.TNr = BStadtTeile.TNr;
```

## Aufgabe 6

Finden Sie die Projektnummern aller Projekte, die mit dem Teil mit der Nummer T1 beliefert werden in einer durchschnittlichen Menge, welche grösser ist als die grösste Menge eines Teiles, das an das Projekt mit der Nummer P1 geliefert wird.  

Lösungen:  

```sql
SELECT
    PNr
FROM
    LTP
WHERE
    TNr = 'T1'
GROUP BY
    PNr
HAVING
    AVG(Menge) > (
        SELECT
            MAX(Menge)
        FROM
            LTP
        WHERE
            PNr = 'P1'
    );
```

## Aufgabe 7

Finden Sie die Teilenummern aller Teile, welche an alle Projekte in Winterthur geliefert werden.  

Lösungen:  

```sql
SELECT
    TNr
FROM
    T
WHERE
    NOT EXISTS (
        SELECT
            ''
        FROM
            P
        WHERE
            P.Stadt = 'Winterthur'
            AND NOT EXISTS (
                SELECT
                    ''
                FROM
                    LTP
                WHERE
                    LTP.PNr = P.PNr
                    AND LTP.TNr = T.TNr
            )
    );
```

## Aufgabe 8

Finden Sie die Lieferantennummern aller Lieferanten mit Status kleiner als der Status von Sulzer.  

Lösungen:  

```sql
SELECT LNr FROM L
WHERE Status < (SELECT Status FROM L WHERE LName= 'Sulzer');
```

## Aufgabe 9

Finden Sie alle Paare von verschiedenen Teilenummern, bei denen es einen Lieferanten gibt, welcher beide Teile liefert.  

Lösungen:  

```sql
SELECT DISTINCT x.TNr, y.TNr
FROM LTP AS x, LTP AS y
WHERE x.LNr = y.LNr AND x.TNr < y.TNr;
```

## Aufgabe 10

Finden Sie die Anzahl Projekte, zu denen der Lieferant mit dem Namen "Sulzer" beiträgt.  

Lösungen:  

```sql
SELECT COUNT(DISTINCT LTP.PNr) AS AnzahlProjekte
FROM LTP
JOIN L
ON LTP.LNr = L.LNr
WHERE L.LName = 'Sulzer';
```

