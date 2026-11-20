# CatalogSearch

E-commerce search for electronics (phones, laptops, PCs, headphones, chargers, power banks).
An **ML classifier** decides what each user query needs:

- **search**, e.g. `sony wh-1000xm5`, goes to classical IR (TF-IDF, BM25, hybrid) and returns ranked products.
- **info**, e.g. `which laptop is best for programming?`, goes to a RAG chatbot (dense retrieval + LLM) and returns an answer.

The classifier is the main contribution of the project. The product data is already scraped, so do not write a scraper.

## ML workflow

The project follows seven steps. Each step has a folder in `src/catalogsearch/`, and most have a notebook.

| # | Step | Code | Notebook | Task |
|---|---|---|---|---|
| 1 | Data collection | `data_collection/` | `01_data_collection` | Products are already scraped (see [Data](#data)). The notebook is a sample scraper for one store (ptc-computer.com.kh) that outputs the same format. |
| 2 | Data analysis | `data_analysis/` | `02_data_analysis` | Explore the catalog and the labelled queries. Find data quality problems. |
| 3 | Data preprocessing | `preprocessing/` | `03_data_preprocessing` | Clean products. Build the text pipeline. Extract classifier features. |
| 4 | Model training | `training/` | `04_model_training` | Train the classifier (Logistic Regression first, then SVM and Random Forest). Build the search indices. |
| 5 | Evaluation | `evaluation/` | `05_evaluation` | Classifier metrics (F1, confusion matrix). IR metrics (nDCG, MAP, MRR). |
| 6 | Deployment | `deployment/` | — | Route each query to search or the chatbot and serve it through a FastAPI API. |
| 7 | Monitoring | `monitoring/` | `06_monitoring` | Log queries. Track intent and confidence over time. Decide when to retrain. |

## Project structure

```
catalogsearch-ml/
├── data/
│   ├── raw/products.jsonl        # 4,207 scraped products (see Data)
│   ├── processed/                # cleaned data and indices
│   └── labeled/
│       ├── intent_queries.csv        # query,label   (search | info)
│       ├── relevance_judgments.csv   # query_id,query,product_id,relevance (0-3)
│       └── benchmark_queries.json    # held-out test queries
├── db/schema.sql                 # products table
├── models/                       # saved classifiers, one file per training run, named with a timestamp
├── notebooks/                    # 01–06, one per step
├── src/catalogsearch/
│   ├── config.py                 # all paths, thresholds, hyperparameters
│   ├── data_collection/          # 1
│   ├── data_analysis/            # 2
│   ├── preprocessing/            # 3
│   ├── training/                 # 4
│   ├── evaluation/               # 5
│   ├── deployment/               # 6
│   └── monitoring/               # 7
├── tests/
├── Dockerfile
├── requirements.txt
└── pyproject.toml
```

## Data

`data/raw/products.jsonl` holds 4,207 products scraped in the earlier `product_search_and_analysis` project.
Every record has the same 12 fields: `product_id, title, description, brand, category, price, specs,
features, image_url, source_url, rating, source`. No `product_id` appears twice.

| Source | Products |
|---|---|
| ptc_computer | 1,247 |
| angkor_meas | 1,085 |
| amazon | 981 |
| kaggle_amazon_in | 263 |
| alibaba | 258 |
| notebookcheck | 205 |
| amazon_web | 121 |
| sunsimexco | 47 |

Things to look at in steps 2–3:

- There are 29 raw categories (e.g. `smartphone`, `powerbank`, `tv`, `printer`), more than the six the project targets. Decide how to map or filter them.
- `rating` is missing in 2,497 records, `brand` in 437 and `price` in 105.

## Files to complete

| File | What it should do |
|---|---|
| `config.py` | Holds every path, threshold and hyperparameter. Nothing is hardcoded elsewhere. |
| `data_collection/load_products.py` | Reads `data/raw/products.jsonl` and saves the products to Postgres. |
| `data_collection/label_queries.py` | Adds labelled queries to `intent_queries.csv`. |
| `data_analysis/eda.py` | Reusable plots and summary tables for notebook 02. |
| `preprocessing/text_pipeline.py` | `preprocess_text(raw: str) -> str`: normalize, tokenize, remove stopwords. Used by both the classifier and search. |
| `preprocessing/clean_products.py` | Maps raw records onto the products schema. Parses prices. Normalizes categories. |
| `preprocessing/features.py` | Turns queries into classifier features (TF-IDF, embeddings). |
| `training/train_classifier.py` | Trains the classifier and saves a timestamped model to `models/`. |
| `training/build_indices.py` | Builds the TF-IDF, BM25 and dense indices over the products. |
| `evaluation/classifier_metrics.py` | Accuracy, precision, recall, F1, confusion matrix. |
| `evaluation/ir_metrics.py` | nDCG@k, MAP, P@k, R@k, MRR. |
| `evaluation/evaluate.py` | Runs every evaluation and writes a report. |
| `deployment/predict.py` | `classify_intent(query: str) -> dict`, returns `{'label': 'search'\|'info', 'confidence': float}`. |
| `deployment/retrieval.py` | `retrieve(query: str, method: str = 'hybrid', top_k: int = 10) -> list[dict]`. `method` is one of `tfidf`, `bm25`, `dense`, `hybrid`. |
| `deployment/rag.py` | `async generate_answer(query: str, history: list = None, top_k: int = 5) -> dict`, returns `{'answer', 'retrieved_products', 'grounded'}`. |
| `deployment/router.py` | `async route_query(query: str, session_id: str) -> dict`: classify, then call `retrieve` or `generate_answer`. Returns `{'intent', 'confidence', 'result'}`. |
| `deployment/api.py` | FastAPI app exposing `POST /query`. |
| `monitoring/query_logger.py` | Logs every query with its intent, confidence and latency. |
| `monitoring/drift.py` | Detects changes in intent mix or confidence and flags queries to relabel. |
| `db/schema.sql` | `CREATE TABLE products (...)`, using the schema in `context.md`. |
| `tests/test_*.py` | One test file per step. Use pytest. |

## Setup

```bash
python -m venv .venv && source .venv/bin/activate      # Windows: .venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env
pytest
```

## Adding training data

Add rows to `data/labeled/intent_queries.csv` (`query,label`, with label `search` or `info`), then retrain.
The 20 seed rows are only a starting point. The product IDs in `relevance_judgments.csv` and
`benchmark_queries.json` are examples. Replace them with IDs from the real catalog.
