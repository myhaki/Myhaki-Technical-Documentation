# Quality Assurance Process



## <span style="font-weight:600; color:#A87352;">MyHaki QA Philosophy</span>

MyHaki is committed to **reliable, secure, and accessible legal technology**. Our QA process ensures every system component—from backend APIs to mobile onboarding flows—meets the highest standards for justice sector digital platforms.

---

## <span style="font-weight:600; color:#A87352;">Testing Strategy</span>

- **Unit Testing**
  - **Backend:** Django REST Framework, pytest/unittest for all models, serializers, and API endpoints.
  - **Android App:** JUnit & MockK for ViewModels, repositories, and API integration; Jetpack Compose UI tested with `createComposeRule()`.
  - **Frontend:** Jest, React Testing Library for Next.js dashboard.
- **Integration Testing**
  - End-to-end flows tested: case submission, AI filtration, assignment, CPD, password reset, onboarding.
  - API contract validation with Postman for all endpoints.
  - External integrations (TranslatePlus, LocationIQ, Gemini, Celery/Redis) are mocked and tested with synthetic and real legal datasets.
- **Performance & Load Testing**
  - JMeter for backend scalability (1,000 concurrent cases/month).
  - SQL query profiling during development to verify migration, data integrity, and attribute updates.
- **Security & Compliance**
  - OWASP ZAP for vulnerability scanning.
  - Manual checks for KDPA and Legal Aid Act compliance.
  - Data encryption (AES-256/TLS 1.3) and anonymization verified.
- **Regression & User Acceptance Testing (UAT)**
  - Automated regression runs on every CI/CD pipeline.
  - Manual UAT with LSK admins/lawyers before major releases.
  - Sprint QA tracked in ClickUp: planning, retrospectives, defect tracking, sign-off.

---

## <span style="font-weight:600; color:#A87352;">Test Data Management</span>

- **Real & Synthetic Data**
  - Synthetic legal cases, lawyers, and detainee profiles generated for load and integration.
  - Real court reports, penal codes, and anonymized Kenya Law JSON used for model and API validation.
- **Chunking & Embedding**
  - Text chunking with LangChain’s RecursiveCharacterTextSplitter for RAG system tests.
  - Embedding migration tested from ChromaDB to pgvector for scalability and storage efficiency.

---

## <span style="font-weight:600; color:#A87352;">Continuous Integration & Code Review</span>

- **CI/CD Pipeline**
  - GitHub Actions automates lint, unit/integration tests, and deployments for all codebases (backend, Android, dashboard, AI agent).
  - FastAPI agent deployment reliability tested via pipeline automation.
- **Code Review**
  - All PRs require 2+ reviews and passing checks for lint, test coverage (>90%), build, and documentation.
  - Feature, bugfix, and documentation branches tracked with semantic versioning (MVP, Phase 2 roadmap).
- **Defect Tracking**
  - ClickUp for defect logging, regression, and sprint QA sign-off.

---

## <span style="font-weight:600; color:#A87352;">Monitoring, Logging & Alerting</span>

- **Monitoring Tools**
  - Sentry for error tracking on frontend/backend.
  - Heroku, Vercel, and Google Cloud logs for real-time monitoring.
  - Custom dashboard visualizes case assignment rates, API latency, and AI accuracy.
- **Alerting**
  - Slack/Email alerts for:
    - >5% API error rate
    - AI accuracy drops below threshold
    - Unassigned cases after 48h

---

## <span style="font-weight:600; color:#A87352;">Release Criteria & Success Metrics</span>

- >95% pass rate on critical tests
- F1 > 0.85 for AI classification
- 99.9% uptime
- 80%+ user satisfaction in UAT
- All tests, lint, and security/compliance checks pass before release

---

## <span style="font-weight:600; color:#A87352;">References & Live Docs</span>

- [Swagger/OpenAPI Live API Docs](https://myhaki-3e53581dd62e.herokuapp.com/redoc/)
- [Postman Collection](https://documenter.getpostman.com/view/45699975/2sB3HqHJX4)
- [ClickUp QA Board](https://app.clickup.com/) (internal)
- [MyHaki Product Documentation](MyHaki-PRD.pdf)

---

<style>
body { font-family:'Poppins',serif; }
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