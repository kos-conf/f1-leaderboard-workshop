# F1 Real-Time Analytics Lab

## Workshop: Building a Live F1 Leaderboard with Kafka and Flink

This hands-on lab will guide you through building a real-time F1 racing leaderboard application using Apache Kafka, Confluent Cloud, Apache Flink SQL, and React. You'll learn how to implement real-time data streaming, performance analytics, and live dashboards.

### 🎯 Learning Objectives

By the end of this lab, you will:
- Set up a complete real-time data streaming pipeline
- Implement Kafka producers and consumers with schema validation
- Use Apache Flink SQL for real-time analytics
- Build a React frontend with live data updates
- Understand event-driven architecture patterns
- Work with Confluent Cloud and Schema Registry

### 🏁 What You'll Build

A fully functional F1 leaderboard application featuring:
- **Real-time race simulation** with live position updates
- **Performance analytics** with average speed tracking
- **Interactive driver selection** and race management
- **Live dashboard** with Server-Sent Events
- **Data streaming pipeline** using Kafka and Flink SQL

> **Duration:** 2-3 hours | **Level:** Intermediate | **Prerequisites:** Basic Python, JavaScript, and cloud concepts

![](images/architecture.gif)

## 📋 Lab Outline

### Part 1: Environment Setup (30 minutes)
- [Lab Prerequisites](#prerequisites)
- [Confluent Cloud Setup](#confluent-cloud-setup)
- [Local Development Environment](#local-development-environment)

### Part 2: Backend Development (45 minutes)
- [Kafka Producer Implementation](#kafka-producer-implementation)
- [Kafka Consumer Implementation](#kafka-consumer-implementation)
- [FastAPI Backend Setup](#fastapi-backend-setup)

### Part 3: Real-Time Analytics (30 minutes)
- [Flink SQL Setup](#flink-sql-setup)
- [Performance Analytics Implementation](#performance-analytics-implementation)

### Part 4: Frontend Development (30 minutes)
- [React Frontend Setup](#react-frontend-setup)
- [Real-Time Data Integration](#real-time-data-integration)

### Part 5: Testing & Validation (15 minutes)
- [End-to-End Testing](#end-to-end-testing)
- [Performance Verification](#performance-verification)

### Part 6: Cleanup (5 minutes)
- [Resource Cleanup](#resource-cleanup)

## 🏗️ Architecture Overview

![](images/architecture.gif)

### System Components

1. **Data Generation Layer**
   - Python-based race simulation
   - Realistic F1 position changes
   - Speed and performance metrics

2. **Streaming Layer**
   - Apache Kafka (Confluent Cloud)
   - Schema Registry for data validation
   - Real-time message streaming

3. **Analytics Layer**
   - Apache Flink SQL
   - Real-time average speed calculations
   - Performance metrics aggregation

4. **Application Layer**
   - FastAPI backend
   - React frontend
   - Server-Sent Events for live updates

5. **Data Flow**
   - Position data → Kafka → Flink SQL → Analytics
   - Live updates → Backend → Frontend via SSE

## 🛠️ Technology Stack

| Component | Technology | Purpose |
|-----------|------------|----------|
| **Backend** | Python 3.11, FastAPI, Uvicorn | API server and business logic |
| **Data Streaming** | Confluent Cloud, Kafka, Schema Registry | Real-time message streaming |
| **Analytics** | Apache Flink SQL | Real-time data processing |
| **Frontend** | React 19.1.1, Vite, JavaScript | User interface and live updates |
| **Data Validation** | Avro, Pydantic | Schema validation and serialization |
| **Real-time Updates** | Server-Sent Events | Live data streaming to frontend |

## Prerequisites

### Required Software

Before starting this lab, ensure you have the following installed on your machine:

| Software | Version | Download Link | Verification Command |
|----------|---------|---------------|---------------------|
| **Node.js** | 18+ | [Download](https://nodejs.org/) | `node --version` |
| **npm** | 9+ | (comes with Node.js) | `npm --version` |
| **Python** | 3.11+ | [Download](https://www.python.org/downloads/) | `python3 --version` |
| **Git** | Latest | [Download](https://git-scm.com/) | `git --version` |

### Required Accounts

- **Confluent Cloud Account** (Free tier available)
  - Sign up at: [https://www.confluent.io/confluent-cloud/tryfree](https://www.confluent.io/confluent-cloud/tryfree/)
  - No credit card required for basic cluster

### System Requirements

- **Operating System:** Windows 10+, macOS 10.15+, or Linux
- **RAM:** Minimum 8GB (16GB recommended)
- **Storage:** At least 2GB free space
- **Internet:** Stable connection for cloud services

### Pre-Lab Verification

Run these commands to verify your environment:

```bash
# Check Node.js and npm
node --version && npm --version

# Check Python
python3 --version

# Check Git
git --version

# Verify you can create virtual environments
python3 -m venv test_env && rm -rf test_env
```

✅ **All commands should complete without errors before proceeding.**

## Part 1: Environment Setup

### Step 1.1: Clone the Repository

**Objective:** Get the lab code and set up the project structure

1. **Open your terminal/command prompt**
2. **Navigate to your desired directory** (e.g., `~/workshops` or `C:\workshops`)
3. **Clone the repository:**
   ```bash
   git clone <repository-url>
   cd f1-flink-version
   ```

**✅ Verification:** You should see the following directory structure:
```
├── backend/
├── frontend/
├── images/
└── README.md
```

### Step 1.2: Set Up Backend Environment

**Objective:** Create a Python virtual environment and install dependencies

1. **Navigate to the backend directory:**
   ```bash
   cd backend
   ```

2. **Create a Python virtual environment:**
   ```bash
   python3 -m venv venv
   ```

3. **Activate the virtual environment:**
   ```bash
   # On macOS/Linux:
   source venv/bin/activate
   
   # On Windows:
   venv\Scripts\activate
   ```

4. **Install Python dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

5. **Return to project root:**
   ```bash
   cd ..
   ```

**✅ Verification:** Run this command to verify the backend setup:
```bash
cd backend
source venv/bin/activate  # or venv\Scripts\activate on Windows
python -c "import fastapi, confluent_kafka; print('Backend dependencies installed successfully!')"
cd ..
```

### Step 1.3: Set Up Frontend Environment

**Objective:** Install Node.js dependencies for the React frontend

1. **Navigate to the frontend directory:**
   ```bash
   cd frontend
   ```

2. **Install Node.js dependencies:**
   ```bash
   npm install
   ```

3. **Return to project root:**
   ```bash
   cd ..
   ```

**✅ Verification:** Run this command to verify the frontend setup:
```bash
cd frontend
npm list react
cd ..
```

You should see React and other dependencies listed without errors.

## Part 2: Confluent Cloud Setup

### Step 2.1: Create Confluent Cloud Account

**Objective:** Set up your Confluent Cloud environment for Kafka streaming

1. **Sign up for Confluent Cloud:**
   - Go to [https://www.confluent.io/confluent-cloud/tryfree](https://www.confluent.io/confluent-cloud/tryfree/)
   - Click "Start free" and create your account
   - No credit card required for the basic cluster

2. **Verify your account:**
   - Check your email and verify your account
   - Log in to the Confluent Cloud console

**✅ Verification:** You should be able to access the Confluent Cloud dashboard at [https://confluent.cloud](https://confluent.cloud)

### Step 2.2: Create Environment and Cluster

**Objective:** Set up the basic infrastructure for your lab

1. **Create an Environment:**
   - In the Confluent Cloud console, click "Create environment"
   - Name it: `f1-lab-environment`
   - Click "Create"

2. **Create a Basic Cluster:**
   - Click "Create cluster"
   - Select "Basic" cluster type
   - Choose your preferred cloud provider and region
   - Name it: `f1-lab-cluster`
   - Click "Create cluster"
   - Wait for the cluster to be ready (2-3 minutes)

**✅ Verification:** You should see your cluster status as "Available" in the Confluent Cloud console

### Step 2.3: Create Kafka API Key

**Objective:** Generate credentials to connect your application to Kafka

1. **Navigate to API Keys:**
   - In the left sidebar, click "API Keys"
   - Click "Add API Key"

2. **Configure the API Key:**
   - Select "User Account" and click "Next"
   - Select your cluster: `f1-lab-cluster`
   - Click "Next"
   - Name: `f1-lab-kafka-key`
   - Description: `API key for F1 lab application`
   - Click "Next"

3. **Download the API Key:**
   - **IMPORTANT:** Click "Download API Key" and save the file
   - The key will not be shown again
   - Click "Complete"

**✅ Verification:** You should have a downloaded file with your API key and secret

### Step 2.4: Create Schema Registry API Key

**Objective:** Set up Schema Registry for data validation

1. **Navigate to Schema Registry:**
   - In the left sidebar, click "Schema Registry"
   - Click "Enable Schema Registry"
   - Choose the same region as your cluster
   - Click "Continue"

2. **Create Schema Registry API Key:**
   - Click "Add API Key"
   - Name: `f1-lab-schema-key`
   - Description: `Schema Registry key for F1 lab`
   - Click "Download API Key" and save the file
   - Click "Complete"

**✅ Verification:** You should have two API key files downloaded

### Step 2.5: Create Kafka Topics

**Objective:** Set up the topics for your data streams

1. **Navigate to Topics:**
   - In the left sidebar, click "Topics"
   - Click "Create topic"

2. **Create Position Topic:**
   - Topic name: `f1-driver-positions`
   - Partitions: 3
   - Click "Create with defaults"

3. **Create Average Speed Topic:**
   - Click "Create topic" again
   - Topic name: `driver-avg-speed`
   - Partitions: 3
   - Click "Create with defaults"

**✅ Verification:** You should see both topics in your topics list

### Step 2.6: Configure Application

**Objective:** Update the application configuration with your Confluent Cloud credentials

1. **Open the configuration file:**
   ```bash
   code backend/config.yaml  # or use your preferred editor
   ```

2. **Update the configuration:**
   Replace the placeholder values with your actual credentials:
   ```yaml
   kafka:
     bootstrap.servers: '<YOUR_CONFLUENT_CLOUD_CLUSTER_URL>'  # From your cluster details
     security.protocol: "SASL_SSL"
     sasl.mechanism: "PLAIN"
     sasl.username: '<YOUR_CONFLUENT_CLOUD_API_KEY>'  # From your Kafka API key file
     sasl.password: '<YOUR_CONFLUENT_CLOUD_API_SECRET>'  # From your Kafka API key file
     schema_registry_url: '<YOUR_SCHEMA_REGISTRY_URL>'  # From Schema Registry details
     schema_registry_api_key: '<YOUR_SCHEMA_REGISTRY_API_KEY>'  # From your Schema Registry API key file
     schema_registry_secret: '<YOUR_SCHEMA_REGISTRY_SECRET>'  # From your Schema Registry API key file
     topics:
       positions: "f1-driver-positions"
       driver_avg_speed: "driver-avg-speed"
     consumer_group: "f1-leaderboard-consumer"
   ```

**✅ Verification:** Your `config.yaml` should contain real values (not placeholders)

## Part 3: Running the Application

### Step 3.1: Start the Backend Server

**Objective:** Launch the FastAPI backend with Kafka integration

1. **Open a new terminal window**
2. **Navigate to the backend directory:**
   ```bash
   cd backend
   ```

3. **Activate the virtual environment:**
   ```bash
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

4. **Start the backend server:**
   ```bash
   python main.py
   ```

**✅ Verification:** You should see:
```
Starting F1 Leaderboard API...
INFO:     Started server process [12345]
INFO:     Uvicorn running on http://0.0.0.0:8001
```

### Step 3.2: Start the Frontend Application

**Objective:** Launch the React frontend for the user interface

1. **Open another new terminal window**
2. **Navigate to the frontend directory:**
   ```bash
   cd frontend
   ```

3. **Start the development server:**
   ```bash
   npm run dev
   ```

**✅ Verification:** You should see:
```
  VITE v5.0.0  ready in 500 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
```

### Step 3.3: Access the Application

**Objective:** Open the F1 leaderboard in your browser

1. **Open your web browser**
2. **Navigate to:** `http://localhost:5173`
3. **You should see the F1 Leaderboard application**

**✅ Verification:** The application loads without errors and shows the driver selection screen

## Part 4: Hands-On Lab Exercises

### Exercise 1: Test the Application Flow

**Objective:** Verify the complete application functionality

1. **Select a Driver:**
   - Choose any F1 driver from the selection screen
   - Watch the race starting animation
   - Observe the countdown sequence

2. **Monitor Live Updates:**
   - Watch the leaderboard update in real-time
   - Notice how positions change dynamically
   - Check the progress bar and timer

3. **View Performance Analytics:**
   - Observe the average speed panel
   - Notice how speeds update continuously
   - See your selected driver highlighted

4. **Test Race Management:**
   - Try stopping the race early
   - Start a new race with a different driver
   - Let a race complete naturally

**✅ Success Criteria:** All features work smoothly without errors

### Exercise 2: Explore the Data Flow

**Objective:** Understand how data flows through the system

1. **Check Kafka Topics:**
   - Go to your Confluent Cloud console
   - Navigate to Topics
   - Click on `f1-driver-positions`
   - View the message flow in real-time

2. **Monitor Schema Registry:**
   - Check the Schema Registry in Confluent Cloud
   - View the registered schemas
   - Understand the data structure

3. **Examine API Endpoints:**
   - Open `http://localhost:8001/api/health`
   - Test `http://localhost:8001/api/drivers`
   - Check the race status endpoint

**✅ Success Criteria:** You can see data flowing through all components

### Exercise 3: Implement Flink SQL Analytics

**Objective:** Set up real-time analytics with Flink SQL

1. **Access Flink SQL:**
   - In Confluent Cloud, navigate to Flink
   - Create a new Flink workspace
   - Open the SQL editor

2. **Create the Average Speed Table:**
   ```sql
   CREATE TABLE driver_avg_speed (
     driver_name STRING,
     race_id STRING,
     avg_speed DOUBLE,
     PRIMARY KEY (driver_name, race_id) NOT ENFORCED
   ) WITH (
     'changelog.mode' = 'upsert',
     'value.format' = 'json-registry'
   );
   ```

3. **Insert Average Speed Data:**
   ```sql
   INSERT INTO driver_avg_speed
   SELECT
     driver_name,
     race_id,
     AVG(speed) AS avg_speed
   FROM `f1-driver-positions`
   GROUP BY driver_name, race_id;
   ```

4. **Monitor the Results:**
   - Check the `driver-avg-speed` topic
   - Verify data is being written
   - Observe the real-time calculations

**✅ Success Criteria:** Flink SQL processes data and writes to the output topic

## Troubleshooting

### Common Issues and Solutions

#### Backend Won't Start

**Problem:** `python main.py` fails with import errors

**Solutions:**
1. **Check virtual environment:**
   ```bash
   source venv/bin/activate  # or venv\Scripts\activate on Windows
   which python  # Should point to venv/bin/python
   ```

2. **Reinstall dependencies:**
   ```bash
   pip install -r requirements.txt --force-reinstall
   ```

3. **Check Python version:**
   ```bash
   python --version  # Should be 3.11+
   ```

#### Frontend Won't Start

**Problem:** `npm run dev` fails

**Solutions:**
1. **Clear npm cache:**
   ```bash
   npm cache clean --force
   ```

2. **Delete node_modules and reinstall:**
   ```bash
   rm -rf node_modules package-lock.json
   npm install
   ```

3. **Check Node.js version:**
   ```bash
   node --version  # Should be 18+
   ```

#### Kafka Connection Issues

**Problem:** Backend can't connect to Confluent Cloud

**Solutions:**
1. **Verify config.yaml:**
   - Check all credentials are correct
   - Ensure no extra spaces or quotes
   - Verify topic names match exactly

2. **Test connection:**
   ```bash
   # In backend directory with venv activated
   python -c "from confluent_kafka import Producer; print('Kafka connection OK')"
   ```

3. **Check Confluent Cloud:**
   - Verify cluster is running
   - Check API key permissions
   - Ensure topics exist

#### No Data in Frontend

**Problem:** Application loads but no live updates

**Solutions:**
1. **Check backend logs:**
   - Look for Kafka consumer errors
   - Verify position generation is working

2. **Test API endpoints:**
   ```bash
   curl http://localhost:8001/api/health
   curl http://localhost:8001/api/drivers
   ```

3. **Check browser console:**
   - Open Developer Tools (F12)
   - Look for JavaScript errors
   - Check Network tab for failed requests

#### Flink SQL Issues

**Problem:** Flink SQL queries don't work

**Solutions:**
1. **Check topic names:**
   - Ensure topic names match exactly
   - Verify topics exist in Confluent Cloud

2. **Verify schemas:**
   - Check Schema Registry has the required schemas
   - Ensure data format is correct

3. **Test with simple query:**
   ```sql
   SELECT * FROM `f1-driver-positions` LIMIT 10;
   ```

### Getting Help

If you encounter issues not covered here:

1. **Check the logs:**
   - Backend: Look at terminal output
   - Frontend: Check browser console
   - Confluent Cloud: Check cluster and topic metrics

2. **Verify prerequisites:**
   - All software versions are correct
   - Confluent Cloud account is active
   - Internet connection is stable

3. **Restart components:**
   - Stop all processes (Ctrl+C)
   - Restart backend and frontend
   - Check Confluent Cloud status

## Part 5: Lab Summary & Next Steps

### What You've Accomplished

Congratulations! You've successfully built a complete real-time data streaming application with:

✅ **Real-time Data Pipeline:** Kafka producers and consumers with schema validation  
✅ **Live Analytics:** Flink SQL for real-time performance calculations  
✅ **Modern Frontend:** React application with live updates via Server-Sent Events  
✅ **Cloud Integration:** Confluent Cloud for managed Kafka and Schema Registry  
✅ **Race Simulation:** Realistic F1 race dynamics with position changes  

### Key Learning Outcomes

- **Event-Driven Architecture:** Understanding how real-time data flows through systems
- **Kafka Integration:** Working with producers, consumers, and schema validation
- **Stream Processing:** Using Flink SQL for real-time analytics
- **Modern Web Development:** Building responsive UIs with real-time updates
- **Cloud Services:** Leveraging managed services for scalable infrastructure

### Lab Results
![](images/finished.png)

### Next Steps & Extensions

#### Beginner Extensions
1. **Add More Analytics:**
   - Calculate fastest lap times
   - Track position changes per driver
   - Add lap-by-lap analysis

2. **Enhance the UI:**
   - Add driver statistics charts
   - Implement race history
   - Create a mobile-responsive design

3. **Improve Data Quality:**
   - Add data validation rules
   - Implement error handling
   - Add data quality metrics

#### Intermediate Extensions
1. **Advanced Flink SQL:**
   - Window functions for time-based analytics
   - Complex event processing
   - Machine learning integration

2. **Microservices Architecture:**
   - Split backend into multiple services
   - Add API gateway
   - Implement service discovery

3. **Data Persistence:**
   - Store race results in a database
   - Add historical data analysis
   - Implement data archiving

#### Advanced Extensions
1. **Real-time ML:**
   - Predict race outcomes
   - Anomaly detection
   - Driver performance scoring

2. **Scalability:**
   - Horizontal scaling
   - Load balancing
   - Auto-scaling based on load

3. **Monitoring & Observability:**
   - Application metrics
   - Distributed tracing
   - Alerting and notifications

## Cleanup

### Step 1: Stop the Application

1. **Stop the backend:**
   - In the backend terminal, press `Ctrl+C`
   - Wait for graceful shutdown

2. **Stop the frontend:**
   - In the frontend terminal, press `Ctrl+C`
   - Close the terminal windows

### Step 2: Clean Up Confluent Cloud

1. **Delete Topics:**
   - Go to Confluent Cloud console
   - Navigate to Topics
   - Delete `f1-driver-positions`
   - Delete `driver-avg-speed`

2. **Delete Cluster:**
   - Go to your cluster settings
   - Click "Delete cluster"
   - Confirm deletion

3. **Delete Environment (Optional):**
   - If you don't need the environment anymore
   - Delete the entire environment

### Step 3: Local Cleanup

1. **Remove Virtual Environment:**
   ```bash
   rm -rf backend/venv
   ```

2. **Clean Node Modules (Optional):**
   ```bash
   rm -rf frontend/node_modules
   ```

## Resources & Further Learning

### Documentation
- [Confluent Cloud Documentation](https://docs.confluent.io/cloud/current/)
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)
- [Apache Flink Documentation](https://flink.apache.org/docs/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [React Documentation](https://react.dev/)

### Community
- [Confluent Community](https://www.confluent.io/community/)
- [Apache Kafka Community](https://kafka.apache.org/community)
- [Flink Community](https://flink.apache.org/community.html)

### Additional Labs
- [Confluent Developer](https://developer.confluent.io/)
- [Kafka Streams Tutorials](https://kafka.apache.org/documentation/streams/)
- [Flink Training](https://flink.apache.org/training/)

---

**🎉 Lab Complete!** You've successfully built a real-time F1 analytics application. Keep exploring and building amazing data streaming applications!
