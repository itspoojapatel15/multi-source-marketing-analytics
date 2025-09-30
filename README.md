# Multi-Source Marketing Analytics Pipeline

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![Debezium](https://img.shields.io/badge/Debezium-2.5-red.svg)](https://debezium.io/)
[![Kafka Connect](https://img.shields.io/badge/Kafka_Connect-3.6-black.svg)](https://kafka.apache.org/)


## Demo

![Project Demo](screenshots/project-demo.png)

*Marketing analytics with Debezium CDC connectors, campaign ROI analysis, and Customer 360 value tier breakdown (Platinum to Prospect)*

## Architecture

```
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”     â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”     â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â” 
â”‚  PostgreSQL  â”‚â”€â”€â”€â–¶â”‚Debezium â”‚â”€â”€â”€â–¶â”‚              â”‚
â”‚  (CRM Data)  â”‚CDC â”‚Connectorâ”‚    â”‚              â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜    â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜    â”‚              â”‚
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”     â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”     â”‚    Kafka     â”‚    â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”     â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â” 
â”‚    MySQL     â”‚â”€â”€â”€â–¶â”‚Debezium â”‚â”€â”€â”€â–¶â”‚   Topics     â”‚â”€â”€â”€â–¶â”‚  Spark   â”‚â”€â”€â”€â–¶â”‚Snowflakeâ”‚
â”‚  (E-comm)   â”‚CDC â”‚Connectorâ”‚    â”‚  (Avro +     â”‚    â”‚  ETL     â”‚    â”‚   DWH   â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜    â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜    â”‚  Schema Reg) â”‚    â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜    â””â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”˜
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”     â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”     â”‚              â”‚                        â”‚
â”‚  REST APIs   â”‚â”€â”€â”€â–¶â”‚  Kafka  â”‚â”€â”€â”€â–¶â”‚              â”‚                   â”Œâ”€â”€â”€â”€â”´â”€â”€â”€â”€â” 
â”‚(GA, Ads, FB) â”‚    â”‚Producer â”‚    â”‚              â”‚                   â”‚   dbt   â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜    â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜    â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜                   â”‚ Models  â”‚
                                                                      â””â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”˜
                                                                           â”‚
                                                                    dim_customer_360
```

## Features

- **Debezium CDC**: Real-time change capture from PostgreSQL + MySQL
- **Kafka Connect**: Managed connectors with Avro serialization + Schema Registry
- **Multi-Source REST**: Google Analytics, Google Ads, Facebook Ads API integrations
- **Schema Registry**: Avro schema evolution and compatibility
- **dbt Transformation**: SCD Type 2 snapshots, dim_customer_360
- **Star Schema**: Full dimensional model with facts and dimensions
- **Docker Compose**: Complete local development environment

## Quick Start

```bash
cp .env.example .env
docker-compose up -d

# Register Debezium connectors
python -m connectors.register_connectors

# Start REST API producers
python -m producers.marketing_producer

# Run dbt models
cd dbt_project && dbt run
```

## Project Structure

```
â”œâ”€â”€ config/                  # Settings and connector configs
â”œâ”€â”€ connectors/              # Debezium connector registration
â”œâ”€â”€ producers/               # REST API â†’ Kafka producers
â”œâ”€â”€ consumers/               # Kafka â†’ Snowflake consumers
â”œâ”€â”€ dbt_project/             # Full dbt transformation layer
â”‚   â”œâ”€â”€ models/staging/      # Source staging views
â”‚   â”œâ”€â”€ models/intermediate/ # Business logic
â”‚   â”œâ”€â”€ models/marts/        # Star schema (facts + dims)
â”‚   â””â”€â”€ snapshots/           # SCD Type 2 tracking
â”œâ”€â”€ schemas/                 # Avro schema definitions
â”œâ”€â”€ docker-compose.yml       # Full local stack
â””â”€â”€ tests/                   # Unit tests
```


## Test Results

All unit tests pass â€” validating core business logic, data transformations, and edge cases.

![Test Results](screenshots/test-results.png)

**14 tests passed** across 3 test suites:
- `TestPostgresConnector` â€” Debezium config, Avro converters, table inclusion
- `TestMySQLConnector` â€” connector class, schema history topic
- `TestCustomer360Logic` â€” value tier classification, health score bounds, ROAS/CTR

## Maintainer

This project is actively maintained by Pooja Patel.

**Pooja Patel**
Data Science Graduate

Pooja is a Data Science graduate with a strong background in statistical analysis, predictive modeling, and data visualization. She is proficient in Python, R, SQL, Tableau, Power BI, and dashboard development, with hands-on experience analyzing large datasets, automating workflows, and improving decision-making across various domains. Her expertise includes streamlining processes, enhancing data accuracy, and delivering clear, actionable insights.

*   **Email**: patel.pooja81599@gmail.com