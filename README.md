# Open-Meteo on AWS Open Data

**AWS Bucket Name and Region: s3://open-meteo; eu-west-1**

Open-Meteo integrates high-resolution weather models from well-known weather services, delivering a rapid weather API. Real-time weather forecasts are unified within a time-series database that covers both historical and future weather data.

The database, made available through the [AWS Open Data Sponsorship program](#), is composed of individual files for various weather models, weather variables, and time intervals.

The following weather services and models are available
- NOAA
    - GFS in 0.125° resolution
    - HRRR
- DWD
- ECMWF
- Environment Canada
- MeteoFrance
- ERA5 / other archive data
- Climate data
- Wave models
- Air quality models
- TODO prepare table with all NWS and models


## Data Organization

Data is structured by weather models, weather variable and time. This allows to retrieve only a small subset of required data.

Bucket contents: 
- `data/<model>/<weather-variable>/<time>.om`
- `README.md`

URL components:
- `model`: All data is grouped by weather model. E.g. `gfs012` or `era5_land`
- `weather-variable`. Each model contains multiple weather variables. E.g. `temperature_2m` or `relative_humidity`. Some weather variables like `wind_speed_10m` are calculated by the API, but `wind_u_component_10m` and `wind_v_component_10m` are stored.
- `time`: For each variable, data is split into time-interface. This can be an entire year for historical data, or chunks of 1-2 weeks of data. An entire year is specified like `year_2010.om` while chunks of varying size use arbitrary indices like `chunk_927382.om`


## Updates to Real-time Weather Forecasts
Real-time weather models refresh every 1, 3, 6, or 12 hours. Upon completion of a new weather model run, Open-Meteo retrieves the original GRIB files from national server services, updates the database, and publishes the revised database on AWS S3.

Data is structured into chunks covering 7 to 14 days per file, resulting in existing files being overwritten with the most recent data. 

Typically, historical weather data doesn't undergo updates. However, in the case of ERA5, daily updates are applied with a 5-7 day delay. Older historical data spanning the past 80 years remains unaltered, of course.


## Download and Interact With Data

Open-Meteo provides a free API for quick data retrieval without the need to download from the AWS bucket. However, there are two primary scenarios where downloading data locally is advantageous:

1. **Research with Past Data:**
Conducting intensive analyses on millions of events with varying locations and time steps is facilitated by having data available locally or on a dedicated high-performance VM instance. With Open-Meteo on AWS Open Data, you can download temperature data for the past 2 years at the full 9 km ECMWF IFS resolution within minutes. Basic steps include:
- Installing the Open-Meteo Docker image `docker pull ghcr.io/open-meteo/open-meteo`
- Download archived ECMWF data for temperature from AWS `docker run open-meteo download-s3 ecmwf_ifs temperature --year 2022,2023` (roughly 8 GB)
- Launch your local API endpoint `docker run -p 8080:8080 open-meteo serve`
- Get data for individual coordinates `curl "http://<instance_ip>:8080/v1/forecast?latitude=47.1&longitude=8.4&hourly=temperature_2m&start_date=20220101&end_date=20231031"`
- Note: Docker commands are exemplary. Follow the [Tutorial for downloading historical weather data](./tutorial_download_era5/) to get it working quickly.

2. **Running Your Own Weather API:**
If you require an extensive amount of weather data through an API daily and wish to run your own weather API, you can obtain up-to-date weather data from Open-Meteo on AWS Open Data. The Open-Meteo Docker container can listen for newly published data and keep your local database current. Similar to using past weather data:
- Install the Open-Meteo Docker image
- Start the data synchronization for a given weather model `docker run open-meteo sync-s3 gfs012 temperature,relative_humidity,wind_u_component_10m,wind_v_component_10m --past_days 3`
- Launch the API instance and obtain the latest forecast from your new API endpoint
- To help you in setting up your own weather API you can follow [this tutorial](./tutorial_realtime_weather_api/) to setup your own weather API on AWS with Elastic File System (EFS) to seamlessly scale your weather API.


## `om` File Format
Data is stored in a customized file format featuring a simple header and compressed data chunks. Files use the extension `om` and contain a 2-dimensional array for data, encompassing spatial and temporal dimensions. For global high-resolution domains like GFS at 0.125°, each file can consist of 4 to 6 million grid cells. With one value per hour over a year, the array dimensions reach `6,000,000 x 8,760`.

To efficiently compress large arrays, data is chunked into blocks ranging from 6 to 20 locations and 2 weeks of data, resulting in chunk dimensions of `20 x 336`. Specific lengths of dimensions and chunks are outlined in the meta-data header.

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

CC-BY-2.4