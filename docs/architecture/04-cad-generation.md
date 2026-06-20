# CAD-piirustusten generointi

## Tavoite

Tuottaa vaatimustenmukaiset CAD-piirustukset:
- Pohjapiirros (kaikki kerrokset)
- 4 julkisivukuvaa (P, E, L, I)
- 2 leikkauskuvaa (A-A pitkittäin, B-B poikittain)
- Asemapiirros

Formaatti: DWG (myöhemmin PDF-vienti).

## Ydinkonsepti: LLM kirjoittaa FreeCAD-koodia

LLM ei tuota valmiita piirustuksia suoraan (se ei ole CAD-työkalu). Sen sijaan se **kirjoittaa Python-koodia joka ajaa FreeCAD:iä**.

```python
# Esimerkki: yksinkertaistettu FreeCAD-skripti pohjapiirrokselle
import FreeCAD
import Part
import Draft
from FreeCAD import Vector

doc = FreeCAD.newDocument("FloorPlan")

# Seinät laser-mittausten mukaan
wall_thickness = 200  # mm
exterior_walls = [
    # (start, end, thickness)
    (Vector(0, 0, 0), Vector(12500, 0, 0), wall_thickness),     # pohjoinen
    (Vector(12500, 0, 0), Vector(12500, 8500, 0), wall_thickness), # itä
    (Vector(12500, 8500, 0), Vector(0, 8500, 0), wall_thickness),  # etelä
    (Vector(0, 8500, 0), Vector(0, 0, 0), wall_thickness),         # länsi
]

for start, end, thickness in exterior_walls:
    line = Part.makeLine(start, end)
    wall = Part.extrude(line, Vector(0, 0, 2700))  # huonekorkeus
    Part.show(wall)

doc.recompute()
doc.saveAs("floorplan.FCStd")
```

LLM:n tehtävä on tuottaa tämä koodi oikein, kontekstin perusteella.

## LLM:n syötteet

LLM saa kontekstiksi:

1. **3D-mallin tilavuusanalyysi**
   - Seinien sijainnit ja suunnat
   - Ikkuna- ja oviaukkojen paikat
   - Korkeustiedot

2. **Laser-mittaukset** (strukturoituna JSON:ina)
   - Absoluuttiset mitat
   - Huoneiden mitat

3. **Kaavamääräykset** (YAML)
   - Sallitut materiaalit, muodot, mitat
   - Vaaditut elementit (esim. autopaikat)

4. **RT-kortit** (rakennussuunnittelun standardit)
   - Mittojen merkitsemistapa
   - Kerroskorkeuden merkitseminen
   - Huoneiden nimeäminen

5. **Käyttäjän korjauspyynnöt** (edelliseltä kierrokselta)
   - "Muuta keittiön oviaukkoa 200 mm suuremmaksi"
   - "Lisää tämä ikkuna länsiseinään"

## LLM:n tuotokset

LLM tuottaa:
- Python-skriptin (FreeCAD-koodia)
- Ihmisluettavan selityksen siitä mitä se teki ja miksi
- Listan olettamuksista joita se teki (käyttäjä tarkistaa)

## FreeCAD-mallinnusstrategia

### Seinät ja aukot
- Seinät luodaan extrudoimalla viivoja
- Aukot tehdään leikkauksilla (boolean cut)
- Seinän paksuus tyypillisesti 200 mm ulkoseinille, 100-150 mm väliseinille

### Ikkunat ja ovet
- Parametrisia objekteja Draft-työkalukirjastosta
- Sijainti, koko, korkeus lattiasta parametreina
- Tunnistus 3D-mallista tapahtuu tässä vaiheessa

### Kerrokset
- Jokainen kerros oma Group FreeCAD-dokumentissa
- Kerroskorkeus parametrina
- Leikkauskuvat projektoidaan koko rakennuksen läpi

### Julkisivut
- Ortogonaaliset projektiot seinäpintojen suuntaisesti
- Materiaalit ja värit visualisointia varten
- Mitoitus seinäpinnoista

## Vahvistusmekanismit

LLM voi tehdä virheitä. Suojaukset:

1. **Deterministinen ydin**: LLM tuottaa koodia, mutta koodi ajetaan deterministisesti. Sama syöte → sama tulos.

2. **Rakenteellinen validointi**: Generoitu malli tarkistetaan:
   - Ovatko seinät yhtenäisiä?
   - Onko rakennus suljettu (closed volume)?
   - Ovatko mitat laser-mittausten rajoissa (±10%)?

3. **Ihmisen tarkistus**: Käyttäjä katsoo valmiin CAD-mallin ennen vientiä.

4. **Iteratiivinen korjaus**: Jos käyttäjä korjaa, korjauspyyntö menee takaisin LLM:lle kontekstina.

## Deterministinen deterministisyys vs. luovuus

LLM voi luoda epästandardeja ratkaisuja. Tämä on sekä mahdollisuus että riski:

- ✅ **Mahdollisuus**: LLM voi keksiä luovia ratkaisuja monimutkaisiin tilanteisiin
- ❌ **Risk**: LLM voi tehdä omituisia ratkaisuja joita kunta ei hyväksy

Strategia: LLM saa luovasti ratkaista geometrian, mutta noudattaa tarkasti **formaalisti validoitavia sääntöjä** (mitat, kaavamääräykset, RT-standardit). Luova osuus on esimerkiksi "miten tämä huonejärjestely esitetään selkeimmin", ei "onko tässä autopaikka vai ei".

## RT-kortit ja standardit

RT-kortit ovat Rakennustieto Oy:n julkaisemia ohjeita rakennussuunnitteluun. Keskeiset tähän projektiin:

- RT 15-10824: Pohjapiirustusmerkinnät
- RT 15-10851: Julkisivupiirustusmerkinnät
- RT 15-10857: Leikkauspiirustusmerkinnät
- RT 15-10828: Asemapiirrosmerkinnät

Nämä muunnetaan konkreettisiksi säännöiksi:
- Mihin piirustuksessa piirretään nimikenttä
- Millä viivanpaksuuksilla seinät piirretään
- Miten mitat merkitään
- Millä fontilla tekstit

Säännöt tallennetaan `standards/`-hakemistoon ja syötetään LLM:lle kontekstina.

## Tuotettavat tiedostot

```
projects/<id>/cad/
├── floorplan.dwg     # pohjapiirros
├── elevations.dwg    # 4 julkisivua yhdessä tiedostossa tai erikseen
├── sections.dwg      # 2 leikkausta
└── site-plan.dwg     # asemapiirros
```

DWG-muoto tuotetaan FreeCAD:stä. Laatu on hyväksyttävää kunhan:
- Viivanpaksuudet oikein
- Kerrokset (layers) oikein nimetty
- Teksti oikeilla fonteilla
- Nimikenttä RT-kortin mukaan

## Testausstrategia

POC-vaiheessa testataan:
1. Yksinkertainen suorakaide (4 seinää, 0 aukkoa)
2. Suorakaide jossa 1 oviaukko ja 2 ikkunaa
3. L-mallinen rakennus
4. Kaksikerroksinen rakennus
5. Useampia aukkoja ja monimutkaisempi pohja

Jokaisessa testissä verrataan LLM:n tuotosta käsin piirrettyyn referenssiin.