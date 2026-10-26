Spracje/Jezik:DE(Deutsch/German) & Kroatisch(Hrvatski)

# Evidencija održavanja i ispitivanja strojeva i uređaja

Lokalna web aplikacija za vođenje evidencije o održavanju, periodičnim ispitivanjima i servisima
strojeva i uređaja. Namijenjena je servisnim tvrtkama, ovlaštenim ispitivačima i službama održavanja
koje trebaju na jednom mjestu imati klijente, njihove uređaje, rokove, zapisnike i dokumentaciju.

Aplikacija radi na lokalnom računalu (Flask + SQLite), u pregledniku, bez interneta i bez
instalacije poslužitelja. Svi podaci spremaju se u jednu datoteku baze (`odrzavanje.db`) i mapu
`dokumenti/`.

## Glavne mogućnosti

### Klijenti i uređaji
- Evidencija **klijenata** (naziv, OIB, adresa, kontakt osoba, telefon, e-mail).
- Evidencija **uređaja** po klijentu: vrsta, proizvođač, model, serijski i inventarni broj,
  godina proizvodnje, lokacija, intervali održavanja i ispitivanja (u mjesecima).
- Vlastiti popis **vrsta uređaja** (alatni stroj, kompresor, dizalica, viličar…) s mogućnošću
  preimenovanja i dodavanja.
- Pretraga i filtriranje uređaja po klijentu, vrsti, lokaciji i statusu servisa.
- Kod unosa novog uređaja može se ručno upisati početno stanje (zadnje i sljedeće održavanje / ispitivanje).
- Ispis **izvještaja po klijentu** sa svim uređajima i njihovim rokovima.

### Održavanja i ispitivanja
- Upis **održavanja** (datum, sljedeći rok, vrsta, opis, izvršitelj, trošak) uz ispis zapisa.
- Upis **ispitivanja** prema obrascima s popisom stavki (ocjena i izmjerena vrijednost po stavci),
  automatski broj zapisnika (`ZI-godina-broj`) i ispis gotovog **zapisnika o ispitivanju**.
- **Obrasci ispitivanja** (predlošci) s unaprijed pripremljenim primjerima: radna oprema,
  električna sigurnost, posude pod tlakom, dizalice. Prazan obrazac može se ispisati za
  popunjavanje na terenu.

### Rokovi i upozorenja
- Pregled **rokova** za sve aktivne uređaje: istekli, uskoro ističu (zadani prag 30 dana,
  podesivo) i u redu.
- Brojači isteklih i skorih rokova u gornjoj traci te na početnoj stranici.
- Popis uređaja koji još nemaju nikakvu evidenciju.
- JSON točka `/api/obavijesti` s popisom hitnih rokova.

### Eksterni servis
- Nalog za slanje uređaja na **eksterni servis** (automatski broj `ES-godina-broj`), s ispisom naloga.
- Evidencija povratka: izvršeni radovi, trošak, broj računa; povratak se po želji automatski
  upisuje kao održavanje.
- Upozorenje za uređaje koji kasne s povratkom.

### Zahtjevi (ticket sustav)
- Prijava zahtjeva (kvar, popravak, nabava…) s prioritetom i oznakom izvršenja.
- Javna stranica **`/prijava`** na kojoj kolege iz lokalne mreže mogu poslati zahtjev i pratiti
  njegov status — bez pristupa ostatku aplikacije.

### Inventar radionice
- Artikli sa šifrom, kategorijom, jedinicom mjere, lokacijom, cijenom i dobavljačem.
- Ulaz, izlaz i inventura s poviješću promjena.
- Upozorenje kad zaliha padne ispod minimalne količine; ukupna vrijednost skladišta i ispis popisa.

### Formulari, dokumenti i vlastiti izbornici
- **Graditelj formulara**: vlastiti obrasci s poljima tipa tekst, broj, datum, da/ne, izbor i naslov
  odjeljka; popunjavanje, vezanje uz uređaj i ispis (prazan ili popunjen).
- **Dokumenti** (Word, Excel, PDF, slike, tekst – do 100 MB): učitavanje uz uređaj ili kao gotovi
  predložak; otvaranje izravno u programu na računalu; izrada kopije predloška za određeni uređaj.
- **Vlastiti izbornici** u gornjoj traci koji grupiraju formulare i dokumente ili vode na vanjsku poveznicu.

### Postavke
- Podaci o tvrtki (naziv, adresa, OIB, telefon, e-mail) koji se prikazuju na ispisima.
- Broj dana za upozorenje o skorom isteku roka.
- Prikaz putanje do baze i mape s dokumentima te gumb za zaustavljanje aplikacije.

## Pokretanje

Potreban je **Python 3** s paketom **Flask** (`pip install flask`).

| Datoteka | Opis |
|---|---|
| `Pokreni.vbs` | Pokreće aplikaciju u pozadini, bez crnog prozora, i otvara preglednik. Ako već radi, samo otvara preglednik. |
| `Pokreni (mreža).vbs` | Isto, uz omogućenu prijavu zahtjeva s drugih računala u lokalnoj mreži. |
| `run.bat` | Pokretanje u prozoru naredbenog retka (po potrebi instalira Flask). |
| `run_mreza.bat` | Isto kao `run.bat`, uz mrežni pristup stranici za prijavu. |

Ručno pokretanje:

```bash
python app.py
```

Aplikacija se otvara na <http://127.0.0.1:5000>.

### Parametri naredbenog retka

| Parametar | Značenje |
|---|---|
| `--demo` | Učitava primjere klijenata i uređaja (samo ako je baza prazna). |
| `--mreza` | Sluša na svim mrežnim sučeljima; ostala računala vide **samo** stranicu `/prijava`. Adresa za kolege ispisuje se u konzoli odnosno u `odrzavanje.log`. |
| `--bez-preglednika` | Ne otvara preglednik automatski. |

### Varijable okruženja

| Varijabla | Zadano | Značenje |
|---|---|---|
| `ODRZAVANJE_DB` | `odrzavanje.db` u mapi aplikacije | Putanja do baze podataka |
| `ODRZAVANJE_DOKUMENTI` | `dokumenti/` u mapi aplikacije | Mapa za učitane dokumente |
| `PORT` | `5000` | Port na kojem aplikacija radi |

### Zaustavljanje
`Ctrl+C` u prozoru naredbenog retka ili **Postavke → Zaustavi aplikaciju** (kad radi u pozadini).

## Sigurnosna kopija

Za kopiju svih podataka dovoljno je kopirati datoteku `odrzavanje.db` i mapu `dokumenti/`
(najbolje dok aplikacija ne radi).

## Struktura projekta

```
app.py            – cijela aplikacija (rute, baza, logika)
templates/        – Jinja2 predlošci stranica i ispisa (ispis_*.html)
static/           – stil (style.css) i logo
odrzavanje.db     – baza podataka (nastaje pri prvom pokretanju)
dokumenti/        – učitane datoteke (nastaje po potrebi)
odrzavanje.log    – zapis rada kad se pokreće bez prozora
```

## Napomena o sigurnosti

Aplikacija nema prijavu korisnika i namijenjena je radu na jednom računalu. Puni pristup moguć je
samo s tog računala (`127.0.0.1`); u mrežnom načinu ostala računala mogu pristupiti isključivo
stranici za prijavu zahtjeva. Aplikaciju ne izlagati javnom internetu.
