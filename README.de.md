# Wartungs- und Prüfnachweis für Maschinen und Geräte

Lokale Webanwendung zur Dokumentation von Wartungen, wiederkehrenden Prüfungen und Servicearbeiten
an Maschinen und Geräten. Sie richtet sich an Servicefirmen, befähigte Prüfer und
Instandhaltungsabteilungen, die Kunden, deren Geräte, Fristen, Prüfprotokolle und Dokumente an
einem Ort verwalten möchten.

Die Anwendung läuft auf dem eigenen Rechner (Flask + SQLite) im Browser – ohne Internet und ohne
Serverinstallation. Alle Daten werden in einer einzigen Datenbankdatei (`odrzavanje.db`) und im
Ordner `dokumenti/` gespeichert.

> Hinweis: Die Benutzeroberfläche der Anwendung ist auf Kroatisch.

## Funktionen

### Kunden und Geräte
- Verwaltung von **Kunden** (Name, OIB/Steuernummer, Adresse, Ansprechpartner, Telefon, E-Mail).
- Verwaltung von **Geräten** je Kunde: Art, Hersteller, Modell, Serien- und Inventarnummer,
  Baujahr, Standort, Wartungs- und Prüfintervalle (in Monaten).
- Eigene Liste von **Gerätearten** (Werkzeugmaschine, Kompressor, Kran, Gabelstapler …), die
  umbenannt und erweitert werden kann.
- Suche und Filter nach Kunde, Geräteart, Standort und Servicestatus.
- Beim Anlegen eines neuen Geräts kann der Anfangszustand (letzte und nächste Wartung / Prüfung)
  manuell eingetragen werden.
- Druckbarer **Kundenbericht** mit allen Geräten und ihren Fristen.

### Wartungen und Prüfungen
- Erfassung von **Wartungen** (Datum, nächste Frist, Art, Beschreibung, Ausführender, Kosten) mit
  Druckansicht.
- Erfassung von **Prüfungen** anhand von Vorlagen mit Prüfpunkten (Bewertung und Messwert je Punkt),
  automatischer Protokollnummer (`ZI-Jahr-Nummer`) und druckfertigem **Prüfprotokoll**.
- **Prüfvorlagen** mit mitgelieferten Beispielen: Arbeitsmittel, elektrische Sicherheit,
  Druckbehälter, Krane/Hebezeuge. Leere Vorlagen können zum Ausfüllen vor Ort gedruckt werden.

### Fristen und Warnungen
- Übersicht der **Fristen** aller aktiven Geräte: abgelaufen, läuft bald ab (Standard 30 Tage,
  einstellbar) und in Ordnung.
- Zähler für abgelaufene und bald fällige Fristen in der oberen Leiste und auf der Startseite.
- Liste der Geräte, für die noch keine Einträge vorhanden sind.
- JSON-Endpunkt `/api/obavijesti` mit allen dringenden Fristen.

### Externer Service
- Auftrag zum Versand eines Geräts an einen **externen Service** (automatische Nummer
  `ES-Jahr-Nummer`) mit druckbarem Auftragsschein.
- Erfassung der Rückgabe: durchgeführte Arbeiten, Kosten, Rechnungsnummer; auf Wunsch wird die
  Rückgabe automatisch als Wartung eingetragen.
- Warnung bei Geräten, deren Rückgabe überfällig ist.

### Anfragen (Ticketsystem)
- Erfassung von Anfragen (Störung, Reparatur, Beschaffung …) mit Priorität und Erledigt-Status.
- Öffentliche Seite **`/prijava`**, auf der Kollegen im lokalen Netzwerk Anfragen stellen und
  deren Status verfolgen können – ohne Zugriff auf den Rest der Anwendung.

### Werkstattlager
- Artikel mit Artikelnummer, Kategorie, Einheit, Lagerort, Preis und Lieferant.
- Zugang, Abgang und Inventur mit Änderungsverlauf.
- Warnung bei Unterschreiten des Mindestbestands; Gesamtwert des Lagers und druckbare Bestandsliste.

### Formulare, Dokumente und eigene Menüs
- **Formular-Designer**: eigene Formulare mit Feldern vom Typ Text, Zahl, Datum, Ja/Nein, Auswahl
  und Abschnittsüberschrift; Ausfüllen, Zuordnung zu einem Gerät und Druck (leer oder ausgefüllt).
- **Dokumente** (Word, Excel, PDF, Bilder, Text – bis 100 MB): Hochladen zu einem Gerät oder als
  fertige Vorlage; direktes Öffnen im Programm auf dem Rechner; Kopie einer Vorlage für ein
  bestimmtes Gerät erstellen.
- **Eigene Menüs** in der oberen Leiste, die Formulare und Dokumente gruppieren oder auf einen
  externen Link verweisen.

### Einstellungen
- Firmendaten (Name, Adresse, OIB, Telefon, E-Mail), die auf Ausdrucken erscheinen.
- Anzahl der Tage für die Vorwarnung vor Fristablauf.
- Anzeige des Pfads zur Datenbank und zum Dokumentenordner sowie Schaltfläche zum Beenden der Anwendung.

## Start

Benötigt wird **Python 3** mit dem Paket **Flask** (`pip install flask`).

| Datei | Beschreibung |
|---|---|
| `Pokreni.vbs` | Startet die Anwendung im Hintergrund ohne Konsolenfenster und öffnet den Browser. Läuft sie bereits, wird nur der Browser geöffnet. |
| `Pokreni (mreža).vbs` | Wie oben, zusätzlich können Anfragen von anderen Rechnern im lokalen Netzwerk gestellt werden. |
| `run.bat` | Start im Konsolenfenster (installiert Flask bei Bedarf). |
| `run_mreza.bat` | Wie `run.bat`, zusätzlich mit Netzwerkzugriff auf die Anfrageseite. |

Manueller Start:

```bash
python app.py
```

Die Anwendung öffnet sich unter <http://127.0.0.1:5000>.

### Kommandozeilenparameter

| Parameter | Bedeutung |
|---|---|
| `--demo` | Lädt Beispielkunden und -geräte (nur bei leerer Datenbank). |
| `--mreza` | Lauscht auf allen Netzwerkschnittstellen; andere Rechner sehen **nur** die Seite `/prijava`. Die Adresse für Kollegen wird in der Konsole bzw. in `odrzavanje.log` ausgegeben. |
| `--bez-preglednika` | Öffnet den Browser nicht automatisch. |

### Umgebungsvariablen

| Variable | Standard | Bedeutung |
|---|---|---|
| `ODRZAVANJE_DB` | `odrzavanje.db` im Anwendungsordner | Pfad zur Datenbank |
| `ODRZAVANJE_DOKUMENTI` | `dokumenti/` im Anwendungsordner | Ordner für hochgeladene Dokumente |
| `PORT` | `5000` | Port der Anwendung |

### Beenden
`Strg+C` im Konsolenfenster oder **Postavke → Zaustavi aplikaciju** (Einstellungen → Anwendung
beenden), wenn sie im Hintergrund läuft.

## Datensicherung

Für eine Sicherung aller Daten genügt es, die Datei `odrzavanje.db` und den Ordner `dokumenti/` zu
kopieren (am besten, während die Anwendung nicht läuft).

## Projektstruktur

```
app.py            – gesamte Anwendung (Routen, Datenbank, Logik)
templates/        – Jinja2-Vorlagen für Seiten und Ausdrucke (ispis_*.html)
static/           – Stylesheet (style.css) und Logo
odrzavanje.db     – Datenbank (wird beim ersten Start angelegt)
dokumenti/        – hochgeladene Dateien (wird bei Bedarf angelegt)
odrzavanje.log    – Protokoll beim Start ohne Konsolenfenster
```

## Sicherheitshinweis

Die Anwendung hat keine Benutzeranmeldung und ist für den Betrieb auf einem einzelnen Rechner
gedacht. Voller Zugriff ist nur von diesem Rechner aus möglich (`127.0.0.1`); im Netzwerkmodus
können andere Rechner ausschließlich die Seite für Anfragen aufrufen. Die Anwendung sollte nicht
öffentlich im Internet erreichbar gemacht werden.
