# Praktikum 10

## Aufgabe 1

Wie hoch ist der durchschnittliche Lagerbestand aller Biere (der Wert soll den Namen Durchschnittslagerbestand erhalten)?

Lösungen:  

```sql
SELECT AVG(AnLager) AS Durchschnittslagerbestand
FROM Sortiment;
```

## Aufgabe 2

Welche Besucher (Name,Vorname, Strasse) wohnen an einer Strasse, deren Bezeichnung das Wort ‚bach‘ enthält? Untersuchen Sie auch die Resultate, die Sie mit ,Bach‘, ‚BACH‘ etc. erhalten. Sehen Sie hier allenfalls ein Problem?

Lösungen:  

```sql
SELECT Name,Vorname,Strasse
FROM Besucher
WHERE LOWER(Strasse) LIKE '%bach%';
```

## Aufgabe 3

An welchen Strassen gibt es mindestens drei Restaurants (GROUP BY verwenden)?

Lösungen:  

```sql
SELECT Strasse
FROM Restaurant
GROUP BY Strasse
HAVING COUNT(*) >= 3;
```

## Aufgabe 4

Bilden Sie das Kreuzprodukt der Tabellen Restaurant und Besucher (mit und ohne CROSS JOIN). Wieviele Tupel enthält das Resultat? Warum? Wozu könnte eine solche Abfrage nützlich sein?

Lösungen:  

```sql
SELECT * FROM Restaurant, Besucher;
```

## Aufgabe 5

Gesucht ist eine Liste von Besuchern mit Name, Vorname und der Anzahl Restaurantbesuche pro Woche (= Frequenz). Falls ein Besucher nie Gast ist, soll er auf der Liste mit einer Anzahl Besuche von 0 erscheinen. Verwenden Sie dazu einen OUTER JOIN. Optional: Lösung ohne JOIN.

Lösungen:  

```sql
SELECT
    Name,
    Vorname,
    SUM(COALESCE(Frequenz, 0)) AS AnzahlBesuche
FROM
    Besucher AS b
    LEFT OUTER JOIN Gast AS g ON b.Name = g.Bname
    AND b.Vorname = g.Bvorname
GROUP BY
    b.Name,
    b.Vorname
ORDER BY
    AnzahlBesuche DESC;
```

oder  

```sql
SELECT
    Bname,
    Bvorname,
    SUM(Frequenz) AS AnzahlBesuche
FROM
    Gast
GROUP BY
    Bname,
    Bvorname
UNION
SELECT
    Name,
    Vorname,
    0 AS AnzahlBesuche
FROM
    Besucher AS x
WHERE
    NOT EXISTS (
        SELECT
            1
        FROM
            Gast AS y
        WHERE
            y.Bname = x.Name
            AND y.Bvorname = x.Vorname
    )
ORDER BY
    AnzahlBesuche DESC;
```

## Aufgabe 6

Gesucht ist eine Liste der Hersteller von Biersorten zusammen mit der Anzahl Biersorten, die sie produzieren und der Anzahl verschiedener dabei verwendeter Grundstoffe.

Lösungen:  

```sql
SELECT Hersteller, COUNT(*) AS AnzahlBiere,
COUNT(DISTINCT Grundstoff) AS AnzahlGrundstoffe
FROM Biersorte
GROUP BY Hersteller;
```

## Aufgabe 7

Welche Biersorten sind von allen mit derselben Note bewertet worden (Hinweis: das bedeutet, dass kleinste Note = durchschnittliche Note = grösste Note)?

Lösungen:  

```sql
SELECT Bsorte
FROM Lieblingsbier
GROUP BY Bsorte
HAVING MIN(Bewertung) = MAX(Bewertung);
```

## Aufgabe 8

Gesucht ist eine Liste von Restaurants mit ihrem Namen, ihrem Suppenpreis, sowie dem durchschnittlichen Suppenpreis aller Restaurants derselben Strasse.

Lösungen:  

```sql
SELECT
    x.Name,
    x.Suppenpreis,
    y.Durchschnittspreis
FROM
    Restaurant x
    JOIN (
        SELECT
            Strasse,
            AVG(Suppenpreis) AS Durchschnittspreis
        FROM
            Restaurant
        GROUP BY
            Strasse
    ) AS y ON x.Strasse = y.Strasse;
```

## Aufgabe 9

Gesucht ist eine Liste von Restaurants und Biersorten, von denen sie am meisten an Lager haben.

Lösungen:  

```sql
SELECT
    Rname,
    Bsorte
FROM
    Sortiment x
WHERE
    AnLager = (
        SELECT
            MAX(AnLager)
        FROM
            Sortiment y
        WHERE
            y.Rname = x.Rname
        GROUP BY
            Rname
    );
```

## Aufgabe 10

Gesucht sind die Strassen, an denen es mehr Restaurants gibt als Besucher.

Lösungen:  

```sql
SELECT
    Strasse
FROM
    Restaurant x
GROUP BY
    Strasse
HAVING
    COUNT(*) > (
        SELECT
            COUNT(*)
        FROM
            Besucher y
        WHERE
            y.Strasse = x.Strasse
        GROUP BY
            Strasse
    );
```

## Aufgabe 11

Gesucht ist eine Liste der Strassen von Besuchern, deren Vornamen den Buchstaben "p" enthält und die in Restaurants verkehren mit einem Suppenpreis zwischen 3 und 5.  

Lösung:  

```sql
SELECT
    DISTINCT x.Strasse
FROM
    Besucher x,
    Gast y,
    Restaurant z
WHERE
    x.Vorname = y.Bvorname
    AND x.Name = y.Bname
    AND y.Rname = z.Name
    AND x.Vorname LIKE '%p%'
    AND z.Suppenpreis BETWEEN 3
    AND 5;
```

## Aufgabe 12

Gesucht ist die Anzahl verschiedener Besucher des Restaurant Löwen.  

Lösung:  

```sql
SELECT
    COUNT(*) AS Besucher_Restaurant_Löwen
FROM
    (
        SELECT
            DISTINCT Bname,
            Bvorname
        FROM
            Gast
        WHERE
            Rname = 'Löwen'
    ) AS x;
```