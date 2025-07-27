## Carbon Emissions Batch Processing Pipeline

This project is a cloud-based batch data processing pipeline built using Google Cloud Platform (GCP) to simulate and analyze ride-level CO₂ emissions. The solution uses free-tier serverless components only, and automates ingestion, processing, and dashboarding of synthetic ride data.

---

## 🏠 Architecture Overview

```
Cloud Scheduler → Pub/Sub → Cloud Workflows
        ↓
Cloud Storage → BigQuery Table (ride_emissions)
        ↓
BigQuery View (emissions_with_co2)
        ↓
Looker Studio Dashboard
```

---

## 💾 Dataset

A synthetic dataset was generated to simulate ride data. It includes the following fields:

- `ride_id`
- `vehicle_type` (petrol\_car, diesel\_car, bus, electric\_bike)
- `distance_km`
- `city`
- `timestamp`
- `co2_grams` (calculated later in BigQuery view)

> The CSV file `rides.csv` is uploaded to a GCS bucket and used as the data source.

---

## ⚙️ Components & Technologies

| Component         | Service Used            | Description                                             |
| ----------------- | ----------------------- | ------------------------------------------------------- |
| Ingestion Trigger | Cloud Scheduler         | Weekly/Manual triggers                                  |
| Message Queue     | Pub/Sub                 | Decouples trigger from logic                            |
| Orchestration     | Cloud Workflows         | YAML-defined pipeline logic                             |
| Storage           | Cloud Storage, BigQuery | Raw and processed data                                  |
| Transformation    | BigQuery SQL View       | Computes `co2_grams` based on distance and vehicle type |
| Visualization     | Looker Studio           | Dashboard with CO₂ metrics                              |

---

## 📄 BigQuery Transformation Logic

```sql
-- emissions_with_co2.sql
SELECT *,
  distance_km * CASE
    WHEN vehicle_type = 'petrol_car' THEN 192
    WHEN vehicle_type = 'diesel_car' THEN 171
    WHEN vehicle_type = 'electric_bike' THEN 21
    WHEN vehicle_type = 'bus' THEN 105
    ELSE 0
  END AS co2_grams
FROM `project.dataset.ride_emissions`;
```

---

## 📎 Dashboard Highlights (Looker Studio)

- Total CO₂ emissions (scorecard)
- Emissions by vehicle type (pie chart)
- Emissions by city (bar chart)
- Emissions trend by month (chronologically sorted)

---

## 📊 Folder Structure

```
carbon-emissions-pipeline/
├── workflows/
│   └── load_to_bigquery.yaml
├── sql/
│   └── emissions_with_co2.sql
├── data/
│   └── rides.csv
├── docs/
│   ├── Sinha_Shivani_92124778_Batch_Processing_Pipeline_Construction_Phase1.docx
│   ├── Sinha_Shivani_92124778_Batch_Processing_Pipeline_Construction_Phase2.pdf
│   └── architecture_diagram.png
│   └── Walkthrough_Video.mp4
├── dashboard/
│   └── Emissions_Dashboard.pdf
│   └── details.md
├── README.md
```

---

## 🚀 How to Run (Summary)

1. Upload `rides.csv` to GCS bucket
2. Trigger Cloud Scheduler (or run Pub/Sub manually)
3. Workflow loads data into BigQuery
4. BigQuery View updates
5. Looker dashboard reflects updated data

---

## 💼 Author

	Shivani Sinha

---

## 🚨 Notes

- Fully GCP free-tier compatible
- No Dataproc, Spark, Cloud Functions, or Docker used
- Project maintained in Git for reproducibility and portfolio readiness

