# Rakennuspiirustus-automaatio

Puoliautomaattinen putki olemassa olevien rakennusten viranomais-CAD-piirustusten tuottamiseen Suomessa.

## Mitä tämä tekee

Ottaa vastaan:
- Puhelinkuvia rakennuksesta
- Laser-mittauksia
- Kaavamääräys-PDF:n kunnalta

Tuottaa:
- Pohjapiirroksen
- Julkisivukuvat (4 suuntaa)
- Leikkauskuvat
- Asemapiirroksen

Ihmisen rooli on tarkistaa ja hyväksyä — ei piirtää alusta asti.

## Kenelle

Rakennusalan ammattilaisille (erityisesti CAD-piirtäjille) jotka haluavat automatisoida rutiininomaista piirtotyötä olemassa oleville rakennuksille.

## Tila

**Vaihe 0: Suunnittelu** — arkkitehtuuridokumentaatio luodaan.

Katso `docs/architecture/` yksityiskohtainen kuvaus.

## Aloitus

1. Lue [`docs/architecture/00-vision.md`](docs/architecture/00-vision.md) — mitä ja miksi
2. Lue [`docs/architecture/01-workflow-overview.md`](docs/architecture/01-workflow-overview.md) — miten
3. Lue [`docs/architecture/09-roadmap.md`](docs/architecture/09-roadmap.md) — milloin

## Lisenssi

[Sisäinen kehitys, lisenssi päätetään myöhemmin]

## Teknologia

- LLM: Claude Sonnet 4+
- CAD: FreeCAD
- 3D-rekonstruktio: COLMAP, OpenMVS, 3D Gaussian Splatting
- Backend: FastAPI
- Frontend: Next.js
- Hostaus: itse hostattu Hetzner-palvelin

Katso [`docs/architecture/08-tech-stack.md`](docs/architecture/08-tech-stack.md) perustelut.