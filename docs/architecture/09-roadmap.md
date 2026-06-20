# Tiekartta

## Vaihe 0: Suunnittelu (NYT)

**Tavoite:** Selkeä käsitys siitä mitä rakennetaan ja miten.

**Tuotokset:**
- ✅ GitHub-repo luotu
- ⏳ Arkkitehtuuridokumentit (`docs/architecture/`)
- ⏳ Ensimmäisen testikohteen valinta ja kuvaussuunnitelma

**Aika:** Tämä viikko.

## Vaihe 1: POC (proof of concept)

**Tavoite:** Todistaa että perusputki toimii yhdellä yksinkertaisella kohteella.

**Sisältö:**
- 3D-rekonstruktio yhdestä testikohteesta (Polycam tai COLMAP)
- LLM tuottaa yhden pohjapiirroksen FreeCAD:llä
- Käsin vertailu referenssiin
- Ei web-käyttöliittymää, komentorivikäyttö riittää

**Onnistumiskriteerit:**
- Pohjapiirros on mittakaavassa oikea (±50 mm)
- Seinät ovat suoria ja yhtenäisiä
- Ikkunat ja ovet oikeilla paikoilla

**Aika:** 2-4 viikkoa aktiivista työtä.

**Riippuvuudet:**
- Testikohde valittu
- Isä paikalla kuvaamassa
- LLM-API käytettävissä

## Vaihe 2: Täysi CAD-putki

**Tavoite:** Kaikki neljä piirustustyyppiä generoituvat automaattisesti.

**Sisältö:**
- Pohjapiirros, julkisivut, leikkaukset, asemapiirros
- Kaavamääräysten tulkinta (PDF → YAML)
- Vaatimustenmukaisuustarkistus
- DWG- ja PDF-vienti
- Komentorivikäyttöliittymä (CLI)

**Onnistumiskriteerit:**
- Kaikki neljä piirustustyyppiä tuotettu testikohteeseen
- Vaatimustenmukaisuusraportti generoituu
- DWG avautuu AutoCADissa ilman virheitä
- PDF tulostuu A3:lle oikealla mittakaavalla

**Aika:** 4-8 viikkoa vaiheesta 1.

## Vaihe 3: Ihmisen tarkistus-UI

**Tavoite:** Isä voi tarkistaa ja hyväksyä piirustukset helposti.

**Sisältö:**
- Web-käyttöliittymä (Next.js)
- Käyttäjätunnukset ja projektinhallinta
- Hyväksy/Korjaa/Hylkää-toiminnot
- Korjauspyyntöjen käsittely LLM:llä
- PDF/DWG-lataus

**Onnistumiskriteerit:**
- Isä tarkistaa yhden projektin piirustukset alle tunnissa
- Korjauspyynnöt johtavat uuteen versioon automaattisesti

**Aika:** 4-6 viikkoa vaiheesta 2.

## Vaihe 4: Tuotanto ja skaalaus

**Tavoite:** Järjestelmä valmis tuotantokäyttöön, voidaan ottaa käyttöön useita kohteita.

**Sisältö:**
- Tuotantopalvelin (Hetzner)
- Varmuuskopioinnit
- Lokitus ja monitorointi
- Dokumentaatio käyttäjille
- Virheenkäsittely ja käyttäjätuki

**Onnistumiskriteerit:**
- 5-10 kohdetta käsitelty onnistuneesti
- Ei kriittisiä virheitä
- Käyttäjä (isä) pärjää itsenäisesti

**Aika:** 2-4 viikkoa vaiheesta 3.

## Myöhemmin (ei nyt)

- Useampi samanaikainen käyttäjä
- Kuntakohtainen automaattinen kaavahaku
- LVI- ja sähkökuvien tuki
- 3D-visualisoinnit
- Asiakasportaali (isän asiakkaille)
- Mobiilisovellus kuvien keräykseen

## Päätöksentekopisteet

Projektin aikana on selkeitä päätöspisteitä joissa arvioidaan uudestaan:

| Piste | Arvioidaan |
|---|---|
| Vaiheen 1 loppu | Onko photogrammetria riittävän tarkkaa? |
| Vaiheen 2 loppu | Onko LLM:n tuottama koodi riittävän luotettavaa? |
| Vaiheen 3 loppu | Onko ihmisen tarkistus todella nopeampaa kuin käsin piirtäminen? |
| Vaiheen 4 aikana | Onko kunta hyväksynyt ensimmäiset kuvat? |

Jos joku näistä ei toteudu, palataan taakse ja etsitään vaihtoehto.

## Riskit ja miten niihin varaudutaan

### Riski 1: Photogrammetria ei ole tarpeeksi tarkkaa

**Vaikutus:** Koko putki kaatuu.

**Varaus:** Jos photogrammetria antaa yli 100 mm virheitä, käytetään käsin mitattuja pisteitä täydentämään. Käyttäjä mittaa kriittiset pisteet laserilla ja syöttää ne järjestelmään.

### Riski 2: LLM tuottaa virheellistä FreeCAD-koodia

**Vaikutus:** CAD-malli on rikkinäinen.

**Varaus:** Kaikki generoitu koodi ajetaan validoinnin läpi ennen käyttöä. Ihmisen tarkistus on pakollinen. Tarvittaessa LLM:lle annetaan esimerkkiskriptejä (few-shot) ja iteratiivinen korjausmekanismi.

### Riski 3: Kunta hylkää kuvat

**Vaikutus:** Käyttäjä joutuu piirtämään käsin.

**Varaus:** Ensimmäinen kohde viedään kunnalle esimerkkinä ja varmistetaan hyväksyntä ennen laajempaa käyttöä. Pidetään yhteyttä rakennusvalvontaan ja opetellaan paikalliset vaatimukset.

### Riski 4: Käyttäjä ei opi käyttöliittymää

**Vaikutus:** Järjestelmä ei vähennä työtä.

**Varaus:** Käyttöliittymä testataan isän kanssa varhaisessa vaiheessa. Yksinkertaisuus on suunnitteluperiaate.

### Riski 5: Laskentateho ei riitä

**Vaikutus:** Hidas käsittely.

**Varaus:** Palvelin skaalautuu. Tarvittaessa voidaan käyttää ulkoisia palveluja (esim. Replicate, Modal) raskaaseen laskentaan.

## Aikataulu yhteenveto

| Vaihe | Kesto | Alkaa | Päättyy (tavoite) |
|---|---|---|---|
| 0. Suunnittelu | 1 viikko | nyt | tällä viikolla |
| 1. POC | 2-4 viikkoa | seuraavaksi | Q3 2024 |
| 2. CAD-putki | 4-8 viikkoa | Q3 2024 | Q4 2024 |
| 3. UI | 4-6 viikkoa | Q4 2024 | Q1 2025 |
| 4. Tuotanto | 2-4 viikkoa | Q1 2025 | Q1 2025 |

Aikataulu on suuntaa antava ja riippuu testikohteen saatavuudesta.