# Open-Meteo on AWS Open Data

**AWS Bucket Name and Region: [s3://openmeteo](https://openmeteo.s3.amazonaws.com/index.html#data/); us-west-2; [AWS Registry](https://registry.opendata.aws/open-meteo/)**

Open-Meteo integrates weather models from well-known national weather services, delivering a rapid weather API. Real-time weather forecasts are unified within a time-series database that covers both historical and future weather data. Open-Meteo is designed to analyse long time-series of weather data any place on earth.

This database is made available through the [AWS Open Data Sponsorship program](https://aws.amazon.com/opendata/open-data-sponsorship-program/).

Weather datasets are sourced from the following national weather services:
- Forecast: NOAA NCEP, DWD, ECMWF, Environment Canada, MeteoFrance, JMA, BOM, CMA, Met Norway, DMI, KNMI, KMA, ItaliaMeteo, MeteoSwiss
- Marine Weather: ECMWF, MeteoFrance, Copernicus Marine, DWD, NOAA NCEP
- Air Quality: CAMS
- Historical data: Copernicus, ECMWF

This open-data distribution is managed by Open-Meteo and is not directly affiliated with national weather services. Open-Meteo does not guarantee the accuracy, completeness, or uninterrupted provision of the data products, and they are provided without any warranty. For support inquiries, please contact Open-Meteo by creating [`issues`](https://github.com/open-meteo/open-data/issues) or [`discussions`](https://github.com/open-meteo/open-data/discussions) in this repository.

## Weather Models

All available data can be explored using the [S3 explorer](https://openmeteo.s3.amazonaws.com/index.html#data/).

### Weather Forecast Models

Weather models can be broadly categories by their coverage:
- Global models run at lower resolution (11-50 km) but offer 7 to 16 days of forecast
- Local models use higher resolution (1-7 km) but offer only 2-5 days of weather forecast

Local models are nested into global models and rely on boundary conditions that drive large scale weather patterns. The Open-Meteo API seamlessly combines local and global weather models. Depending on your use-case, you may want to use different weather models. E.g. If you only need 2 days of forecast for North America, use `ncep_hrrr_conus`, but for more than 2 days, you have to add `ncep_gfs013`. Further more, you can select only `temperature_2m` to more fine grained of how much data is being transferred.

Ideally, familiarise yourself with the [Weather Forecast API](https://open-meteo.com/en/docs) and explore the [S3 explorer](https://openmeteo.s3.amazonaws.com/index.html#data/) to select the right weather models.


| Model                           | Region                           | Resolution             | Timeinterval | Forecast length | Updates        | # Surface Variables | # Pressure Variables | Available since |
| ------------------------------- | -------------------------------- | ---------------------- | ------------ | --------------- | -------------- | ------------------- | -------------------- | --------------- |
| dwd_icon                        | Global                           | 0.1° (~11 km)          | Hourly       | 7.5 days        | Every 6 hours  | 49                  | 5 (18 levels)        | 2023-12-15      |
| dwd_icon_eu                     | Europe                           | 0.0625° (~7 km)        | Hourly       | 5 days          | Every 3 hours  | 42                  | 5 (17 levels)        | 2023-12-15      |
| dwd_icon_d2                     | Central Europe                   | 0.02° (~2 km)          | Hourly       | 2 days          | Every 3 hours  | 44                  | 5 (11 levels)        | 2023-12-15      |
| dwd_icon_d2_15min               | "                                | "                      | 15-Minutely  | "               | "              | 8                   | -                    | 2023-12-15      |
| ncep_gfs013                     | Global                           | 0.11° (~13 km)         | Hourly       | 16 days         | Every 6 hours  | 27                  | -                    | 2023-12-15      |
| ncep_gfs025                     | Global                           | 0.25° (~25 km)         | Hourly       | 16 days         | Every 6 hours  | 11                  | 7 (38 levels)        | 2023-12-15      |
| ncep_nbm_conus                  | U.S. Conus                       | 2.5 km                 | Hourly       | 11 days         | Every hour     | 20                  | -                    | 2024-10-03      |
| ncep_hrrr_conus                 | U.S. Conus                       | 3 km                   | Hourly       | 2 days          | Every hour     | 23                  | 7 (39 levels)        | 2023-12-15      |
| ncep_hrrr_conus_15min           | "                                | "                      | 15-Minutely  | "               | "              | 12                  | -                    | 2023-12-15      |
| meteofrance_arpege_world025     | Global                           | 0.25° (~25 km)         | Hourly       | 4 days          | Every 6 hours  | 29                  | 6 (23 levels)        | 2023-12-15      |
| meteofrance_arpege_europe       | Europe                           | 0.1° (~11 km)          | Hourly       | 4 days          | Every 6 hours  | 29                  | 6 (23 levels)        | 2023-12-15      |
| meteofrance_arome_france0025    | France                           | 0.025° (~2.5 km)       | Hourly       | 51 hours        | Every 3 hours  | 29                  | 6 (24 levels)        | 2023-12-15      |
| meteofrance_arome_france_hd     | "                                | 0.01° (~1.5 km)        | "            | "               | "              | 12                  | -                    | 2023-12-15      |
| ecmwf_ifs04                     | Global                           | 0.4 (~44 km)           | 3-Hourly     | 10 days         | Every 6 hours  | 14                  | 7 (9 levels)         | 2023-12-15      |
| ecmwf_ifs025                    | Global                           | 0.25 (~25 km)          | 3-Hourly     | 10 days         | Every 6 hours  | 14                  | 7 (9 levels)         | 2024-02-03      |
| ukmo_global_deterministic_10km  | Global                           | 0.09 (~10 km)          | Hourly       | 7 days          | Every 6 hours  | 19                  | 5 (59 levels)        | 2022-03-01      |
| ukmo_uk_deterministic_2km       | UK, Ireland                      | 2 km                   | Hourly       | 2 days          | Every hour     | 24                  | 5 (59 levels)        | 2022-03-01      |
| cmc_gem_gdps                    | Global                           | 0.15° (~15 km)         | 3-Hourly     | 10 days         | Every 12 hours | 24                  | 5 (31 levels)        | 2023-12-15      |
| cmc_gem_rdps                    | North America, North Pole        | 10 km                  | Hourly       | 3.5 days        | Every 6 hours  | 24                  | 5 (31 levels)        | 2023-12-15      |
| cmc_gem_hrdps                   | Canada, Northern US              | 2.5 km                 | Hourly       | 2 days          | Every 6 hours  | 24                  | 5 (28 levels)        | 2023-12-15      |
| jma_gsm                         | Global                           | 0.5° (~55 km)          | 6-Hourly     | 11 days         | Every 6 hours  | 8                   | 6 (11 levels)        | 2023-12-15      |
| jma_msm                         | Japan, Korea                     | 0.05° (~5 km)          | Hourly       | 4 days          | Every 3 hours  | 11                  | -                    | 2023-12-15      |
| metno_nordic_pp                 | Norway, Denmark, Sweden, Finland | 1 km                   | Hourly       | 2.5 days        | Every hour     | 9                   | -                    | 2023-12-15      |
| cma_grapes_global               | Global                           | 0.125° (~13 km)        | 3-Hourly     | 10 days         | Every 6 hours  | 48                  | 8                    | 2024-01-01      |
| bom_access_global               | Global                           | 0.175°/0.117° (~15 km) | Hourly       | 10 days         | Every 6 hours  | 33                  | -                    | 2024-01-01      |
| dmi_harmonie_arome_europe       | Central & Northern Europe        | 2 km                   | Hourly       | 60 hours        | Every 3 hours  | 39                  | -                    | 2024-07-01      |
| knmi_harmonie_arome_europe      | Central & Northern Europe        | 5.5 km                 | Hourly       | 60 hours        | Every hour     | 22                  | 5 (5 levels)         | 2024-07-01      |
| knmi_harmonie_arome_netherlands | Netherlands, Belgium             | 2 km                   | Hourly       | 60 hours        | Every hour     | 28                  | -                    | 2024-07-01      |
| kma_gdps                        | Global                           | 0.13° (~12 km)         | 3-Hourly     | 12 days         | Every 6 hours  | 28                  | -                    | 2024-07-01      |
| kma_ldps                        | South And North Korea            | 1.5 km                 | Hourly       | 2 days          | Every 6 hours  | 28                  | -                    | 2024-07-01      |
| italia_meteo_arpae_icon_2i      | Southern Europe                  | 2 km                   | Hourly       | 60 hours        | Every 12 hours | 28                  | -                    | 2025-04-13      |
| meteoswiss_icon_ch1             | Central Europe                   | 1 km                   | Hourly       | 33 hours        | Every 3 hours  | 28                  | -                    | 2025-07-20      |
| meteoswiss_icon_ch2             | Central Europe                   | 2 km                   | Hourly       | 120 hours       | Every 6 hours  | 28                  | -                    | 2025-07-20      |

### Marine Wave Models

The following ocean wave models are integrated into the [Marine Wave API](https://open-meteo.com/en/docs/marine-weather-api). 

| Model                               | Region | Resolution     | Timeinterval | Forecast length | Updates        | # Surface Variables | # Pressure Variables | Available since |
| ----------------------------------- | ------ | -------------- | ------------ | --------------- | -------------- | ------------------- | -------------------- | --------------- |
| ecmwf_wam025                        | Global | 0.25° (~25 km) | 3-Hourly     | 10 days         | Every 6 hours  | 4                   | -                    | 2024-03-01      |
| meteofrance_currents                | Global | 0.08° (~8 km)  | Hourly       | 10 days         | Every 24 hours | 1                   | -                    | 2022-01-01      |
| meteofrance_wave                    | Global | 0.08° (~8 km)  | 3-Hourly     | 10 days         | Every 12 hours | 9                   | -                    | 2021-10-01      |
| meteofrance_sea_surface_temperature | Global | 0.08° (~8 km)  | 6-Hourly     | 10 days         | Every 24 hours | 1                   | -                    | 2022-01-01      |
| ncep_gfswave025                     | Global | 0.25° (~25 km) | Hourly       | 16 days         | Every 6 hours  | 9                   | -                    | 2024-06-20      |
| dwd_gwam                            | Global | 0.25° (~25 km) | Hourly       | 7.5 days        | Every 12 hours | 11                  | -                    | 2023-12-15      |
| dwd_ewam                            | Europe | 0.05° (~5 km)  | Hourly       | 4 days          | Every 12 hours | 11                  | -                    | 2023-12-15      |
| copernicus_era5_ocean               | Global | 0.5° (~50 km)  | Hourly       | 5 days delay    | Every 24 hours | 5                   | -                    | 2023-12-15      |

### Air Quality Models

The following models are used in the [Air Quality API](https://open-meteo.com/en/docs/air-quality-api)

| Model       | Region | Resolution    | Timeinterval | Forecast length | Updates        | # Surface Variables | # Pressure Variables | Available since |
| ----------- | ------ | ------------- | ------------ | --------------- | -------------- | ------------------- | -------------------- | --------------- |
| cams_global | Global | 0.4° (~44 km) | Hourly       | 4.5 days        | Every 12 hours | 10                  | -                    | 2023-12-15      |
| cams_europe | Europe | 0.1° (~11 km) | Hourly       | 4 days          | Every 12 hours | 14                  | -                    | 2023-12-15      |

### Historical Weather Data

The following models are used in the [Historical Weather API](https://open-meteo.com/en/docs/historical-weather-api).

| Model                | Region | Resolution     | Timeinterval | Delay to realtime | Updates        | # Surface Variables | # Pressure Variables | Available since |
| -------------------- | ------ | -------------- | ------------ | ----------------- | -------------- | ------------------- | -------------------- | --------------- |
| copernicus_era5      | Global | 0.25° (~25 km) | Hourly       | 5 days            | Every 24 hours | 23                  | -                    | 1940-01-01      |
| copernicus_era5_land | Global | 0.1° (~11 km)  | Hourly       | 5 days            | Every 24 hours | 11                  | -                    | 1950-01-01      |
| ecmwf_ifs            | Global | 9 km           | Hourly       | 2 days            | Every 24 hours | 24                  | -                    | 2017-01-01      |


### Digital Elevation Models

Based on the [GLO-90 digital elevation model (DEM)](https://spacedata.copernicus.eu/collections/copernicus-digital-elevation-model) from Copernicus, the weather API uses terrain information to optimise and downscale weather data. Although the Open-Meteo weather API works without elevation information, forecasts in mountainous terrain is less accurate. 

| Model            | Region | Resolution | Timeinterval | Forecast length | Updates | # Surface Variables | # Pressure Variables | Available since |
| ---------------- | ------ | ---------- | ------------ | --------------- | ------- | ------------------- | -------------------- | --------------- |
| copernicus_dem90 | Global | 90 m       | -            | -               | -       | -                   | -                    | 2023-12-15      |

### Other Models

Climate, flood, satellite and ensemble models are not published on AWS due to their immense size.


## Data Organization
Weather data is stored in formats tailored to different access patterns such as time-series APIs, weather map generation, and AI model training. Each storage layout has its own strengths and trade-offs, but together they ensure efficient and flexible data access. All datasets are stored as multi-dimensional arrays in cloud-native formats, enabling direct access to parts of each file without the need for a centralized database management system.

The same underlying weather data is offered through multiple layout strategies, each optimized for a specific use case:

- **Rolling Timeseries** (`data/<model>/<weather-variable>/<time-chunk>.om`): A continuously updated archive optimized for time-series access at specific locations. The timeseries is split into `chunks`, with the most recent ones overwritten on each model update cycle, typically every few hours. This format supports long-term retention—multiple years of data are available and preserved indefinitely. It powers the Open-Meteo weather API and is ideal for applications requiring historical context.

- **Spatial Data** (`data_spatial/<model>/<run>/<timestamp>.om`): Ideal for visualization and map-based applications. Each file includes all weather variables for a specific timestamp and is updated in near real-time as models are processed. Data is available with minimal latency and retained for 7 days.

- **Run-Based Data** (`data_run/<model>/<run>/<weather-variable>.om`): Designed for granular access to individual model runs, this layout supports full time-series retrieval of specific variables - ideal for AI training workflows that require all timesteps from a given run, without needing global coverage. Data is publicly available on AWS for 3 months, with extended archives available directly from Open-Meteo.

URL components:
- `model`: All data is grouped by weather model. E.g. `ncep_gfs013` or `dwd_icon_eu`.
- `weather-variable`. Each model contains multiple weather variables. E.g. `temperature_2m` or `relative_humidity_2m`. Some weather variables like `wind_speed_10m` are calculated by the API, but `wind_u_component_10m` and `wind_v_component_10m` are stored.
- `time-chunk`: For each variable, data is split by time. This can be an entire year for historical data, or chunks of 1-2 weeks of data. An entire year is specified like `year_2010.om` while chunks of varying size use arbitrary indices like `chunk_927382.om`
- `timestamp`: For `data_spatial/` each timestamp is an ISO timestamp `YYYY-MM-DDThhmm`
- `run`: The model run also known as forecast reference time is split into directories `YYYY/MM/DD/hhmmZ`
- `.om` file extension: All data is stored in multi-dimensional arrays. Dimensions are either `[ny,nx,ntime]` for time-series optimised access or just `[ny,nx]` for spatial orientation. For an optimal compression, a custom file format is used. See [below](#file-format).


## Updates to Real-time Weather Forecasts
Real-time weather models refresh every 1, 3, 6, or 12 hours. Once the first data becomes available from national weather services, Open-Meteo begins downloading and processing it. Some models may take up to 2 hours to complete their run. Open-Meteo initiates parallel downloads even while the model is still running.

The update process follows these steps:

1. **Spatial Data** (`data_spatial/`): As each time-step is processed, spatial access data is generated and uploaded immediately. This allows data to be accessed even while a weather model is still running. After each forecast hour is published, the metadata file `data_spatial/<model>/in-progress.json` is updated. This file lists all variables and time-steps processed so far. Once the entire run is complete, `data_spatial/<model>/latest.json` is updated to show the most recent completed model run.

2. **Rolling Timeseries** (`data/`): Once all time-steps have been downloaded, the time-series database at `data/` is updated. Data is organized into chunks spanning 3 to 14 days per file. These files are overwritten with the newest data during each update cycle. Chunk lengths are individually tuned per model to strike a balance between file size, compression efficiency, and read performance. Upon update, metadata is written to `data/<model>/static/meta.json`.

3. **Run-Based Data** (`data_run/`): In the final step, a full distribution is generated for data_run. All timestamps are transposed into a time-series-optimized format.To minimize file size, only 13 pressure levels and model levels below 200 meters are included. At most, one model run every 3 hours is retained. Upon completion, metadata is written to `data_run/<model>/<run>/meta.json`.

Caveats:
- Time steps in `data_spatial/` and `data_run/` reflect the native resolution of the underlying weather model. Some models provide high-frequency (e.g. 1-hourly) forecasts for initial hours, then shift to coarser resolutions (e.g. 3- or 6-hourly) for later periods. In contrast, the rolling timeseries distribution in `data/` always interpolates all data to the highest available temporal resolution.

- Variables that represent a backward sum or backward average — such as precipitation or solar radiation — do not include the first timestep in `data_spatial/` and `data_run/`.

- For `data_spatial/`, all variables are stored within a single `.om` file per time-step. Users must read the metadata in each `.om` file to locate and extract specific weather variables. The `.om` format is cloud-native, allowing partial downloads of only the required data segments. This design avoids the overhead of managing billions of small files.

- Certain weather variables may be published with a delay by some models. For example, DWD ICON models release high-altitude wind forecasts up to an hour later than standard variables. To accommodate this, a secondary set of files is created in data_spatial, such as `data_spatial/dwd_icon/latest_model-level.json`, referencing delayed data files like `data_spatial/dwd_icon/<run>/<time>_model-level.json`.

Typically, historical weather data doesn't undergo updates. However, in the case of ERA5, daily updates are applied with a 5-7 day delay. Older historical data spanning the past 80 years remains unaltered, of course.


## Download and Interact With Data

Open-Meteo provides a [free API](https://open-meteo.com) for quick data retrieval without the need to download from the AWS bucket.

However, there are two primary scenarios where downloading data locally is advantageous:

1. **Research with Historical Weather Data:**
Conducting intensive analyses on millions of events with varying locations and time steps is facilitated by having data available locally or on a dedicated high-performance VM instance. With Open-Meteo on AWS Open Data, you can download temperature data for the past 80 years using the Copernicus ERA5-Land dataset. Basic steps include:
- Installing the Open-Meteo Docker image `docker pull ghcr.io/open-meteo/open-meteo`
- Download archived ERA5 data for temperature from AWS `docker run open-meteo sync copernicus_era5_land temperature_2m --past-days 730` (roughly 8 GB)
- Launch your local API endpoint `docker run -p 8080:8080 open-meteo serve`
- Get data for individual coordinates `curl "http://127.0.0.1:8080/v1/archive?latitude=47.1&longitude=8.4&hourly=temperature_2m&start_date=20220101&end_date=20231031"`

Note: Docker commands are exemplary. Follow the [Tutorial for downloading historical weather data](./tutorial_download_era5/) to get it working quickly.

2. **Running Your Own Weather API:**
If you require an extensive amount of weather data through an API daily and wish to run your own weather API, you can obtain current weather data from Open-Meteo on AWS Open Data. The Open-Meteo Docker container can listen for newly published data and keep your local database up-to-date. Similar to using past weather data:
- Install the Open-Meteo Docker image
- Start the data synchronization for a given weather model `docker run open-meteo sync ncep_gfs013 temperature_2m,relative_humidity_2m,wind_u_component_10m,wind_v_component_10m --past_days 3 --repeat-interval 5`
- Launch the API instance and get the latest forecast from your new API endpoint

To help you in setting up your own weather API you can follow [this tutorial](./tutorial_weather_api/) to setup your own weather API.

3. **Direct access to OM files**:
The `data_spatial` and `data_run` layouts have been recently added. We will provide instructions for access this data using Python and other programming languages in the upcoming months.


## File Format
Data is stored in a custom file format called OM-Files, designed for chunked data access and efficient compression. The structure is similar to NetCDF or HDF5 but with significantly lower overhead.

OM-Files are cloud-native and can be accessed directly from S3. Thanks to small chunk sizes, users can retrieve only the data they need—without downloading the entire file. Chunks typically range from 1–4 KiB. For example, a temperature dataset like ERA5-Land might be 9 GiB, but reading data for a single location requires just 16 KiB—including all metadata, indices, and data.

OM-Files can also be written sequentially and streamed directly to S3. Paired with a fast chunking and compression pipeline, this allows for data generation at GB/s speeds. This high performance is essential for working with large-scale meteorological datasets. Open-Meteo alone processes over 2 TiB of weather data every day.

The underlying OM-File library is implemented in C — [source code available here](https://github.com/open-meteo/om-file-format). OM-Files can also be read from [Python](https://github.com/open-meteo/python-omfiles), [Rust](https://github.com/open-meteo/rust-omfiles), [Swift](https://github.com/open-meteo/om-file-format/tree/main/Swift), and [TypeScript WASM](https://github.com/open-meteo/typescript-omfiles).

# License

CC-BY-4.0