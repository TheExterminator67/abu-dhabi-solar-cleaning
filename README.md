# Abu Dhabi Solar Data Cleaning & Forecast Baseline

Cleaning a messy year of daily weather and solar irradiance (GHI) data for Abu Dhabi, then using it to analyze sunlight patterns and build a simple forecasting baseline.

> **Note:** The dataset is synthetic. It was generated for practice and modeled on realistic Abu Dhabi conditions, with errors deliberately added. It is not real station data.

## Dataset

`data/messy_abu_dhabi_solar_2025.csv`: 365 rows of daily data for 2025.

| Column | Description |
|---|---|
| Date | Day of the reading |
| Station | Weather station |
| Condition | Sky condition (Clear, Hazy, Dusty, Cloudy) |
| Temp | Average temperature |
| Humidity % | Relative humidity |
| cloud_cover | Cloud cover (%) |
| Wind Speed | Wind speed (km/h) |
| GHI (kWh/m2) | Global Horizontal Irradiance: daily solar energy reaching the ground |

## What was cleaned

- **Missing values** written in several styles (`?`, `N/A`, blanks, and the sensor code `-999`)
- **Dates** in three different formats, parsed one format at a time to avoid day/month mix-ups
- **Duplicate days**, including the same day written in two different date formats
- **Mixed units**: Fahrenheit temperatures converted to Celsius
- **Text in numbers**: units like `°C` and `km/h`, and comma decimals (`5,8` → `5.8`)
- **Inconsistent labels**: station names and condition names (e.g. Dust / Sandstorm → Dusty)
- **Impossible values** set to NaN instead of guessed: humidity outside 0–100%, cloud cover over 100%, negative GHI, and a 71.5°C reading in June
- **Missing days**: compared against a full 2025 calendar to find days with no record

Rows are only dropped when the date itself is unusable, so a day with one bad reading is kept.

## Results

| Question | Result |
|---|---|
| Month with highest average GHI | **June** (≈ 6.99 kWh/m²/day) |
| Missing days | **6** (4 Mar, 12 Mar, 23 Jun, 25 Sep, 30 Sep, 8 Dec) |
| Avg GHI by condition | Clear 5.88 · Hazy 4.99 · Dusty 3.75 · Cloudy 2.71 |
| Cloud cover vs GHI correlation | **−0.56** (moderate negative) |
| Persistence forecast MAE | **0.86 kWh/m²** (mean GHI ≈ 5.41) |

**Takeaways**
- Sunlight peaks in early summer, when the sun is highest and skies are clearer than in the more humid, hazy months that follow.
- Clouds cut GHI the most, but dust and haze also reduce it a lot, which is why the cloud cover correlation is only moderate.

## Forecasting baseline

The simplest forecast assumes **tomorrow's GHI equals today's** (a persistence forecast). On this data, it is off by about 0.86 kWh/m² per day on average, roughly 16% of the mean. It does well during runs of clear days and badly when dust or clouds arrive.

This is the baseline any real forecasting model, such as an ML model using weather features, would need to beat.

## How to run

1. Open the notebook in Jupyter or Google Colab.
2. Make sure the CSV path in the first cell points to `data/messy_abu_dhabi_solar_2025.csv`.
3. Run all cells.

Requires `pandas` and `numpy`.

## Next steps

- Train an ML model (e.g. gradient boosting) using condition, cloud cover, humidity and past GHI, and compare its error to the persistence baseline
- Try the same pipeline on real irradiance data from NASA POWER or Open-Meteo
