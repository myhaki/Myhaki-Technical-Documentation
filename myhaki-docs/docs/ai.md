# MyHaki Agent AI Integration

MyHaki Agent is an AI-powered legal assistant designed to help users access, understand, and summarize legal information with ease. It uses a Retrieval-Augmented Generation (RAG) pipeline that combines Supabase vector search and Google Gemini to deliver accurate, context-aware answers from legal documents. The system processes user queries by retrieving relevant chunks from a vector database and generating responses grounded in Kenyan legal texts, ensuring reliability for pre-trial detainees and legal professionals.

---

# AI Models


### Legal-BERT  
Specialized BERT model for legal text understanding and embeddings generation (`nlpaueb/legal-bert-base-uncased`).  

**How to Use:**  
<div class="api-block">
<pre class="api-dark">
from transformers import AutoTokenizer, AutoModel
import torch

tokenizer = AutoTokenizer.from_pretrained("nlpaueb/legal-bert-base-uncased")
model = AutoModel.from_pretrained("nlpaueb/legal-bert-base-uncased")

inputs = tokenizer(text, return_tensors="pt", padding=True, truncation=True, max_length=512)

with torch.no_grad():
outputs = model(**inputs)

embeddings = outputs.last_hidden_state.mean(dim=1).numpy().tolist() 

</pre>
</div>

**Apply:** Embed legal chunks (e.g., court judgments) and store in pgvector; query similarity for RAG retrieval.

---
### all-mpnet-base-v2  
all-mpnet-base-v2 :- is a type of sentence transformer model used to convert detainee case descriptions into numerical embeddings, enabling the AI agent to understand their meaning. 

**How to Use:**  
<div class="api-block">
<pre class="api-dark">
from google.generativeai import GenerativeModel, configure
from sentence_transformers import SentenceTransformer

@functools.lru_cache(maxsize=1)
def get_embedding_model():
    return SentenceTransformer("sentence-transformers/all-mpnet-base-v2")

def embed_query(query: str) -> list[float]:
    model = get_embedding_model()
    return model.encode(query).tolist()

def retrieve_relevant_chunks(query: str, top_k: int = 5):
    query_embedding = embed_query(query)
    response = supabase.rpc(
        "match_legal_embeddings",
        {"query_embedding": query_embedding, "match_count": top_k}
    ).execute()
    docs = [row["document"] for row in response.data] if response.data else []
    metas = [row["metadata"] for row in response.data] if response.data else []
    return docs, metas 

</pre>
</div>


**Apply:** convert detainee case descriptions into numerical embeddings.



### RAG Pipeline  
Retrieval-Augmented Generation for context-aware case processing (LangChain + Vector Search).  

**How to Use:**  
<div class="api-block">
<pre class="api-dark">
from google.generativeai import GenerativeModel, configure
from sentence_transformers import SentenceTransformer



api_key = os.getenv("GEMINI_API_KEY")
if not api_key:
    raise ValueError("Missing GEMINI_API_KEY in .env file")
configure(api_key=api_key)
gemini_model = GenerativeModel('gemini-2.5-flash')

SUPABASE_URL = os.getenv("SUPABASE_URL")
SUPABASE_KEY = os.getenv("SUPABASE_KEY")
supabase = create_client(SUPABASE_URL, SUPABASE_KEY)

VECTOR_SIZE = int(os.getenv("VECTOR_SIZE", 768))

@functools.lru_cache(maxsize=1)
def get_embedding_model():
    return SentenceTransformer("sentence-transformers/all-mpnet-base-v2")
</pre>
</div>

**Apply:** Chain with Gemini for JSON outputs (case_type, urgency).

---

### Gemini LLM  
Advanced language model for classification and reasoning (Gemini 2.5 Flash).  

**How to Use:** 
<div class="api-block">
<pre class="api-dark"> 
from google.generativeai import GenerativeModel, configure
import json

configure(api_key="your_api_key")
model = GenerativeModel('gemini-2.5-flash')

prompt =""" You are a legal assistant AI.
Analyze the following context and query, then return ONLY the following fields in JSON format:
- case_type: inferred case type(s)
- urgency: classify as "urgent" or "normal"
- reasoning: a short explanation of why you classified it this way
 Context:
{context}

Metadata:
{metadata_context}

Query:
{query} 

Respond strictly in JSON with keys: case_type, urgency, reasoning. Do not include anything else.
"""

    response = gemini_model.generate_content(prompt)
    raw_text = response.text.strip()

    if raw_text.startswith("```"):
        raw_text = raw_text.strip("`")
        if raw_text.lower().startswith("json"):
            raw_text = raw_text[4:].strip()

    try:
        result = json.loads(raw_text)
    except json.JSONDecodeError:
        result = {
            "case_type": None,
            "urgency": None,
            "reasoning": raw_text
        }

    if trial_date:
        result["urgency"] = determine_urgency_from_date(trial_date)

    return result"
</pre>
</div>

**Apply:** Use in RAG for structured outputs; override urgency with date rules (e.g., trial <15 days = "urgent").

---

## Data Pipeline Architecture

**Ingestion → Processing → Delivery**

### Pipeline Stages (Detailed Usage):

- **Collection:** Raw case data intake via API or upload (e.g., CSV/PDF/JSON); use Pandas for initial parsing:  
<div class="api-block">
<pre class="api-dark">
import pandas as pd
df = pd.read_csv("legal_cases.csv")

</pre>
</div>

- **Cleaning:** Data normalization with regex/BeautifulSoup for text extraction; remove duplicates, handle missing fields.

- **Chunking:** Text segmentation (page/sentence/character-level) using LangChain:  
<div class="api-block">
<pre class="api-dark">
from langchain.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=50)
chunks = splitter.split_text(text)


</pre>
</div>

- **Embedding:** Vector generation with Legal-BERT (as above); batch process for efficiency.

- **Storage:** Insert into Supabase pgvector for similarity searches:  
<div class="api-block">
<pre class="api-dark">
from supabase import create_client

client = create_client(SUPABASE_URL, SUPABASE_KEY)
client.table("embeddings").insert({"id": chunk_id, "embedding": embedding}).execute() 


</pre>
</div>

---

## API Query Endpoint (FastAPI Example) 
<div class="api-block">
<pre class="api-dark"> 
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def health_check():
    return {"status": "ok"}

class CaseInput(BaseModel):
    case_description: str
    trial_date: str  


@app.post("/predict/")
def predict_case(data: CaseInput):
    query = f"Case: {data.case_description}"
    results = run_rag(query, trial_date=data.trial_date)
    return {
        "input": {
            "case_description": data.case_description,
            "trial_date": data.trial_date
        },
        "prediction": results
    }
</pre>
</div>
---
# Technologies

## Technology Stack:

- **LangChain:**  
  Framework for RAG apps; chain retrievers/LLMs.  
  Example: Use `RetrievalQA` to integrate Supabase vectorstore with Gemini.  
  <div class="api-block">
<pre class="api-dark">
qa_chain.run("Classify case: [query]") 
</pre>
</div>

- **pgvector:**  
PostgreSQL vector similarity extension; query embeddings efficiently.  
Example:  
<div class="api-block">
<pre class="api-dark">
SELECT * FROM embeddings ORDER BY embedding <=> query_embedding LIMIT 5;
</pre>
</div>

Executed via Supabase RPC for scalable searches on legal chunks.

- **Hugging Face:**  
Model hub and transformers library.  
Example: Load Sentence Transformers for quick embedding generation.  
<div class="api-block">
<pre class="api-dark">
from sentence_transformers import SentenceTransformer
import functools

@functools.lru_cache()
def get_embedding_model():
return SentenceTransformer("sentence-transformers/all-mpnet-base-v2")
</pre>
</div>
---

# Deployment Process

## AI Agent Deployment (FastAPI)

- **Platform:** Google Cloud Platform (Cloud Run) for scalable, serverless hosting. Selected after Heroku’s space limits to manage variable LSK loads.

- **Build:** Dockerized FastAPI microservice providing endpoints, using stateless containers for fast scaling.

- **Environment Variables:** Securely managed in Google Cloud Secret Manager (e.g., `GEMINI_API_KEY`, `SUPABASE_URL`); loaded in code with `os.getenv` to avoid exposure.

- **Scaling:** Auto-scales via Cloud Run with 2Gi memory and port 8080; handles spikes from user submissions.

- **Monitoring:** Google Cloud Monitoring for logs and alerts (e.g., >5s latency alerts); health check implemented with `curl` for uptime.

---

## Deployment Steps (Concise Usage):

- **Push to Feature Branches/PRs:** GitHub triggers reviews; merging to `main` activates deployment pipeline.

- **GitHub Actions CI/CD:** Verifies tests and lint, builds Docker image.

**Sample YAML snippet:**
<div class="api-block">
<pre class="api-dark">
name: Deploy FastAPI App from Docker Hub to Cloud Run

on:
  push:
    branches:
      - feature/myhaki_predictive_agent  

jobs:
  deploy:
    name: Deploy to Cloud Run
    runs-on: ubuntu-latest
    timeout-minutes: 30

    env:
      GEMINI_API_KEY: ${{ secrets.GEMINI_API_KEY }}
      SUPABASE_DB_URL: ${{ secrets.SUPABASE_DB_URL }}
      SUPABASE_URL: ${{ secrets.SUPABASE_URL }}
      SUPABASE_KEY: ${{ secrets.SUPABASE_KEY }}
      VECTOR_SIZE: ${{ secrets.VECTOR_SIZE }}
      IMAGE_NAME: dockerhub account user name/fastapi-rag  
      REGION: europe-west1
      SERVICE_NAME: myhaki-agent

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Authenticate to Google Cloud
        uses: google-github-actions/auth@v2
        with:
          credentials_json: '${{ secrets.GCP_SA_KEY }}'

      - name: Set up gcloud CLI
        uses: google-github-actions/setup-gcloud@v2

      - name: Deploy to Cloud Run
        run: |
          gcloud run deploy $SERVICE_NAME \
            --image "docker.io/${IMAGE_NAME}:feature-myhaki_predictive_agent" \
            --project "${{ secrets.GCP_PROJECT_ID }}" \
            --region "$REGION" \
            --allow-unauthenticated \
            --port 8080 \
            --memory 1Gi \
            --set-env-vars GEMINI_API_KEY=${GEMINI_API_KEY},
            SUPABASE_DB_URL=${SUPABASE_DB_URL},
            SUPABASE_URL=${SUPABASE_URL},
            SUPABASE_KEY=${SUPABASE_KEY},
            VECTOR_SIZE=${VECTOR_SIZE}
</pre>
</div>

- **Explanation:** Automates checkout, authentication, and deployment; pulls Docker image from Docker Hub; sets environment variables securely.

- **Merge Triggers Deployment:**  
<div class="api-block">
<pre class="api-dark">
  Build and push to Google Container Registry (GCR):  

docker build -t dockerhub account user name/fastapi-rag:feature-myhaki_predictive_agent .
docker push dockerhub account user name/fastapi-rag:feature-myhaki_predictive_agent

git add.
git commit -m"feat: deploy myhaki agent"
git push -u origin feature/myhaki_predictive_agent
</pre>
</div>





---

## Health and Monitoring

- **Dockerfile Health Check:**
<div class="api-block">
<pre class="api-dark">
HEALTHCHECK CMD curl --fail http://localhost:8080/ || exit 1
</pre>
</div>

- **Explanation:** Periodically probes the FastAPI endpoint for readiness; integrates with Google Cloud Monitoring alerts.

---

