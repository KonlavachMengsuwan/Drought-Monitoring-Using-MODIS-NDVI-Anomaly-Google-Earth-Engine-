# 🌍 Drought Monitoring Using MODIS NDVI Anomaly (Google Earth Engine)

This project monitors drought by analyzing vegetation condition through **NDVI anomalies** derived from **MODIS satellite imagery** in **Google Earth Engine (GEE)**.

🛰️ **Dataset:** MODIS Terra Vegetation Indices (MOD13Q1 v6)  
📍 **Study Area:** Example region: Kenya (coordinates adjustable)

---

## 🎯 Objective

✅ Detect and map areas affected by vegetation stress (potential drought impact)  
✅ Compare current NDVI values to a long-term historical mean  
✅ Visualize spatial patterns of NDVI anomaly  
✅ Analyze NDVI time series over the study region

---

## 📝 Methodology

The project follows these steps:

1. **Load MODIS NDVI Image Collection** (MOD13Q1)
2. Filter NDVI data for the target **month and year** (e.g., June 2022)
3. Compute **historical mean NDVI** for the same month across **2001–2021**
4. Calculate **NDVI anomaly (%)**:
   
## NDVI anomaly (%) = ((NDVI_current - NDVI_mean) / NDVI_mean) × 100

5. Visualize:
- Historical NDVI
- Current NDVI
- NDVI anomaly map
6. Chart NDVI time series over the area

---

## 📚 Datasets

| Dataset                 | Source               | Resolution | Interval  |
|------------------------|--------------------|------------|-----------|
| MOD13Q1 (NDVI)          | MODIS/Terra (NASA) | 250 m      | 16 days   |

Accessed via **Google Earth Engine public data catalog.**

---

## 📍 Study Area

✅ Example AOI: rectangle over Kenya

Coordinates:

```text
[36, -2, 39, 1]
```

| Kenya | NDVI June 2022 |
|-----------------|-----------------------|
| ![1](https://github.com/user-attachments/assets/28aee014-d4fc-40ca-a7e4-6df3f8257932) | ![2](https://github.com/user-attachments/assets/1b57fe26-3017-4b95-beaa-05183ea8f2d4) |

| NDVI Anomaly Map | Mean June NDVI (2001-2021) |
|-----------------|-----------------------|
| ![3](https://github.com/user-attachments/assets/7da5dbeb-a105-4804-af25-4da6d74d55ae) | ![4](https://github.com/user-attachments/assets/1d0367f1-55d0-4b80-8f75-3fefe060338c) |

👉 Area can be customized to any region globally by adjusting coordinates.

## 🖼️ Results

### NDVI anomaly map (June 2022 vs. 2001–2021 mean):

- 🌿 **Blue areas:** above-average vegetation (positive anomaly)
- 🌾 **White areas:** near-normal vegetation
- 🔴 **Red areas:** below-average vegetation → **potential drought stress**

Example output:

*(Insert map screenshot here, e.g., outputs/ndvi_anomaly_map.png)*

---

## 📈 Time Series

A chart of NDVI over the study area provides insight into seasonal trends, greening, or drying cycles.

*(Insert time series chart screenshot here, e.g., outputs/ndvi_time_series.png)*

The chart shows NDVI from 2001 through 2022, allowing historical comparisons and recent deviations.

---

## 💬 Interpretation

✅ Areas showing **significant negative anomaly (red)** are experiencing vegetation greenness below the 20-year average → indicating **potential drought stress or poor growing conditions.**

✅ Areas showing **positive anomaly (blue)** may have benefited from favorable rainfall, irrigation, or other factors leading to better-than-average vegetation.

⚠️ NDVI anomaly should be interpreted in the context of crop calendars, land cover type, and phenology → negative anomaly during harvest season may not indicate drought.

---

## 💻 Code

The main script (`code/drought_monitoring_ndvi.js`) is designed to run in **Google Earth Engine Code Editor.**

Here’s the core code:

```js
// Define AOI
var aoi = ee.Geometry.Rectangle([36, -2, 39, 1]);
Map.centerObject(aoi, 7);

// Load MODIS NDVI
var modisNDVI = ee.ImageCollection('MODIS/006/MOD13Q1')
  .select('NDVI')
  .filterBounds(aoi);

// Target year and month
var targetYear = 2022;
var targetMonth = 6; // June

// Current NDVI
var targetNDVI = modisNDVI
  .filter(ee.Filter.calendarRange(targetYear, targetYear, 'year'))
  .filter(ee.Filter.calendarRange(targetMonth, targetMonth, 'month'))
  .mean();

// Historical mean NDVI
var historicalNDVI = modisNDVI
  .filter(ee.Filter.calendarRange(2001, 2021, 'year'))
  .filter(ee.Filter.calendarRange(targetMonth, targetMonth, 'month'))
  .mean();

// NDVI anomaly (%)
var ndviAnomaly = targetNDVI.subtract(historicalNDVI)
  .divide(historicalNDVI)
  .multiply(100)
  .rename('NDVI_Anomaly');

// Visualization
var visNDVI = {min: 0, max: 9000, palette: ['brown', 'yellow', 'green']};
var visAnomaly = {min: -50, max: 50, palette: ['red', 'white', 'blue']};

Map.addLayer(historicalNDVI.clip(aoi), visNDVI, 'Mean NDVI 2001-2021');
Map.addLayer(targetNDVI.clip(aoi), visNDVI, 'NDVI June ' + targetYear);
Map.addLayer(ndviAnomaly.clip(aoi), visAnomaly, 'NDVI Anomaly (%) June ' + targetYear);

// Time series chart
var chart = ui.Chart.image.series({
  imageCollection: modisNDVI,
  region: aoi,
  reducer: ee.Reducer.mean(),
  scale: 250,
  xProperty: 'system:time_start'
}).setOptions({
  title: 'NDVI Time Series over AOI',
  vAxis: {title: 'NDVI (scaled)'},
  lineWidth: 2,
  pointSize: 4
});
print(chart);
```

## 🚀 How to Run

1. Open [Google Earth Engine Code Editor](https://code.earthengine.google.com/).
2. Create a new script.
3. Copy and paste the code from `code/drought_monitoring_ndvi.js`.
4. Adjust the following parameters if needed:
   - `aoi` (area of interest coordinates)
   - `targetYear` (e.g., 2022)
   - `targetMonth` (e.g., 6 for June)
5. Click **Run** to generate:
   - Historical mean NDVI map
   - Current NDVI map
   - NDVI anomaly map
   - NDVI time series chart
6. Use the map and chart panels for exploration.
7. (Optional) Export outputs as GeoTIFF or CSV using Earth Engine’s `Export.image.toDrive()` or `Export.table.toDrive()` functions.

---

## 💡 Applications

✅ Monitor drought stress spatially and temporally  
✅ Support early warning and agricultural planning  
✅ Identify vegetation greenness deficits  
✅ Visualize long-term vegetation trends

---

## 📝 References

- MODIS NDVI Documentation: https://modis.gsfc.nasa.gov/data/dataprod/mod13.php
- Google Earth Engine Documentation: https://developers.google.com/earth-engine

---

## 🤝 Acknowledgements

Developed by [Your Name] as part of a drought monitoring remote sensing project using Google Earth Engine.

---

## 📢 License

MIT License

