
This architecture ensures a **no-code/low-code, reproducible, and scalable** data pipeline suitable for any organization managing recurring CSV data updates.

---

## ⚙️ Workflow Description

### 1. Email Trigger and OAuth Connection
- The workflow starts with a **Gmail Trigger** node in n8n.  
- Gmail is connected through **Google Cloud OAuth 2.0**:
  - A new project is created in **Google Cloud Console**.
  - The **Gmail API** is enabled.
  - **OAuth credentials** (Client ID & Client Secret) are created and securely stored in n8n.
  - Redirect URI configured for `http://localhost:5678/rest/oauth2-credential/callback`.

### 2. Attachment Extraction
- When a new email with a specific **subject keyword** (e.g. `USA Orders`, `Supply Data`) arrives,  
  n8n automatically downloads the **attached CSV files**.

### 3. CSV Parsing and Data Transformation
- The attached CSVs (e.g. `fact_order_line_usa_2025-05-17.csv`, `fact_aggregate_usa_2025-05-17.csv`) are parsed using the **CSV Node**.
- Data is cleaned and transformed into a normalized format ready for database ingestion.

### 4. Upload to Supabase (PostgreSQL)
- Data is uploaded to **Supabase** using the **Supabase Node**.
- Tables represent fact models such as:
  - `fact_order_line` — detailed order-level data
  - `fact_aggregate` — summarized performance data
- The database serves as the central **data warehouse** for all supply chain metrics.

### 5. Connection to Quadratic
- **Quadratic** is linked to the Supabase database as a live data source.
- This enables advanced data analytics, including:
  - AI-powered natural language queries (e.g., *"show delayed shipments this week"*)
  - Forecasting using embedded AI models
  - Real-time data visualization dashboards

### 6. Analytics Output
- Quadratic interprets data patterns and provides insights such as:
  - Order fulfillment performance
  - Regional distribution efficiency
  - Supply bottleneck identification
  - Forecasted demand vs. current stock

---

## 🧠 Tools and Technologies

| Tool | Purpose |
|------|----------|
| **n8n** | Workflow automation platform orchestrating all tasks |
| **Docker** | Containerized runtime for n8n |
| **Supabase (PostgreSQL)** | Cloud database for structured data storage |
| **Quadratic AI** | AI-powered data analysis and visualization |
| **Google Cloud Platform (GCP)** | OAuth 2.0 authentication for Gmail API |
| **Gmail API** | Source for automatic CSV ingestion |

---

## 📊 Data Sources

The workflow processes recurring supplier or logistics CSV reports, including:

- **fact_order_line_usa_2025-05-17.csv**  
  Contains order-level transactional data such as order IDs, shipment dates, quantities, and customer regions.

- **fact_aggregate_usa_2025-05-17.csv**  
  Contains aggregated KPIs such as delivery performance, average delay, and warehouse-level summaries.

---

## 🚀 Implementation Steps

1. **Run n8n in Docker**
   ```bash
   docker run -it --rm \
   -p 5678:5678 \
   -v ~/.n8n:/home/node/.n8n \
   n8nio/n8n
