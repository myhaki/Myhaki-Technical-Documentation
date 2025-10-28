# Database Schema


## <span style="font-weight:600; color:#A87352;">Core Tables</span>

1. **users_user**
    - user_id (PK), email, role, first_name, last_name, phone_number, is_deleted, created_at
2. **lawyers_lawyer**
    - lawyer_id (PK), user_id (FK), practice_number, specialization (JSON), latitude, longitude, verified
3. **detainees_detainee**
    - detainee_id (PK), user_id (FK), first_name, last_name, id_number, gender, relation_to_applicant
4. **cases_case**
    - case_id (PK), detainee_id (FK), case_description, predicted_case_type, predicted_urgency_level, trial_date, latitude, longitude, stage, status
5. **case_assignments_caseassignment**
    - assignment_id (PK), case_id (FK), lawyer_id (FK), is_assigned, confirmed_by_applicant/lawyer, reject_reason
6. **cpd_points_cpdpoint**
    - cpd_id (PK), lawyer_id (FK), case_id (FK), points_earned, description
7. **vector_embeddings (pgvector)**
    - embedding_id (PK), case_id (FK), embedding_vector (VECTOR(768)), content, source

---

## <span style="font-weight:600; color:#A87352;">Relationships</span>

- User → Lawyer (1:1)
- User (applicant) → Detainee (1:N)
- Detainee → Case (1:N)
- Case → CaseAssignment (1:N)
- Lawyer → CaseAssignment (1:N)
- Case → CPDPoint (1:1)
- Case → VectorEmbedding (1:N)

---

![Database Schema](images/schema.png)

---
