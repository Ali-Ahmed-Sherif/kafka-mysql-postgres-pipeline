# Kafka-Data-Pipeline

A Change Data Capture (CDC) pipeline built using Apache Kafka and Kafka Connect to stream data in real-time from a **MySQL** database (`ecomm`) to a **PostgreSQL** database (`ecomm`) and the using  pipeline is set up with two connectors and uses plugin-based configuration to enable CDC via Kafka.

---

## 📌 Project Summary

- 🔄 **Source**: MySQL database named `ecomm`
- 🎯 **Sink**: PostgreSQL database named `ecomm`
- 🔌 **Connectors Used**:
  - **MySQL Source Connector** for capturing CDC events
  - **PostgreSQL Sink Connector** for writing to the destination
- 📦 **Plugins**: Uploaded to Kafka Connect for enabling connector functionality
- 📍 **Next Step**: Add support for **multiple sinks** (e.g., MongoDB, Elasticsearch) to fan out the data stream

---

## ⚙️ Technologies Used

- Apache Kafka
- Kafka Connect
- MySQL
- PostgreSQL
- Docker & Docker Compose

---

## 🚀 How to Run

1. **Clone the repository and go to the working directory**  

   > 💡 *Make sure Docker Desktop or Docker Engine is running before continuing.*
   ```bash
   git clone https://github.com/your-username/Kafka-Data-Pipeline.git

   cd Kafka-Data-Pipeline/path/to/your-working-directory

   docker compose -f dc.yaml up -d

   docker ps
   ```
<img width="2172" height="1015" alt="Screenshot 2025-09-18 004749" src="https://github.com/user-attachments/assets/c2f99903-27f6-4ed4-a26c-d23801367a5e" />

   
   
## 📋 Included Services

| Service | Port | Description | Access URL |
|---------|------|-------------|------------|
| **Kafka Broker**    | 9092        | Apache Kafka broker with KRaft mode              | `localhost:9092`            |
| **Schema Registry** | 8081        | Confluent Schema Registry                        | `http://localhost:8081`     |
| **Kafka Connect**   | 8083        | Kafka Connect for data integration               | `http://localhost:8083`     |
| **ksqlDB Server**   | 8088        | Stream processing with ksqlDB                    | `http://localhost:8088`     |
| **Kafka UI**        | 8090        | Web-based Kafka management interface             | `http://localhost:8090`     |
| **MySQL**           | 3306        | Source database for CDC (ecomm)                  | `localhost:3306`            |
| **PostgreSQL**      | 5432        | Sink database for CDC (ecomm)                    | `localhost:5432`            |
| **JMX Metrics**     | 1234, 1235  | Prometheus JMX metrics endpoints                 | -                           |

<img width="470" height="348" alt="Screenshot 2025-09-18 024027" src="https://github.com/user-attachments/assets/4ae4a151-2af1-4505-a7d6-0294443cb774" />

<img width="439" height="398" alt="Screenshot 2025-09-18 024033" src="https://github.com/user-attachments/assets/280d4970-196b-4ada-8067-34c29d52cb8b" />

## 🔗 Connect to Databases via VS Code

You can connect to the MySQL and PostgreSQL containers using VS Code with a database extension like **Database Client** or **SQLTools**.

### 🐬 MySQL Connection (Port: 3305)

| Field     | Value     |
|-----------|-----------|
| Host      | 127.0.0.1 |
| Port      | 3305      |
| Username  | myuser    |
| Password  | mypassword|
| Database  | mydb      |

### 🐘 PostgreSQL Connection (Port: 5432)

| Field     | Value     |
|-----------|-----------|
| Host      | 127.0.0.1 |
| Port      | 5432      |
| Username  | admin     |
| Password  | password  |
| Database  | admin     |

## 🛠 Database Preparation

Create a new blank database named `ecomm` in both MySQL and PostgreSQL before running the pipeline:

- 🐬 **MySQL** (Port: 3305)
- 🐘 **PostgreSQL** (Port: 5432)

You can do this via CLI or any database GUI:

```sql
CREATE DATABASE ecomm;
```


## 🔌 Launch Kafka Connectors

After starting all services, run the following commands to deploy the **MySQL** and **PostgreSQL** connectors:
<img width="2098" height="1062" alt="Screenshot 2025-09-18 024051" src="https://github.com/user-attachments/assets/cb26366f-5719-4668-b789-b30b93027124" />

### 🐬 Register MySQL Connector

```bash
curl -sS -X POST \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  http://localhost:8083/connectors \
  -d @connectors/mysql/mysql-connect.json
```

### 🐘 Register PostgreSQL Connector
```bash
curl -sS -X POST \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  http://localhost:8083/connectors \
  -d @connectors/pg/pg-connect.json
```
<img width="2559" height="520" alt="Screenshot 2025-09-18 024100" src="https://github.com/user-attachments/assets/9c953fb2-06bf-4280-b37e-734eb7b78e86" />

<img width="2559" height="1316" alt="Screenshot 2025-09-18 024109" src="https://github.com/user-attachments/assets/4bd2b37b-e279-48d3-b9cd-23d48351d9a3" />


### ✅ Verify Connectors Are Running
Open the following URL in your browser to check the status of both connectors:
```bash
http://localhost:8083/connectors?expand=status
```
You should see both connectors listed with their status as "RUNNING"
![connectors.png](Images/image1.png)

## 🔄 CDC in Action

With the connectors successfully deployed, any DDL operations (like creating tables) or DML operations (like inserting, updating, or deleting records) in the MySQL `ecomm` database will be automatically reflected in the PostgreSQL `ecomm` database.

## 📄 Database Initialization Script

To set up the initial database schema and insert sample data into your MySQL `ecomm` database, use the provided SQL script: sql-scripts/mysql.sql


## ✅ Kafka Consumer Status

The Kafka consumer service is now fully functional and **listening to all topics** produced by the CDC pipeline.


<img width="1590" height="320" alt="Screenshot 2025-09-18 024123" src="https://github.com/user-attachments/assets/7d248e0a-b50f-41df-9c66-18966c2de390" />
<img width="1585" height="264" alt="Screenshot 2025-09-18 024130" src="https://github.com/user-attachments/assets/bf5f1470-ea55-4fd0-9aac-a2be00b96394" />
<img width="1574" height="320" alt="Screenshot 2025-09-18 024136" src="https://github.com/user-attachments/assets/a1515b6a-d2e7-4e50-90eb-846101e9cfe1" />

This confirms that:

- All MySQL events are being streamed via Debezium
- Kafka topics are populated accordingly
- PostgreSQL sink receives updates in real-time
- Your consumer is subscribed and processing the data as expected
![consumer.png](Images/image2.png)

## 🎯 End-to-End Pipeline Verification

The data is now flowing successfully from the MySQL source database to the PostgreSQL target database.

![postgres.png](Images/image3.png)

