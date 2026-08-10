# DATABASE SCHEMA
Version: 1.0

---

# Purpose

This document defines the database structure for storing all representative data in Janavani.

The schema is designed for:

• PostgreSQL
• FastAPI backend
• Scalable querying
• AI + RAG compatibility

---

# Design Principles

1. Normalized Structure
2. Evidence-first design
3. No data without source
4. Modular expansion
5. AI-friendly retrieval

---

# Core Tables

## 1. representatives

```
id (PK)
full_name
office_type (MP / MLA)
house
state
constituency
party
current_status
term_start
term_end
photo_url
photo_status
created_at
updated_at
```

---

## 2. elections

```
id (PK)
representative_id (FK)

year
type
votes_received
vote_percent
winning_margin
runner_up
result

affidavit_url
eci_url

created_at
```

---

## 3. legislative_activity

```
id (PK)
representative_id (FK)

attendance_percent
questions_asked
debates
bills_introduced
private_member_bills
committee_meetings
zero_hour
special_mentions
calling_attention

last_updated
```

---

## 4. committees

```
id (PK)
representative_id (FK)

committee_name
role
start_date
end_date
status
```

---

## 5. development_funds

```
id (PK)
representative_id (FK)

fund_type (MPLADS / MLA)

allocation
released
spent
balance

projects_total
projects_completed
projects_ongoing
projects_pending

last_updated
```

---

## 6. assets

```
id (PK)
representative_id (FK)

election_year
declared_assets
declared_liabilities
annual_income
profession
education
criminal_cases

source_url
```

---

## 7. official_links

```
id (PK)
representative_id (FK)

website
lok_sabha_profile
assembly_profile
eci_profile

facebook
twitter
instagram
youtube

official_email
office_address
```

---

## 8. evidence

This is the MOST IMPORTANT table.

```
id (PK)

entity_type
entity_id

field_name

source_name
source_url

retrieved_date
last_updated

verification_status
confidence

notes
```

---

# Relationships

```
Representative

↓

Elections

↓

Assets

↓

Legislative Activity

↓

Committees

↓

Funds

↓

Links

↓

Evidence
```

---

# Critical Rule

NO FIELD is trusted unless linked to evidence.

---

# Indexing Strategy

```
INDEX representative_id

INDEX constituency

INDEX office_type

INDEX year

INDEX source_name
```

---

# Future Tables

To be added later:

• constituencies
• projects
• departments
• manifesto_promises
• budgets
• complaints
• rti_requests

---

# AI Compatibility

This schema supports:

• Vector DB integration
• RAG retrieval
• Structured querying
• Evidence-backed responses

---

End of Version 1.0
