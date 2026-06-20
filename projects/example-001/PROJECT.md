# Testikohde 001

## Kohteen perustiedot

| Kenttä | Arvo |
|---|---|
| Osoite | Länsikaari 9, Heinola |
| Kunta | Heinola (Päijät-Häme) |
| Kiinteistötunnus | _täytetään (löytyy kaava-PDF:stä tai kiinteistörekisteristä)_ |
| Rakennustyyppi | Piharakennus |
| Kerrosluku | 1 |
| Kerrosala-arvio | ~30 m² |
| Rakennusvuosi | 2026 (valmistunut tänä vuonna) |
| Kattomuoto | Pulpettikatto |
| Tontin pinta-ala | _täytetään_ |
| Kaava | _täytetään_ |

## Tilanne

- Kuvia ei ole koskaan tehty — rakennus on valmistunut tänä vuonna ilman asianmukaisia lupakuvia
- Tarvitaan nyt viranomaiskuvat (pohja, julkisivut, leikkaus, asemapiirros) rakennusvalvontaa varten
- Pulpettikatto helpottaa: ei harjaviivaa, yksinkertainen kattogeometria
- ~30 m² = tyypillinen piharakennus, todennäköisesti vain rakennusilmoitus-menettely (ei täysi rakennuslupa), mutta silti kuvat vaaditaan

## Kohteen erityispiirteet

- **Pulpettikatto** — yksinkertainen yhden kaltevuuden kattomuoto, hyvä testitapaus (ei harjaa)
- **Uusi rakennus** — pinnat siistejä, valokuvaukselle otollisia (ei rapistumista, ei peittävää kasvillisuutta)
- **Pieni koko** — photogrammetria nopeaa, CAD-mallinnus yksinkertaista
- **Käyttötarkoitus?** — piharakennus voi olla autotalli, varasto, sauna, vierasmökki tms. Käyttötarkoitus vaikuttaa kaavamääräysten soveltamiseen (esim. saunan ja asuinrakennuksen vaatimukset eroavat)
- **Rakennusvuosi 2026** — uusimman lainsäädännön alainen (mm. energiamääräykset, esteettömyys)

## Kuvien keräyssuunnitelma

### Ulkokuvat (tavoite 30-60)
- [ ] Jokainen sivu eri kulmista (P, E, L, I)
- [ ] Nurkat läheltä ja kaukaa
- [ ] Eri korkeuksia (polvi, vyötärö, pää)
- [ ] Räystäät ja kattokaltevuus näkyvissä
- [ ] Perustukset näkyvissä

### Sisäkuvat (tavoite 40-100)
- [ ] Jokaisesta huoneesta 8-15 kuvaa
- [ ] Ovet ja ikkunat erikseen
- [ ] Lattia, seinät, katto
- [ ] Kiintokalusteet ja varustelut näkyvissä

### Yksityiskohdat
- [ ] Ikkunoiden mitat (laseria käyttäen)
- [ ] Ovien mitat
- [ ] Räystäskorkeus
- [ ] Harjakorkeus
- [ ] Sokkelin korkeus

## Laser-mittaukset (täytetään paikan päällä)

Kopioi alla olevat kohdat ja täytä mittauksesi:

```json
{
  "project_id": "example-001",
  "measured_by": "",
  "measured_at": "",
  "reference_points": [
    {
      "id": "ref-001",
      "description": "ensisijainen referenssipiste",
      "value_mm": 0,
      "used_for_scaling": true
    }
  ],
  "external_dimensions": {
    "length_mm": 0,
    "width_mm": 0,
    "height_eave_mm": 0,
    "height_ridge_mm": 0
  },
  "rooms": [],
  "windows": []
}
```

## Lähdetiedostot

- `inputs/sketch.jpg` — käsin piirretty pohjaluonnos
- `inputs/zoning.pdf` — kaava-asiakirja kunnalta
- `inputs/photos/` — kaikki puhelinkuvat
- `inputs/measurements.json` — laser-mittaukset

## Tila

- [ ] Kohteessa käyty ja kuvat otettu
- [ ] Laser-mittaukset dokumentoitu
- [ ] Kaava-PDF hankittu
- [ ] Pohjaluonnos piirretty
- [ ] Photogrammetria ajettu
- [ ] 3D-malli tarkistettu
- [ ] CAD-piirustukset generoitu
- [ ] Ihmisen tarkistus tehty
- [ ] Lopulliset tiedostot toimitettu kuntaan

## Muistiinpanot

_Tämän kentän alle voit kirjoittaa vapaasti paikan päällä huomaamiasi asioita — mikä näyttää erilaiselta kuin odotit, mitä piti arvailla, mitä et muistanut mitata._