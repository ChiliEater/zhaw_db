# Praktikum 9

## Aufgabe 1

Geben Sie die Namen und Vornamen aller Besucher an, die an der Dorfstrasse wohnen.  

Lösung:  

```sql
SELECT Name, Vorname
FROM Besucher
WHERE Strasse = 'Dorfstrasse';
```

## Aufgabe 2

Geben Sie die Namen aller Personen an, deren Lieblingsbier Malzdrink ist und die dieses besser als mit 3 bewerten.  

Lösung:  

```sql
SELECT DISTINCT Bname
FROM Lieblingsbier
WHERE Bsorte = 'Malzdrink' AND Bewertung > 3;
```

## Aufgabe 3

Sortieren Sie das Ergebnis aus Aufgabe 1 absteigend nach Name und Vorname.  

Lösung:  

```sql
SELECT Name, Vorname
FROM Besucher
WHERE Strasse = 'Dorfstrasse'
ORDER BY Name DESC, Vorname DESC;
```

## Aufgabe 4

Geben Sie den Suppenpreis und den Restaurantnamen aller Restaurants an, die Biere mit dem Grundstoff "Hopfen" anbieten.  

Lösung:  

```sql
SELECT DISTINCT r.Name,r.Suppenpreis
FROM Restaurant r
JOIN Sortiment s ON r.Name = s.Rname
JOIN Biersorte b ON s.Bsorte = b.Name
WHERE b.Grundstoff = 'Hopfen';
```

## Aufgabe 5

Geben Sie Namen und Geburtstag aller Gäste an, die ein Restaurant mit Eröffnungsdatum vor dem Jahr 2010 besuchen.  

Lösung:  

```sql
SELECT DISTINCT b.Name, b.Gebtag
FROM Besucher b
JOIN Gast g ON b.Name = g.Bname AND b.Vorname = g.Bvorname
JOIN Restaurant r ON g.Rname = r.Name
WHERE r.Eroeffnungsdatum < '2010-01-01';
```

## Aufgabe 6

Formulieren Sie den folgenden Ausdruck in SQL:  

$$
\pi_{Name,Vorname} (\text{Besucher}) \backslash \left( \pi_{Bname,Bvorname} (\text{Besucher}) \sqcup \pi_{Bname,BVorname} (\text{Lieblingsbier}) \right)
$$

Hier identifizieren wir Name mit Bname, etc. Es soll angenommen werden, dass das Datenbanksystem EXCEPT nicht kennt. Formulieren Sie den  Ausdruck auch in Prosa.  

Lösung:  

```sql
SELECT
    Name,
    Vorname
FROM
    Besucher AS x
WHERE
    NOT EXISTS (
        SELECT
            1
        FROM
            Gast
        WHERE
            Bname = x.Name
            AND Bvorname = x.Vorname
    )
    AND NOT EXISTS (
        SELECT
            1
        FROM
            Lieblingsbier
        WHERE
            Bname = x.Name
            AND Bvorname = x.Vorname
    );
```

Namen und Vornamen der Besucher, die weder Gast sind noch ein Lieblingsbier haben.

## Aufgabe 7

Gesucht sind Name, Vorname und Strasse der Besucher, die kein Restaurant an ihrer Wohnstrasse besuchen.  

Lösung:  

```sql
SELECT
    Name,
    Vorname,
    Strasse
FROM
    Besucher AS x
WHERE
    NOT EXISTS (
        SELECT
            1
        FROM
            Gast y,
            Restaurant z
        WHERE
            x.Name = y.Bname
            AND x.Vorname = y.Bvorname
            AND y.Rname = z.Name
            AND x.Strasse = z.Strasse
    );
```

## Aufgabe 8

Gesucht sind Namen und Vornamen von Besuchern, die das Glück haben, dass es ein Restaurant gibt, welches eines ihrer Lieblingsbiere im  Sortiment hat.  

Lösung:  

```sql
SELECT DISTINCT Bname, Bvorname
FROM Lieblingsbier NATURAL JOIN Sortiment;
```

## Aufgabe 9

Gesucht sind die Lieblingsbiersorten derjenigen Gäste des Restaurant "Löwen", deren Vorname mit "P" beginnt. (Algebraischer Ausdruck und SQL)  

Lösung:  

```sql
SELECT
    DISTINCT Bsorte
FROM
    Gast x,
    Lieblingsbier y
WHERE
    x.Bvorname LIKE 'P%'
    AND x.Rname = 'Löwen'
    AND x.Bname = y.Bname
    AND x.Bvorname = y.Bvorname;
```

$$
\delta \left( \pi_{\text{Bsorte}} \left( \sigma_{\text{Bvorname LIKE 'P\%' AND Rname='Löwen'}}(\text{Gast}) \bowtie \text{Lieblingsbier} \right) \right)
$$

## Aufgabe 10

An welchen Strassen gibt es mindestens drei Restaurants? (ohne Gruppierung zu lösen)  

Lösung:  

```sql
SELECT
    DISTINCT x.Strasse
FROM
    Restaurant x,
    Restaurant y,
    Restaurant z
WHERE
    x.Strasse = y.Strasse
    AND y.Strasse = z.Strasse
    AND NOT (
        x.Name = y.Name
        OR x.Name = z.Name
        OR y.Name = z.Name
    );
```