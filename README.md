# knex-postgis

Extension for use postgis functions in [knex](http://knexjs.org) SQL query builder.



## Example
This example show the sql generated by the extension.


```js
var knex = require('knex')({
  dialect: 'postgres'
});

// install postgis functions in knex.postgis;
var st = require('knex-postgis')(knex);
/* or:
 * require('knex-postgis')(knex);
 * st = knex.postgis;
 */

// insert a point
var sql1 = knex.insert({
  id: 1,
  geom: st.geomFromText('Point(0 0)', 4326)
}).into('points').toString();
console.log(sql1);
// insert into "points" ("geom", "id") values (ST_geomFromText('Point(0 0)'), '1')

// find all points return point in wkt format
var sql2 = knex.select('id', st.asText('geom')).from('points').toString();
console.log(sql2);
// select "id", ST_asText("geom") as "geom" from "points"

// all methods support alias
var sql3 = knex.select('id', st.asText(st.centroid('geom')).as('centroid')).from('geometries').toString();
console.log(sql3);
// select "id", ST_asText(ST_centroid("geom")) as "centroid" from "geometries"

```

## Currently supported functions

- area(geom)
- asText(column)
- asGeoJSON(column)
- asEWKT(column)
- buffer(geom, radius)
- centroid(geom)
- distance(geom, geom)
- dwithin(geom, geom, distance, /* optional bool spheroid */)
- intersection(geom1, geom2)
- intersects(geom1, geom2)
- geography(geom)
- geometry(geography)
- geomFromText(geom, srid)
- geomFromGeoJSON(geojson /*object, string or column name*/)
- makeEnvelope(minlon, minlat, maxlon, maxlat, /* optional integer SRID */)
- makePoint(lon, lat, /* optional z */, /* optional measure */)
- makeValid(geom)
- point(lon, lat)
- transform(geom, srid)
- within(geom, geom)
- x
- y

## Define extra functions

```js
var knex = require('knex')({
  dialect: 'postgres'
});

require('knex-postgis')(knex);

st = knex.postgis;

knex.postgisDefineExtras(function(knex, formatter){
  return {
    utmzone: function(geom) {
      return knex.raw('utmzone(?)', [formatter.wrapWKT(geom)]);
    }
  };
});

//now you can use st.utmzone function in the same way as predefined functions
```
