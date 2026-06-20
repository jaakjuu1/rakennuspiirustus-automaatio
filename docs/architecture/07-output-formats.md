# Tulostusformaatit

## Tavoite

Tuottaa piirustukset formaateissa jotka:
- Kunnat hyväksyvät
- Arkistoidaan pitkäaikaisesti
- Ovat ihmisen luettavissa ilman erityistyökaluja

## Formaattivalikoima

### Ensisijainen: PDF/A

**Käyttö:** Toimitus kunnalle, arkistointi.

PDF/A on arkistointiin standardoitu PDF-versio. Kaikki kunnat hyväksyvät sen.

**Ominaisuudet:**
- A3-koko (tai A1 tarvittaessa)
- Mittakaava ilmoitettu piirustuksessa (1:50, 1:100)
- Kerroskorkeudet oikeilla symboleilla
- Nimikenttä RT-kortin mukaan
- Kaikki tekstit vektoroituina (ei kuvia)

**Työkalu:** FreeCAD:n PDF-vienti + jälkikäsittely Pythonilla.

### Toissijainen: DWG

**Käyttö:** Jatkomuokkaus, integrointi muihin CAD-järjestelmiin.

DWG on AutoCAD:n natiiviformaatti, mutta muut CAD-ohjelmat tukevat sitä. FreeCAD osaa viedä DWG:ksi.

**Haasteet:**
- DWG-versioiden yhteensopivuus (AutoCAD 2018 vs. 2024)
- Suomalaisten standardien mukaiset tasot ja nimet

**Strategia:** Käytetään FreeCAD:n ODA File Converter -työkalua DWG-vientiin.

### Mahdollinen: IFC

**Käyttö:** Tulevaisuudessa, BIM-integrointiin.

IFC on rakennusalan yhteinen formaatti. Ei vaadita nyt.

## Nimikenttä (RT-kortin mukainen)

Jokaisen piirustuksen oikeaan alakulmaan tulee nimikenttä:

```
┌────────────────────────────────────────────────┐
│                                                │
│  Rakennuspiirustus                            │
│                                                │
│  Kohde: Esimerkkikohde                       │
│  Osoite: Esimerkkikatu 1                     │
│  Päiväys: 15.8.2024                          │
│                                                │
│  Pohjapiirros, 1. kerros                      │
│  Mittakaava: 1:50                            │
│                                                │
│  Suunnittelija: [isän nimi]                  │
│  Piirtäjä: [isän nimi]                      │
│                                                │
│  ─────────────────────────────────────────    │
│                                                │
│  Dokumenttinumero: 001                       │
│  Muutos: A                                   │
│                                                │
└────────────────────────────────────────────────┘
```

Kenttä konfiguroidaan projektikohtaisesti.

## Kerrokset (layers) DWG:ssä

DWG-tiedostoissa käytetään nimettyjä kerroksia:

| Kerros | Sisältö |
|---|---|
| `WALL_EXT` | Ulkoseinät |
| `WALL_INT` | Väliseinät |
| `DOOR` | Ovimerkinnät |
| `WINDOW` | Ikkunamerkinnät |
| `DIM` | Mitat |
| `TEXT` | Tekstit |
| `HATCH` | Täytöt |
| `TITLE` | Nimikenttä |
| `BORDER` | Kehys |

Kerrosten nimet konfiguroidaan `standards/layers.yaml` -tiedostossa, ja niitä voi muokata kuntakohtaisesti.

## Vienti-prosessi

```
Hyväksytyt CAD-tiedostot (FreeCAD .FCStd)
            ↓
[1. DWG-vienti]
    FreeCAD → DWG (ODA Converter)
            ↓
[2. PDF-vienti]
    FreeCAD → PDF/A korkealla resoluutiolla
            ↓
[3. Nimikentän lisäys]
    Lisätään nimikenttä jos FreeCAD ei tuottanut sitä
            ↓
[4. Validointi]
    Tarkistetaan että kaikki elementit näkyvät oikein
            ↓
[5. Pakkaus]
    Kaikki tiedostot ZIP-pakettiin projektikansion mukana
            ↓
Toimitus kunnalle
```

## Laadunvarmistus

Jokaiselle viedylle tiedostolle tehdään:
1. **Avaaminen** ilman virheitä (PDF: mikä tahansa lukija, DWG: AutoCAD tai DraftSight)
2. **Sisällön tarkistus**: ovatko kaikki seinät, mitat, tekstit näkyvissä
3. **Mittakaavan tarkistus**: tulostuskoe tulostetaan A3-paperille ja mitataan

## Tiedostojen nimeäminen

```
<projekti-id>_<piirustustyyppi>_<muutos>.pdf
```

Esimerkki:
- `esimerkki-2024_floorplan_A.pdf`
- `esimerkki-2024_elevations_A.pdf`

Muutos A on ensimmäinen versio, B on korjattu versio, jne.

## Pitkäaikaisarkistointi

Projektin valmistuttua:
- Kaikki tiedostot säilytetään versionhallinnassa (Git)
- Alkuperäiset FreeCAD-tiedostot säilytetään (muokattavissa myöhemmin)
- Vientiformaatit arkistoidaan erilliseen "deliverables"-kansioon

Jos kunta palauttaa kuvia korjattavaksi, alkuperäiset FreeCAD-tiedostot ovat kullanarvoisia.