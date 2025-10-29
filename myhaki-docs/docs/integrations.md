# Integrations

Detailed documentation for integrating **MyHaki** with our core APIs and external services—**TranslatePlus, LocationIQ, Agentic AI (Gemini), Celery/Redis, and PostgreSQL/pgvector.**  
This guide covers setup, configuration, API usage, troubleshooting, and law-themed best practices.

---

## <span style="font-weight:600; color:#A87352;">TranslatePlus Integration (Kiswahili → English)</span>

**Overview:**  
TranslatePlus enables real-time translation of Kiswahili case descriptions to English, powering AI-driven case classification.

**Steps:**
1. **API Key Setup**
   - Obtain your TranslatePlus API key from the official portal.
   - Store securely in `.env`:
     ```
     TRANSLATEPLUS_API_KEY=your_api_key
     ```

2. **Sample API Call**
   ```python
   import requests
   response = requests.post(
       "https://api.translateplus.com/translate",
       headers={"Authorization": f"Token {TRANSLATEPLUS_API_KEY}"},
       json={
           "text": "Kukamatwa wakati wa maandamano",
           "source": "sw",
           "target": "en"
       }
   )
   print(response.json()) 
   ```

3. **API Endpoint (Backend):**
   <pre class="api-dark">POST /ai/translate/</pre>

4. **Troubleshooting:**
   - **401 Unauthorized:** Check API key validity.
   - **Slow response:** Use glossary uploads for legal terms.

5. **Best Practices:**
   - Never log API keys.
   - Validate all input/output for legal compliance.

---

## <span style="font-weight:600; color:#A87352;">LocationIQ Integration (Geocoding)</span>

**Overview:**  
Converts police station names into latitude/longitude for proximity-based lawyer assignment.

**Steps:**
1. **API Key Setup**
   - Register at [LocationIQ](https://locationiq.com/) and obtain your API key.
   - Store securely in `.env`:
     ```
     LOCATIONIQ_API_KEY=your_api_key
     ```

2. **Sample API Call**
   ```python
   import requests
   response = requests.get(
       "https://us1.locationiq.com/v1/search.php",
       params={
           "key": LOCATIONIQ_API_KEY,
           "q": "Kisumu Police Station",
           "format": "json"
       }
   )
   coords = response.json()[0]
   print(coords)  # {'lat': '-0.0894', 'lon': '34.7594', ...}
   ```

3. **API Endpoint (Backend):**
   <pre class="api-dark">GET /location/geocode/</pre>

4. **Troubleshooting:**
   - **Empty results:** Check spelling/format of station names.
   - **Rate limit:** Monitor usage and set alerts.

5. **Best Practices:**
   - Use Haversine formula for accurate distance calculations.
   - Store coordinates in PostgreSQL for analytics.

---

## <span style="font-weight:600; color:#A87352;">Agentic AI Integration (Gemini Reasoning Engine)</span>

**Overview:**  
Uses Google Gemini (Agentic AI) to classify cases, prioritize urgency, and match lawyers to detainees.

**Steps:**
1. **API Key Setup**
   - Enable Gemini API in your Google AI account.
   - Store key securely in `.env`:
     ```
     GEMINI_API_KEY=your_api_key
     ```

2. **Sample API Call**
   ```python
   import requests
   headers = {"Authorization": f"Token {GEMINI_API_KEY}"}
   data = {
       "case_description": "Arrested during protest",
       "trial_date": "2025-10-02",
       "location": "Nairobi"
   }
   response = requests.post("https://gemini.googleapis.com/v1/classify", json=data, headers=headers)
   print(response.json())  # {'category': 'criminal', 'priority': 'high'}
   ```

3. **API Endpoint (Backend):**
   <pre class="api-dark">POST /ai/classify-case/</pre>


4. **Best Practices:**
   - Anonymize sensitive data before sending.
   - Log AI decisions for audit and fairness.

---

## <span style="font-weight:600; color:#A87352;">Celery + Redis (Automated Assignment)</span>

**Overview:**  
Automates assignment of cases to lawyers based on location, availability, and expertise.

**Steps:**
1. **Setup**
   - Install Celery and Redis in your backend environment.
   - Configure Celery worker for Django:
     ```python
     # settings.py
     CELERY_BROKER_URL = 'redis://localhost:6379/0'
     CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
     ```

2. **Sample Task**
   ```python
   from myhaki.tasks import assign_case_to_lawyer
   assign_case_to_lawyer.delay(case_id=123)
   ```

3. **API Endpoint:**
   <pre class="api-dark">POST /case-assignments/</pre>


4. **Best Practices:**
   - Monitor assignment queue for overload.
   - Log assignment decisions for transparency.

---

## <span style="font-weight:600; color:#A87352;">PostgreSQL + pgvector (Embeddings & Data Storage)</span>

**Overview:**  
Stores case, lawyer, and embedding data for semantic search and analytics.

**Steps:**
1. **Setup**
   - Ensure PostgreSQL 14+ with pgvector extension installed.
   - Migrate Django models for embedding storage.

2. **Sample Storage**
   ```python
   from pgvector.django import VectorField
   class CaseEmbedding(models.Model):
       embedding_vector = VectorField(dimensions=768)
       case = models.ForeignKey(Case, on_delete=models.CASCADE)
   ```

3. **Best Practices:**
   - Index embeddings for efficient search.
   - Regularly backup database for compliance.

---

## <span style="font-weight:600; color:#A87352;">General Troubleshooting & Lawful Practices</span>

- Use `.env` for all secrets and credentials.
- Never expose API keys or sensitive tokens.
- Encrypt all data in transit and at rest.
- Log all integration actions for audit.
- Monitor API usage to prevent abuse.
- Validate all external data for compliance with Kenya’s Data Protection Act.
- Test integrations in sandbox before production.

---

## <span style="font-weight:600; color:#A87352;">References & Further Reading</span>

- [TranslatePlus API Docs](https://www.translateplus.com/)
- [LocationIQ API](https://locationiq.com/docs)
- [Gemini (Google AI) Docs](https://ai.google.dev/gemini)
- [Celery & Redis Docs](https://docs.celeryproject.org/en/stable/)
- [pgvector for PostgreSQL](https://github.com/pgvector/pgvector)
- [MyHaki API Reference](backend.md)

---

<style>
.api-block {
  background: #A87352;
  border-radius: 10px;
  padding: 18px 18px 10px 18px;
  margin: 18px 0 24px 0;
  overflow-x: auto;
  font-size: 1.09em;
  font-family: 'Fira Mono', 'Consolas', 'monospace';
  color: #fff;
}
.api-dark {
  background: #621616 !important;
  color: #fff !important;
  border-radius: 6px;
  padding: 12px 14px 8px 14px;
  margin: 0;
  font-size: 1em;
  font-family: 'Fira Mono', 'Consolas', 'monospace';
}
</style>