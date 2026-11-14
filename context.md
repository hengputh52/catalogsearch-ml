You are helping me set up a new Machine Learning project called CatalogSearch — an
e-commerce search platform for electronic devices (phones, laptops, PCs, headphones,
chargers, power banks). The data has ALREADY BEEN SCRAPED — do not generate scraping
code. Assume product data exists in /data/raw/products.jsonl.

## Project Goal
Build a system that:
1. Takes a user query
2. Uses an ML CLASSIFIER to decide if it is a "Direct Product Search" or an
   "Informational Question"
3. Routes "Direct Product Search" queries → classical IR (TF-IDF, BM25, Hybrid)
4. Routes "Informational Question" queries → RAG chatbot (dense retrieval + LLM)
5. Returns ranked products or grounded conversational answers

The ML classifier IS the central contribution. Classical IR and RAG are supporting
components it routes to.

## Tech Stack
- Python 3.11+
- scikit-learn (classifier baseline)
- sentence-transformers (dense embeddings)
- rank_bm25 (BM25 ranker)
- FastAPI (backend)
- PostgreSQL via Neon (already set up)
- pandas, numpy, matplotlib, seaborn (analysis)
- pytest (tests)
- Jupyter (notebooks)

## Architectural Principles

1. **ML-first layout** — the `ml/` module is the project's core, not an afterthought
2. **Separation of concerns** — one responsibility per module:
   - `preprocessing/` — shared text pipeline (used by classifier AND retrieval)
   - `ml/` — classifier training, inference, evaluation
   - `retrieval/` — classical IR methods
   - `rag/` — LLM-grounded answers
   - `router/` — uses classifier to dispatch queries
   - `evaluation/` — both IR metrics (nDCG, MAP) and ML metrics (F1, confusion matrix)
3. **Reproducible experiments** — notebooks for exploration, scripts for production
4. **Versioned models** — classifier saved to `models/` with timestamp
5. **No hardcoding** — all paths, thresholds, hyperparameters in `config/constants.py`
6. **Testable** — pure functions where possible, pytest coverage for ML and IR code

## Create This Project Structure

You are helping me set up a new Machine Learning project called CatalogSearch — an
e-commerce search platform for electronic devices (phones, laptops, PCs, headphones,
chargers, power banks). The data has ALREADY BEEN SCRAPED — do not generate scraping
code. Assume product data exists in /data/raw/products.jsonl.

## Project Goal
Build a system that:
1. Takes a user query
2. Uses an ML CLASSIFIER to decide if it is a "Direct Product Search" or an
   "Informational Question"
3. Routes "Direct Product Search" queries → classical IR (TF-IDF, BM25, Hybrid)
4. Routes "Informational Question" queries → RAG chatbot (dense retrieval + LLM)
5. Returns ranked products or grounded conversational answers

The ML classifier IS the central contribution. Classical IR and RAG are supporting
components it routes to.

## Tech Stack
- Python 3.11+
- scikit-learn (classifier baseline)
- sentence-transformers (dense embeddings)
- rank_bm25 (BM25 ranker)
- FastAPI (backend)
- PostgreSQL via Neon (already set up)
- pandas, numpy, matplotlib, seaborn (analysis)
- pytest (tests)
- Jupyter (notebooks)

## Architectural Principles

1. **ML-first layout** — the `ml/` module is the project's core, not an afterthought
2. **Separation of concerns** — one responsibility per module:
   - `preprocessing/` — shared text pipeline (used by classifier AND retrieval)
   - `ml/` — classifier training, inference, evaluation
   - `retrieval/` — classical IR methods
   - `rag/` — LLM-grounded answers
   - `router/` — uses classifier to dispatch queries
   - `evaluation/` — both IR metrics (nDCG, MAP) and ML metrics (F1, confusion matrix)
3. **Reproducible experiments** — notebooks for exploration, scripts for production
4. **Versioned models** — classifier saved to `models/` with timestamp
5. **No hardcoding** — all paths, thresholds, hyperparameters in `config/constants.py`
6. **Testable** — pure functions where possible, pytest coverage for ML and IR code

## Create This Project Structure

catalogsearch-ml/
├── .env.example
├── .gitignore
├── requirements.txt
├── pyproject.toml
├── README.md
├── docker-compose.yml
├── data/
│   ├── raw/                            # KEEP EMPTY — scraped data goes here
│   │   └── .gitkeep
│   ├── processed/
│   │   └── .gitkeep
│   ├── labeled/
│   │   ├── intent_queries.csv          # SEED THIS with 20 example rows
│   │   └── relevance_judgments.csv
│   └── embeddings/
│       └── .gitkeep
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   ├── 02_intent_label_annotation.ipynb
│   ├── 03_classifier_training.ipynb
│   ├── 04_classifier_evaluation.ipynb
│   ├── 05_ir_evaluation.ipynb
│   └── 06_error_analysis.ipynb
├── models/
│   └── .gitkeep
├── db/
│   └── migrations/
│       └── 001_create_products.sql
└── src/catalogsearch/
    ├── __init__.py
    ├── config/
    │   ├── __init__.py
    │   ├── env.py
    │   └── constants.py
    ├── utils/
    │   ├── __init__.py
    │   ├── logger.py
    │   └── timer.py
    ├── db/
    │   ├── __init__.py
    │   ├── client.py
    │   └── repositories/
    │       ├── __init__.py
    │       └── product_repository.py
    ├── preprocessing/
    │   ├── __init__.py
    │   ├── tokenizer.py
    │   ├── stopwords.py
    │   ├── normalizer.py
    │   └── pipeline.py
    ├── ingestion/
    │   ├── __init__.py
    │   ├── loader.py                   # reads /data/raw/products.jsonl
    │   ├── cleaner.py
    │   └── indexer.py
    ├── ml/
    │   ├── __init__.py
    │   ├── classifier/
    │   │   ├── __init__.py
    │   │   ├── features.py             # TF-IDF + embedding feature extractors
    │   │   ├── train.py
    │   │   ├── predict.py
    │   │   └── evaluate.py
    │   ├── dataset/
    │   │   ├── __init__.py
    │   │   ├── labeling.py
    │   │   └── splitter.py
    │   └── models/
    │       ├── __init__.py
    │       ├── logistic_regression.py
    │       ├── svm_classifier.py
    │       ├── random_forest.py
    │       └── distilbert_classifier.py
    ├── retrieval/
    │   ├── __init__.py
    │   ├── tfidf_cosine.py
    │   ├── bm25.py
    │   ├── dense.py
    │   ├── hybrid.py
    │   └── retriever.py
    ├── rag/
    │   ├── __init__.py
    │   ├── pipeline.py
    │   ├── prompts/
    │   │   ├── __init__.py
    │   │   ├── system_prompt.py
    │   │   └── context_template.py
    │   ├── llm/
    │   │   ├── __init__.py
    │   │   ├── base.py
    │   │   ├── openai_client.py
    │   │   └── ollama_client.py
    │   └── grounding.py
    ├── router/
    │   ├── __init__.py
    │   └── query_router.py             # the ML decision maker
    ├── evaluation/
    │   ├── __init__.py
    │   ├── ir_metrics.py
    │   ├── classifier_metrics.py
    │   ├── benchmark_queries.json
    │   └── run_evaluation.py
    ├── api/
    │   ├── __init__.py
    │   ├── main.py
    │   ├── routes/
    │   │   ├── __init__.py
    │   │   ├── query.py                # unified /query endpoint
    │   │   ├── search.py
    │   │   ├── chat.py
    │   │   └── analytics.py
    │   ├── schemas/
    │   │   ├── __init__.py
    │   │   ├── query.py
    │   │   └── search.py
    │   └── logging/
    │       ├── __init__.py
    │       └── logger.py
    └── scripts/
        ├── __init__.py
        ├── load_data.py
        ├── build_indices.py
        ├── label_queries.py
        ├── train_classifier.py
        ├── evaluate_all.py
        └── run_api.py

tests/
├── __init__.py
├── test_preprocessing.py
├── test_classifier.py
├── test_retrieval.py
├── test_router.py
└── test_api.py

## Module Contracts

### src/catalogsearch/preprocessing/pipeline.py
    def preprocess_text(raw: str) -> str: ...    # normalize + tokenize + stopwords

### src/catalogsearch/ml/classifier/predict.py
    def classify_intent(query: str) -> dict:
        """Returns {'label': 'search'|'info', 'confidence': float}"""

### src/catalogsearch/retrieval/retriever.py
    def retrieve(query: str, method: str = 'hybrid', top_k: int = 10) -> list[dict]:
        """method ∈ {'tfidf', 'bm25', 'dense', 'hybrid'}"""

### src/catalogsearch/rag/pipeline.py
    async def generate_answer(query: str, history: list = None, top_k: int = 5) -> dict:
        """Returns {'answer': str, 'retrieved_products': [...], 'grounded': bool}"""

### src/catalogsearch/router/query_router.py
    async def route_query(query: str, session_id: str) -> dict:
        """
        1. classify_intent(query)
        2. if label == 'search': call retrieve()
        3. if label == 'info':  call generate_answer()
        4. Return {'intent': ..., 'confidence': ..., 'result': ...}
        """

## Seed Data You Should Create

### data/labeled/intent_queries.csv (20 rows)
Mix of both labels. Examples:
    query,label
    "iPhone 15 Pro Max",search
    "which laptop is best for programming?",info
    "sony wh-1000xm5",search
    "do noise cancelling headphones damage hearing?",info
    ...etc

### data/labeled/relevance_judgments.csv (20 rows)
    query_id,query,product_id,relevance
    1,wireless headphones,P102,3
    1,wireless headphones,P103,2
    1,wireless headphones,P054,0
    ...etc (relevance: 0=irrelevant, 1=marginal, 2=relevant, 3=highly relevant)

### evaluation/benchmark_queries.json (10 example queries)
    [{"query": "best laptop under $1000", "expected_product_ids": [...], "intent": "info"}, ...]

### db/migrations/001_create_products.sql
    CREATE TABLE products (
      product_id TEXT PRIMARY KEY,
      title TEXT NOT NULL,
      description TEXT NOT NULL,
      brand TEXT, category TEXT NOT NULL,
      price NUMERIC,
      specs JSONB DEFAULT '{}'::jsonb,
      features TEXT[] DEFAULT '{}',
      image_url TEXT, source_url TEXT,
      rating NUMERIC, source TEXT NOT NULL,
      collected_at TIMESTAMPTZ DEFAULT now()
    );
    CREATE INDEX idx_products_category ON products(category);

## requirements.txt
    psycopg2-binary>=2.9.9
    python-dotenv>=1.0.0
    pandas>=2.2.0
    numpy>=1.26.0
    scikit-learn>=1.4.0
    sentence-transformers>=2.6.0
    rank-bm25>=0.2.2
    matplotlib>=3.8.0
    seaborn>=0.13.0
    fastapi>=0.110.0
    uvicorn>=0.27.0
    pydantic>=2.6.0
    httpx>=0.27.0
    jupyter>=1.0.0
    pytest>=8.0.0
    tqdm>=4.66.0

## package.json scripts (in pyproject.toml console_scripts)
    load-data       = catalogsearch.scripts.load_data:main
    build-indices   = catalogsearch.scripts.build_indices:main
    label-queries   = catalogsearch.scripts.label_queries:main
    train           = catalogsearch.scripts.train_classifier:main
    evaluate        = catalogsearch.scripts.evaluate_all:main
    serve           = catalogsearch.scripts.run_api:main

## README should document:
- Architecture diagram (ML classifier at the center routing to IR or RAG)
- Pipeline flow: scraped data → preprocess → index → classifier training → router → API
- How to run each stage: load-data, build-indices, train, evaluate, serve
- Which notebook to open first for new contributors
- How to add training examples (append to intent_queries.csv, rerun `train`)

## Build Order
1. Scaffold folders, requirements.txt, pyproject.toml
2. config/, utils/, db/ (connection + repository)
3. preprocessing/ (used by both classifier and retrieval)
4. ingestion/ (reads existing /data/raw/products.jsonl)
5. db/migrations + run migration
6. retrieval/ (bm25, tfidf, dense, hybrid)
7. ml/classifier/ (logistic regression baseline first, then compare SVM, Random Forest)
8. router/ (ties classifier + retrieval + RAG)
9. rag/ (prompts, LLM client, grounding)
10. api/ (FastAPI routes)
11. evaluation/ (IR metrics + classifier metrics)
12. notebooks/ (create empty starter notebooks with section headings)
13. README

## DO NOT create:
- Any scraper (data is already scraped)
- Data collection tutorials
- API keys or secrets

## What to deliver
- All files above
- Runnable `pip install -r requirements.txt && python -m catalogsearch.scripts.load_data`
- Notebook 03_classifier_training.ipynb with a complete LogisticRegression baseline
- Notebook 04_classifier_evaluation.ipynb with confusion matrix via seaborn
- README with architecture diagram
├── .env.example
├── .gitignore
├── requirements.txt
├── pyproject.toml
├── README.md
├── docker-compose.yml
├── data/
│   ├── raw/                            # KEEP EMPTY — scraped data goes here
│   │   └── .gitkeep
│   ├── processed/
│   │   └── .gitkeep
│   ├── labeled/
│   │   ├── intent_queries.csv          # SEED THIS with 20 example rows
│   │   └── relevance_judgments.csv
│   └── embeddings/
│       └── .gitkeep
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   ├── 02_intent_label_annotation.ipynb
│   ├── 03_classifier_training.ipynb
│   ├── 04_classifier_evaluation.ipynb
│   ├── 05_ir_evaluation.ipynb
│   └── 06_error_analysis.ipynb
├── models/
│   └── .gitkeep
├── db/
│   └── migrations/
│       └── 001_create_products.sql
└── src/catalogsearch/
    ├── __init__.py
    ├── config/
    │   ├── __init__.py
    │   ├── env.py
    │   └── constants.py
    ├── utils/
    │   ├── __init__.py
    │   ├── logger.py
    │   └── timer.py
    ├── db/
    │   ├── __init__.py
    │   ├── client.py
    │   └── repositories/
    │       ├── __init__.py
    │       └── product_repository.py
    ├── preprocessing/
    │   ├── __init__.py
    │   ├── tokenizer.py
    │   ├── stopwords.py
    │   ├── normalizer.py
    │   └── pipeline.py
    ├── ingestion/
    │   ├── __init__.py
    │   ├── loader.py                   # reads /data/raw/products.jsonl
    │   ├── cleaner.py
    │   └── indexer.py
    ├── ml/
    │   ├── __init__.py
    │   ├── classifier/
    │   │   ├── __init__.py
    │   │   ├── features.py             # TF-IDF + embedding feature extractors
    │   │   ├── train.py
    │   │   ├── predict.py
    │   │   └── evaluate.py
    │   ├── dataset/
    │   │   ├── __init__.py
    │   │   ├── labeling.py
    │   │   └── splitter.py
    │   └── models/
    │       ├── __init__.py
    │       ├── logistic_regression.py
    │       ├── svm_classifier.py
    │       ├── random_forest.py
    │       └── distilbert_classifier.py
    ├── retrieval/
    │   ├── __init__.py
    │   ├── tfidf_cosine.py
    │   ├── bm25.py
    │   ├── dense.py
    │   ├── hybrid.py
    │   └── retriever.py
    ├── rag/
    │   ├── __init__.py
    │   ├── pipeline.py
    │   ├── prompts/
    │   │   ├── __init__.py
    │   │   ├── system_prompt.py
    │   │   └── context_template.py
    │   ├── llm/
    │   │   ├── __init__.py
    │   │   ├── base.py
    │   │   ├── openai_client.py
    │   │   └── ollama_client.py
    │   └── grounding.py
    ├── router/
    │   ├── __init__.py
    │   └── query_router.py             # the ML decision maker
    ├── evaluation/
    │   ├── __init__.py
    │   ├── ir_metrics.py
    │   ├── classifier_metrics.py
    │   ├── benchmark_queries.json
    │   └── run_evaluation.py
    ├── api/
    │   ├── __init__.py
    │   ├── main.py
    │   ├── routes/
    │   │   ├── __init__.py
    │   │   ├── query.py                # unified /query endpoint
    │   │   ├── search.py
    │   │   ├── chat.py
    │   │   └── analytics.py
    │   ├── schemas/
    │   │   ├── __init__.py
    │   │   ├── query.py
    │   │   └── search.py
    │   └── logging/
    │       ├── __init__.py
    │       └── logger.py
    └── scripts/
        ├── __init__.py
        ├── load_data.py
        ├── build_indices.py
        ├── label_queries.py
        ├── train_classifier.py
        ├── evaluate_all.py
        └── run_api.py

tests/
├── __init__.py
├── test_preprocessing.py
├── test_classifier.py
├── test_retrieval.py
├── test_router.py
└── test_api.py

## Module Contracts

### src/catalogsearch/preprocessing/pipeline.py
    def preprocess_text(raw: str) -> str: ...    # normalize + tokenize + stopwords

### src/catalogsearch/ml/classifier/predict.py
    def classify_intent(query: str) -> dict:
        """Returns {'label': 'search'|'info', 'confidence': float}"""

### src/catalogsearch/retrieval/retriever.py
    def retrieve(query: str, method: str = 'hybrid', top_k: int = 10) -> list[dict]:
        """method ∈ {'tfidf', 'bm25', 'dense', 'hybrid'}"""

### src/catalogsearch/rag/pipeline.py
    async def generate_answer(query: str, history: list = None, top_k: int = 5) -> dict:
        """Returns {'answer': str, 'retrieved_products': [...], 'grounded': bool}"""

### src/catalogsearch/router/query_router.py
    async def route_query(query: str, session_id: str) -> dict:
        """
        1. classify_intent(query)
        2. if label == 'search': call retrieve()
        3. if label == 'info':  call generate_answer()
        4. Return {'intent': ..., 'confidence': ..., 'result': ...}
        """

## Seed Data You Should Create

### data/labeled/intent_queries.csv (20 rows)
Mix of both labels. Examples:
    query,label
    "iPhone 15 Pro Max",search
    "which laptop is best for programming?",info
    "sony wh-1000xm5",search
    "do noise cancelling headphones damage hearing?",info
    ...etc

### data/labeled/relevance_judgments.csv (20 rows)
    query_id,query,product_id,relevance
    1,wireless headphones,P102,3
    1,wireless headphones,P103,2
    1,wireless headphones,P054,0
    ...etc (relevance: 0=irrelevant, 1=marginal, 2=relevant, 3=highly relevant)

### evaluation/benchmark_queries.json (10 example queries)
    [{"query": "best laptop under $1000", "expected_product_ids": [...], "intent": "info"}, ...]

### db/migrations/001_create_products.sql
    CREATE TABLE products (
      product_id TEXT PRIMARY KEY,
      title TEXT NOT NULL,
      description TEXT NOT NULL,
      brand TEXT, category TEXT NOT NULL,
      price NUMERIC,
      specs JSONB DEFAULT '{}'::jsonb,
      features TEXT[] DEFAULT '{}',
      image_url TEXT, source_url TEXT,
      rating NUMERIC, source TEXT NOT NULL,
      collected_at TIMESTAMPTZ DEFAULT now()
    );
    CREATE INDEX idx_products_category ON products(category);

## requirements.txt
    psycopg2-binary>=2.9.9
    python-dotenv>=1.0.0
    pandas>=2.2.0
    numpy>=1.26.0
    scikit-learn>=1.4.0
    sentence-transformers>=2.6.0
    rank-bm25>=0.2.2
    matplotlib>=3.8.0
    seaborn>=0.13.0
    fastapi>=0.110.0
    uvicorn>=0.27.0
    pydantic>=2.6.0
    httpx>=0.27.0
    jupyter>=1.0.0
    pytest>=8.0.0
    tqdm>=4.66.0

## package.json scripts (in pyproject.toml console_scripts)
    load-data       = catalogsearch.scripts.load_data:main
    build-indices   = catalogsearch.scripts.build_indices:main
    label-queries   = catalogsearch.scripts.label_queries:main
    train           = catalogsearch.scripts.train_classifier:main
    evaluate        = catalogsearch.scripts.evaluate_all:main
    serve           = catalogsearch.scripts.run_api:main

## README should document:
- Architecture diagram (ML classifier at the center routing to IR or RAG)
- Pipeline flow: scraped data → preprocess → index → classifier training → router → API
- How to run each stage: load-data, build-indices, train, evaluate, serve
- Which notebook to open first for new contributors
- How to add training examples (append to intent_queries.csv, rerun `train`)

## Build Order
1. Scaffold folders, requirements.txt, pyproject.toml
2. config/, utils/, db/ (connection + repository)
3. preprocessing/ (used by both classifier and retrieval)
4. ingestion/ (reads existing /data/raw/products.jsonl)
5. db/migrations + run migration
6. retrieval/ (bm25, tfidf, dense, hybrid)
7. ml/classifier/ (logistic regression baseline first, then compare SVM, Random Forest)
8. router/ (ties classifier + retrieval + RAG)
9. rag/ (prompts, LLM client, grounding)
10. api/ (FastAPI routes)
11. evaluation/ (IR metrics + classifier metrics)
12. notebooks/ (create empty starter notebooks with section headings)
13. README

## DO NOT create:
- Any scraper (data is already scraped)
- Data collection tutorials
- API keys or secrets

## What to deliver
- All files above
- Runnable `pip install -r requirements.txt && python -m catalogsearch.scripts.load_data`
- Notebook 03_classifier_training.ipynb with a complete LogisticRegression baseline
- Notebook 04_classifier_evaluation.ipynb with confusion matrix via seaborn
- README with architecture diagram