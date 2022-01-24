# Praktikum 12

|Format|Constraints|Beschreibung|
|-|-|-|
|**L** (LNr, LName, Status, Stadt)|{LNr} Primary Key <br> {LName} Unique|Lieferant|
|**T** (TNr, TName, Farbe, Gewicht, Stadt)|{TNr} Primary Key <br> {TName} Unique|Teil|
|**P** (PNr, PName, Stadt)|{PNr} Primary Key <br> {PName} Unique|Projekt|
|**LTP** (LNr, TNr, PNr, Menge)|{LNr} Foreign Key auf L <br> {TNr} Foreign Key auf T <br> {PNr} Foreign Key auf P <br> {LNr, TNr, PNr} Unique|"Welcher Lieferant liefert welche Teile für welche Projekte in welcher Menge" Eine Lieferung entspricht einer Zeile in LTP|

## Aufgabe 1

Finden Sie die Teilenummern von allen Teilen, welche von einem Lieferanten aus derselben Stadt geliefert werden, wie das Projekt, für welches das Teil geliefert wird.

Lösung:  

```sql
SELECT DISTINCT LTP.TNr
FROM LTP, L, P
WHERE LTP.LNr = L.LNr AND LTP.PNr = P.PNr AND
L.Stadt = P.Stadt;
```

## Aufgabe 2

Finden Sie die Gesamtmenge von Teil ‚T1‘, die Sulzer (für irgendein Projekt) liefert.

Lösung:  

```sql
SELECT SUM(Menge)
FROM LTP, L
WHERE LTP.LNr = L.LNr AND L.LName ='Sulzer' AND LTP.TNr ='T1';
```

## Aufgabe 3

Finden Sie die Teilenummern aller Teile, welche an mindestens ein Projekt mit durchschnittlicher Menge grösser als 350 geliefert werden.

Lösung:  

```sql
SELECT DISTINCT TNr
FROM LTP
GROUP BY TNr, PNr
HAVING AVG(Menge) > 350;
```

## Aufgabe 4

Finden Sie die Projektnamen aller Projekte, die durch Sulzer beliefert werden.

Lösung:  

```sql
SELECT
    Pname
FROM
    P
WHERE
    PNr IN (
        SELECT
            LTP.PNr
        FROM
            LTP,
            L
        WHERE
            LTP.LNr = L.LNr
            AND L.LName = 'Sulzer'
    );
```

oder  

```sql
SELECT
    DISTINCT P.Pname
FROM
    LTP,
    L,
    P
WHERE
    LTP.LNr = L.LNr
    AND LTP.PNr = P.PNr
    AND L.LName = 'Sulzer';
```

## Aufgabe 5

Finden Sie die Farben aller Teile, die durch Sulzer geliefert werden.

Lösung:  

```sql
SELECT
    DISTINCT Farbe
FROM
    T
WHERE
    TNr IN (
        SELECT
            LTP.TNr
        FROM
            LTP,
            L
        WHERE
            LTP.LNr = L.LNr
            AND L.LName = 'Sulzer'
    );
```

oder  

```sql
SELECT
    DISTINCT T.Farbe
FROM
    LTP,
    L,
    T
WHERE
    LTP.LNr = L.LNr
    AND LTP.TNr = T.TNr
    AND L.LName = 'Sulzer';
```

## Aufgabe 6

Finden Sie die Teilenummern aller Teile, welche an irgendein Projekt in Winterthur geliefert werden.

Lösung:  

```sql
SELECT DISTINCT LTP.TNr
FROM LTP, P
WHERE LTP.PNr = P.PNr AND P.Stadt ='Winterthur';
```

## Aufgabe 7

Finden Sie die Projektnummern aller Projekte, welche mindestens ein Teil geliefert bekommen, das auch von Sulzer (irgendwohin) geliefert wird.

Lösung:  

```sql
SELECT
    DISTINCT PNr
FROM
    LTP
WHERE
    TNr IN (
        SELECT
            TNr
        FROM
            LTP,
            L
        WHERE
            LTP.LNr = L.LNr
            AND L.LName = 'Sulzer'
    );
```

oder  

```sql
SELECT DISTINCT x.PNr
FROM LTP AS x, LTP AS y, L
WHERE x.TNr = y.TNr AND y.LNr = L.LNr AND L.LName ='Sulzer';
```

## Aufgabe 8

Finden Sie alle Lieferanten/Teile-Paare, bei denen der Lieferant das Teil nicht liefert.

Lösung:  

```sql
(SELECT LNr, TNr FROM L, T)
EXCEPT
(SELECT LNr, TNr FROM LTP);
```

oder

```sql
SELECT
    LNr,
    TNr
FROM
    L,
    T
WHERE
    NOT EXISTS (
        SELECT
            ''
        FROM
            LTP
        WHERE
            LTP.LNr = L.LNr
            AND LTP.TNr = T.TNr
    );
```

## Aufgabe 9

Finden Sie die Lieferantennummern aller Lieferanten, welche das Teil mit der Nummer "T1" in einer Menge liefern, die grösser ist als die durchschnittliche Menge von Teilen "T1", welche an dasselbe Projekt geliefert werden.

Lösung:  

```sql
SELECT
    DISTINCT x.LNr
FROM
    LTP AS x
WHERE
    x.TNr = 'T1'
    AND x.Menge > (
        SELECT
            AVG(y.Menge)
        FROM
            LTP AS y
        WHERE
            x.PNr = y.PNr
            AND y.TNr = 'T1'
    );
```

## Aufgabe 10

Finden Sie alle Städte, in denen sich mindestens ein Lieferant, ein Teil oder ein Projekt befindet.

Lösung:  

```sql
SELECT Stadt FROM L
UNION
SELECT Stadt FROM T
UNION
SELECT Stadt FROM P;
```

