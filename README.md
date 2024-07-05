# Open-Meteo on AWS Open Data

**AWS Bucket Name and Region: [s3://openmeteo](https://openmeteo.s3.amazonaws.com/index.html#data/); us-west-2**

Open-Meteo integrates weather models from well-known national weather services, delivering a rapid weather API. Real-time weather forecasts are unified within a time-series database that covers both historical and future weather data. Open-Meteo is designed to analyse long time-series of weather data any place on earth.

The database, made available through the [AWS Open Data Sponsorship program](https://aws.amazon.com/opendata/open-data-sponsorship-program/).

Weather datasets are sourced from the following national weather services:
- Forecast: NOAA NCEP, DWD, ECMWF, Environment Canada, MeteoFrance, JMA, BOM, CMA, Met Norway
- Historical data: Copernicus
- Climate data: CMIPS 

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
| ncep_hrrr_conus                 | U.S. Conus                       | 3 km                   | Hourly       | 2 days          | Every hour     | 23                  | 7 (39 levels)        | 2023-12-15      |
| ncep_hrrr_conus_15min           | "                                | "                      | 15-Minutely  | "               | "              | 12                  | -                    | 2023-12-15      |
| meteofrance_arpege_world025     | Global                           | 0.25° (~25 km)         | Hourly       | 4 days          | Every 6 hours  | 29                  | 6 (23 levels)        | 2023-12-15      |
| meteofrance_arpege_europe       | Europe                           | 0.1° (~11 km)          | Hourly       | 4 days          | Every 6 hours  | 29                  | 6 (23 levels)        | 2023-12-15      |
| meteofrance_arome_france0025    | France                           | 0.025° (~2.5 km)       | Hourly       | 51 hours        | Every 3 hours  | 29                  | 6 (24 levels)        | 2023-12-15      |
| meteofrance_arome_france_hd     | "                                | 0.01° (~1.5 km)        | "            | "               | "              | 12                  | -                    | 2023-12-15      |
| ecmwf_ifs04                     | Global                           | 0.4 (~44 km)           | 3-Hourly     | 10 days         | Every 6 hours  | 14                  | 7 (9 levels)         | 2023-12-15      |
| ecmwf_ifs025                    | Global                           | 0.25 (~25 km)          | 3-Hourly     | 10 days         | Every 6 hours  | 14                  | 7 (9 levels)         | 2024-02-03      |
| cmc_gem_gdps                    | Global                           | 0.15° (~15 km)         | 3-Hourly     | 10 days         | Every 12 hours | 24                  | 5 (31 levels)        | 2023-12-15      |
| cmc_gem_rdps                    | North America, North Pole        | 10 km                  | Hourly       | 3.5 days        | Every 6 hours  | 24                  | 5 (31 levels)        | 2023-12-15      |
| cmc_gem_hrdps                   | Canada, Northern US              | 2.5 km                 | Hourly       | 2 days          | Every 6 hours  | 24                  | 5 (28 levels)        | 2023-12-15      |
| jma_gsm                         | Global                           | 0.5° (~55 km)          | 6-Hourly     | 11 days         | Every 6 hours  | 8                   | 6 (11 levels)        | 2023-12-15      |
| jma_msm                         | Japan, Korea                     | 0.05° (~5 km)          | Hourly       | 4 days          | Every 3 hours  | 11                  | -                    | 2023-12-15      |
| metno_nordic_pp                 | Norway, Denmark, Sweden, Finland | 1 km                   | Hourly       | 2.5 days        | Every hour     | 9                   | -                    | 2023-12-15      |
| cma_grapes_global               | Global                           | 0.125° (~13 km)        | 3-Hourly     | 10 days         | Every 6 hours  | 48                  | 8                    | 2024-01-01      |
| bom_access_global               | Global                           | 0.175°/0.117° (~15 km) | Hourly       | 10 days         | Every 6 hours  | 33                  | -                    | 2024-01-01      |
| arpae_cosmo_5m                  | Europe                           | 5 km                   | Hourly       | 3 days          | Every 12 hours | 9                   | -                    | 2024-02-01      |
| arpae_cosmo_2i                  | Italy                            | 2.2 km                 | Hourly       | 2 days          | Every 12 hours | 9                   | -                    | 2024-02-01      |
| arpae_cosmo_2i_ruc              | Italy                            | 2.2 km                 | Hourly       | 18 hours        | Every 3 hours  | 9                   | -                    | 2024-02-01      |
| dmi_harmonie_arome_europe       | Central & Northern Europe        | 2 km                   | Hourly       | 60 hours        | Every 3 hours  | 39                  | -                    | 2024-07-01      |
| knmi_harmonie_arome_europe      | Central & Northern Europe        | 5.5 km                 | Hourly       | 60 hours        | Every hour     | 22                  | 5 (5 levels)         | 2024-07-01      |
| knmi_harmonie_arome_netherlands | Netherlands, Belgium             | 2 km                   | Hourly       | 60 hours        | Every hour     | 28                  | -                    | 2024-07-01      |

### Marine Wave Models

The following ocean wave models are integrated into the [Marine Wave API](https://open-meteo.com/en/docs/marine-weather-api). 

| Model                 | Region | Resolution     | Timeinterval | Forecast length | Updates        | # Surface Variables | # Pressure Variables | Available since |
| --------------------- | ------ | -------------- | ------------ | --------------- | -------------- | ------------------- | -------------------- | --------------- |
| ecmwf_wam025          | Global | 0.25° (~25 km) | 3-Hourly     | 10 days         | Every 6 hours  | 4                   | -                    | 2024-03-01      |
| meteofrance_currents  | Global | 0.08° (~8 km)  | Hourly       | 10 days         | Every 24 hours | 1                   | -                    | 2022-01-01      |
| meteofrance_wave      | Global | 0.08° (~8 km)  | 3-Hourly     | 10 days         | Every 12 hours | 9                   | -                    | 2021-10-01      |
| ncep_gfswave025       | Global | 0.25° (~25 km) | Hourly       | 16 days         | Every 6 hours  | 9                   | -                    | 2024-06-20      |
| dwd_ewam              | Europe | 0.05° (~5 km)  | Hourly       | 4 days          | Every 12 hours | 11                  | -                    | 2023-12-15      |
| copernicus_era5_ocean | Global | 0.5° (~50 km)  | Hourly       | 5 days delay    | Every 24 hours | 5                   | -                    | 2023-12-15      |

### Air Quality Models

The following models are used in the [Air Quality API](https://open-meteo.com/en/docs/air-quality-api)

| Model       | Region | Resolution    | Timeinterval | Forecast length | Updates        | # Surface Variables | # Pressure Variables | Available since |
| ----------- | ------ | ------------- | ------------ | --------------- | -------------- | ------------------- | -------------------- | --------------- |
| cams_global | Global | 0.4° (~44 km) | Hourly       | 4.5 days        | Every 12 hours | 10                  | -                    | 2023-12-15      |
| cams_europe | Europe | 0.1° (~11 km) | Hourly       | 4 days          | Every 12 hours | 14                  | -                    | 2023-12-15      |

### Historical Weather Data

The following models are used in the [Historical Weather API](https://open-meteo.com/en/docs/historical-weather-api). Note: The historical weather API integrates high resolution data from ECMWF IFS which is not part of the open-data distribution.

| Model                | Region | Resolution     | Timeinterval | Delay to realtime | Updates        | # Surface Variables | # Pressure Variables | Available since |
| -------------------- | ------ | -------------- | ------------ | ----------------- | -------------- | ------------------- | -------------------- | --------------- |
| copernicus_era5      | Global | 0.25° (~25 km) | Hourly       | 5 days            | Every 24 hours | 23                  | -                    | 1940-01-01      |
| copernicus_era5_land | Global | 0.1° (~11 km)  | Hourly       | 5 days            | Every 24 hours | 11                  | -                    | 1950-01-01      |

### Ensemble Weather Models

TBA

### Climate Models

TBA

### Digital Elevation Models

Based on the [GLO-90 digital elevation model (DEM)](https://spacedata.copernicus.eu/collections/copernicus-digital-elevation-model) from Copernicus, the weather API uses terrain information to optimise and downscale weather data. Although the Open-Meteo weather API works without elevation information, forecasts in mountainous terrain is less accurate. 

| Model            | Region | Resolution | Timeinterval | Forecast length | Updates | # Surface Variables | # Pressure Variables | Available since |
| ---------------- | ------ | ---------- | ------------ | --------------- | ------- | ------------------- | -------------------- | --------------- |
| copernicus_dem90 | Global | 90 m       | -            | -               | -       | -                   | -                    | 2023-12-15      |




## Data Organization

Data is structured by weather models, variable and time. This allows to retrieve only a small subset of required data. In many cases only a limited number of weather variables like temperature is required significantly reducing the required data size.

Bucket contents: 
- `data/<model>/<weather-variable>/<time>.om`
- `README.md`

URL components:
- `model`: All data is grouped by weather model. E.g. `ncep_gfs013` or `dwd_icon_eu`
- `weather-variable`. Each model contains multiple weather variables. E.g. `temperature_2m` or `relative_humidity_2m`. Some weather variables like `wind_speed_10m` are calculated by the API, but `wind_u_component_10m` and `wind_v_component_10m` are stored.
- `time`: For each variable, data is split by time. This can be an entire year for historical data, or chunks of 1-2 weeks of data. An entire year is specified like `year_2010.om` while chunks of varying size use arbitrary indices like `chunk_927382.om`
- `.om` file extension: All data is stored in compressed multi-dimensional arrays. For an optimal compression, a custom file format is used. See [below](#file-format).


## Updates to Real-time Weather Forecasts
Real-time weather models refresh every 1, 3, 6, or 12 hours. Upon completion of a new weather model run, Open-Meteo retrieves the original GRIB files from national server services, updates the database, and publishes the revised database on AWS S3.

Data is structured into chunks covering 3 to 14 days per file, resulting in existing files being overwritten with the most recent data. The length of each time-chunk is manually picked for each model for a good balance between file size, compression ratio and read performance. 

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

1. **Running Your Own Weather API:**
If you require an extensive amount of weather data through an API daily and wish to run your own weather API, you can obtain current weather data from Open-Meteo on AWS Open Data. The Open-Meteo Docker container can listen for newly published data and keep your local database up-to-date. Similar to using past weather data:
- Install the Open-Meteo Docker image
- Start the data synchronization for a given weather model `docker run open-meteo sync ncep_gfs013 temperature_2m,relative_humidity_2m,wind_u_component_10m,wind_v_component_10m --past_days 3 --repeat-interval 5`
- Launch the API instance and get the latest forecast from your new API endpoint

To help you in setting up your own weather API you can follow [this tutorial](./tutorial_weather_api/) to setup your own weather API.


## File Format
Data is stored in a customized file format featuring a simple header and compressed data chunks. Files use the extension `om` and contain a 2-dimensional array for data, encompassing spatial and temporal dimensions. For global high-resolution domains like GFS at 0.125°, each file can consist of 4 to 6 million grid cells. With one value per hour over a year, the array dimensions reach `6,000,000 x 8,760`.

To efficiently compress large arrays, data is chunked into blocks ranging from 6 to 20 locations and 2 weeks of data, resulting in chunk dimensions of `20 x 336`. Specific lengths of dimensions and chunks are outlined in the meta-data header. Data published is optimised for time-series access. Accessing larger areas for single time steps is less efficient.

Meta data includes:
- Length of time and space dimensions (denoted as `dim0` and `dim1`)
- Chunk dimensions (denoted as `chunk0` and `chunk1`)
- Compression type and data scale-factor

Compressing 2-dimensional chunks achieves high compression ratios due to the homogeneity of gridded weather data in space and time. Compared to GRIB files, compression ratios are approximately 5 times higher on average. The compression scheme involves scaling weather data to reasonable accuracy (e.g., 0.05K for temperature), 2-dimensional delta coding, outlier filtering, and bit-length encoding. The reference implementation can be found in the [Open-Meteo GitHub repository](https://github.com/open-meteo/open-meteo/blob/main/Sources/SwiftPFor2D/SwiftPFor2D.swift). Libraries in other programming languages may follow based on public interest.

Each individual chunk varies in size. The format includes a lookup table addressing each chunk individually, enabling random reads to specific portions of a file. Thus, the overall format comprises meta-data, a lookup table, and a data blob to store compressed chunks.

Binary Format:
- `2 byte`: magic number "OM"
- `1 byte`: version
- `1 byte`: compression type with filter
- `4 byte`: float scalefactor
- `8 byte`: dim0 dim (slow)
- `8 byte`: dom0 dim1 (fast)
- `8 byte`: chunk dim0
- `8 byte`: chunk dim1
- `Array of 64-bit Integer`: Offset lookup table
- `Blob`: Data for each chunk, offset but the lookup table

This format draws inspiration from formats such as Zarr or HDF5 but is designed to encompass only a single 2-dimensional array. Opting for a custom format was a deliberate choice to seamlessly integrate a specialized compression scheme, interact directly with data, and eliminate any abstraction present in HDF5 or other libraries. Previous file formats and compression methods fell short of achieving the necessary compression performance.

The reference implementation is also designed to be thread-safe, minimize dynamic allocations, leverage high queue depth in SSDs, and deliver high throughput by harnessing the capabilities of modern CPUs with vector instructions.


### Decoding Example

A typical scenario involves retrieving one week of hourly weather data for a specific location. To accomplish this, an application must:
- Calculate the grid-cell position, for example, `1,345,434` out of `6,000,000` grid cells. For a global 0.125° regular grid, the calculation is `longitude / 0.125 + (latitude / 0.125) * (360/0.125)`.
- Open the `om` file and read the first 56 bytes to decode the header.
- Utilize the array and chunk dimensions to determine the chunk number.
- Access the offset through the lookup table.
- Read data from the specified offset.
- Decompress the data.

All these decoding steps are seamlessly integrated into the Open-Meteo API. There's no need for you to implement them. It is highly recommended to use the provided Docker files for reading from `om` files.


### Cloud-Native implementation
The `om` file format enables reading small subsections of data without the need to download the entire file. Only three read operations are necessary to obtain the data, and the first two reads can be efficiently cached.

As of now, the Open-Meteo API does not directly support partial reads from S3, but this functionality will be implemented in the future.


# License

CC-BY-4.0