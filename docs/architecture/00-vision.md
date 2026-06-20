# Visio

## Ongelma

Suomessa rakennusvalvonta vaatii rakennusluvan hakemista varten useita teknisiä piirustuksia: pohjapiirrokset, julkisivukuvat, leikkauspiirrokset ja asemapiirroksen. Nämä piirustukset on perinteisesti tuotettu käsin CAD-ohjelmistolla.

Tilanne, jota tämä projekti palvelee: **olemassa oleva rakennus on jo pystyssä, mutta sille tarvitaan viranomaiskuvia**. Syitä ovat tyypillisesti:

- Rakennus on rakennettu ilman asianmukaisia lupakuvia
- Alkuperäiset kuvat ovat kadonneet tai vanhentuneet
- Kuntotarkastus, vakuutus tai kiinteistökauppa vaatii dokumentaatiota
- Muutos- tai korjaushanke tarvitsee nykytilakuvia lähtökohdaksi

Kuvien tuottaminen vaatii erityisosaamista (CAD-piirtäjä, arkkitehti). Käsin piirtäminen on hidasta ja kallista. Kokeneen piirtäjän (tässä projektissa isän) tietotaito on arvokas, mutta hän ei halua tehdä rutiininomaista piirtotyötä eikä hallitse tietokoneavusteista työskentelyä riittävästi.

## Ratkaisu

Puoliautomaattinen putki, joka:

1. Ottaa vastaan puhelimella otettuja valokuvia olemassa olevasta rakennuksesta
2. Ottaa vastaan laser-mittauksia antamaan absoluuttisen mittakaavan
3. Lukee kunnan kaavamääräykset käyttäjän tuomasta PDF:stä
4. Rekonstruoi rakennuksen 3D-malliksi photogrammetrialla
5. Tuottaa CAD-piirustukset FreeCAD:llä LLM:n ohjaamana
6. Tarkistaa piirustukset kaavamääräyksiä vasten automaattisesti
7. Antaa ihmisen (isän) tarkistaa ja hyväksyä lopputulos
8. Vie lopulliset kuvat PDF- ja DWG-muotoon toimitettavaksi kunnalle

Ihmisen rooli: **päätöksentekijä ja hyväksyjä**, ei rutiinipiirtäjä. LLM:n rooli: tekninen toteuttaja, joka noudattaa kaavamääräyksiä ja tuottaa piirustukset. Järjestelmän rooli: orkestroida, tarkistaa ja varmistaa vaatimustenmukaisuus.

## Kohderyhmä ja käyttäjät

### Ensisijainen käyttäjä: piirtäjä (isä)
- Hallitsee rakennussuunnittelun ja CAD-piirtämisen ammattisäännöt
- Ei halua tehdä rutiininomaista piirtotyötä
- Ei ole tietokoneavusteisen CAD-työskentelyn asiantuntija
- Hyväksyy ja korjaa lopputulosta

### Toissijainen käyttäjä: tontin omistaja / rakennuksen haltija
- Kerää kuvat ja mittaukset kohteessa
- Hakee kaava-asiakirjat kunnan sivuilta
- Toimittaa kuvat viranomaisille

### Järjestelmä itse
- Ei ole ammattilaisen korvike vaan työkalu, joka hoitaa rutiinin

## Menestyskriteerit

Projekti on onnistunut, kun:

1. Yhdestä kohteesta voidaan tuottaa vaatimustenmukaiset kuvat ilman käsin piirtämistä
2. Ihminen (isä) pystyy hyväksymään kuvat nopeasti (muutamia korjauksia, ei koko piirtämistä)
3. Kunnan rakennusvalvonta hyväksyy tuotetut kuvat ensimmäisellä kerralla
4. Putki on toistettavissa eri kohteisiin ilman merkittävää uudelleensuunnittelua

## Rajaus

**Mukana:**
- Yksittäisen rakennuksen viranomaiskuvat (pohja, julkisivut, leikkaukset, asemapiirros)
- Kaavamääräysten tulkinta käyttäjän tuomasta PDF:stä
- Ihmisen hyväksymiskierros ennen toimitusta

**Ei mukana (myöhemmin):**
- Kuntakohtainen automaattinen kaavahaku rajapinnoista
- LVI-, sähkö- tai rakennesuunnittelu
- 3D-visualisoinnit myyntiä varten
- Energiatodistukset ja muut liitteet
- Lupaprosessin sähköinen asiointi

## Ei tavoitteita

- Ei korvaa ammattipiirtäjää kokonaan
- Ei skaalaudu automaattisesti kaikkiin kuntiin
- Ei käsittele uudiskohteita (joissa suunnitelmat tehdään ennen rakentamista)