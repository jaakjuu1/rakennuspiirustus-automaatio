# 3D-rekonstruktio

## Tavoite

Tuottaa rakennuksesta 3D-malli, jossa:
- Geometria on oikea (seinät suorassa, aukot oikeilla paikoilla)
- Mittakaava on absoluuttinen (metrit, ei suhteellinen)
- Malli on tarpeeksi kevyt käsiteltäväksi (alle 100 MB)

Mallia käytetään:
- Visuaaliseen tarkistukseen (onko kaikki paikallaan?)
- CAD-piirustusten lähtökohtana
- Julkisivujen ja leikkausten automaattiseen projektioon

## Menetelmä: photogrammetria + mittakaavaus

Photogrammetria tuottaa 3D-pistepilven tai mesh-mallin useista valokuvista. Se ei yksinään anna absoluuttista mittakaavaa — laser-mittaukset tekevät sen.

### Vaiheet

```
Puhelinkuvat (50-200 kpl)
        ↓
[Feature extraction] - SIFT/SuperPoint - piirteiden tunnistus
        ↓
[Feature matching] - kuvaparien yhdistäminen
        ↓
[SfM - Structure from Motion] - kamerapaikkojen ja 3D-pisteiden laskenta
        ↓
[Multi-view stereo / Gaussian splatting] - tiheän mallin tuotto
        ↓
[Scale alignment] - laser-mittauksia vastaan
        ↓
[Mesh generation] - pistepilvestä kolmioverkko
        ↓
[Cleanup] - ylimääräisen datan poisto, seinien tasoitus
        ↓
GLB-malli
```

## Työkaluvalinnat

### Ensisijainen: 3D Gaussian Splatting

**Plussat:**
- Nopea (30-60 min tyypilliselle rakennukselle)
- Erinomainen visuaalinen laatu
- Toimii kohtuullisella kuvamäärällä
- Avoin lähdekoodi

**Miinukset:**
- Ei tuota geometrista mesh:iä suoraan (tarvitsee erillisen muunnoksen)
- CAD-käyttöön pistepilvi on raskas

**Toteutus:** `gsplat` (kirjasto) + `nerfstudio`-pohjainen putki

### Vaihtoehto: COLMAP + OpenMVS

**Plussat:**
- Tuottaa mesh-mallin suoraan
- Vakiintunut, hyvin tuettu
- Parempi CAD-käyttöön (seinät tunnistettavissa)

**Miinukset:**
- Hitaampi kuin Gaussian Splatting
- Vaatii enemmän kuvia

**Toteutus:** COLMAP CLI + OpenMVS-jälkikäsittely

### Vaihtoehto kaupallisena: Polycam / Luma AI

**Plussat:**
- Helppokäyttöinen mobiilisovellus
- Ei tarvitse paikallista laskentatehoa
- Hyvä laatu nopeasti

**Miinukset:**
- Maksullinen
- Datan omistus kolmannella osapuolella
- Ei yhtä hyvä mittatarkkuus kuin avoimilla työkaluilla

**Käyttö:** Vaiheessa 1 POC-mielessä nopeaan testaukseen.

## Suositus vaiheittain

| Vaihe | Työkalu | Syy |
|---|---|---|
| POC (ensimmäinen testikohde) | Polycam | Nopea testi ilman infraa |
| Tuotanto (vaihe 2) | COLMAP + OpenMVS | Ilmainen, tarkka, paikallinen |
| Myöhemmin (vaihe 4) | Gaussian Splatting | Visuaalinen laatu |

## Kuvien keräysprotokolla

Käyttäjä ohjeistetaan seuraavasti (dokumentoidaan käyttöliittymään):

### Ulkokuvat
- Kävele rakennuksen ympäri 2-3 metrin etäisyydellä
- Ota kuva joka 1-2 metrin välein
- Eri korkeuksia: polvet, vyötärö, pää
- Kaikki neljä sivua + nurkat
- Yhteensä 30-60 kuvaa

### Sisäkuvat
- Jokaisesta huoneesta 8-15 kuvaa
- Eri kulmista, kattokin mukaan
- Ovet ja ikkunat erikseen läheltä
- Yhteensä 40-100 kuvaa

### Yksityiskohdat
- Aukot (ikkunat, ovet) läheltä
- Korkeuserot, kynnys
- Liitokset ja rakenteet näkyvillä

## Laser-mittaukset

Vähimmäismittaukset skaalausta varten:
- **Rakennuksen ulkomitat:** pituus × leveys
- **Yhden huoneen kaikki seinäpituudet** (referenssi 3D-mallille)
- **Vähintään yksi oviaukon leveys** (toissijainen referenssi)
- **Räystäskorkeus ja harjakorkeus** (julkisivukuvia varten)

Mittaukset annetaan JSON-muodossa, kts. `02-data-model.md`.

## Ihmisen tarkistusvaihe

3D-mallin valmistuttua käyttäjä katsoo sitä ja vahvistaa tai korjaa:
- "Tämä seinä on oikein" → OK
- "Tämä ikkuna puuttuu" → otetaan lisää kuvia tai täydennetään mittauksilla
- "Tämä uloke on väärän kokoinen" → korjaus laser-mittauksilla

Tämä on kriittinen vaihe: huono 3D-malli → huonot CAD-kuvat.

## Rajaukset ja rajoitteet

**Ei tueta:**
- Maanalaiset rakenteet (kellarit)
- Monimutkaiset kattorakenteet (esim. useampia harjoja)
- Erittäin heijastavat tai läpinäkyvät pinnat

**Heikosti tuettu:**
- Hyvin tiheä kasvillisuus peittää seinää
- Pimeät sisätilat ilman lisävalaistusta
- Liikkuvat kohteet (ihmiset, autot kuvissa)

Nämä dokumentoidaan käyttäjälle etukäteen.