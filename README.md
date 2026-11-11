<p align="center">
  <img src="assets/chronos_banner.svg" alt="Chronos Surveillance OS Banner" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/ARCHITECTURE-MICROSERVICES-00ff80?style=for-the-badge&logoColor=black" alt="Microservices">
  <img src="https://img.shields.io/badge/PYTHON-3.10%2B-00e5ff?style=for-the-badge&logoColor=black" alt="Python Version">
  <img src="https://img.shields.io/badge/PACKAGE_MANAGER-UV-00ff80?style=for-the-badge&logoColor=black" alt="uv Package Manager">
  <img src="https://img.shields.io/badge/STREAMING_BUS-REDPANDA%20%2F%20KAFKA-ff3344?style=for-the-badge&logoColor=white" alt="Message Bus">
  <img src="https://img.shields.io/badge/VECTOR_ENGINE-QDRANT-00e5ff?style=for-the-badge&logoColor=black" alt="Qdrant">
  <img src="https://img.shields.io/badge/INTELLIGENCE-LOCAL%20GEMMA--4%20LLM-00ff80?style=for-the-badge&logoColor=black" alt="Gemma-4 LLM">
  <img src="https://img.shields.io/badge/CLOUD_DEPENDENCY-ZERO%20(100%25%20OPEN%20SOURCE)-111827?style=for-the-badge" alt="Zero Cloud">
</p>

<p align="center">
  <b>[ <a href="#overview">Overview</a> ]</b> &bull;
  <b>[ <a href="#architecture">Architecture</a> ]</b> &bull;
  <b>[ <a href="#project-tree">Project Tree</a> ]</b> &bull;
  <b>[ <a href="#workflow-diagrams">Workflow Diagrams</a> ]</b> &bull;
  <b>[ <a href="#microservices-ecosystem">Microservices</a> ]</b> &bull;
  <b>[ <a href="#step-by-step-setup-guide">Step-by-Step Setup</a> ]</b> &bull;
  <b>[ <a href="#camera-feeds-integration">Camera Feeds</a> ]</b> &bull;
  <b>[ <a href="#local-llm--ai-agents">Local LLM &amp; Agents</a> ]</b> &bull;
  <b>[ <a href="#documentation-index">Documentation Index</a> ]</b> &bull;
  <b>[ <a href="#troubleshooting">Troubleshooting</a> ]</b>
</p>

---

## Overview

**Chronos Surveillance OS** is an autonomous, cyber-tactical smart city surveillance and spatial threat reconnaissance platform. Engineered to run entirely without proprietary cloud dependencies, Chronos replaces monolithic, vendor-locked architectures with an event-driven, distributed microservice mesh powered by modern open-source technologies.

The platform continuously monitors heterogeneous video feeds across entire municipal districts—ranging from high-end RTSP/ONVIF traffic cameras and aerial drone telemetry to everyday mobile smartphone cameras and laptop webcams. Frames are ingested into an ultra-low-latency streaming bus (Redpanda / Apache Kafka), where real-time spatial-temporal vision models detect anomalies, biometric engines reconstruct 468-point 3D facial morphometry meshes, and local Large Language Models (LLMs) synthesize tactical situation reports with Human-in-the-Loop (HITL) oversight.

### Core Architectural Pillars

- **Zero-Cloud & Zero-Vendor-Lockin**: Designed from the ground up to operate in air-gapped municipal data centers, private clouds, or local operator workstations with zero external API fees.
- **Microservices Managed by uv**: Every subsystem is a fully isolated microservice containing its own `pyproject.toml`, lockfiles, documentation, and independent test harness managed via the `uv` toolchain.
- **Universal Multi-Camera Ingestion**: Ingests feeds from RTSP surveillance cameras, USB webcams, HTTP/MJPEG smartphone streams (IP Webcam, DroidCam), and continuous video test loops.
- **Spatial-Temporal Threat Radar**: Detects unattended assets, sudden loitering, crowd surges, stampedes, and restricted perimeter violations using dense optical flow and background subtraction.
- **3D Biometric Morphometry (.OBJ Export)**: Extracts 468 3D facial coordinates via MediaPipe, persists Wavefront `.OBJ` point-clouds, and matches identities against vector feature stores without emitting null values.
- **Agentic Threat Ontology & Local LLM**: Employs an in-memory knowledge graph coupled with local OpenAI-compatible inference servers (such as Gemma-4 at `http://10.210.8.63:8080/v1`) to generate natural language tactical action directives.
- **Cyber-Tactical Mission Control HUD**: Military-grade dark-mode command console built with real-time target reticles, 3D facial mesh inspections, verified OSINT badge overlays, and Human-in-the-Loop (HITL) authorization queues.

---

## Architecture

Chronos decouples data ingestion, machine intelligence, vector search, graph reasoning, and operator command into distinct, horizontally scalable microservice pods connected via a high-throughput message backbone.

### Text-Based System Topology

```
+---------------------------------------------------------------------------------------------------+
|                                  PHYSICAL & IP SENSOR LAYER                                       |
|  +-----------------------+   +-----------------------+   +-------------------------------------+  |
|  | Enterprise CCTV / RTSP|   | Smartphones / DroidCam|   | Laptop Webcams / Aerial Drone Feeds |  |
|  +-----------+-----------+   +-----------+-----------+   +------------------+------------------+  |
+--------------|---------------------------|----------------------------------|---------------------+
               |                           |                                  |
               +---------------------------+----------------------------------+
                                           |
                                           v
+---------------------------------------------------------------------------------------------------+
|                         INGESTION GATEWAY MICROSERVICE (Port 8001)                                |
|   - OpenCV Stream Normalizer    - Dynamic Device Registry API    - MJPEG Ring Buffer Distributor  |
+------------------------------------------+--------------------------------------------------------+
                                           | Raw Frame Frames / Telemetry (Topic: chronos.video)
                                           v
+---------------------------------------------------------------------------------------------------+
|                         DISTRIBUTED STREAMING BUS (Redpanda / Kafka :9092)                        |
|   - Zero-JVM C++ Broker         - Sub-millisecond Latency        - Redpanda Web Console (:8088)   |
+-------------------+---------------------------------------------------+---------------------------+
                    |                                                   |
                    v                                                   v
+---------------------------------------+   +-------------------------------------------------------+
|  ANOMALY DETECTOR POD (Port 8004)     |   |  BIOMETRIC RECONNAISSANCE POD (Port 8002)             |
|  - MOG2 Foreground Accumulator        |   |  - MediaPipe 468-point 3D Landmark Extractor          |
|  - Farneback Dense Optical Flow       |   |  - Wavefront .OBJ 3D Mesh Generator                   |
|  - Stampede / Unattended Asset Scorer |   |  - 512D Vector Embedding Identity Matcher             |
+-------------------+-------------------+   +---------------------------+---------------------------+
                    |                                                   |
                    +---------------------------+-----------------------+
                                                |
                                                v
+---------------------------------------------------------------------------------------------------+
|                         AGENTIC THREAT ONTOLOGY POD (Port 8003)                                   |
|  - Spatial Knowledge Graph: (Camera) -> (Sector) -> (Citizen) -> (Threat Incident)                |
|  - Local Gemma-4 LLM Gateway (10.210.8.63:8080/v1) for Autonomous Situation Directives            |
+-----------------------------------------------+---------------------------------------------------+
                                                |
                                                v
+---------------------------------------------------------------------------------------------------+
|                         TACTICAL MISSION CONTROL HUD (Port 8501)                                  |
|  - Live Targeting Reticle & Metadata HUD      - 3D Facial Point-Cloud Wireframe Inspector         |
|  - Non-Null OSINT Verification Badge Card     - Human-in-the-Loop (HITL) Clearance Terminal       |
+---------------------------------------------------------------------------------------------------+
```

---

## Project Tree

The repository adheres to a modular microservice layout. No shared monolithic directories exist; each service owns its dependencies, execution entrypoints, and documentation.

<details>
<summary><b>Click to expand full repository directory tree</b></summary>

```
AWS-Smart-City-Surveillance/
|
|-- .env                                  # Active environment configuration with local LLM and port bindings
|-- .env.example                          # Blueprint environment file with full variable documentation
|-- .gitignore                            # Excludes caches, temporary models, virtual environments, and weights
|-- docker-compose.yml                    # Multi-container orchestration (Redpanda, Qdrant, MinIO, UI, Gateways)
|-- LICENSE.md                            # Open source software license terms
|-- README.md                             # Master comprehensive repository documentation
|
|-- assets/                               # Visual assets, vector graphics, and system branding
|   |-- chronos_banner.svg                # Master tactical HUD banner with system telemetry
|   `-- chronos_icon.svg                  # High-tech targeting reticle vector icon
|
|-- data/                                 # Local storage mount points for assets and profiles
|   |-- 3d_profiles/                      # Persisted 468-point Wavefront .OBJ 3D facial coordinate meshes
|   |-- sample_faces/                     # Verification test images and enrollment photos
|   `-- sample_videos/                    # Sample CCTV and municipal test video footage
|
|-- docs/                                 # Centralized technical guides and architectural specifications
|   |-- architecture/
|   |   `-- system_overview.md            # In-depth architectural decomposition and communication protocols
|   `-- guides/
|       |-- camera_integration_guide.md   # Step-by-step connection guide for CCTV, laptops, and smartphones
|       `-- datasets_and_training.md      # Public surveillance datasets and 3D facial reconstruction guide
|
`-- services/                             # Independent, scalable microservice pods
    |
    |-- ingestion-gateway/                # Universal video stream acquisition and normalization service
    |   |-- docs/
    |   |   `-- guide.md                  # Comprehensive developer and operator manual for ingestion gateway
    |   |-- main.py                       # FastAPI application handling RTSP, USB, phone feeds, and MJPEG loops
    |   |-- pyproject.toml                # Service metadata and dependencies managed by uv
    |   `-- README.md                     # Microservice quickstart and technical overview
    |
    |-- anomaly-detector/                 # Spatial-temporal threat radar microservice
    |   |-- docs/
    |   |   `-- guide.md                  # Algorithmic guide detailing optical flow and threat scoring logic
    |   |-- main.py                       # FastAPI service running Farneback optical flow and MOG2 subtraction
    |   |-- pyproject.toml                # Python dependencies (Torch, OpenCV, NumPy) managed by uv
    |   `-- README.md                     # Detection thresholds and evaluation documentation
    |
    |-- biometric-recon/                  # 3D facial morphometry and identity intelligence microservice
    |   |-- docs/
    |   |   `-- guide.md                  # 3D landmark mesh construction and HITL queue operator manual
    |   |-- main.py                       # MediaPipe 3D face mesh extractor, .OBJ exporter, and vector lookup
    |   |-- pyproject.toml                # Dependencies (MediaPipe, ChromaDB, Qdrant-Client) managed by uv
    |   `-- README.md                     # Biometric scanning and identity verification reference
    |
    |-- agentic-ontology/                 # Multi-agent threat reasoning and knowledge graph service
    |   |-- docs/
    |   |   `-- guide.md                  # Graph schema, entity ontology, and local LLM integration guide
    |   |-- main.py                       # NetworkX knowledge graph and Gemma-4 LLM tactical directive engine
    |   |-- pyproject.toml                # Dependencies (OpenAI SDK, NetworkX, HTTPX) managed by uv
    |   `-- README.md                     # Agentic loop and incident assessment reference
    |
    `-- mission-control-ui/               # Tactical command dashboard and operator HUD
        |-- assets/
        |   `-- chronos_icon.svg          # Streamlit UI tactical icon
        |-- docs/
        |   `-- guide.md                  # Operator guide for HUD controls, 3D viewer, and HITL authorization
        |-- app.py                        # Streamlit HUD with real-time video, target cards, and diagnostics
        |-- pyproject.toml                # Frontend dependencies (Streamlit, Pillow, OpenCV) managed by uv
        `-- README.md                     # UI features, configuration, and execution instructions
```
</details>

---

## Workflow Diagrams

### 1. Master System Data Flow

<details>
<summary><b>Click to expand Master System Data Flow Diagram</b></summary>

```mermaid
flowchart TD
    subgraph Data Acquisition
        CCTV[RTSP / ONVIF Fixed Cameras]
        Phone[Mobile Smartphone IP Streams]
        Webcam[Laptop / USB Integrated Webcams]
        Drone[Aerial Drone Telemetry]
        
        CCTV & Phone & Webcam & Drone --> Gateway[Ingestion Gateway :8001]
    end

    subgraph Streaming Bus
        Gateway -->|Raw Normalized Frames| MessageBus[(Redpanda / Kafka :9092)]
    end

    subgraph Machine Intelligence
        MessageBus -->|Frame Dispatch| Anomaly[Anomaly Detector :8004]
        MessageBus -->|Frame Dispatch| Biometrics[Biometric Recon :8002]
        
        Anomaly -->|Threat Scores & Contours| Ontology[Agentic Ontology :8003]
        Biometrics -->|3D Landmarks & Identity Embeddings| Ontology
        
        LocalLLM[Local Gemma-4 LLM Engine\n10.210.8.63:8080/v1] <-->|Context Prompts & Directives| Ontology
    end

    subgraph Persistence Layer
        Biometrics -->|3D Wavefront .OBJ Files| FileStorage[(data/3d_profiles / MinIO)]
        Biometrics -->|512D Embeddings| VectorDB[(Qdrant / ChromaDB)]
        Ontology -->|Graph Associations| GraphDB[(NetworkX / Neo4j)]
    end

    subgraph Command Center
        Gateway & Anomaly & Biometrics & Ontology --> HUD[Mission Control UI :8501]
        HUD -->|Human Operator Approvals| Biometrics
    end
```
</details>

### 2. Video Ingestion to Intelligence Sequence

<details>
<summary><b>Click to expand Ingestion to Intelligence Sequence Diagram</b></summary>

```mermaid
sequenceDiagram
    autonumber
    participant Cam as Physical Camera Feed
    participant Ingest as Ingestion Gateway
    participant Bus as Redpanda Bus
    participant Anom as Anomaly Detector
    participant Bio as Biometric Recon
    participant Agent as Agentic Ontology
    participant LLM as Local Gemma-4 Server
    participant HUD as Mission Control HUD

    Cam->>Ingest: Stream H.264 / MJPEG Bytes
    Ingest->>Ingest: Normalize Frame (640x360 @ 15fps)
    Ingest->>Bus: Publish to chronos.video.raw-frames
    
    par Spatial-Temporal Scan
        Bus->>Anom: Consume Frame
        Anom->>Anom: Compute Optical Flow & MOG2 Mask
        Anom->>Agent: Alert: Elevated Kinetic Energy
    and Biometric Reconstruction
        Bus->>Bio: Consume Frame
        Bio->>Bio: Detect Faces & 468 3D Landmarks
        Bio->>Bio: Export Wavefront .OBJ Mesh
        Bio->>Agent: Subject Detected: SUBJECT_KHR_001
    end

    Agent->>Agent: Link Entities in Graph Ontology
    Agent->>LLM: Generate Tactical Situation Directive
    LLM-->>Agent: "Subject verified. Maintain passive perimeter patrol."
    Agent->>HUD: Push Telemetry & Incident Assessment
    HUD->>HUD: Render Targeting Reticle & 3D Point-Cloud
```
</details>

### 3. Biometric 3D Morphometry & HITL State Machine

<details>
<summary><b>Click to expand 3D Morphometry & HITL State Machine Diagram</b></summary>

```mermaid
stateDiagram-v2
    [*] --> FrameAcquisition: Camera Frame Ingested
    FrameAcquisition --> FaceDetection: MediaPipe Pipeline
    
    state FaceDetection {
        [*] --> CheckBoundingBox
        CheckBoundingBox --> ExtractLandmarks: Face Present
        CheckBoundingBox --> NoFaceDetected: No Subject
    }
    
    NoFaceDetected --> [*]: Next Frame
    
    state ExtractLandmarks {
        [*] --> Compute3DCoordinates: 468 Cartesian Vertices
        Compute3DCoordinates --> GenerateOBJ: Write Wavefront File
        GenerateOBJ --> VectorLookup: Compute 512D Embedding
    }
    
    VectorLookup --> MatchDecision: Compare Similarity Score
    
    state MatchDecision {
        [*] --> CheckThreshold
        CheckThreshold --> VerifiedSubject: Similarity >= 0.95
        CheckThreshold --> EnqueueHITL: Similarity < 0.95
    }
    
    VerifiedSubject --> HUDDisplay: Render Non-Null Verified Card
    
    state EnqueueHITL {
        [*] --> PendingReview: Add to Operator Queue
        PendingReview --> OperatorApprove: Approve Identity
        PendingReview --> OperatorFlag: Flag Threat
        PendingReview --> RequestHighRes: Request High-Res Scan
    }
    
    OperatorApprove --> HUDDisplay: Clearance Granted
    OperatorFlag --> AlertCenter: Broadcast Tactical Alarm
    RequestHighRes --> FrameAcquisition: Adjust Camera PTZ
```
</details>

---

## Microservices Ecosystem

| Microservice | Location | Default Port | Core Functionality | Primary Tech Stack | Documentation Link |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Ingestion Gateway** | `services/ingestion-gateway` | `8001` | Multi-source video capture, resolution normalization, MJPEG ring buffer distribution, REST device management. | FastAPI, OpenCV, Pydantic, Confluent-Kafka, Uvicorn | [Gateway Manual](services/ingestion-gateway/docs/guide.md) |
| **Anomaly Detector** | `services/anomaly-detector` | `8004` | Real-time spatial-temporal vision analysis, Farneback optical flow velocity tracking, MOG2 foreground density scoring. | Torch, OpenCV, NumPy, FastAPI, Uvicorn | [Anomaly Manual](services/anomaly-detector/docs/guide.md) |
| **Biometric Recon** | `services/biometric-recon` | `8002` | 468-point 3D facial mesh extraction, Wavefront `.OBJ` point-cloud generation, vector search, Human-in-the-Loop review queue. | MediaPipe, SciPy, Qdrant, ChromaDB, FastAPI | [Recon Manual](services/biometric-recon/docs/guide.md) |
| **Agentic Ontology** | `services/agentic-ontology` | `8003` | Directed knowledge graph ontology, entity relationship mapping, local Gemma-4 LLM tactical situation reasoning. | NetworkX, OpenAI Client, HTTPX, FastAPI | [Ontology Manual](services/agentic-ontology/docs/guide.md) |
| **Mission Control UI** | `services/mission-control-ui` | `8501` | High-tech cyber-tactical HUD, live targeting reticles, non-null OSINT intelligence profile card, 3D facial mesh wireframe inspector. | Streamlit, Pillow, OpenCV, NumPy | [HUD Manual](services/mission-control-ui/docs/guide.md) |

---

## Step-by-Step Setup Guide

This guide covers everything required to take Chronos Surveillance OS from a fresh machine to a fully running tactical command installation.

### 1. Prerequisites Installation

<details>
<summary><b>Click to expand Prerequisites Setup (Windows, Linux, macOS)</b></summary>

#### Windows 10 / 11

1. **Install Git**:
   Download and install Git from [git-scm.com](https://git-scm.com/download/win).
2. **Install Python 3.10 or 3.11**:
   Download Python from [python.org](https://www.python.org/downloads/windows/). Check the box: `Add python.exe to PATH`.
3. **Install uv (Modern High-Speed Package Manager)**:
   Open PowerShell and execute:
   ```powershell
   powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
   ```
   Verify installation:
   ```powershell
   uv --version
   ```
4. **Install Docker Desktop (Optional, for containerized run)**:
   Download and install Docker Desktop from [docker.com](https://www.docker.com/products/docker-desktop/). Ensure WSL2 backend is selected.

#### Linux (Ubuntu 22.04 / 24.04 LTS / Debian)

1. **Update package repository**:
   ```bash
   sudo apt-get update && sudo apt-get install -y git curl build-essential libgl1-mesa-glx libglib2.0-0
   ```
2. **Install Python 3.10+**:
   ```bash
   sudo apt-get install -y python3 python3-pip python3-venv
   ```
3. **Install uv**:
   ```bash
   curl -LsSf https://astral.sh/uv/install.sh | sh
   source $HOME/.cargo/env
   uv --version
   ```
4. **Install Docker & Docker Compose**:
   ```bash
   curl -fsSL https://get.docker.com -o get-docker.sh
   sudo sh get-docker.sh
   sudo usermod -aG docker $USER
   ```

#### macOS (Apple Silicon / Intel)

1. **Install Homebrew**:
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```
2. **Install Dependencies and uv**:
   ```bash
   brew install git python@3.10 uv
   uv --version
   ```
</details>

### 2. Repository Cloning & Environment Setup

<details>
<summary><b>Click to expand Environment Configuration Guide</b></summary>

Clone the repository to your local drive:

```bash
git clone https://github.com/Kennny7/AWS-Smart-City-Surveillance.git
cd AWS-Smart-City-Surveillance
```

Initialize your local `.env` configuration file from the template:

```bash
# On Linux / macOS:
cp .env.example .env

# On Windows PowerShell:
Copy-Item .env.example .env
```

#### Key Environment Variables Explained

| Variable Name | Default Value | Description |
| :--- | :--- | :--- |
| `ENVIRONMENT` | `development` | Deployment environment mode (`development`, `staging`, `production`). |
| `PRIMARY_CAMERA_SOURCE` | `0` | Camera feed index (`0` for laptop webcam, RTSP URL, or phone HTTP link). |
| `LLM_PROVIDER` | `openai` | Protocol format for local inference server. |
| `LLM_BASE_URL` | `http://10.210.8.63:8080/v1` | Local Gemma-4 / vLLM / llama.cpp OpenAI-compatible endpoint. |
| `LLM_MODEL` | `google/gemma-4-E4B-it-qat-q4_0-gguf:Q4_0` | Targeted local model identifier. |
| `KAFKA_BOOTSTRAP_SERVERS` | `localhost:9092` | Distributed streaming broker address. |
| `VECTOR_DB_TYPE` | `chromadb` | Vector engine backend (`chromadb` or `qdrant`). |
| `MESH_OUTPUT_DIR` | `./data/3d_profiles` | Output directory for 468-point `.OBJ` facial mesh exports. |
| `GENERATE_3D_MESH` | `true` | Enables real-time Wavefront 3D point cloud generation. |
</details>

### 3. Option A: Full Deployment via Docker Compose

<details>
<summary><b>Click to expand Docker Compose Full Stack Instructions</b></summary>

Docker Compose starts the entire ecosystem—message broker, vector database, object storage, and all microservices—with a single command.

#### Start All Services

```bash
docker compose up -d
```

#### Check Running Service Containers

```bash
docker compose ps
```

Expected containers running:
- `chronos-message-bus` (Redpanda Kafka broker on ports `9092`, `19092`)
- `chronos-message-console` (Redpanda Web Console on port `8088`)
- `chronos-vector-db` (Qdrant Vector Database on port `6333`)
- `chronos-object-storage` (MinIO Storage on ports `9000`, `9001`)
- `chronos-ingestion-gateway` (Video Ingestion Gateway on port `8001`)
- `chronos-anomaly-detector` (Anomaly Detection Engine on port `8004`)
- `chronos-biometric-recon` (Biometric Reconnaissance Engine on port `8002`)
- `chronos-agentic-ontology` (Agentic Reasoning Engine on port `8003`)
- `chronos-mission-control-ui` (Tactical HUD on port `8501`)

#### View Combined System Logs

```bash
docker compose logs -f
```

#### Stopping the System

```bash
docker compose down
```
</details>

### 4. Option B: Running Microservices Locally with `uv`

<details>
<summary><b>Click to expand Local Microservices Startup Guide</b></summary>

To run services locally without Docker containers, open separate terminal windows for each service. The `uv` package manager automatically manages virtual environments and dependencies on demand.

#### Terminal 1: Ingestion Gateway (Port 8001)

```bash
cd services/ingestion-gateway
uv sync
uv run python main.py
```
*Health check:* Open `http://localhost:8001/health` in your browser.

#### Terminal 2: Anomaly Detector (Port 8004)

```bash
cd services/anomaly-detector
uv sync
uv run python main.py
```
*Health check:* Open `http://localhost:8004/health` in your browser.

#### Terminal 3: Biometric Recon & 3D Profiler (Port 8002)

```bash
cd services/biometric-recon
uv sync
uv run python main.py
```
*Health check:* Open `http://localhost:8002/health` in your browser.

#### Terminal 4: Agentic Threat Ontology (Port 8003)

```bash
cd services/agentic-ontology
uv sync
uv run python main.py
```
*Health check:* Open `http://localhost:8003/health` in your browser.

#### Terminal 5: Mission Control UI (Port 8501)

```bash
cd services/mission-control-ui
uv sync
uv run streamlit run app.py
```
*Access UI:* Navigate to **`http://localhost:8501`** in any web browser.
</details>

---

## Camera Feeds Integration

Chronos supports concurrent ingestion from virtually any video acquisition device.

### 1. Connecting Laptop or USB Webcams

Hardware devices connected directly to your computer are accessed via an integer index:
- Primary Integrated Webcam: `0`
- External USB Webcam / Capture Card: `1` or `2`

To register a hardware camera dynamically via the Ingestion Gateway API:

```bash
curl -X POST http://localhost:8001/api/v1/cameras/register \
     -H "Content-Type: application/json" \
     -d '{
       "camera_id": "LAPTOP_NODE_01",
       "source_url": "0",
       "location": "Operator Command Desk",
       "camera_type": "LAPTOP_CAM",
       "fps_limit": 15
     }'
```

### 2. Connecting Smartphones (Android / iOS)

Smartphones can be transformed into wireless tactical cameras over your local Wi-Fi or private network.

#### Using "IP Webcam" (Android)
1. Install **IP Webcam** from Google Play.
2. Open the application, configure video resolution to `1280x720`, and tap **Start Server**.
3. Note the displayed network URL (e.g., `http://192.168.1.45:8080`).
4. Register the mobile camera feed:
   ```bash
   curl -X POST http://localhost:8001/api/v1/cameras/register \
        -H "Content-Type: application/json" \
        -d '{
          "camera_id": "MOBILE_PATROL_01",
          "source_url": "http://192.168.1.45:8080/video",
          "location": "Sector 07 Mobile Unit",
          "camera_type": "MOBILE_PHONE",
          "fps_limit": 15
        }'
   ```

#### Using "DroidCam" (iOS & Android)
1. Install **DroidCam** on your mobile device and connect to the same Wi-Fi subnet.
2. Retrieve the device IP and port displayed on screen (e.g., `192.168.1.80:4747`).
3. Set `source_url` to `http://192.168.1.80:4747/video` when registering.

### 3. Connecting Standard RTSP CCTV Surveillance Cameras

For professional enterprise security cameras (Hikvision, Dahua, Axis, Hanwha):

```bash
curl -X POST http://localhost:8001/api/v1/cameras/register \
     -H "Content-Type: application/json" \
     -d '{
       "camera_id": "CCTV_PLAZA_NORTH",
       "source_url": "rtsp://admin:securepass123@192.168.1.150:554/h264Preview_01_main",
       "location": "Metropolis Central Plaza",
       "camera_type": "RTSP_FIXED",
       "fps_limit": 20
     }'
```

For detailed configurations, see the [Universal Camera Integration Guide](docs/guides/camera_integration_guide.md).

---

## Local LLM & AI Agents

Chronos incorporates an agentic threat reasoning loop that links physical observations to a structured city knowledge graph.

### Local Gemma-4 Integration

The reasoning engine connects to an OpenAI-compatible API running locally on your private network:

```
Endpoint: http://10.210.8.63:8080/v1
Model:    google/gemma-4-E4B-it-qat-q4_0-gguf:Q4_0
```

### Knowledge Graph Schema

The ontology maintains a directed relationship graph:
- `(Camera Node)` &bull; `[MONITORS]` &bull; `(City Sector)`
- `(Camera Node)` &bull; `[OBSERVES]` &bull; `(Identified Subject)`
- `(Identified Subject)` &bull; `[INVOLVED_IN]` &bull; `(Threat Event)`
- `(Threat Event)` &bull; `[EVALUATED_BY]` &bull; `(Gemma-4 Agent)` &bull; `[ISSUES_DIRECTIVE]` &bull; `(Operator Queue)`

### Deterministic Offline Safeguard

If the local LLM server is temporarily unreachable, the agentic engine automatically falls back to a deterministic rule-based evaluation matrix, ensuring zero interruption to city surveillance monitoring.

---

## Documentation Index

Comprehensive guides and architectural specifications are located in the `docs/` folder and individual service directories:

| Documentation File | Target Audience | Summary of Contents |
| :--- | :--- | :--- |
| [System Architecture Overview](docs/architecture/system_overview.md) | Architects, DevOps | System decomposition, data flow diagrams, network port matrix, and security model. |
| [Camera Integration Guide](docs/guides/camera_integration_guide.md) | Operators, Field Techs | Comprehensive instructions for connecting CCTV, smartphones (IP Webcam, DroidCam), and webcams. |
| [Datasets & 3D Profiling Guide](docs/guides/datasets_and_training.md) | ML Engineers, Researchers | Public surveillance datasets (UCSD, ShanghaiTech, LFW), 3D facial mesh training, and evaluation. |
| [Ingestion Gateway Manual](services/ingestion-gateway/docs/guide.md) | Backend Engineers | REST API reference, stream buffering architecture, and frame serialization specifications. |
| [Anomaly Detector Manual](services/anomaly-detector/docs/guide.md) | Vision Engineers | Dense optical flow mathematics, MOG2 background subtraction parameters, and threat scoring. |
| [Biometric Recon Manual](services/biometric-recon/docs/guide.md) | Biometric Specialists | 468-point 3D landmark mesh construction, `.OBJ` coordinate formatting, and HITL authorization queue. |
| [Agentic Ontology Manual](services/agentic-ontology/docs/guide.md) | AI Engineers | Knowledge graph schema, prompt engineering guidelines for Gemma-4, and incident direct synthesis. |
| [Mission Control UI Manual](services/mission-control-ui/docs/guide.md) | Operators, Commanders | HUD layout documentation, target acquisition reticles, 3D point cloud visualizer, and diagnostics. |

---

## Troubleshooting

<details>
<summary><b>Click to expand Troubleshooting Matrix & FAQs</b></summary>

### 1. Camera Stream Fails to Open (`Could not open camera stream`)

- **Cause**: The specified hardware index (`0`) is occupied by another application, or the IP camera URL is unreachable.
- **Solution**:
  1. Verify no other application (Zoom, Teams, Skype, or browser) is using your webcam.
  2. For mobile phone streams, ensure your computer and phone are connected to the exact same Wi-Fi subnet. Verify by opening the camera URL in your desktop browser.
  3. If physical hardware is offline, Chronos automatically activates the **Synthetic Tactical Simulator**, allowing full development and UI testing without hardware.

### 2. Port Conflict (`Address already in use`)

- **Cause**: Another service is using one of the assigned ports (`8001`, `8002`, `8003`, `8004`, `8501`, `9092`).
- **Solution**:
  Identify and terminate the occupying process:
  ```powershell
  # On Windows PowerShell:
  Get-Process -Id (Get-NetTCPConnection -LocalPort 8501).OwningProcess | Stop-Process -Force
  ```
  ```bash
  # On Linux / macOS:
  sudo lsof -i :8501 -t | xargs kill -9
  ```

### 3. Local LLM Connection Timeout (`10.210.8.63:8080`)

- **Cause**: The local LLM inference workstation is offline or firewall rules block port `8080`.
- **Solution**:
  1. Test connectivity using `curl http://10.210.8.63:8080/v1/models`.
  2. Chronos automatically falls back to its internal deterministic tactical reasoning agent if the endpoint does not reply within 8 seconds.

### 4. MediaPipe Landmark Import Warnings

- **Cause**: Missing native system graphics libraries on certain headless Linux distributions.
- **Solution**:
  Install required OpenGL libraries:
  ```bash
  sudo apt-get install -y libgl1-mesa-glx libglib2.0-0
  ```
</details>

---

## License

This project is licensed under the Apache 2.0 / MIT License. Refer to [LICENSE.md](LICENSE.md) for full legal terms.
