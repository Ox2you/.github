# OMI Air Quality Monitoring System

## Overview

This project provides a complete data ingestion, processing, and visualization pipeline for monitoring air quality using NASA’s OMI satellite data. Currently, the system focuses on sulfur dioxide (SO₂) concentrations, but the platform is designed to be extensible in the future to handle additional pollutants such as CO₂, NO₂, and others. The system automatically downloads, processes, and stores air quality data every hour, exposes it through a RESTful API, and displays the results on an interactive React-based heatmap interface.

---

## System Architecture

### Components

1. **Python Ingestion Service (Scheduler)**

   * Runs hourly using an internal scheduler.
   * Downloads OMI Level 2 granules from NASA’s Earthdata API.
   * Parses `.he5` files to extract geospatial data fields (currently SO₂).
   * Classifies air quality levels (`good`, `moderate`, `unhealthy_sensitive`, `unhealthy`, `very_unhealthy`).
   * Inserts processed data into a PostgreSQL database.

2. **Node.js REST API**

   * Serves as the backend interface for querying air quality data.
   * Provides endpoints for fetching aggregated or filtered data (e.g., by date range, region, or pollution level).
   * Handles connection pooling and query optimization for large datasets.

3. **PostgreSQL Database**

   * Central data repository for all measurements.
   * Stores geospatial coordinates, pollutant levels, timestamps, and quality classifications.
   * Indexed for efficient geospatial queries (PostGIS optional).

4. **React Frontend (Heatmap Dashboard)**

   * Displays real-time and historical data on an interactive heatmap.
   * Uses color coding to represent pollution levels (e.g., green for good, red for very unhealthy).
   * Allows users to filter by date/time and pollution intensity.

---

## Data Flow

1. **Scheduler Execution**: Every hour, the Python script runs automatically.
2. **Data Fetching**: The script downloads new `.he5` granules from NASA’s Earthdata API.
3. **Data Processing**: The granules are parsed, validated, and transformed into structured records.
4. **Database Storage**: Valid records are inserted into the PostgreSQL database.
5. **API Exposure**: The Node.js API provides access to this data for external clients.
6. **Frontend Visualization**: The React app consumes the API to render a heatmap visualization.

---

## Technology Stack

| Layer     | Technology                               |
| --------- | ---------------------------------------- |
| Ingestion | Python, h5py, requests, psycopg2, dotenv |
| Database  | PostgreSQL                               |
| API       | Node.js (Express.js)                     |
| Frontend  | React, Leaflet.js, TailwindCSS           |

---

## Deployment Architecture

```text
+-------------------------+
|      React Frontend     |
|  (Leaflet Heatmap UI)   |
+-----------+-------------+
            |
            v
+-------------------------+
|     Node.js REST API    |
|  (Data Query Endpoint)  |
+-----------+-------------+
            |
            v
+-------------------------+
|     PostgreSQL DB       |
|  (Air Quality Records)  |
+-----------+-------------+
            ^
            |
+-------------------------+
|  Python Scheduler/ETL   |
| (NASA Data Downloader)  |
+-------------------------+
```

---

## Data Classification

| Quality Level       | Range (mol/m²) | Description                    |
| ------------------- | -------------- | ------------------------------ |
| good                | < 0.0001       | Clean air                      |
| moderate            | < 0.0003       | Slight pollution               |
| unhealthy_sensitive | < 0.0005       | Unhealthy for sensitive groups |
| unhealthy           | < 0.001        | Unhealthy                      |
| very_unhealthy      | ≥ 0.001        | Hazardous                      |

---

## Future Improvements

* Extend the platform to handle additional pollutants such as CO₂, NO₂, and PM2.5.
* Add **Docker Compose** for unified deployment.
* Implement **WebSocket** updates for real-time visualization.
* Integrate **PostGIS** for optimized spatial queries.
* Add **Grafana dashboards** for analytics.
* Implement historical trend analysis and predictive modeling.

---

## License

This project is licensed under the MIT License.
