# Kaavamääräysten käsittely

## Tavoite

Muuttaa kunnan asemakaava-asiakirja (PDF) strukturoiduksi rajoitelistaksi, jota CAD-generointi ja vaatimustenmukaisuustarkistus voivat käyttää.

## Rajaus

**Vaiheessa 1:** Käyttäjä tuo kaava-PDF:n käsin. Järjestelmä tulkitsee sen.
**Myöhemmin (ei nyt):** Automaattinen haku kunnan rajapinnoista.

## Syötemuoto

PDF-asiakirja sisältää tyypillisesti:
- **Kaavakartan** (karttakuva tontista ja rakennusalasta)
- **Kaavamääräystekstin** (kirjallinen lista säännöistä)
- **Selityksiä ja liitteitä** (esim. rakennustapaohjeet)

PDF voi olla:
- Digitaalinen (teksti valittavissa)
- Skannattu (tarvitsee OCR)
- Näiden yhdistelmä

## Käsittelyputki

```
PDF
  ↓
[1. Tekstin erotus]
  - Digitaalinen: pdftotext / pymupdf
  - Skannattu: OCR (Tesseract, PaddleOCR)
  ↓
[2. Osioiden tunnistus]
  - Kaavakartta (kuvat) vs. määräysteksti
  - Käyttäen otsikoiden, fonttien ja asettelun tunnistusta
  ↓
[3. LLM-tulkinta]
  - Strukturoitu poiminta: tontin tiedot, rakennusoikeus, rajoitukset
  - Käyttäen pitkää kontekstia (200K tokenia)
  ↓
[4. Käyttäjän vahvistus]
  - Käyttäjä katsoo tulkinnan ja korjaa
  ↓
constraints.yaml (validoitu, vahvistettu)
```

## LLM:n poimintatehtävä

LLM:lle annetaan koko PDF-teksti (tai tarvittaessa osioittain), ja sen tehtävä on tuottaa YAML-rakenne joka noudattaa skeemaa (kts. `02-data-model.md`).

```yaml
# Esimerkki LLM:n tuotoksesta
site:
  plot_area_m2: 850        # "Tontin pinta-ala on 850 m²"
  build_right_k_m2: 220    # "Rakennusoikeus 220 k-m²"

building_constraints:
  max_stories: 2            # "II"
  max_height_m: 7.5         # "Rakennuksen enimmäiskorkeus 7,5 m"
  required_setbacks_m:
    front: 5                # "Etäisyys kadusta vähintään 5 m"
    side: 4                 # "Etäisyys tontin rajasta vähintään 4 m"

roof:
  allowed_types: ["harjakatto"]
  max_pitch_ratio: "1:2"    # "Katon kaltevuus saa olla enintään 1:2"
```

## Skeema ja validointi

Käytetään JSON Schema -tyyliä YAML:lle (tai Pydantic-malleja Python-puolella).

Validointi kattaa:
- **Rakenteellinen**: ovatko vaaditut kentät läsnä
- **Looginen**: esim. `max_height > min_height`
- **Ristiriidaton**: eri kohdissa ei ristiriitaisia tietoja

Jos validointi epäonnistuu, käyttäjä saa virheilmoituksen ja joutuu korjaamaan tulkinnan käsin.

## Käyttäjän vahvistusvaihe

LLM:n tulkinta näytetään käyttäjälle käyttöliittymässä:
- Vasemmalla puolella: PDF (selattavissa)
- Oikealla puolella: tulkittu YAML
- Käyttäjä voi muokata YAML-kenttiä suoraan

Käyttäjä vahvistaa joko:
- ✅ "Tulkinta on oikein"
- ✏️ Muokkaa kenttää ja vahvistaa sitten

Vahvistus kirjataan metadataan (`confirmed_by_user: true, confirmed_at: <timestamp>`).

## Vaikeat tapaukset

### Epäselvät määräykset

Jos kaavassa on "rakennus tulee sopeuttaa ympäristöön" — tämä ei ole konkreettinen rajoitus. LLM merkitsee tällaiset kohdat `notes`-kenttään:

```yaml
subjective_constraints:
  - text: "Rakennus tulee sopeuttaa ympäristöön"
    type: "subjektiivinen"
    enforced: false
```

Näitä ei käytetä automaattiseen tarkistukseen, mutta ne näytetään ihmiselle kontekstina.

### Viittaukset muihin asiakirjoihin

"noudatettava rakennustapaohjetta" → linkki ulkoiseen dokumenttiin. Tämä tallennetaan erillisenä tietona, ei sisällytetä YAML:ään.

### Kuntakohtaiset erot

Jokainen kunta käyttää omaa sanastoaan ja rakennettaan. LLM:n täytyy tulkita konteksti, ei vain hakea avainsanoja.

## Tulevaisuuden laajennus (myöhemmin)

Kuntakirjasto:
- `standards/municipalities/<kunta>.yaml` — kunnan erityispiirteet
- `standards/municipalities/<kunta>/<kaava-id>.yaml` — kaavakohtaiset
- Automaattinen haku kunnan karttapalveluista

Tätä ei rakenneta nyt. Käyttäjä tuo kaavan aina itse.

## Integraatio vaatimustenmukaisuustarkistukseen

Tuotettu `constraints.yaml` syötetään sekä:
- **CAD-generointiin** (LLM noudattaa rajoituksia)
- **Vaatimustenmukaisuustarkistukseen** (automaattinen vertailu)

Näin varmistetaan, että kaikki rajoitukset näkyvät sekä suunnittelussa että tarkistuksessa.