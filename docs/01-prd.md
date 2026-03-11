# Product Requirements Document — AllVet

## 1.1 Problem & Vision

### The Problem

An independent veterinarian in Colombia does house calls for horses and pets. She manages her own finances entirely by hand: a Google Sheet per month where she logs income and expenses, manually applies conditional formatting to review totals, creates billing statements (cuentas de cobro) in Canva as PDFs, stores receipts and payment confirmations scattered across folders, and at month-end does manual math to determine net income for obligations (seguridad social, tithe, student loan, household budget).

This workflow is:

- **Time-consuming** — every entry, every document, every summary is manual
- **Error-prone** — mental math, copy-paste between sheets, no validation, prone to memory-failure
- **Scattered** — financial data in Sheets, documents in Drive/folders, billing templates in Canva
- **Hard to review** — no easy way to filter by client, patient, period, or payment status
- **No reminders** — due dates for seguridad social, pending client payments, and other obligations are tracked mentally

### The Vision

A single, mobile-friendly app where she can:

1. **Log income and expenses** quickly from her phone after each house call, on the road, and probably even without internet (might be a future enhancement)
2. **Attach documents** (receipts, payment confirmations, billing statements) linked directly to each transaction, retrievable and previewable directly through the app
3. **See summaries** by week, month, year — and filter by client, patient, or category
4. **Track unpaid clients** at a glance
5. **Calculate net income** and see how much goes to each obligation (seguridad social, tithe, loan, household)
6. **Track obligations** and review that she is up to date with them
7. **Get reminders** for due dates and pending payments, both from clients, and for her obligations
8. **Generate billing statements** from templates with prefilled services, medicines, and travel expenses

The app starts as a personal finance tool for one vet. It's built robust enough to eventually support clinical records, multiple users, and other independent professionals.

### Why Build It (vs. Buy)

- **Custom fit** — tailored exactly to her workflow and mindset, in Spanish, with Colombian tax context (PILA, seguridad social, IBC calculations)
- **No subscription cost** — existing tools like Alegra or Siigo charge monthly fees
- **Learning opportunity** — the developer (you) builds fullstack skills, clean architecture, and product thinking
- **Gift** — it's personal, built with care for a specific person
- **Growth potential** — could expand to other vets, friends, colleagues

---

## 1.2 Target Users

### Primary User: The Independent Vet

- **Name (persona):** Sofía (fiancée)
- **Role:** Independent veterinarian in Colombia
- **Work style:** House calls for horses and small pets (mostly dogs)
- **Tech comfort:** Uses smartphone daily, comfortable with Google Sheets and Canva, not a developer
- **Financial context:** Independent worker — responsible for her own seguridad social (salud 12.5%, pensión 16% on IBC), tithes, student loan payments, and household contributions
- **Pain points:**
  - Manual data entry across multiple tools
  - No unified view of finances
  - Document storage is disorganized
  - Month-end reconciliation is tedious
  - No reminders for obligations
  - Forgets things easily

### Secondary User: The Partner (Future)

- You, reviewing monthly finances together
- Simpler needs — mostly read-only dashboards and month-end review
- Could eventually manage combined household finances

### Future Users (Out of Scope for v1)

- Other independent vets / colleagues
- Other independent professionals (non-vet)
- Multi-user clinics

---

## 1.3 Goals & Success Metrics

### Goals

- Minimize the time it takes for the user to write down expenses and income on a daily basis
- Reduce the amount of errors and missed invoices/expenses/income by encouraging the user to constantly register them, while also having an accurate and easily reviewable bookkeeping
- Enable an organized and easily accessible storage of invoice/expense files that reduces the possibility of losing documents
- Make it possible to produce and compute obligations in a very easy and fast way at the end of each month without the need for manual computations
- Allow the user to keep track of upcoming expenses, obligations, payments and to be notified of them.

### Success Metrics

- The user can see where money is going, what the main sources of income are, and drill into details on any time frame (daily, weekly, monthly, yearly). At a glance, the user knows which obligations need to be paid, which clients haven't paid yet, and how the month is trending
- Reviewing expenses and income at the end of the month is visually appealing, easy to understand, and is there in a matter of few clicks. It also takes 30 mins instead of hours going through documents and a spreadsheet
- Finding a receipt, invoice, or any document takes seconds and few clicks instead of searching into nested folders
- Storing information (data and documents) feels like writing something down on a notebook, it is easy and natural. No friction
- The user is able to register invoices/expenses/income on the fly from a cellphone or computer while on the road or a farm
- The user logs information on a daily basis and reviews finances at the end of the month
- The app generates correct and fast invoices which the user only needs to enter each expense/invoice item in an easy way vs current way of modifying manually a canva template with a table

---

## 1.4 Competitive Analysis

> *To be defined in step 1.4*

### Reference: Existing Tools & APIs

| Tool | What It Does | API Available? | Notes |
|------|-------------|---------------|-------|
| **Alegra** | Cloud accounting for LATAM. Invoicing, reports, facturación electrónica. | Yes — REST API (`api.alegra.com/api/v1/`). Endpoints for accounts, invoices, contacts, payments. [Developer docs](https://developer.alegra.com/) | Closest off-the-shelf fit. Popular in Colombia. Paid plans. |
| **Siigo** | Colombian accounting/ERP. Invoices, purchases, financial reports. | Yes — REST API with JWT auth. Products, customers, invoices, credit notes, journals. [Developer docs](https://developers.siigo.com/docs/siigoapi) | More enterprise-oriented. Good API docs. Requires paid plan. |
| **Wave** | Free accounting for small businesses. Invoicing, expense tracking. | Yes — GraphQL API. Invoices, expenses, reports. [Developer portal](https://developer.waveapps.com/) | Free but no LATAM tax support. Good UX reference. |
| **FreshBooks** | Invoicing + expense tracking + time tracking. | Yes — REST API + Node.js SDK (`@freshbooks/api`). Clients, invoices, expenses, reports. [API docs](https://freshbooks.com/api/start) | Clean UX, good mobile. Paid. No Colombia specifics. |
| **Huli** | Vet practice management — appointments, EHR, prescriptions. | Yes — REST API with JWT. Patients, appointments, checkups, doctors. [API docs](https://api.huli.io/docs/) | Vet-specific. Good reference for clinical features (phase 2). Not financial. |
| **Splitwise** | Bill splitting and shared expenses between people. | Yes — REST API. Groups, expenses, balances. [API docs](https://dev.splitwise.com/) | Not the same domain, but **top UX reference** for quick expense entry. The "add expense" flow is dead simple — big button, minimal fields, done. Inspiration for our income/expense entry. |
| **PILA platforms** (MiPlanilla, Aportes en Línea, SOI) | Seguridad social payment for independientes. | No public API. Web-only with PSE payments. | No integration possible. But we can calculate IBC and amounts to pay, and remind the user of due dates based on cédula last digits. |

### Key Takeaways from Research

- **Alegra and Siigo** have solid APIs — if she ever wants facturación electrónica (DIAN compliance), integrating with one of these could be a path instead of building it
- **PILA has no API** — we can only calculate and remind, not automate payment
- **Huli** is a reference for phase 2 clinical features (patient records, appointments)
- **Wave and FreshBooks** are good UX references for clean expense/income entry
- **Splitwise** is the gold standard for frictionless expense entry UX

> **Note for later (Architecture / Constraints):** The app's API layer should be built behind an abstraction so the backend can be swapped with minimal friction — our own backend for v1 (learning + full control), but designed so we could plug in Alegra, Siigo, or any other API later if it makes sense at scale. This applies to both the data layer (repository pattern) and any external service integrations. Revisit in steps 1.5, 1.7, and 4.

---

## 1.5 Scope

> *To be defined in step 1.5*

---

## 1.6 User Stories

> *To be defined in step 1.6*

---

## 1.7 Constraints & Assumptions

> *To be defined in step 1.7*

> **Carry-forward:** Backend abstraction layer — see note in 1.4 Key Takeaways.