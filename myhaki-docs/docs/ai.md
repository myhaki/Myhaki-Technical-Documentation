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

### RAG Pipeline  
Retrieval-Augmented Generation for context-aware case processing (LangChain + Vector Search).  

**How to Use:**  
<div class="api-block">
<pre class="api-dark">
from langchain.vectorstores import Chroma
from langchain.embeddings import HuggingFaceEmbeddings

embeddings = HuggingFaceEmbeddings(model_name="nlpaueb/legal-bert-base-uncased")
vectorstore = Chroma.from_documents(docs, embeddings)

from langchain.chains import RetrievalQA
from langchain.llms import GoogleGenerativeAI

llm = GoogleGenerativeAI(model="gemini-2.5-flash")
qa_chain = RetrievalQA.from_chain_type(llm, retriever=vectorstore.as_retriever(search_kwargs={"k": 5}))

result = qa_chain.run("Classify this case: [query text]") 

</pre>
</div>

**Apply:** Chain with Gemini for JSON outputs (case_type, urgency); handle multilingual by prompt engineering.

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

prompt = "Analyze query and context for case_type, urgency, reasoning in JSON."
response = model.generate_content(prompt)

result = json.loads(response.text) # Parse for {"case_type": "civil", "urgency": "high", "reasoning": <br>"..."}

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

@app.post("/query")
def query_case(q: str):
results = vectorstore.similarity_search(q, k=5)
return {
"chunks": results,
"metadatas": [r.metadata for r in results]
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

- **Build:** Dockerized FastAPI microservice providing endpoints (e.g., case query/classification), using stateless containers for fast scaling.

- **Environment Variables:** Securely managed in Google Cloud Secret Manager (e.g., `GEMINI_API_KEY`, `SUPABASE_URL`); loaded in code with `os.getenv` to avoid exposure.

- **Scaling:** Auto-scales via Cloud Run with 1Gi memory and port 8080; handles spikes from user submissions.

- **Monitoring:** Google Cloud Monitoring for logs and alerts (e.g., >5s latency alerts); health check implemented with `curl` for uptime.

---

## Deployment Steps (Concise Usage):

- **Push to Feature Branches/PRs:** GitHub triggers reviews; merging to `main` activates deployment pipeline.

- **GitHub Actions CI/CD:** Verifies tests and lint, builds Docker image.

**Sample YAML snippet:**
<div class="api-block">
<pre class="api-dark">
name: Deploy FastAPI App
on:
push:
branches:
- feature/myhaki_predictive_agent

jobs:
deploy:
runs-on: ubuntu-latest
steps:
- uses: actions/checkout@v4
- uses: google-github-actions/auth@v2
with:
credentials_json: '${{ secrets.GCP_SA_KEY }}'
- uses: google-github-actions/setup-gcloud@v2
- run: |
gcloud run deploy myhaki-agent
--image docker.io/${{ secrets.IMAGE_NAME }}:tag
--project "${{ secrets.GCP_PROJECT_ID }}"
--region europe-west1
--allow-unauthenticated
--port 8080
--memory 1Gi
--set-env-vars GEMINI_API_KEY=${{ secrets.GEMINI_API_KEY }},SUPABASE_URL=${{ secrets.SUPABASE_URL }}

</pre>
</div>

- **Explanation:** Automates checkout, authentication, and deployment; pulls Docker image from Docker Hub; sets environment variables securely.

- **Merge Triggers Deployment:**  
<div class="api-block">
<pre class="api-dark">
  Build and push to Google Container Registry (GCR):  
docker tag myhaki-agent gcr.io/project-id/myhaki-agent:tag
docker push gcr.io/project-id/myhaki-agent:tag

</pre>
</div>
- **Auto-Deployment:**  
<div class="api-block">
<pre class="api-dark">
gcloud run deploy --image gcr.io/project-id/myhaki-agent:tag --platform managed --allow-unauthenticated <br>--port 8080
</pre>
</div>


---

## Secrets Access at Runtime

<div class="api-block">
<pre class="api-dark">
from google.cloud import secretmanager

client = secretmanager.SecretManagerServiceClient()
response = client.access_secret_version(name="projects/project-id/secrets/gemini-key/versions/latest")
api_key = response.payload.data.decode("UTF-8")

</pre>
</div>

- **Explanation:** Fetches secrets at runtime to prevent leaks.

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

