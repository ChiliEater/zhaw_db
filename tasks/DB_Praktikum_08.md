# Praktikum 8

## Aufgabe 1

Schreiben Sie SQL-Anweisungen für sämtliche benötigte CREATE TABLE-Anweisungen für Mitarbeiter, welche höchstens bei einer Firma arbeiten (analog zum Beispiel aus der Vorlesung 5). Firmen stellen naturgemäss mehrere Mitarbeiter an. Mitarbeiter haben Vor- und Nachnamen, Firmen jeweils einen Namen. Definieren Sie hierzu auch Primärschlüssel: Kombination von Name und Vorname auf Mitarbeiter, Name auf Firma. Stellen Sie die Kardinalitäten mit Schlüsseln sicher. Führen Sie die SQL Statements auf Ihrem DBMS aus. Bereinigen Sie allfällige Fehler, bis das Schema in Tabellenform auf der Datenbank vorliegt.  

Lösung:  

```sql
DROP TABLE IF EXISTS Mitarbeiter;

CREATE TABLE Mitarbeiter(
    Vorname varchar(100) NOT NULL,
    Name varchar(100) NOT NULL,
    CONSTRAINT PK_Mitarbeiter PRIMARY KEY(Vorname, Name)
);

DROP TABLE IF EXISTS Firma;

CREATE TABLE Firma(
    Name varchar(100) NOT NULL,
    CONSTRAINT PK_Firma PRIMARY KEY(Name)
);

DROP TABLE IF EXISTS Anstellung;

CREATE TABLE Anstellung(
    MVorname varchar(100) NOT NULL,
    MName varchar(100) NOT NULL,
    FName varchar(100) NOT NULL,
    CONSTRAINT PK_Anstellung PRIMARY KEY (MVorname, MName),
    CONSTRAINT FK_Mitarbeiter FOREIGN KEY (MVorname, MName) REFERENCES Mitarbeiter(Vorname, Name),
    CONSTRAINT FK_Firma FOREIGN KEY (FName) REFERENCES Firma(Name)
);
```

## Aufgabe 2

Fügen Sie der Tabelle Firma ein Attribut hinzu, um das Gründungsjahr mit einem geeigneten Datentyp zu erfassen. Es soll zusätzlich erfasst werden, welchen Jahreslohn ein Mitarbeiter bei einer Firma erhält. Beide neuen Attribute sollen bei der Dateneingabe immer Werte erhalten.  

1) Auf welchem Element wird der Jahreslohn am sinnvollsten erfasst und warum?
2) Schreiben Sie die notwendigen SQL-Anweisungen (ALTER TABLE) und führen Sie diese aus.

Lösung:  

1) Der Jahreslohn sollte in der Beziehung zwischen Mitarbeitern und Firmen erfasst werden. Logisch gesehen ist dies ein Attribut der Anstellung, nicht eines Mitarbeiters an sich. Zusätzlich ist dies auch für zukünftige Änderungen sinnvoll, da zum Beispiel die Einschränkung gelockert werden kann, dass Mitarbeiter nur bei einer Firma tätig sind. Dann müssen auch unterschiedliche Gehälter je Anstellung erfasst werden können.
2) :

```sql
ALTER TABLE Firma ADD Gründungsjahr integer NOT NULL;
ALTER TABLE Anstellung ADD Jahreslohn decimal(10,2) NOT NULL;
```

## Aufgabe 3

Die Adressen von Mitarbeitern sollen nun auch erfasst werden. Schreiben Sie SQL-Anweisungen für das Hinzufügen von Attributen für PLZ, Ort, Strasse und Hausnummer hinzu. Überlegen Sie, für welche Attribute es sinnvoll ist, nicht immer einen Wert bei der Eingabe zu erzwingen. Überlegen Sie die Konsequenzen Ihrer Wahl der Datentypen. Führen Sie die SQL-Anweisungen auf Ihrem DBMS aus.  

Lösung:  

```sql
ALTER TABLE Mitarbeiter ADD PLZ integer NOT NULL;
ALTER TABLE Mitarbeiter ADD Ort varchar(100) NOT NULL;
ALTER TABLE Mitarbeiter ADD Strasse varchar(100) NOT NULL;
ALTER TABLE Mitarbeiter ADD HausNr varchar(8) NULL;
```

## Aufgabe 4

Fügen Sie nun Tupel in Ihre Tabellen ein (INSERT). Erstellen Sie SQL-Anweisungen für:  

- 3 Firmen
- 5 Mitarbeiter, verteilt auf verschiedene Firmen

Führen Sie die SQL-Anweisungen aus.  

Lösung:  

```sql
-- Firmen
INSERT INTO Firma (Name, Gründungsjahr) VALUES ('Migros', 1925);
INSERT INTO Firma (Name, Gründungsjahr) VALUES ('Coop', 1890);
INSERT INTO Firma (Name, Gründungsjahr) VALUES ('Volg', 1886);
-- Mitarbeiter
INSERT INTO Mitarbeiter (Name, Vorname, PLZ, Ort, Strasse, HausNr)
VALUES ('Müller', 'Heinz', 8400, 'Winterthur', 'Badgasse', '12');
INSERT INTO Mitarbeiter (Name, Vorname, PLZ, Ort, Strasse)
VALUES ('Meier', 'Hans', 8400, 'Winterthur', 'Neustadtgasse');
INSERT INTO Mitarbeiter (Name, Vorname, PLZ, Ort, Strasse, HausNr)
VALUES ('Schmid', 'Peter', 8400, 'Winterthur', 'Marktgasse', '120');
INSERT INTO Mitarbeiter (Name, Vorname, PLZ, Ort, Strasse, HausNr)
VALUES ('Kunz', 'Ulrich', 8400, 'Winterthur', 'Wülflingerstrasse', '27b');
INSERT INTO Mitarbeiter (Name, Vorname, PLZ, Ort, Strasse, HausNr)
VALUES ('Tanner', 'Roger', 8400, 'Winterthur', 'Römerstrasse', '113');
INSERT INTO Anstellung (MName, MVorname, FName, Jahreslohn)
VALUES ('Müller', 'Heinz', 'Coop', 70000);
INSERT INTO Anstellung (MName, MVorname, FName, Jahreslohn)
VALUES ('Meier', 'Hans', 'Migros', 72000);
INSERT INTO Anstellung (MName, MVorname, FName, Jahreslohn)
VALUES ('Schmid', 'Peter', 'Volg', 69000);
INSERT INTO Anstellung (MName, MVorname, FName, Jahreslohn)
VALUES ('Kunz', 'Ulrich', 'Volg', 68000);
INSERT INTO Anstellung (MName, MVorname, FName, Jahreslohn)
VALUES ('Tanner', 'Roger', 'Coop', 74000);
```

## Aufgabe 5

Die Telefonnummern von Mitarbeitern sollen zusätzlich erfasst werden. Es wird verlangt, dass zwingend eine Nummer vorhanden sein muss (NOT NULL).  

1) Fügen Sie der Mitarbeitertabelle eine entsprechende Spalte hinzu. Schreiben Sie dazu eine entsprechende SQL-Anweisung und führen Sie diese aus.
2) Was geschieht, wenn Sie bei der neuen Spalte „DEFAULT ...“ nicht ebenfalls spezifizieren? Warum?

Lösung:  

1)

```sql
ALTER TABLE Mitarbeiter ADD TelNr varchar(12) NOT NULL DEFAULT '/a';
```

2) Da bereits Datensätze für Mitarbeiter vorhanden sind, werden auch diese um eine neue Spalte erweitert. Wenn nun „NOT NULL“ definiert ist, aber kein „DEFAULT“, wird automatisch NULL als Wert in den neuen Spalten der bestehenden Datensätze eingefügt. Dies verletzt in diesem Moment jedoch sofort die eben definierte Einschränkung.

## Aufgabe 6

Ändern Sie nun die Adresse eines Mitarbeiters (UPDATE). Verwenden Sie dazu eine neue Adresse ohne Hausnummer.  

Lösung:  

```sql
UPDATE
    Mitarbeiter
SET
    PLZ = '8401',
    Ort = 'Winterthur',
    Strasse = 'Im Lee',
    HausNr = NULL
WHERE
    Name = 'Müller'
    AND Vorname = 'Heinz';
```

## Aufgabe 7

Ändern Sie den Namen einer Ihrer erfassten Firmen (UPDATE).  

1) Erstellen Sie ein passendes SQL Statement und führen Sie es aus. Was passiert und warum?
2) Was ist notwendig, um dieses Update durchführen zu können? Nehmen Sie die Änderungen vor und führen Sie das UPDATE-Statement aus.

Lösung:  

1) Das DBMS meldet einen Fehler wegen der Verletzung einer Schlüsselbedingung. Grund ist die referentielle Integrität, welche gewahrt wird. In der Tabelle Anstellung (bzw. Äquivalent der Studierenden) ist die Firma ebenfalls eingetragen, würde aber durch das Update nicht ebenfalls geändert, womit die Referenz zerstört wird.

```sql
UPDATE Firma SET Name='Migrolino' WHERE Name='Migros';
```

2)  

```sql
ALTER TABLE Anstellung DROP CONSTRAINT fk_firma;

UPDATE Firma SET Name = 'Migrolino' WHERE Name = 'Migros';

UPDATE Anstellung SET FName = 'Migrolino'
WHERE FName = 'Migros';

ALTER TABLE Anstellung ADD CONSTRAINT FK_Firma
FOREIGN KEY (FName) REFERENCES Firma(Name);
```

## Aufgabe 8

Stellen Sie sicher, dass Sie alle bisherigen SQL-Anweisungen noch in einer Datei zur Verfügung haben, um das Schema wiederherzustellen. Alternativ können Sie versuchen, von Ihrem DBMS ein Script für das Schema zu exportieren (ob und wie das funktioniert ist abhängig vom DBMS).  

Schreiben Sie ein SQL Statement zum Löschen der Mitarbeitertabelle (DROP TABLE) und führen Sie es aus. Warum geht dies nicht ohne weiteres? Ergänzen Sie das Statement um den notwendigen Zusatz, damit die Tabelle gelöscht werden kann.  

Lösung:  

```sql
DROP TABLE Mitarbeiter;
```

Das geht nicht direkt, da noch Anstellungen vorhanden sind. Grund ist wiederum die referentielle Integrität, welche gewahrt wird. Je nach System muss folgendes gemacht werden:  

```sql
DROP TABLE Mitarbeiter CASCADE;
```

oder

```sql
DROP TABLE Anstellung;
DROP TABLE Mitarbeiter;
```

Oder nur FK_Mitarbeiter in Anstellung löschen und dann Tabelle Mitarbeiter löschen.



