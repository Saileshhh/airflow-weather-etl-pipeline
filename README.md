#  Apache Airflow Weather ETL Pipeline

A fully containerized **ETL pipeline** built with **Apache Airflow**, **Astro Runtime**, and **PostgreSQL**, designed to extract real-time weather data from the **Open-Meteo API**, transform it, and load it into a database for analytics.

This project demonstrates modern Data Engineering practices including orchestration, scheduling, hooks, environment configuration, and Docker-based local development.

---

## 🚀 Features

-  Extracts real-time weather data using **HttpHook**  
-  Transforms JSON into structured Python objects  
-  Loads clean records into **PostgreSQL** using `PostgresHook`  
-  Fully containerized using **Astro CLI** (Astronomer Runtime)  
-  Automatic table creation  
-  Daily scheduled DAG (`@daily`)  
-  Modular ETL pipeline using Airflow TaskFlow API  
-  Easy to extend for more cities or more API attributes

---

## 🗂 Project Structure
ETL WEATHER/
│── dags/
│ ├── etlweather.py # Main ETL DAG
│ ├── exampledag.py
│── include/
│── plugins/
│── tests/
│ └── dags/
│ └── test_dag_example.py
│── Dockerfile
│── docker-compose.yml
│── airflow_settings.yaml
│── requirements.txt
│── .env # (ignored)
│── .gitignore
│── README.md


---

## ⚙️ Technologies Used

- **Apache Airflow 2.10+**
- **Astronomer Runtime (Astro CLI)**
- **Python 3.10**
- **PostgreSQL 13**
- **Docker / Docker Compose**
- **Open-Meteo Weather API**

---

# 🔧 ETL Workflow

### **1️⃣ Extract**
Uses Airflow’s `HttpHook` and Airflow Connection (`open_meteo_api`):

- Latitude: **51.5074**  
- Longitude: **-0.1278** (London)

### **2️⃣ Transform**
Extracts fields:

- temperature  
- windspeed  
- winddirection  
- weathercode  

Organized into a clean Python dictionary.

### **3️⃣ Load**
Writes the transformed data into PostgreSQL:

- Auto-creates table `weather_data`
- Inserts timestamp automatically

---

#  DAG Code (Main Pipeline)



```python
with DAG(dag_id='weather_etl_pipeline',
         default_args=default_args,
         schedule_interval='@daily',
         catchup=False) as dags:
    
    weather_data = extract_weather_data()
    transformed_data = transform_weather_data(weather_data)
    load_weather_data(transformed_data)

          +--------------------+
          |  Open-Meteo API    |
          +---------+----------+
                    |
                    v
          +--------------------+
          |  Extract Task      |
          | (HttpHook)         |
          +---------+----------+
                    |
                    v
          +--------------------+
          | Transform Task     |
          | (Clean JSON)       |
          +---------+----------+
                    |
                    v
          +--------------------+
          | Load Task          |
          | (PostgresHook)     |
          +---------+----------+
                    |
                    v
          +--------------------+
          | PostgreSQL DB      |
          +--------------------+

 Running the Project (Local Setup)
1️⃣ Install Astro CLI

https://www.astronomer.io/docs/astro/cli/install

2️⃣ Start the project
astro dev start

3️⃣ Access Airflow

UI: http://localhost:8080

Username: admin

Password: admin

4️⃣ Add Airflow Connection
HTTP Connection (open_meteo_api)

Conn ID: open_meteo_api

Host: https://api.open-meteo.com

No auth needed

Postgres Connection

Already provided by Astro: postgres_default
