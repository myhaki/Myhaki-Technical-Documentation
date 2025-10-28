# Platform Features

Explore the core features of **MyHaki**, designed to streamline access to legal aid for pretrial detainees, empower lawyers, and enable the Law Society of Kenya (LSK) to monitor and coordinate justice delivery.

---

## <span style="font-weight: 600; color: #A87352;">1. Case Application & Tracking (Android App)</span>

- <span style="color: #232f3e;"><b>Accessible Case Submission:</b></span>  
  Detainees and their families submit case applications via the Android app, entering personal details, case descriptions, and supporting documents.
- <span style="color: #232f3e;"><b>Eligibility & Verification:</b></span>  
  The app guides users through basic eligibility checks and captures proof for legal aid.
- <span style="color: #232f3e;"><b>Status Updates & Notifications:</b></span>  
  Users receive real-time notifications for every case event—verification, assignment, in-progress, and resolution.
- <span style="color: #232f3e;"><b>Secure Messaging:</b></span>  
  Communicate directly with assigned lawyers inside the app.
- <span style="color: #232f3e;"><b>Low-End Device Support:</b></span>  
  Optimized for informal settlements and low-income users.

![Case Submission Screenshot](images/case-submission.png)

---

## <span style="font-weight: 600; color: #A87352;">2. Lawyer Android App Dashboard</span>

- <span style="color: #232f3e;"><b>Assigned Case Management:</b></span>  
  Lawyers access a dashboard showing all cases assigned to them, updated in real time.
- <span style="color: #232f3e;"><b>Case Details & Progress Reporting:</b></span>  
  View case specifics, update statuses, submit progress reports, and communicate securely with clients.
- <span style="color: #232f3e;"><b>CPD Points & Rewards:</b></span>  
  Track Continuing Professional Development points earned via pro bono work.
- <span style="color: #232f3e;"><b>Real-Time Notifications:</b></span>  
  Receive alerts about new cases, deadlines, and messages.

![Lawyer Dashboard Screenshot](images/lawyer-dashboard.png)

---

## <span style="font-weight: 600; color: #A87352;">3. AI-Assisted Case Filtration & Assignment</span>

- <span style="color: #232f3e;"><b>Agentic AI Filtration:</b></span>  
  AI automatically filters and prioritizes cases (civil, criminal, urgency) using NLP and Kenyan legal data.
- <span style="color: #232f3e;"><b>TranslatePlus Integration:</b></span>  
  Kiswahili case descriptions are translated to English, improving classification accuracy.
- <span style="color: #232f3e;"><b>Geolocation Matching (LocationIQ):</b></span>  
  Police station addresses are geocoded, enabling proximity-matching between detainees and lawyers.
- <span style="color: #232f3e;"><b>Automated Assignment:</b></span>  
  Celery + Redis automate lawyer assignment based on specialty, location, and workload, using Haversine formula for distance calculations.
- <span style="color: #232f3e;"><b>Equitable Distribution:</b></span>  
  The system prevents lawyer overload and ensures fair case allocation.


---

## <span style="font-weight: 600; color: #A87352;">4. LSK Admin Dashboard (Web)</span>

- <span style="color: #232f3e;"><b>Oversight & Case Monitoring:</b></span>  
  LSK administrators view all active cases, assignments, and lawyer activities.
- <span style="color: #232f3e;"><b>Reporting & Analytics:</b></span>  
  Monthly reports, dashboards, and charts show case volumes, resolution times, lawyer performance, and assignment metrics.
- <span style="color: #232f3e;"><b>Reward Management:</b></span>  
  Allocate and manage CPD points for lawyer activities.
- <span style="color: #232f3e;"><b>Compliance & Audit Logging:</b></span>  
  Track all actions for regulatory compliance and transparent governance.

![Admin Dashboard Screenshot](images/lsk-dashboard.png)

---

## <span style="font-weight: 600; color: #A87352;">5. Secure Authentication, Communication & Data Management</span>

- <span style="color: #232f3e;"><b>Role-Based Access Control:</b></span>  
  Segregated interfaces and permissions for detainees, lawyers, and LSK admins.
- <span style="color: #232f3e;"><b>Encrypted Data Storage:</b></span>  
  All sensitive data (case records, messages, tokens) is encrypted at rest and in transit.
- <span style="color: #232f3e;"><b>Audit Trails:</b></span>  
  Every user action and system event is logged for analytics and compliance.

---





<style>
.api-block {
  background: #621616;
  border-radius: 10px;
  padding: 18px 18px 10px 18px;
  margin: 18px 0 24px 0;
  overflow-x: auto;
  font-size: 1.09em;
  font-family: 'Fira Mono', 'Consolas', 'monospace';
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