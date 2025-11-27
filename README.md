# 📌 E-Commerce Fraud Detection – Cloud Architecture

Below is the high-level system architecture:

```mermaid
graph TD
    %% 1. Ingestion & Streaming
    subgraph "1. Ingestion & Streaming"
        CSV[(Fraud Dataset CSV)]
        Producer[Python Event Producer]
        EventHub[Azure Event Hubs]

        CSV -->|Batch Load / Replay| Producer
        Producer -->|JSON Stream| EventHub
    end

    %% 2. Storage & Processing
    subgraph "2. Storage & Processing"
        ASA[Azure Stream Analytics]
        Blob[Azure Data Lake / Blob Storage]
        SQL[(Azure SQL Database)]
        AML[Azure Machine Learning]

        EventHub -->|Path A: Hot| ASA
        EventHub -->|Path B: Cold| Blob

        ASA -->|Suspicious Txns| SQL
        Blob -->|Historical Data| AML
    end

    %% 3. AI & Machine Learning
    subgraph "3. AI & Machine Learning"
        Train[Training Notebook / Pipeline]
        Balancing[SMOTE / Class Weights]
        Model[Random Forest / XGBoost]
        MLflow[MLflow / AML Registry]
        Endpoint[Real-Time Inference API]

        AML -->|Read Features| Train
        Train -->|Imbalanced Data| Balancing
        Balancing -->|Train Model| Model
        Model -->|Track Metrics| MLflow
        MLflow -->|Deploy Best Model| Endpoint
    end

    %% 4. Consumption & Visualization
    subgraph "4. Consumption & Visualization"
        Streamlit[Streamlit Fraud Analyst App]
        PowerBI[Power BI Fraud Dashboard]

        Endpoint -->|Score Txn| Streamlit
        SQL -->|Direct Query| PowerBI
        Streamlit -->|Manual Label / Feedback| SQL
    end

    %% Support (Security, CI/CD)
    subgraph "Support"
        KV[Azure Key Vault]
        GitHub[GitHub Actions CI/CD]

        KV -.->|Secrets| Producer
        KV -.->|Secrets| Streamlit
        KV -.->|Secrets| Endpoint
        GitHub -.->|Build & Deploy App| Streamlit
        GitHub -.->|ML Pipeline| Train
    end
