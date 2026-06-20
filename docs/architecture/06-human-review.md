# Ihmisen tarkistus ja hyväksyntä

## Miksi tämä on kriittisin vaihe

LLM voi tuottaa kelvollisen näköistä jälkeä joka on virheellinen. Ainoa keino varmistaa laatu on ihmisen tarkistus. Käyttäjä (isä) on ammattilainen joka tunnistaa virheet heti.

Tämä vaihe on myös käyttäjäkokemuksen ydin: jos tämä on tuskallinen, koko järjestelmä on hyödytön.

## Käyttöliittymän vaatimukset

### Periaatteet

1. **Yksinkertainen.** Käyttäjä ei ole tietokoneasiantuntija.
2. **Nopea.** Tarkistus ei saa kestää tunteja.
3. **Konkreettinen.** Käyttäjä näkee tarkan asian jota tarkistaa.
4. **Palautettava.** Käyttäjä voi aina peruuttaa ja palata taakse.

### Näkymät

#### Pohjapiirros
- Keskellä suuri kuva pohjapiirroksesta
- Oikealla lista huoneista ja niiden mitoista
- Alhaalla kaavamääräykset joita tässä piirustuksessa noudatetaan
- Toimintopainikkeet: Hyväksy / Korjaa / Hylkää

#### Julkisivut
- Neljä julkisivua vierekkäin (P, E, L, I)
- Jokaisen alla kyseisen suunnan kaavamääräykset
- Vertaa vierekkäin 3D-mallin renderöinnin kanssa

#### Leikkaukset
- Kaksi leikkausta vierekkäin
- Merkinnät kerroskorkeuksista ja tasoista

#### Asemapiirros
- Tontin rajat, rakennuksen sijainti, etäisyydet
- Autopaikat, viheralueet, hulevedet

#### Vaatimustenmukaisuusraportti
- Lista kaikista tarkistuksista
- Väreillä merkityt tilat (OK, varoitus, virhe)
- Klikkaamalla avautuu tarkempi selitys

## Toimintopainikkeet

### Hyväksy
- Siirry seuraavaan piirustukseen
- Kirjaa hyväksymisaika ja -käyttäjä
- Kun kaikki hyväksytty, siirry vientiin

### Korjaa
- Avaa tekstikenttä korjauspyynnölle
- Käyttäjä kirjoittaa: "Muuta keittiön oviaukkoa 200 mm itään päin"
- Tai: "Lisää ikkuna länsiseinään, 1200×1400 mm, korkeus 900 mm lattiasta"
- Korjauspyyntö menee takaisin LLM:lle uudelleenkäsittelyyn
- Uusi versio näytetään, kun valmis

### Hylkää
- Koko piirustus hylätään
- Käyttäjä perustelee hylkäyksen
- Käyttäjä päättää: palata vaiheeseen 1 (uudet mittaukset) vai vain generoida uudestaan

### Lisätietoa
- Näyttää kontekstin: 3D-malli, laser-mittaukset, kaavamääräykset
- Käyttäjä voi vertailla rinnakkain

## Korjauspyyntöjen käsittely

LLM saa korjauspyynnön kontekstiksi:
- Alkuperäinen syöte (mittaukset, kaavamääräykset)
- Aiempi generoitu versio
- Käyttäjän korjauspyyntö

Tuottaa uuden version. Käyttäjä tarkistaa uudestaan.

## Tarkistuksen kesto

Tavoite: yhden piirustuksen tarkistus 5-10 minuuttia. Koko putken tarkistus 30-60 minuuttia.

Jos tarkistus kestää kauemmin, järjestelmässä on jotain vialla.

## Mobiiliystävällisyys

Käyttöliittymä toimii tabletilla ja puhelimella. Käyttäjä voi tarkistaa kuvia myös kohteessa käynnin jälkeen.

## Lokalisointi

Käyttöliittymä suomeksi. Termit rakennusalan suomalaista sanastoa.

## Saavutettavuus

- Riittävä kontrasti
- Suuri fonttikoko oletuksena
- Näppäimistön käyttö tuettu

## Tietoturva ja yksityisyys

Projektitiedot ovat luottamuksellisia:
- Käyttäjätunnukset ja salasanat
- Istuntoaikakatkaisu
- Tiedot eivät joudu kolmannelle osapuolelle

Alusta: itse hostattu (Hetzner-palvelin, ei pilveä).