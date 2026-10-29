# polygon_CB_add_on
## Polygon map overlay

The polygon overlay is separate from CollectionBuilder's item-map data: it draws polygon boundaries from GeoJSON, while item-map data places collection objects at their latitude and longitude. To use your own polygon data:

1. **Prepare the data.** Export your boundaries as a GeoJSON `FeatureCollection`. Each feature needs a `Polygon` or `MultiPolygon` geometry and coordinates in GeoJSON order: longitude, then latitude. Add a display name to each feature's `properties`; `name` is recommended (the overlay also recognizes `NAME` and `display_name`). For example:

   ```json
   {
     "type": "FeatureCollection",
     "features": [
       {
         "type": "Feature",
         "properties": { "name": "North District" },
         "geometry": {
           "type": "Polygon",
           "coordinates": [[[-117.2, 46.8], [-117.1, 46.8], [-117.1, 46.9], [-117.2, 46.8]]]
         }
       }
     ]
   }
   ```

2. **Add the data file.** Save the FeatureCollection in `assets/js/` as a JavaScript file, or replace the existing example data in `assets/js/map-polygons.js`. Assign the GeoJSON object to the global variable `mapPolygons`:

   ```js
   var mapPolygons = {
     "type": "FeatureCollection",
     "features": [
       // Add the GeoJSON features here.
     ]
   };
   ```

   The map loads this script directly, so a plain `.geojson` file or a module export will not work without changing the loader.

3. **Include the overlay in the site.** Copy `assets/js/map-leaflet.js` (Leaflet) or `assets/js/map-esri.js` (ArcGIS), `_includes/js/map-js.html`, `_layouts/map.html`, and your polygon data file. This repository's `assets/js/map-polygons.js` is example mining-district data; replace it with your own data or use a different filename. Add a map page that uses `layout: map`. For Leaflet, keep the Leaflet libraries already included with the template.

4. **Point the map at your file.** In `_data/theme.yml`, set `polygon-data-file` to your filename in `assets/js/` (not the full path), and choose the provider. The default is `map-polygons.js`, so change it when using a different filename:

   ```yaml
   map_provider: leaflet # or esri
   polygon-data-file: my-polygons.js
   ```

   The `embed` provider displays an embedded map and does not load this overlay.

5. **Optionally connect polygons to collection items.** To list related objects in a polygon's popup, set `polygon-item-match-field` to the metadata column whose values match the polygon names:

   ```yaml
   polygon-item-match-field: district
   ```

   For example, if a feature has `properties.name: "North District"`, the `district` value on an item should be `North District`. Matching ignores letter case and accepts multiple values separated by commas or semicolons. The popup shows up to five related items and a link to browse more. Leave this setting blank if the polygons should not be matched to items.

6. **Build and check the map.** Confirm the map page loads, polygon shapes and labels appear in the expected locations, and—if item matching is enabled—click a polygon to verify its related-item links.
