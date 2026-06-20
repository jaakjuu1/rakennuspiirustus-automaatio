# Teknologiavalinnat

## Yhteenveto valinnoista

| Kerros | Valinta | Vaihtoehdot harkittu |
|---|---|---|
| **LLM** | Claude Sonnet 4+ | GPT-4o, Gemini 2.5 Pro |
| **CAD-moottori** | FreeCAD | AutoCAD (lisenssi), BricsCAD |
| **3D-rekonstruktio** | COLMAP + OpenMVS (myöh. Gaussian Splatting) | Polycam (POC), Metashape |
| **OCR/PDF** | PyMuPDF + Tesseract | pdfplumber, PaddleOCR |
| **Backend** | FastAPI (Python) | Node.js, Go |
| **Frontend** | Next.js (React) | Vue, Svelte |
| **Tietokanta** | PostgreSQL + tiedostojärjestelmä | MongoDB |
| **Laskenta** | GPU-palvelin (Hetzner) | Pilvi (AWS, GCP) |
| **Hostaus** | Itse hostattu Hetzner-palvelin | SaaS-palvelu |

## Perustelut

### Claude Sonnet 4+ LLM:ksi

**Miksi:**
- Erinomainen koodigeneraattori (erityisesti Python ja CAD-skriptit)
- Pitkä konteksti (200K tokenia) → koko kaava-PDF mahtuu kontekstiin
- Vahva suomen kielen ymmärrys
- Antaa selityksiä päätöksilleen (vs. musta laatikko)

**Hinta:** API-kutsu n. $3-15 per projekti (riippuen koosta).

**Vaihtoehtona GPT-4o:**
- Yhtä hyvä koodi
- Lyhyempi konteksti
- Kalliimpi

**Avoinna oleva kysymys:** Pitääkö olla offline-versio (oma malli)? Ei nyt.

### FreeCAD CAD-moottoriksi

**Miksi:**
- Täysi Python API → LLM voi kirjoittaa sille koodia
- Avoin ja ilmainen
- DWG-vienti tuettu (ODA Converter)
- Aktiivinen kehitys
- Toimii Linuxilla (headless)

**Miinukset:**
- DWG-viennin laatu ei aina täydellinen (AutoCAD vs. FreeCAD -erot)
- Osa RT-korttien mukaisista merkinnöistä vaatii manuaalista työtä

**Vaihtoehtona AutoCAD:**
- Paras DWG-laatu
- Lisenssi n. €1500/vuosi per käyttäjä
- Ei Python API:a (vain .NET ja Lisp)
- Ei sovellu tähän käyttöön

**Vaihtoehtona BricsCAD:**
- Halvempi kuin AutoCAD
- DWG-yhteensopiva
- Ei merkittävästi parempi kuin FreeCAD tähän tarkoitukseen

### COLMAP + OpenMVS 3D-rekonstruktioon

**Miksi:**
- Avoin ja ilmainen
- Tuottaa mesh-mallin (CAD-käyttöön sopiva)
- Vakiintunut, hyvin dokumentoitu
- Paikallinen laskenta (data ei poistu)

**Miinukset:**
- Vaatii kohtuullisen kuvamäärän (50+)
- Laskenta-aika tunteja suurilla aineistoilla
- Vaatii GPU:ta nopeaan käsittelyyn

**Myöhemmin: Gaussian Splatting:**
- Nopeampi
- Visuaalisesti kauniimpi
- Ei tuota mesh:iä suoraan (tarvitsee muunnoksen)

### PyMuPDF + Tesseract PDF-käsittelyyn

**Miksi:**
- PyMuPDF on nopea ja luotettava PDF-parsija
- Tesseract on kypsä OCR-moottori
- Molemmat ilmaisia ja avoimia

**Vaihtoehtona pdfplumber:**
- Parempi tekstin erotus
- Hitaampi
- Samaan tarkoitukseen riittävä

### FastAPI backendille

**Miksi:**
- Python → sama kieli kuin CAD-skriptit ja ML-putket
- Hyvä async-tuki
- Automaattinen OpenAPI-dokumentaatio
- Suorituskykyinen

**Vaihtoehtona Node.js:**
- Nopeampi web-socket-tuki
- Eristetty Python-ympäristöstä
- Lisää monimutkaisuutta (kaksi kieltä)

Päätös: Python kauttaaltaan, FastAPI backend.

### Next.js frontendille

**Miksi:**
- React-pohjainen, tuttu ja hyvin tuettu
- SSR/SSG tarvittaessa
- Hyvä TypeScript-tuki
- Runsaasti komponenttikirjastoja (piirustusten katseluun)

**Vaihtoehtona Svelte/Vue:**
- Kevyempi
- Pienempi ekosysteemi
- Vähemmän valmiita CAD-katselukomponentteja

### PostgreSQL + tiedostojärjestelmä tietokannaksi

**Miksi PostgreSQL:**
- Projekti-data (käyttäjät, projektit, tilat)
- Vahva tyyppijärjestelmä
- Ilmainen ja itse hostattavissa

**Miksi tiedostojärjestelmä:**
- 3D-mallit ja CAD-tiedostot ovat suuria
- Git-versionhallinta toimii tiedostoille hyvin
- Ihmisen selattavissa ja muokattavissa

**Vaihtoehtona MongoDB:**
- Joustavampi skeema
- Vähemmän sopiva rakenteiselle datalle
- Lisenssikysymyksiä

### Hetzner-palvelin laskennalle

**Miksi:**
- Edullinen (n. €50-100/kk GPU-palvelin)
- EU-datakeskukset (tietosuoja)
- Itse hallittavissa
- Hyvä Linux-tuki

**Vaatimukset:**
- GPU (RTX-sarja) photogrammetriaa varten
- Riittävästi RAM (32-64 GB)
- Nopea levy (NVMe SSD)

**Vaihtoehtona AWS/GCP:**
- Helpompi skaalautuvuus
- Kalliimpi
- Tietosuoja-kysymyksiä

Päätös: Hetzner itse hostattuna.

## Lokalisaatio

Kaikki käyttäjäkohtaiset tekstit suomeksi. Tekninen dokumentaatio englanniksi (yhteensopivuus kirjastojen kanssa).

## Versiohallinta ja kehitys

- **Git**: kaikki koodi, konfiguraatio, dokumentaatio
- **Branching**: trunk-based, lyhyet feature branchit
- **CI/CD**: GitHub Actions (testit, deploy)
- **Pakettien hallinta**: `uv` Pythonille, `pnpm` Node:lle

## Turvallisuus

- HTTPS kaikkialla (Caddy-käänteinen proxy)
- Salasanat hashattu (Argon2)
- Istuntoaikakatkaisu
- Pääsy vain tarvittaville henkilöille
- Lokitus ja audit trail kaikille toiminnoille
- Ei kolmannen osapuolen analytiikkaa

## Kapasiteettisuunnittelu

**POC-vaihe:** 1 käyttäjä, 1-5 projektia, kaikki yhdellä palvelimella.

**Tuotanto (vaihe 2):** 5-20 käyttäjää, 10-100 projektia, skaalautuva palvelin.

**Ei tarvita:** miljoona käyttäjää, reaaliaikainen käsittely, globaali CDN.