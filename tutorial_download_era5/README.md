# Historical Weather Data from ERA5

Conducting intensive analyses on millions of events with varying locations and time steps is facilitated by having data available locally or on a dedicated high-performance VM instance. In this tutorial, you can download temperature data for the past 80 years using the Copernicus ERA5-Land dataset with Open-Meteo on AWS Open Data.

The Open-Meteo API can be installed locally using Docker or prebuilt Ubuntu packages. This tutorial uses Docker. More information can be found in the [Getting Started Guide](https://github.com/open-meteo/open-meteo/blob/main/docs/getting-started.md).

To download historical weather data and run an API instance locally, you will have to
1. Install the Open-Meteo Docker image
2. Download archived ERA5 data for temperature from AWS.
3. Launch your local API endpoint

Before you start, please familiarize yourself with the [Open-Meteo Historical Weather API](https://open-meteo.com/en/docs/historical-weather-api). In this tutorial you will basically replicate the same API endpoint.

## 1. Install Open-Meteo Docker image

The latest Open-Meteo Docker image can be downloaded from GitHub. If you want to run Open-Meteo on an Apple M1, please [build the Docker image from source](https://github.com/open-meteo/open-meteo/blob/main/docs/development.md).

```bash
# Download prebuilt image
docker pull ghcr.io/open-meteo/open-meteo
```

In order to store weather data, a docker volume `open-meteo-data` must be created. All downloaded weather data will be stored in this volume.

```bash
# Create a Docker volume to store weather data
docker volume create --name open-meteo-data
```


## 2. Download ERA5 Data

To download ERA5-Land temperature data run:

```bash
# Download ERA5-Land temperature
docker run -it --rm -v open-meteo-data:/app/data ghcr.io/open-meteo/open-meteo sync copernicus_era5_land temperature_2m --past-days 730

# Download the digital elevation model
docker run -it --rm -v open-meteo-data:/app/data ghcr.io/open-meteo/open-meteo sync copernicus_dem90 static
```

In addition to ERA5-Land, a digital elevation model (DEM) is downloaded to [improve data in mountainous terrain](https://openmeteo.substack.com/p/improving-weather-forecasts-with). With a resolution of 90 meters, it can resolve small valleys.

Parameter explanation:
- `-it --rm` instructs Docker to only run one command, exit and clean-up any running container
- `-v open-meteo-data:/app/data` assigns the Docker volume to store weather data
- `ghcr.io/open-meteo/open-meteo sync` run the `sync` command of the Open-Meteo Image
- `copernicus_era5_land` Select the weather dataset ERA5-Land. All datasets are listed in the [S3 bucket](https://openmeteo.s3.amazonaws.com/index.html#data/).
- `temperature_2m` Download temperature on 2 meters above ground. All variables for each dataset are also listed in the S3 bucket.
- `--past-days 730` will include all files that contain data from the past 2 years. If set to `3650`, data of the last 10 years would be downloaded.

It is essential to understand which datasets and variables are available. Open-Meteo uses a variety of weather models and computes certain weather variables on-demand. E.g. Relative humidity is calculated from temperature and dew point. In the case of ERA5, some variables are available in 0.25° and some on 0.1° resolution using ERA5-Land.

## 3. Start API endpoint

Data is now downloaded into the volume `open-meteo-data`, but it is not directly accessible. In order to access it locally, you have to start the Open-Meteo API endpoint.

```bash
# Start the API service on http://127.0.0.1:8080
docker run -d --rm -v open-meteo-data:/app/data -p 8080:8080 ghcr.io/open-meteo/open-meteo

# Get your forecast
curl "http://127.0.0.1:8080/v1/archive?latitude=52.52&longitude=13.41&start_date=2024-01-06&end_date=2024-01-20&hourly=temperature_2m&models=era5_land"
```

Once the API is running you can fetch data via HTTP. As the endpoint is the same as the Open-Meteo Historical Weather API, you can use the API documentation to configure your API URL or use the provided SDKs to analyze data.

For Python, you could install `pip install openmeteo-requests numpy pandas`

And run:

```python
import openmeteo_requests
import pandas as pd

# Setup the Open-Meteo API client
openmeteo = openmeteo_requests.Client()

# Make sure all required weather variables are listed here
# The order of variables in hourly or daily is important to assign them correctly below
url = "http://127.0.0.1:808/v1/archive"
params = {
	"latitude": 52.52,
	"longitude": 13.41,
	"start_date": "2024-01-06",
	"end_date": "2024-01-20",
	"hourly": "temperature_2m",
	"models": "era5_land"
}
responses = openmeteo.weather_api(url, params=params)

# Process first location. Add a for-loop for multiple locations or weather models
response = responses[0]
print(f"Coordinates {response.Latitude()}°E {response.Longitude()}°N")
print(f"Elevation {response.Elevation()} m asl")
print(f"Timezone {response.Timezone()} {response.TimezoneAbbreviation()}")
print(f"Timezone difference to GMT+0 {response.UtcOffsetSeconds()} s")

# Process hourly data. The order of variables needs to be the same as requested.
hourly = response.Hourly()
hourly_temperature_2m = hourly.Variables(0).ValuesAsNumpy()

hourly_data = {"date": pd.date_range(
	start = pd.to_datetime(hourly.Time(), unit = "s"),
	end = pd.to_datetime(hourly.TimeEnd(), unit = "s"),
	freq = pd.Timedelta(seconds = hourly.Interval()),
	inclusive = "left"
)}
hourly_data["temperature_2m"] = hourly_temperature_2m

hourly_dataframe = pd.DataFrame(data = hourly_data)
print(hourly_dataframe)
```
