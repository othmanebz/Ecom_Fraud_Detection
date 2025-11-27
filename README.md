# Ecom_Fraud_Detection

graph TD
    %% Source
    subgraph "1. Ingestion & Streaming"
    CSV[(Fraud Dataset CSV)] -->|Python Script| Producer[Python Event Producer]
    Producer -->|JSON Stream| EventHub[Azure Event Hubs]
    end

    %% Storage & Processing
    subgraph "2. Storage & Processing"
    EventHub -->|Path A: Hot| ASA[Azure Stream Analytics]
    EventHub -->|Path B: Cold| Blob[Azure Data Lake / Blob Storage]
    
    ASA -->|Suspicious Txns| SQL[(Azure SQL Database)]
    Blob -->|History Data| AML[Azure Machine Learning]
    end

    %% Machine Learning
    subgraph "3. AI & Machine Learning"
    AML -->|Read Data| Train[Training Notebook]
    Train -->|SMOTE| Balancing[Data Balancing]
    Balancing -->|Train Model| Model[Random Forest / XGBoost]
    Model -->|Track Metrics| MLflow[MLflow Registry]
    MLflow -->|Deploy| Endpoint[Real-Time Inference API]
    end

    %% Usage
    subgraph "4. Consumption & Visualization"
    Endpoint -->|API Response| Streamlit[Streamlit Analyst App]
    SQL -->|Direct Query| PowerBI[Power BI Dashboard]
    Streamlit -->|Manual Check| SQL
    end

    %% Governance
    subgraph "Support"
    KV[Azure Key Vault] -.->|Secrets| Producer
    KV -.->|Secrets| Streamlit
    GitHub[GitHub Actions] -.->|CI/CD| Streamlit
    end
