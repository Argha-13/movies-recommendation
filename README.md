# 🎬 End-to-End Movie Recommendation System
An end-to-end Content-Based Movie Recommendation web application. The system processes and analyzes textual metadata (genres, production keywords, cast, crew, and overviews) using Natural Language Processing (NLP) to recommend contextually similar films. It features a high-performance FastAPI backend service to handle core machine learning vector computations and an interactive, responsive Streamlit frontend dashboard that pulls live movie posters via the official TMDB API.

## 🏗️ Architecture Design
The application is built using a completely decoupled, two-tier microservice architecture to separate computational workloads from user interface rendering:

Streamlit Frontend (User Interface Grid) ---> 🛰️ HTTP Requests ---> FastAPI Backend (Vector Math & Recommender)
Streamlit Frontend (User Interface Grid) <--- 📦 JSON Responses <--- FastAPI Backend (Vector Math & Recommender)

Streamlit Frontend ---> 🌐 REST API Hits ---> TMDB API (Live Poster Assets)
FastAPI Backend ---> 💾 Binary Loads ---> Serialized ML Artifacts (df.pkl, tfidf_matrix.pkl)

🧠 Backend (FastAPI): Memory-maps pre-computed TF-IDF sparse matrices into RAM upon startup. It exposes optimized REST endpoints that compute vectorized Cosine Similarity arrays on the fly, returning structured JSON recommendations.

🎨 Frontend (Streamlit): Captures reactive user search strings, coordinates layout configurations, and communicates directly with external TMDB endpoints to resolve secure metadata poster pathways dynamically.


## 🛠️ Machine Learning Pipeline & Core Concepts
1. 🧹 Data Cleaning & Feature Engineering (movies.ipynb)
The source data undergoes rigorous string preprocessing to construct a single, comprehensive text profile per movie:

🔍 JSON Parsing: Extracts clean string literals from raw structured columns (e.g., extracting ['Action', 'Sci-Fi'] from stringified JSON blobs).

📑 Text Consolidation: Merges individual metadata tags (Cast, Crew, Director, Genres, Keywords, and Plot Overviews) into a singular string feature column named 'tags'.

🧼 Text Normalization: Lowercases all characters and sanitizes HTML/special characters to prevent vector noise.

2. 📈 TF-IDF Vectorization
The text profile engine leverages a Term Frequency-Inverse Document Frequency vector space model to weight keywords based on how unique they are to a specific film relative to the entire dataset. This mathematically penalizes ubiquitous stop words while emphasizing highly descriptive tokens (e.g., specific character names or niche sci-fi concepts).

3. 📐 Cosine Similarity Score Calculation
To calculate the contextual distance between any two films, the system measures the angular cosine alignment between their high-dimensional TF-IDF unit vectors. When a user selects a target film, the backend pulls its vector row index, runs an inner-product calculation against all remaining items, sorts the scores in descending order, and returns the top matches.

🚀 Getting Started & Execution
📋 Prerequisites
🐍 Python 3.12+ (Configured and verified on Python 3.14 environments)

🔑 A valid TMDB API Account Key (V3 Auth)

1. ⚙️ Environment Configurations
Clone or open your project workspace directory in your terminal and activate your local environment:

🔑 Windows PowerShell Execution:
.venv\Scripts\activate

🍎 Linux / macOS Execution:
source .venv/bin/activate

📝 Create a standard .env configuration file in the project's root folder to securely declare API credentials:
TMDB_API_KEY=your_actual_32_character_hexadecimal_api_key

## 2. ⚡ Launch the FastAPI Microservice
Navigate to the root directory containing main.py and start the asynchronous Uvicorn ASGI server:
uvicorn main:app --reload --port 8000

🌐 Local Server Gateway: http://127.0.0.1:8000

📄 Interactive API Documentation (Swagger UI): http://127.0.0.1:8000/docs

## 3. 🖥️ Launch the Streamlit Interactive Interface
Open a separate split terminal window in VS Code, ensure the virtual environment is fully active, and boot the frontend user interface:
streamlit run app.py

🔗 Local Web Application URL: http://localhost:8501

⚙️ Core Optimization Features Implemented
🔄 Global Client Session Reuse: Refactored the core background networking logic to leverage a singular, persistent global httpx.AsyncClient(). This pool architecture mitigates local SSL handshake overhead, eliminating downstream network read timeouts (read timeout=25).

🛡️ Robust Error Isolation: Wraps individual backend execution contexts with explicit try-except blocks tracking httpx.RequestError anomalies, converting low-level network failures into predictable HTTP 502 Bad Gateway standard status codes.

📱 Responsive Element Rendering: Built with custom dynamic grid configurations utilizing native Streamlit row layout containers (use_container_width=True) to secure clean, deprecation-free multi-column media scaling.
