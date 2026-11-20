# json-basemaps

Higher detail basemaps for the GeoIP maps of `luci-app-banip` and `luci-app-adblock`.

Both apps ship coarse Natural Earth 1:110m country outlines (`world.json`, ~36 kB). On every map run LuCI first looks for an optional `world-50m.json` next to the shipped file and falls back to `world.json` if it's missing. There is no config option involved. This repo provides two ready-made variants of that file, so you don't need to build one yourself.

## Variants

| Directory | Source | Simplification | Size | Detail |
| --- | --- | --- | --- | --- |
| `normal/` | Natural Earth 1:50m | 20% | ~325 kB | sharper coastlines, larger islands (e.g. Peloponnese, Crete, Euboea) |
| `fine/` | Natural Earth 1:50m | 50% | ~750 kB | nearly all smaller islands (e.g. Aegean, Baltic) |

Both files are named `world-50m.json` and can be used as is. Pick one. The `fine` variant is larger, so the map takes longer to load and parse in the browser.

## Installation

Run on the router, e.g. for the `normal` variant and banIP:

```
uclient-fetch -O /www/luci-static/resources/view/banip/world-50m.json https://raw.githubusercontent.com/dibdot/json-basemaps/main/normal/world-50m.json
```

For adblock use `/www/luci-static/resources/view/adblock/world-50m.json` as target. For the `fine` variant replace `normal` with `fine` in the URL. Reload the map page afterwards.

Please note: the file is not part of any package, so it is removed on sysupgrade unless you add its path to `/etc/sysupgrade.conf`, and it stays behind when the luci app is uninstalled.

To go back to the shipped outlines simply remove `world-50m.json`.

## Build

The files were built from Natural Earth v5.1.2 with [mapshaper](https://github.com/mbloch/mapshaper). Use `-simplify 20%` for `normal` and `-simplify 50%` for `fine`:

```
curl -sSLo ne50.geojson https://raw.githubusercontent.com/nvkelso/natural-earth-vector/v5.1.2/geojson/ne_50m_admin_0_countries.geojson
npx -y mapshaper ne50.geojson -filter-fields -simplify 20% keep-shapes -o force precision=0.01 format=geojson world-50m.json
```

## License

The map data is from [Natural Earth](https://www.naturalearthdata.com) and is in the public domain.
