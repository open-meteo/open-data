# Run Your Own Weather API

If you require an extensive amount of weather data through an API daily and wish to run your own weather API, you can obtain current weather data from Open-Meteo on AWS Open Data. The Open-Meteo Docker container can listen for newly published data and keep your local database up-to-date. 

Basic steps include:
- Install the Open-Meteo Docker image
- Start the data synchronization for given weather models
- Launch the API instance and get the latest forecast from your new API endpoint

Before you start, please familiarize yourself with the [Open-Meteo Weather API](https://open-meteo.com/en/docs). In this tutorial you will basically replicate the same API endpoint.

## 1. Install Open-Meteo Docker image

The latest Open-Meteo Docker image can be downloaded with:

```bash
# Download prebuilt image
docker pull ghcr.io/open-meteo/open-meteo
```

In order to store weather data, a docker volume `open-meteo-data` must be created. All downloaded weather data will be stored in this volume.

```bash
# Create a Docker volume to store weather data
docker volume create --name open-meteo-data
```


## 2. Download Weather Forecasts

To download a simple temperature forecast from the global GFS weather model run:

```bash
# Download the digital elevation model
docker run -it --rm -v open-meteo-data:/app/data ghcr.io/open-meteo/open-meteo sync copernicus_dem90 static

# Download GFS temperature on 2 meter above ground
docker run -it --rm -v open-meteo-data:/app/data ghcr.io/open-meteo/open-meteo sync ncep_gfs013 temperature_2m --past-days 3

# Check every 10 minutes for updated forecasts. Runs in background
docker run -d --rm -v open-meteo-data:/app/data ghcr.io/open-meteo/open-meteo sync ncep_gfs013 temperature_2m --past-days 3 --repeat-interval 10
```

In addition, a digital elevation model (DEM) is downloaded to [improve data in mountainous terrain](https://openmeteo.substack.com/p/improving-weather-forecasts-with). With a resolution of 90 meters, it can resolve small valleys.

Parameter explanation:
- `-it --rm` instructs Docker to only run one command, exit and clean-up any running container
- `-v open-meteo-data:/app/data` assigns the Docker volume to store weather data
- `ghcr.io/open-meteo/open-meteo sync` run the `sync` command of the Open-Meteo Image
- `ncep_gfs013` Select the weather dataset ERA5-Land. All datasets are listed in the [S3 bucket](https://openmeteo.s3.amazonaws.com/index.html#data/).
- `temperature_2m` Download temperature on 2 meters above ground. All variables for each dataset are also listed in the S3 bucket.
- `--past-days 3` will include all files with forecast data and data from the past 3 days.
- `--repeat-interval 10` check every 10 minutes for updated weather forecasts. Continues running indefinitely.

It is essential to understand which datasets and variables are available. Open-Meteo uses a variety of weather models and computes certain weather variables on-demand. E.g. Relative humidity is calculated from temperature and dew point. More information below.



## 3. Start API endpoint

Data is now downloaded into the volume `open-meteo-data`, but it is not directly accessible. In order to access it locally, you have to start the Open-Meteo API endpoint.

```bash
# Start the API service on http://127.0.0.1:8080
docker run -d --rm -v open-meteo-data:/app/data -p 8080:8080 ghcr.io/open-meteo/open-meteo

# Get your forecast
curl "http://127.0.0.1:8080/v1/forecast?latitude=47.1&longitude=8.4&models=gfs_global&hourly=temperature_2m"
```

Once the API is running you can fetch data via HTTP. As the endpoint is the same as the Open-Meteo Historical Weather API, you can use the API documentation to configure your API URL or use the provided SDKs to analyze data.

This repository also contains a [`docker-compose.yml`](/docker-compose.yml) to automate the steps above. Make sure to edit [`.env`](/.env) and select the right weather models and variables. For optimal performance, use AWS region `us-west-2` and deploy Open-Meteo on ECS.

```bash
git clone https://github.com/open-meteo/open-data open-meteo
cd open-meteo
docker compose up -d
docker compose down
```


## Downloading Weather Models
Open-Meteo fetches raw weather data from national weather services and transforms it into a highly optimized time-series database. Each weather model has unique properties, different weather variables, different resolution and may only provide regional forecast. To operate your own weather API, a good knowledge about weather models is essential.

As illustrated earlier, the `sync` command enables the direct download of the Open-Meteo weather database from AWS S3. It requires two arguments:
1. One or more weather model, such as `ecmwf_ifs04` or `dwd_icon,dwd_icon_eu,dwd_icon_d2`
2. A list of weather variables, for example, `temperature_2m,relative_humidity_2m,wind_u_component_10m,wind_v_component_10m`

The weather models and variables may seem unfamiliar because the Open-Meteo weather API automatically selects the most suitable weather model for each location. You can find a complete list of all weather models in the [open-data distribution](https://github.com/open-meteo/open-data) documentation and browse the S3 bucket.

The `sync` command also accepts two optional parameters:
1. `--past-days <number>`: Specifies the duration for downloading past weather data, with a default of 3 days. Note that due to data compression in short time intervals, 2-7 days of past data will always be available.
2. `--repeat-interval <number>`: If set, the API will run indefinitely, checking for new data every N minutes.

### Basic Configuration
To run a weather API covering the variables `temperature_2m, relative_humidity_2m, precipitation_probability, rain, snowfall, weather_code, cloud_cover, wind_speed_10m, wind_direction_10m`, consider the following configuration:

- Weather models: `dwd_icon,ncep_gfs013,ncep_gfs025,ncep_gefs025_probability`
- Variables: `temperature_2m,dew_point_2m,relative_humidity_2m,precipitation_probability,precipitation,rain,cloud_cover,snowfall_water_equivalent,snowfall_convective_water_equivalent,weather_code,wind_u_component_10m,wind_v_component_10m,cape,lifted_index`

Note the expanded list of weather variables. Additional data is required for on-demand computation of certain variables, such as deriving `wind_speed_10m` from `wind_u_component_10m` and `wind_v_component_10m`. NCEP GFS and DWD ICON are selected as the list of weather models, both being global models with strong forecast accuracy worldwide. For GFS, the variants `gfs013` and `gfs025` are downloaded, offering temperature data at resolutions of 13 km and 25 km, respectively. Some variables like `cape` are exclusively available in 25 km resolution for GFS. This configuration required 12 GB of storage.

To further refine the forecast accuracy, consider adding high-resolution models for Europe and North America, namely `ncep_hrrr_conus`, `dwd_icon_eu`, and `dwd_icon_d2`. These models enhance local precision and provide updates every 1 or 3 hours. Simply include them in the list of weather models: `dwd_icon,ncep_gfs013,ncep_gfs025,ncep_gefs025_probability,ncep_hrrr_conus,dwd_icon_eu,dwd_icon_d2`. Integrating local models requires an additional 3 GB of storage.

The selection of weather models and variables should be approached thoughtfully. Refer to the [open-data documentation](https://github.com/open-meteo/open-data) for a comprehensive understanding of available weather models and variables.