# Grocery Bot 3D map preview: Bergen

A copy of [grocery-bot-3d-hills-preview](https://github.com/BusinessManBab/grocery-bot-3d-hills-preview)
built for **Bergen** with the steps in [OTHER-CITIES.md](OTHER-CITIES.md):

- Real terrain: the 3D map is draped over an elevation grid of Bergen (`data/terrain.bin`,
  742 × 689 samples, 0–640 m, relief ×1.0). The grid covers the whole play area and reaches
  Ulriken (643 m) in the east, so the mountains and the fjord read in the section.
- The real street network, water, Byfjorden/Puddefjorden, green areas and ten place names from
  OpenStreetMap (`data/game-map.json`); 88 of 90 delivery homes match a real building footprint
  (15 912 OSM footprints in `data/buildings.json`).
- Cartoon buildings (`src/buildings3d.js`): pitched gable roofs on small houses, one shared
  facade texture with recessed windows, and muted wall/roof colours taken from OSM tags. All
  context buildings stay one merged mesh; order homes keep their own highlight materials.
- Forest on both maps (`src/forest.js`): the 2D map paints the woodland green under the roads,
  and the 3D view plants 22 000 spruce and birch trees there. The tree density is thinned evenly
  over the whole map (`FOREST_STEP` 14) instead of stopping at the cap in the north.
- Sea, lakes and rivers; no trees on or next to water; the river streaks run downstream.
- Bridges: roads cross water on raised decks with railings and piers; trucks drive over them.
- Shop window (bottom right): the v1 grocerybot.no store animation (Erik's `arena.js`, ported
  to `src/shop-iso.js`) showing the robots and trucks inside a shop as the match plays, switched
  by clicking any shop or any row in the standings; order card (left) when you click an order
  house. The floor has the v1 store's four signed departments - Dairy fridges, Pantry shelves,
  Produce bins and the Bakery rack - with fixtures from `shop.json`, and the trucks in the bays
  wear the players' own `looks.js` truck roofs, cabs and carriage colours.
- The replay is a simulated match with the user's rush-ai, recorded from the engine on the
  Bergen map (`tools/simulate_match.py ... 7 bergen`): 601 frames, 5 seats, Rush AI 2 768
  points and 46 deliveries, with the department shop layout.

Bergen data built with the town pipeline in
`grocery-bot-town-bergen/prototypes/town-rush` (`NEW_TOWN_GUIDE.md`):

- 10 shops, hand-picked for road-distance fairness (spread 9 %, every shop within ±5 % of the mean).
- 90 delivery points: 24 real Kartverket addresses (spread across Sentrum, Bryggen, Nordnes,
  Skuteviken, Sandviken, Kalfaret, Nygård, Møhlenpris and Årstad) and 66 named-street points.
- Frozen graph `bergen-bot-v2`: 16 494 nodes and 31 388 directed edges, tunnels excluded.
- Road closure incident: Puddefjordsbroen (13 directed edges), checked not to disconnect any shop
  from any customer.
- Labels: Puddefjorden, Bryggen, Fisketorget, Bergen stasjon, Fløyen, Bergenhus festning,
  Akvariet, Hurtigruteterminalen, Nygårdsparken and Ulriken.
- No part of the play area was cropped: the map keeps the full `build.road_bounds`
  ([5.29, 60.372]–[5.358, 60.414]) and the OSM/terrain box is extended to
  [5.28, 60.365]–[5.40, 60.42] so the fjord, the harbour, the suburbs and Ulriken are all in view.
- Calibration: the OSRM demo server was reachable and an OSRM matrix was cached, but the
  measurement run did not finish; `town.json` keeps the engine defaults and says so.

To make this for another town, follow [OTHER-CITIES.md](OTHER-CITIES.md) (data scripts in
`tools/build_city.py`).

## Landmarks

Bergen's trademarks stand out in the 3D view and are in the "Explore Bergen" menu
(`tools/landmarks.spec.json` -> `node tools/bake_landmarks.mjs` -> `data/landmarks.json`, models in
`src/landmarks.js`; spec written by hand from the OSM data, all outlines are the real OSM ones):

- **Bryggen**: the UNESCO row of wooden Hanseatic wharf houses on the east side of Vågen. 55 houses
  (14 in the front row) on their own OSM outlines, each with weatherboard walls with small windows
  and a steep gabled roof whose ridge runs inland, so the gables face the harbour. Heights, levels
  and facade colours (ochre, oxblood red, white, brown) are the OSM tags of each house; untagged
  houses behind the front row get muted Bryggen colours. Model `wharf`.
- **Bergenhus festning**: Håkonshallen (c. 1261, 37 x 16.4 m stone hall, 25 m to the ridge, dark
  roof) between its two crow-stepped gables, as restored after the Scholeus view of c. 1580
  (model `stonehall`), and the **Rosenkrantz Tower** from its OSM building parts: the 22 m stone
  body, its gabled roof and the onion turret with its 10 m spire (model `osmparts`).
- **Fløibanen**: the funicular from the upper tunnel portal to Fløyen on the real OSM line, with a
  short tunnel above the passing loop, and the two cars, the blue Blåmann and the red Rødhette,
  meeting on the loop (model `funicular`). The forest is cleared along the track.
- **Johanneskirken**: the red-brick neo-Gothic church (1894) from its 20 OSM building parts (nave,
  transepts, turrets, copper roofs) with the 61 m tower, the tallest in Bergen (model `osmparts`).

Cost: 59 sites in 5 draw calls (one per material), about 2 500 triangles. The landmark houses
replace their 61 background footprints, so nothing is drawn twice.

Sources: Wikipedia (Bryggen, Bergenhus Fortress, Fløibanen), Norwegian Wikipedia (Håkonshallen:
stepped gables restored after Scholeus; Rosenkrantztårnet: spire over 10 m), lokalhistoriewiki.no
(Håkonshallen 37 x 16.4 m), kirken.no / visitnorway (Johanneskirken, 61 m tower), OpenStreetMap
tags (heights, levels, colours, building parts).

## Loading

The 3D view uses the fast-loading engine of the Oslo map (from the Kongsberg preview's kit):
`data/buildings.bin` (compact, chunked, simplified footprints, baked by `tools/bake_buildings.mjs`)
is built into meshes by module workers nearest-first; trees are pre-baked (`data/trees.bin`,
`tools/bake_trees.cjs`); a lower quality tier on phones (`?quality=low|high`). On the test GPU
the 3D view is on screen in about 3 s at 60 fps (before: about 44 s and 21 fps).

Flicker fixes (z-fighting): Bergen's buildings.json has about 9 800 shared walls, 146 duplicate
footprints and delivery homes that also stand as background buildings. The bake keeps each shared
wall on one side only (8 252), drops the duplicates and the homes' background copies, doors stand
off the wall, eaves are offset a few centimetres per building, and the camera's near plane follows
its height. The landmark models have no coplanar faces (the wharf houses stand 15 cm in from their
outlines, stacked parts have no floors).

Switch views with the 2D / 3D buttons (or V, or `?view=3d`).

Elevation: Mapzen Terrarium tiles (AWS Open Data). Map data © OpenStreetMap contributors (ODbL).
