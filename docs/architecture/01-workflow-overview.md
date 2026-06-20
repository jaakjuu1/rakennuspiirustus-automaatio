# Työnkulku yleisesti

## Putki yhdellä silmäyksellä

```
┌──────────────────────────────────────────────────────────────────┐
│ 1. Syötteiden kerääminen (käyttäjä)                              │
│    - Puhelinkuvat rakennuksesta                                   │
│    - Laser-mittaukset (ulkomitat, seinät, aukot)                  │
│    - Pohjaluonnos (paperi tai kännykkäpiirros)                    │
│    - Kaava-PDF kunnalta                                           │
└──────────────────────────────────────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│ 2. Kaavamääräysten tulkinta (LLM)                                │
│    - PDF → teksti                                                 │
│    - LLM poimii rajoitteet                                        │
│    - Käyttäjä vahvistaa tulkinnan                                 │
└──────────────────────────────────────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│ 3. 3D-rekonstruktio (automaattinen)                              │
│    - Photogrammetria puhelinkuvista                               │
│    - Laser-mittaukset skaalaukseen                                │
│    - Ihmisen tarkistus (onko malli oikean muotoinen?)             │
└──────────────────────────────────────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│ 4. CAD-piirustusten generointi (LLM + FreeCAD)                   │
│    - LLM kirjoittaa FreeCAD Python-skriptin                       │
│    - FreeCAD tuottaa pohjan, julkisivut, leikkaukset              │
│    - Järjestelmä noudattaa kaavamääräyksiä                        │
└──────────────────────────────────────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│ 5. Vaatimustenmukaisuustarkistus (automaattinen)                 │
│    - Vertaa generoituja piirustuksia kaavamääräyksiin             │
│    - Liputtaa mahdolliset rikkomukset                              │
└──────────────────────────────────────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│ 6. Ihmisen tarkistus ja hyväksyntä (isä)                          │
│    - Web-UI jossa kuvat vierekkäin kaavamääräysten kanssa         │
│    - Hyväksy / korjaa / hylkää per piirustus                      │
│    - Korjaukset menevät takaisin vaiheeseen 4                      │
└──────────────────────────────────────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│ 7. Vienti (automaattinen)                                        │
│    - PDF (A3, mittakaava, nimikenttä)                             │
│    - DWG (vaihtoehtoinen)                                         │
│    - Toimitus kunnalle                                            │
└──────────────────────────────────────────────────────────────────┘
```

## Vaiheiden tarkempi kuvaus

### Vaihe 1: Syötteiden kerääminen

**Käyttäjän vastuulla.** Tämä on ainoa vaihe jossa käyttäjä on fyysisesti paikalla kohteessa.

**Kuvat:** 50-200 puhelinkuvaa rakennuksen ulkopuolelta ja sisätiloista. Hyvä peitto eri kulmista ja korkeuksista.

**Laser-mittaukset:** Vähintään:
- Yhden huoneen molemmat seinäpituudet ja korkeus
- Rakennuksen ulkomitat (pituus × leveys)
- Vähintään yksi referenssipiste (esim. oviaukon leveys)
- Ikkuna- ja oviaukkojen mitat

**Pohjaluonnos:** Käsin piirretty luonnos, jossa näkyy huoneiden jako ja keskeiset mitat. Ei tarvitse olla mittakaavassa.

**Kaava-PDF:** Käyttäjä hakee tonttiaan koskevan asemakaavan kunnan sivuilta tai kaupungin karttapalvelusta. Toimittaa PDF:n järjestelmään.

### Vaihe 2: Kaavamääräysten tulkinta

LLM lukee kaava-PDF:n ja poimii siitä strukturoidun listan rajoitteista:

```yaml
tontti:
  pinta_ala: 850  # m²
  rakennusoikeus: 220  # k-m²
  kerrosluku_max: II
  rakennusala: määritelty erilliskartassa
rajoitukset:
  - kattomuoto: harjakatto, kaltevuus 1:2 - 1:3
  - julkisivumateriaali: puu
  - väritys: vaaleat sävyt
  - autopaikat_vähintään: 2
  - hulevedet: imeytettävä tontilla
```

Käyttäjä vahvistaa tulkinnan ennen jatkamista.

### Vaihe 3: 3D-rekonstruktio

Photogrammetria-työkalu tuottaa 3D-mallin puhelinkuvista. Laser-mittaukset antavat absoluuttisen mittakaavan.

Käyttäjä tarkistaa visuaalisesti: onko malli oikean muotoinen, näkyykö kaikki olennainen? Korjaa puutteita (esim. "tämä ikkuna puuttuu").

### Vaihe 4: CAD-generointi

LLM saa syötteenä:
- 3D-mallin geometria
- Laser-mittaukset
- Kaavamääräykset strukturoituna
- Käyttäjän korjauspyynnöt

Tuottaa: FreeCAD Python-skriptin joka piirtää:
- Pohjapiirroksen (kaikki kerrokset)
- 4 julkisivukuvaa (N, S, E, W)
- 2 leikkauskuvaa (pitkittäis, poikittainen)
- Asemapiirroksen (vaatii tontin rajat erillisenä syötteenä)

### Vaihe 5: Vaatimustenmukaisuustarkistus

Järjestelmä vertaa tuotettuja piirustuksia kaavamääräyksiin:

- Onko kerrosluku sallittu?
- Onko rakennusoikeus ylittymätön?
- Onko etäisyys tontin rajoista säännöissä?
- Onko autopaikkojen määrä riittävä?
- Onko kattokaltevuus sallitulla välillä?

Liputtaa mahdolliset rikkomukset ihmisen tarkistettavaksi.

### Vaihe 6: Ihmisen tarkistus

Web-käyttöliittymä jossa isä katsoo tuotettuja piirustuksia. Jokaisen piirustuksen kohdalla:
- Hyväksy (siirry seuraavaan)
- Korjaa (kirjoita kommentti, palaa vaiheeseen 4)
- Hylkää (aloita alusta)

Näkee rinnalla kaavamääräykset ja mittaukset kontekstina.

### Vaihe 7: Vienti

Hyväksytyistä piirustuksista:
- PDF A3-muotoon oikealla mittakaavalla
- Nimikenttä RT-kortin mukaan
- DWG erikseen pyydettäessä

## Aikajänne yhdelle kohteelle

| Vaihe | Kesto (tavoite) | Kuka tekee |
|---|---|---|
| 1. Syötteiden keruu | 2-4 h kohteessa | Käyttäjä |
| 2. Kaavatulkinta | 5-15 min | LLM + käyttäjän vahvistus |
| 3. Rekonstruktio | 30-60 min laskentaa | Automaattinen |
| 4. CAD-generointi | 5-15 min | LLM |
| 5. Vaatimustenmukaisuus | 1 min | Automaattinen |
| 6. Ihmisen tarkistus | 30-60 min | Isä |
| 7. Vienti | 1 min | Automaattinen |
| **Yhteensä** | **~4-7 h työtä + odotus** | |

Verrattuna käsin piirtämiseen (tyypillisesti 1-3 päivää piirtäjän työtä), tämä on merkittävä säästö.