# Grocery Bot 3D map preview: Bergen

A copy of [grocery-bot-3d-hills-preview](https://github.com/BusinessManBab/grocery-bot-3d-hills-preview)
built for **Bergen** with the steps in [OTHER-CITIES.md](OTHER-CITIES.md):

- Real terrain: the 3D map is draped over an elevation grid of Bergen (`data/terrain.bin`,
  742 × 689 samples, 0–640 m, relief ×1.0). The grid covers the whole play area and reaches
  Ulriken (643 m) in the east, so the mountains and the fjord read in the section.
- The real street network, water, Byfjorden/Puddefjorden, green areas and ten place names from
  OpenStreetMap (`data/game-map.json`); 88 of 90 delivery homes match a real building footprint
  (`data/buildings.json`).
- Sea, lakes and rivers; no trees on or next to water; the river streaks run downstream.
- Bridges: roads cross water on raised decks with railings and piers; trucks drive over them.
- Shop window (bottom right): the v1 grocerybot.no store animation (Erik's `arena.js`, ported
  to `src/shop-iso.js`) showing the robots and trucks inside a shop as the match plays, switched
  by clicking any shop; order card (left) when you click an order house.
- The replay is a simulated match with the user's rush-ai, recorded from the engine on the
  Bergen map (`tools/simulate_match.py ... 7 bergen`).

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

Switch views with the 2D / 3D buttons (or V, or `?view=3d`).

Elevation: Mapzen Terrarium tiles (AWS Open Data). Map data © OpenStreetMap contributors (ODbL).
