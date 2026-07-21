# Al Noor Hospital Management System

> A full-featured hospital operations application built with Oracle APEX 26.1, designed to replace paper forms and spreadsheets with a unified, database-driven internal system.

---

## Overview

I built this system for Al Noor Hospital — a mid-sized hospital that was tracking patients, doctors, appointments, prescriptions, and admissions entirely on paper and in spreadsheets. My goal was to design and deliver the first working version of a digital operations platform that hospital staff can actually use day-to-day.

The application covers every core workflow: registering patients, managing doctors, booking appointments, recording clinical visits, issuing prescriptions, managing ward admissions, and giving management a live dashboard of hospital KPIs. I enforced 12 business rules directly inside the application and validated all critical fields so bad data can never enter the system.

- **Platform:** Oracle APEX 26.1 (Application ID 103)
- **Schema:** `ALNOUR-DB`
- **Pages:** 18 | 

---

## Tech Stack

| Layer | Technology |
|---|---|
| Application framework | Oracle APEX 26.1 |
| Database | Oracle Database |
| Business logic | PL/SQL (processes, validations, dynamic actions) |
| Reporting | APEX Interactive Reports, Charts |

---

## Database Schema

I designed 13 tables split into two groups.

### Lookup / Reference Tables

| Table | Purpose |
|---|---|
| `DEPARTMENTS` | Hospital departments (Cardiology, Pediatrics, Emergency, etc.) with floor number and status |
| `DOCTOR_SPECIALTIES` | Doctor specialization codes (Cardiologist, Surgeon, Pediatrician, etc.) |
| `MEDICINE_CATEGORIES` | Medicine category codes (Antibiotic, Painkiller, Diabetes, etc.) |
| `APPOINTMENT_STATUSES` | Valid appointment statuses: Scheduled, Completed, Cancelled, No Show |

### Core Tables

| Table | Purpose |
|---|---|
| `PATIENTS` | Full patient profile — Civil ID, name, gender, DOB, blood group, emergency contacts |
| `DOCTORS` | Doctor records linked to department and specialty, with consultation fee |
| `MEDICINES` | Medicine master data with current stock, reorder level, and category |
| `APPOINTMENTS` | Patient–doctor appointment bookings with date, time, status, and reason |
| `PATIENT_VISITS` | Actual visit records after an appointment — symptoms, diagnosis, notes, follow-up |
| `PRESCRIPTIONS` | Prescription header linked to a visit, patient, and doctor |
| `PRESCRIPTION_ITEMS` | Individual medicines within a prescription — dosage, frequency, duration, instructions |
| `ROOMS` | Hospital rooms with type (General, Private, ICU), daily rate, and occupancy status |
| `ADMISSIONS` | Patient admission records linked to patient, doctor, and room — tracks discharge too |

---

## Application Pages

I built 9 functional screens covering every required workflow.

| Page | Name | What I built |
|---|---|---|
| 1 | Home Dashboard | KPI cards (total patients, today's appointments, active doctors, current admissions, low-stock medicines) + 5 charts: appointments by department, patients by gender, medicine stock status, admissions by room type, monthly patient visits |
| 2 | Patient Management | Full CRUD — add, edit, delete patients. Search by name, Civil ID, and mobile. Computed age field. Unique Civil ID validation. |
| 3 | Doctor Management | Full CRUD with department and specialty select lists. Shows doctor name, department, specialty, consultation fee, and status in the report. |
| 4 | Medicine Management | Full CRUD with category lookup. Conditional formatting highlights medicines below reorder level. Separate low-stock report. |
| 5 | Appointment Booking | Patient and doctor popup LOVs. Date picker with past-date prevention. Dynamic action to auto-display doctor's department and specialty on selection. |
| 6 | Patient Profile | Master-detail: one patient at the top with three linked child reports — appointment history, visit history, and admission history. |
| 7 | Visit & Prescription | Three-level master-detail connecting patient visits → prescriptions → prescription items. I can open a visit, create a prescription, and add as many medicines as needed. |
| 8 | Clinical Visit Workspace | Complex multi-table screen. Opens from an appointment and automatically pulls in patient summary, appointment summary, and doctor info. I enter symptoms, diagnosis, notes, and the prescription all from one page. Previous visits for the same patient are shown below. |
| 9 | Patient Admission | Admits a patient to an available room. A dynamic process flips the room status to Occupied on admission and back to Available on discharge. Validates that occupied rooms cannot be double-assigned. |

---

## Reports

I created four dedicated reports, each with filters so staff can drill down quickly.

| Report | Key Columns | Filters |
|---|---|---|
| Appointment Report | Appointment ID, patient, doctor, department, date, time, status, reason | Date range, department, doctor, status |
| Patient Visit Report | Patient, doctor, visit date, symptoms, diagnosis, follow-up date | Doctor, department, visit date |
| Medicine Stock Report | Medicine, category, current stock, reorder level, stock status (Normal / Low Stock) | — |
| Admission Report | Patient, doctor, room number, room type, admission date, discharge date, status | — |

---

## APEX Features I Used

- **Interactive Reports** — patients, doctors, appointments, visits, medicines, admissions
- **Forms** — all CRUD operations across every core table
- **Master-Detail Pages** — patient profile (1 master : 3 detail) and visit/prescription (3-level)
- **Select Lists & Popup LOVs** — all relational fields use proper lookup components, never free text
- **Validations** — mandatory fields, Civil ID uniqueness, past-date prevention, stock limits
- **Dynamic Actions** — auto-populate doctor details on appointment form, update room status on admission/discharge, conditional region display
- **Charts** — bar, pie, and line charts on the dashboard
- **KPI Cards** — five metric cards on the home page backed by SQL COUNT queries
- **SQL Joins** — reports and the clinical workspace combine 3–6 tables in a single query
- **Conditional Formatting** — low-stock medicines highlighted in red on the medicine report
- **Page Navigation** — link from the appointment report directly into the Clinical Visit Workspace

---

## Business Rules Enforced

I built these 12 rules directly into APEX validations and PL/SQL processes — the application rejects bad data rather than trusting users to get it right.

1. Patient Civil ID must be unique across the system.
2. Patient full name and mobile number are mandatory.
3. Every doctor must belong to a department.
4. Every doctor must have a specialty.
5. Appointments cannot be created for a past date or time.
6. An appointment must have both a patient and a doctor selected.
7. Every patient visit must be linked to an existing appointment.
8. Every prescription item must be linked to a prescription.
9. Medicine stock cannot go below zero.
10. A room cannot be assigned to a new admission if it is already Occupied.
11. A patient marked as Discharged must have a discharge date entered.
12. Low-stock medicines are flagged clearly in both the dashboard and the stock report.

---

## Repository Contents

```
repo/
├── ALNOOR_SCHEMA.sql          # All 13 table definitions — run this first
├── ALNOOR_Full_Data_Load.sql  # Sample data: 20+ patients, 10+ doctors, 5+ departments,
│                              #   20+ medicines, 30+ appointments, 10+ visits & prescriptions
└── f103.sql                   # Full Oracle APEX application export (App ID 103, APEX 26.1)
```

---

## How to Deploy

Run the scripts in this exact order using a SQL client (SQL Developer, SQL*Plus, or the APEX SQL Workshop) connected as the schema owner.

**Step 1 — Create the schema objects**
```sql
@ALNOOR_SCHEMA.sql
```

**Step 2 — Load the sample data**
```sql
@ALNOOR_Full_Data_Load.sql
```

**Step 3 — Import the APEX application**

In Oracle APEX:
1. Go to **App Builder → Import**
2. Upload `f103.sql`
3. Set the parsing schema to `ALNOUR-DB`
4. Click **Install Application**

The application will be available immediately after import. Log in and navigate to the Home Dashboard to verify the KPI cards populate correctly.
