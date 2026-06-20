# Tietomalli

## Ylätason rakenne

Jokainen projekti (yksi rakennus yhdellä tontilla) on kansio, joka sisältää kaikki siihen liittyvät tiedot. Kansiorakenne on ihmisluettava ja versionhallittavissa.

```
projects/
└── <projekti-id>/
    ├── inputs/
    │   ├── photos/
    │   ├── measurements.json
    │   ├── sketch.jpg
    │   └── zoning.pdf
    ├── processed/
    │   ├── model.glb
    │   ├── constraints.yaml
    │   └── compliance-report.md
    ├── cad/
    │   ├── floorplan.dwg
    │   ├── elevations.dwg
    │   ├── sections.dwg
    │   └── site-plan.dwg
    └── output/
        ├── floorplan.pdf
        ├── elevations.pdf
        ├── sections.pdf
        ├── site-plan.pdf
        └── README.md
```

## Keskeiset tietorakenteet

### measurements.json — laser-mittaukset

```json
{
  "project_id": "esimerkki-2024",
  "measured_by": "isä",
  "measured_at": "2024-08-15",
  "reference_points": [
    {
      "id": "ref-001",
      "description": "etusisäänkäynnin oviaukon leveys",
      "value_mm": 900,
      "used_for_scaling": true
    }
  ],
  "external_dimensions": {
    "length_mm": 12500,
    "width_mm": 8500,
    "height_eave_mm": 2700,
    "height_ridge_mm": 4900
  },
  "rooms": [
    {
      "id": "kitchen",
      "name": "Keittiö",
      "floor": 1,
      "walls_mm": {
        "north": 4200,
        "east": 3100,
        "south": 4200,
        "west": 3100
      },
      "height_mm": 2700,
      "openings": [
        {"type": "door", "wall": "north", "width_mm": 900, "position_mm_from_west": 1500}
      ]
    }
  ],
  "windows": [
    {"wall": "south", "floor": 1, "width_mm": 1200, "height_mm": 1400, "sill_height_mm": 900}
  ]
}
```

### constraints.yaml — kaavamääräykset (LLM:n tulkitsemina)

```yaml
source:
  document: "zoning.pdf"
  municipality: "Tampere"
  plan_id: "asemakaava 1234"
  interpreted_at: "2024-08-15T10:30:00Z"
  interpreted_by: "claude-sonnet-4"
  confirmed_by_user: true

site:
  plot_area_m2: 850
  buildable_area_m2: 180
  build_right_k_m2: 220

building_constraints:
  max_stories: 2
  max_height_m: 7.5
  max_eave_height_m: 4.0
  required_setbacks_m:
    front: 5
    side: 4
    rear: 4

roof:
  allowed_types: ["harjakatto", "aumakatto"]
  min_pitch_ratio: "1:3"
  max_pitch_ratio: "1:1.5"
  allowed_materials: ["tiili", "pelti", "huopa"]

facade:
  allowed_materials: ["puu", "tiili", "rapattu"]
  color_palette: "vaaleat, maanläheiset sävyt"

site_requirements:
  parking_spaces_min: 2
  stormwater_management: "imeytys tontilla"
  green_area_min_percent: 30

other:
  energy_class_min: "C"
  accessibility: "ei vaatimusta yksityisasunnoksi"
```

### compliance-report.md — tarkistuksen tulos

```markdown
# Vaatimustenmukaisuusraportti

Tarkistettu: 2024-08-15T11:00:00Z

## Yhteenveto
- ✅ Kerrosluku: OK (1 kerros, sallittu 2)
- ⚠️ Kerrosala: 195 k-m² / sallittu 220 k-m² (89% käytössä)
- ✅ Korkeus: 4.9 m < 7.5 m
- ❌ Autopaikat: 1 / vaaditaan 2
- ⚠️ Tontin käyttö: vihreää alaa 28% < vaadittu 30%
```

## Tallennusformaatit

- **Mittaukset**: JSON, ihmisen muokattavissa, skeema validoitu
- **Kaavamääräykset**: YAML, ihmisen muokattavissa, LLM:n tuottama
- **3D-malli**: GLB (glTF 2.0), standardi ja laajasti tuettu
- **CAD**: DWG (lähtö) + PDF (toimitus)
- **Muu data**: Markdown, ihmisluettava

## Validointi ja skeemat

Jokaiselle tietorakenteelle on JSON Schema tai YAML Schema. Validointi ajetaan:
- Syötteitä vastaanotettaessa (estää huonoa dataa etenemästä)
- Ennen CAD-generointia (varmistaa rakenteen eheyden)
- Ennen vientiä (varmistaa lopullisen laadun)

Skeemat tallennetaan `schemas/`-hakemistoon.