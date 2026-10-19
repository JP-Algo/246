# 246 South 2nd Street — floor area model

A single-page zoning floor area model for 246 South 2nd Street: a 3D massing view, a floor plan editor with dimensions, and live totals checked against the FAR and height caps.

- `index.html` is the whole app. Open it in a browser to run it locally (edits then save in that browser only).
- The published version lives at https://claude.ai/artifact/EpKos4iRHSvJAjeTEHAeVV, where edits save to the artifact's database and show up on every device.

Reusable, parametric design rules (lot line separation, deductions, core, amenity and Zone Green caps) live in `design-rules.json`, so the next building can start from them.

To publish changes, update `index.html` here and republish it to the same artifact URL.

## Renderings
Photoreal Cycles renderings built from the floor area model and NYC open data (no hand modelling), the same pipeline as 725 Grand:
```bash
python scripts/render/fetch_assets.py      # once: CC0 sky, textures, trees, props from Poly Haven (~600 MB)
python scripts/render/context_nyc.py       # once: NYC footprints + roof heights, street centerlines, street trees
node scripts/render/export_scene.mjs render/state.json render/scene.json
blender -b -P scripts/render/render_scene.py -- --view 1      # and --view 2
```
1. `render/state.json` is the model: the artifact database's `model/current` document (or a saved model state).
   `export_scene.mjs` loads it into `index.html` in headless Edge and writes each floor's elevation, height, areas,
   core and light well through the page's own code to `render/scene.json`.
2. `context_nyc.py` pulls NYC Open Data (Building Footprints, CSCL centerline, 2015 Street Tree Census) within 800 m
   and puts it in the model's plan frame, located from the MapPLUTO tax lot: x from the rear lot line to South 2nd
   Street, y (z in the render) from the #242 line to the #248 line.
3. `render_scene.py` (with `city.py`) builds the scene in Blender 5.2: the building from the floor outlines, terraces,
   balconies, core and light well; neighbours extruded to their surveyed roof heights with generated street façades
   (#242 and #248 tuned to Street View); census trees, cobra-head lights, parked cars; the Poly Haven sky turned so the
   sun is at the requested azimuth. The model has no façade, so it follows the `facade` rule in `design-rules.json`.
   View 1 is eye level across South 2nd, view 2 from a roof across the street. It writes `render/246-s2nd_view<N>.png`
   (16-bit) plus a JPEG. Camera, lens, sun and resolution are flags; see its header.

`render/` is git-ignored.
