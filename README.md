# Awesome GERS

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated list of resources for the [Global Entity Reference System (GERS)](https://overturemaps.org/gers/), Overture Maps Foundation's open framework for assigning stable, persistent UUIDs to geographic entities.

GERS solves the "conflation tax": the recurring cost of re-matching your dataset to every new data release. Every feature in Overture carries a GERS UUID. Bridge files map those UUIDs to source IDs (OSM, Microsoft, Meta, etc.), so any dataset linked to GERS once stays linked across releases. Think of it as a persistent join key for the physical world.

Reached General Availability June 25, 2025. In OGC community standard process as of early 2026.

---

## Contents

- [Awesome GERS](#awesome-gers)
  - [Contents](#contents)
  - [Official Documentation](#official-documentation)
  - [Data](#data)
  - [Tools and Libraries](#tools-and-libraries)
  - [Platform Integrations](#platform-integrations)
  - [Tutorials and Guides](#tutorials-and-guides)
  - [Talks and Podcasts](#talks-and-podcasts)
  - [Articles and Blog Posts](#articles-and-blog-posts)
  - [Case Studies](#case-studies)
  - [Standards and Governance](#standards-and-governance)
  - [Contributing](#contributing)

---

## Official Documentation

- [GERS Overview](https://overturemaps.org/gers/): Product landing page covering what GERS is, the partner ecosystem, and entry points to technical docs.
- [What is GERS?](https://docs.overturemaps.org/gers/): Core technical reference covering IDs, changelogs, the registry, and bridge files.
- [GERS Tutorial](https://docs.overturemaps.org/gers/gers-tutorial/): Step-by-step guide to querying Overture via GERS IDs using DuckDB.
- [GERS Registry](https://docs.overturemaps.org/gers/registry/): Queryable Parquet index of all stable GERS IDs across every Overture release (S3/Azure).
- [Bridge Files](https://docs.overturemaps.org/gers/bridge-files/): Per-release join tables mapping GERS UUIDs to source dataset IDs (OSM, Meta, Microsoft, Esri, and more).

## Data

### Overture Core Releases

All six themes are published monthly under open licenses and are fully GERS-tagged. Access via `s3://overturemaps-us-west-2/release/` or Azure equivalent.

- [Overture on AWS Open Data](https://registry.opendata.aws/overture/): 64M+ places, 2.6B+ buildings, 447M+ addresses, 321M+ road segments: all carrying GERS UUIDs.

### Bridge Files (open, shipped with each release)

Pre-built cross-walk tables at `s3://overturemaps-us-west-2/bridgefiles/<release>/`. Current coverage:

| Source | Theme(s) |
| ------ | -------- |
| OpenStreetMap | Buildings, Places, Divisions, Transportation |
| Microsoft ML Building Footprints | Buildings |
| Meta Places | Places |
| Esri Community Maps | Buildings, Divisions |
| PinMeTo | Places |
| geoBoundaries | Divisions |
| Instituto Geográfico Nacional (Spain) | Buildings |

### Third-Party GERSified Datasets

- [Regrid Parcel × GERS Bridge](https://support.regrid.com/parcel-data/gers-faq): 11M+ Overture place IDs matched to 8M+ U.S. parcels; three-field join table (`ll_uuid`, `gers_id`, `geoid`). Available to Regrid bulk data customers.

## Tools and Libraries

### Official

- [overturemaps-py](https://github.com/OvertureMaps/overturemaps-py): Python CLI + library. Includes a `gers <UUID>` subcommand that queries the GERS Registry and returns a feature as GeoJSON or GeoParquet.
- [match-inspector](https://github.com/OvertureMaps/match-inspector): Local Flask/Folium web app for manually reviewing building conflation candidates before GERS ID assignment.
- [osm-pbf-parquet](https://github.com/OvertureMaps/osm-pbf-parquet): Rust CLI converting OSM PBF → Parquet; feeds the OSM-to-GERS conflation pipeline.
- [overture-tiles](https://github.com/OvertureMaps/overture-tiles): Generates PMTiles from Overture GeoParquet for map visualization and QA of GERS-identified features.

### Community

- [overture-gers-tiles-example](https://github.com/bdon/overture-gers-tiles-example): Planetiler-based demo joining Overture Places (GERS) to Regrid parcel zoning data to produce enriched vector tiles; Dallas, TX case study. ([Live map](https://bdon.github.io/overture-gers-tiles-example/))
- [OvertureMapsDownloader](https://github.com/Youssef-Harby/OvertureMapsDownloader): Python/DuckDB downloader with a browser app (DuckDB-WASM) for client-side queries against Overture Parquet including GERS columns.
- [overtureR](https://github.com/arthurgailes/overtureR): R package loading Overture datasets (with GERS ID columns) as dbplyr/sf data frames.
- [Fused GERS UDFs](https://github.com/fusedio/udfs): Serverless UDFs exposing HTTPS endpoints to spatially join any polygon dataset to Overture building GERS IDs; exploits H3 encoding for efficient filtering.
- [overture-geocoder](https://github.com/brad-richardson/overture-geocoder): Serverless geocoder on Cloudflare Workers backed by Overture Maps data; supports forward/reverse lookup and direct GERS ID resolution via edge-cached SQLite shards (Rust + Python).

## Platform Integrations

- [CARTO + Databricks](https://carto.com/blog/pain-free-data-integration-with-overture-maps-gers-databricks/): Overture data (all six themes, GERS IDs included) published as Databricks Marketplace listings; enables direct SQL joins on GERS IDs without ETL.
- [Esri ArcGIS / Living Atlas](https://www.esri.com/arcgis-blog/products/arcgis-online/mapping/enriching-overture-data-with-gers/): Workflow for enriching user data with GERS IDs inside ArcGIS Online and ArcGIS Pro using Living Atlas Overture layers.
- [Wherobots (Apache Sedona)](https://wherobots.com/overture-maps-data-cloud-native-geoparquet-apache-sedona/): Cloud-native access to Overture GeoParquet via Apache Sedona/Spark; native GERS support for large-scale spatial joins.
- [TomTom Global Entity Matcher (GEM)](https://www.tomtom.com/newsroom/explainers-and-insights/tomtom-global-entity-matcher-gem/): Commercial deep-learning conflation service that maps any client dataset to GERS IDs.
- [Precisely Data Link](https://overturemaps.org/case-study/2025/precisely-accelerates-data-integration-with-overture-gers-ids/): Pre-built link tables connecting PreciselyID, D&B DUNS numbers, Regrid property IDs, and GeoX IDs to GERS Places, Addresses, and Buildings.

## Tutorials and Guides

- [GERS Workshop Module](https://labs.overturemaps.org/workshop/4-gers.html): Official hands-on lab: querying GERS IDs, joining bridge files, querying the changelog, and enriching external datasets: all in DuckDB. Runnable in GitHub Codespaces.
- [Conflating Overture Places with DuckDB, Ollama, and Embeddings](https://www.dbreunig.com/2024/09/27/conflating-overture-points-of-interests-with-duckdb-ollama-and-more.html): Detailed walkthrough matching a restaurant inspection dataset to Overture Places using DuckDB spatial joins, string similarity, and LLM embeddings. Referenced in official Overture GERS docs.
- [Mapping GERS IDs to US Census FIPS Codes](https://www.dbreunig.com/2024/06/25/using-duckdb-spatial-joins-to-map-overture-gers-ids-to-us-census-fips-codes.html): DuckDB spatial join enriching GERS buildings with Census FIPS codes; canonical example of GERS as a cross-dataset join key.
- [Enhance Your Data with GERS IDs (Fused)](https://docs.fused.io/blog/enhance-your-data-with-gers-ids/): Spatially join any dataset to Overture buildings via a public Fused UDF endpoint; no backend required.
- [GERSifying Overture Places (CARTO)](https://carto.com/blog/gersifying-overture-places-a-seamless-path-to-richer-insights): End-to-end GERSification workflow in CARTO using an earthquake risk analysis as the use case.
- [GERS in Wherobots](https://wherobots.com/blog/wherobots-gers-ids-tutorial-geospatial-integration/): Step-by-step Wherobots notebook linking records across datasets using GERS IDs on Spark.

## Talks and Podcasts

- [SE Radio 694: Jennings Anderson & Amy Rose on Overture Maps](https://se-radio.net/2025/11/se-radio-694-jennings-anderson-and-amy-rose-on-overture-maps/): In-depth interview on GERS, stable IDs, dataset scale (4.2B features), and interoperability. November 2025.
- [FOSS4G NA 2024: Overture Map Data: What, Why, & How](https://www.youtube.com/watch?v=FPnxqwBB6w8): Recorded session by Steven Pousty and Jennings Anderson; GERS coverage starts around the data model section.
- [FOSS4G 2024: Jennings Anderson (Overture/GERS)](https://talks.osgeo.org/foss4g-2024/speaker/9H9JMS/): Belem talk on GERS enabling cross-dataset entity reference across organizations.
- [Overture GA + Beyond AMA](https://www.youtube.com/watch?v=yEn3xw36C6w): Panel with Amy Rose, Jennings Anderson, Pascal Clarysse, and Marc Prioleau; GERS design rationale discussed at length. August 2024.
- [Eliminating the Data Onboarding Tax (CARTO SDSC)](https://www.youtube.com/watch?v=k_DTLNk4Fek): Drew Breunig presents GERS as the solution to the recurring cost of geospatial data integration. December 2025.

## Articles and Blog Posts

- [Towards Standardizing Place](https://www.dbreunig.com/2024/07/31/towards-standardizing-place.html): Drew Breunig's influential argument that GERS has a real shot at making "place" a stable data primitive.
- [Eliminating the Conflation Tax (GeoBuiz 2025 recap)](https://overturemaps.org/blog/2025/overture-maps-foundation-at-geobuiz-2025-eliminating-the-conflation-tax-on-geospatial-data/): Framing piece from Overture on the problem GERS solves.
- [Understanding Overture's Global Entity Reference System](https://overturemaps.org/blog/2025/understanding-overtures-global-entity-reference-system/): Deep-dive on GERS components (reference map, changelogs, registry, bridge files) written at GA launch.
- [The Battle for the Map](https://projectgeospatial.org/geospatial-frontiers/the-battle-for-the-map-how-overtures-gers-proposal-ignited-a-cultural-war-in-open-source-geospatial-data): Long-form analysis of OSM community concerns around the OGC GERS standardization effort.
- [SimonPoole's Diary: Persisting Your IDs](https://www.openstreetmap.org/user/SimonPoole/diary/408332): OSM community perspective on persistent IDs and the tradeoffs in the GERS approach.

## Case Studies

- [Addresscloud: Geospatial Risk Analysis at Scale](https://overturemaps.org/case-study/2024/how-addresscloud-scaled-geospatial-risk-analysis-with-gers/): Insurance risk scoring of 300M building footprints using GERS; in production since March 2024.
- [Precisely: Accelerating Data Integration](https://overturemaps.org/case-study/2025/precisely-accelerates-data-integration-with-overture-gers-ids/): Pre-linked GERS IDs across Places, Addresses, and Buildings; turns months of conflation into instant ID joins.
- [Regrid: Connecting Parcels and POIs](https://www.prnewswire.com/news-releases/regrid-and-overture-maps-foundation-connect-parcels-and-pois-with-gers-id-integration-302505110.html): Nationwide US parcel-to-GERS bridge enabling enriched site selection analytics.

## Standards and Governance

- [opengeospatial/Overture-Maps-GERS](https://github.com/opengeospatial/Overture-Maps-GERS): OGC Standards Working Group charter and draft GERS community standard documentation; 40+ supporting organizations.
- [OGC Public Comment: GERS as an OGC Community Standard](https://www.ogc.org/requests/ogc-seeks-public-comment-on-justification-for-the-global-entity-reference-system-gers-framework-and-model-as-an-ogc-community-standard/): RFC from February 2026 seeking adoption of GERS as a formal OGC standard.
- [OvertureMaps/schema](https://github.com/OvertureMaps/schema): Schema working group repo governing GERS ID format and Overture data schema design.

---

## Contributing

Contributions welcome. Please open a PR with a short description of what you're adding and why it belongs here. Links should be to specific resources (not homepages), publicly accessible, and meaningfully related to GERS: not just Overture in general.
