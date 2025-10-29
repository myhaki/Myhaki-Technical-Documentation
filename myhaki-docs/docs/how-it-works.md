# Developer Docs

Comprehensive documentation for developers building, testing, and deploying the MyHaki platform.  
Learn about architecture, workflows, integrations, and best practices for reliable legal tech.

---

## <span style="font-weight: 600; color: #A87352;">System Architecture</span>

[![System Architecture](images/system.png)](images/system.png)

- **Frontend:** Next.js + Tailwind CSS (LSK dashboard, responsive informational website)
- **Mobile:** Android app (MVVM, Jetpack Compose, Koin DI, Retrofit)
- **Backend:** Django REST Framework (Python), internal REST APIs
- **Database:** PostgreSQL with pgvector for AI embeddings
- **Integrations:** 
  - TranslatePlus (form translation to English for AI)
  - LocationIQ (police station geocoding)
  - Agentic AI (Python + Google ADK/Gemini for case classification/prioritization)
  - Celery + Redis (automated case assignment)
- **Testing:** Unit & integration tests (Postman, pytest, JUnit, Compose Test)
- **CI/CD:** GitHub Actions automates tests and deployment (Heroku for API, Vercel for dashboard/web, Google Cloud for FastAPI agent)
- **Branding:** All UI follows MyHaki brand guidelines (see [Brand Guideline](images/brand-guideline.png))

---


## <span style="font-weight: 600; color: #A87352;">.env Usage & Secrets Management</span>

- Sensitive values (API keys, secrets) are stored in `.env` files and never committed to source control.
- Use environment variables for config overrides (dev, staging, production).
- Django loads `.env` via [`python-dotenv`](https://github.com/theskumar/python-dotenv).
- Next.js uses `process.env.VAR_NAME`.
- FastAPI agent uses Google Cloud Secret Manager or `.env`.

---

## <span style="font-weight: 600; color: #A87352;">Automated Testing</span>

- **Backend:** Unit and integration tests for models, views, APIs using `pytest`, `unittest`, and Postman for endpoint validation.
- **Frontend:** Component and API tests using Jest and React Testing Library.
- **Android:** Unit tests (JUnit, MockK), UI tests (Compose Test).
- **Test coverage:** All codebases report coverage in CI.
- **Commands:**
  ```bash
  # Backend
  python manage.py test
  pytest --cov=backend

  # Frontend
  npm test
  npm run coverage

  # Android
  ./gradlew test
  ./gradlew connectedAndroidTest
  ```

---


## <span style="font-weight: 600; color: #A87352;">Branching & PR Workflow</span>

- Feature branches: `feature/xxx`
- Bugfix branches: `bugfix/xxx`
- PR reviews required for all merges to `main`
- Descriptive PR titles, linked issues (`Fixes #issue_num`)
- Checklist: All tests pass, lint passes

---

## <span style="font-weight: 600; color: #A87352;">Directory Structure</span>

- `frontend/` — Next.js dashboard and informational website
- `backend/` — Django REST API
- `android/` — Android app (Compose, MVVM, Koin)
- `agent/` — FastAPI microservice (Agentic AI, Google ADK/Gemini)
- `docs/` — Documentation, brand guidelines
- `tests/` — All tests and testing utilities

---
