# Deployment Process



## Frontend Deployment (LSK Dashboard & Informational Site)

- **Platform:** Vercel (Next.js/Tailwind)
- **Branch:** Auto-deployment from `main`
- **Environment Variables:** Managed securely via `.env` in Vercel dashboard
- **Build & Preview:** Each PR triggers preview builds; production deploys on merge to `main`
- **Brand Compliance:** Ensure final builds use MyHaki colors, fonts, and logo ([see Brand Guideline](images/brand-guideline.png))

**Steps**

- Developers create feature branches from the `main` branch to work on updates.  
- Code changes are committed and pushed to the remote repository.  
- A Pull Request (PR) is opened targeting the `main` branch.  
- Vercel automatically generates preview builds for each PR, allowing team members to review the changes live.  
- Reviewers perform tests and validate the UI and functionality.  
- Brand compliance is enforced by verifying the use of MyHaki colors, fonts, and logo according to the brand guidelines.  
- After approval, the PR is merged into `main`.  
- Vercel initiates an automatic production deployment from `main`.  
- Environment variables are injected securely during build time from the Vercel dashboard `.env`.  
- Final validation confirms the live site aligns with brand requirements and functions correctly.  


---

## Mobile Deployment (Android App)

- **Platform:** Google Play Console (Production), Firebase App Distribution (Testing)
- **Build:** Release builds via Gradle (`assembleRelease`), signed with team keystore
- **Environment Variables:** API URLs, keys stored in `local.properties` and encrypted secrets
- **CI:** GitHub Actions runs unit/UI tests on push; releases only after passing all tests
- **Brand Compliance:** All assets, colors, and typography must match MyHaki guidelines


**Steps**

 - Feature development occurs on dedicated branches.  
  - Changes are pushed to GitHub, triggering automated unit and UI tests through GitHub Actions.  
  - Upon successful tests, the Gradle build process is initiated using `assembleRelease`.  
  - The generated APK is signed using the team’s keystore.  
  - Environment variables such as API URLs and keys are securely managed via `local.properties` and encrypted secrets.  
  - Release APKs are distributed through two channels:  
  - Firebase App Distribution for testing builds.  
  - Google Play Console for production release.  
  - Prior to submission, all assets and UI elements are verified to meet MyHaki’s brand compliance standards.  
  - The production APK is published officially on Google Play after all validations. 
---

## Backend Deployment (Django REST API)

- **Platform:** Heroku (Staging & Production)
- **Branch:** Deploy from `main`
- **Environment Variables:** Managed securely in Heroku dashboard; never commit secrets
- **Scaling:** Automatic scaling via Heroku dynos based on demand
- **Rollback:** Previous stable releases can be redeployed via Heroku dashboard


**Steps**

 - Development and code changes happen on feature branches.  
  - GitHub Actions run automated tests and linting upon each push.  
  - Merging into the `main` branch triggers a Heroku deployment.  
  - Heroku pulls the latest code and deploys it to the staging or production environment.  
  - Environment variables and secrets are securely managed within the Heroku dashboard, never committed to code.  
  - Heroku Dynos scale automatically based on live traffic demands.  
  - In the event of deployment issues, rollback to previous stable releases is possible directly from the Heroku dashboard. 

---

## AI Agent Deployment (FastAPI)

- **Platform:** Google Cloud Platform (Cloud Run, GKE, or Compute Engine)
- **Build:** Dockerized FastAPI microservice
- **Environment Variables:** Managed in Google Cloud Secret Manager
- **Scaling:** Automatic scaling via Google Cloud Run/GKE
- **Monitoring:** Use Google Cloud Monitoring for logs, alerts, and uptime

**Steps**

 - Developers push changes on feature branches and open PRs.  
  - GitHub Actions verify code quality, run tests, lint, and build Docker images upon each push.  
  - After tests are successful, merging into `main` triggers deployment pipelines.  
  - Docker images are pushed to Google Container Registry.  
  - Automatic deployment happens on Google Cloud Run, GKE, or Compute Engine.  
  - Environment variables and secrets are managed securely in Google Cloud Secret Manager.  
  - Monitoring is handled through Google Cloud Monitoring for logs, uptime, and alert notifications to maintain service reliability.  

---

## CI/CD Pipeline

- **Tool:** GitHub Actions
- **Pre-Deployment:** All codebases run tests, build, and lint checks before deploy
- **Automation:** Automatic deployment on merge to `main`
- **Status:** Build/test status visible in PRs and repository dashboard
- **Security:** Secrets scanned before deploy, API keys never exposed

**Steps**

  - Code pushed to feature or `main` branches triggers GitHub Actions workflows.  
  - Workflows run tests, build processes, and static analysis (linting).  
  - Secrets and API keys are scanned to prevent leaks before deployment.  
  - Only code passing all validations can be merged into the `main` branch.  
  - Merging to `main` automatically starts deployment of respective components.  
  - Real-time build and deployment statuses are displayed on pull requests and repository dashboards.  
  - Failures in tests or security scans immediately block deployments, ensuring only stable code is delivered. 

---

## Deployment Flow Diagram

![Deployment Architecture](images/deployment-diagram.png)

---

## Release Checklist

- [x] All tests passing (unit, integration, UI)
- [x] Linting with zero errors
- [x] Secrets verified (no `.env` in repo)
- [x] Brand assets up to date
- [x] Team notified of release

---

## References

- [Brand Guideline](images/brand-guideline.png)
- [Code Standards](code-standards.md)
- [QA Process](qa-process.md)
- [API Reference](api-reference.md)

---

<style>
  summary {
    cursor: pointer;
    font-weight: bold;
    color: #A87352;
    padding: 8px;
    border: 1px solid #621616;
    border-radius: 5px;
    margin-bottom: 5px;
    background-color: #f9f5f2;
  }
  details[open] summary {
    background-color: #621616;
    color: #A87352;
  }
  details {
    border: 1px solid #621616;
    border-radius: 7px;
    padding: 10px 15px;
    margin-bottom: 15px;
    background-color: #fff8f5;
  }
  li {
    margin-bottom: 6px;
    color: #621616;
  }
</style>

