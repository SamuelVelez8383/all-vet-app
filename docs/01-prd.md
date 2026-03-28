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
- 

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

### MVP (v0.1) — "Replace the spreadsheet"

Ship fast, get feedback. The minimum for Sofía to stop using Google Sheets.

| # | Feature | Detail |
|---|---------|--------|
| 1 | Quick income/expense entry | Splitwise-style: big button, minimal fields, done. From phone or desktop. |
| 2 | Categories | Configurable categories for income and expenses (consultations, surgery, meds, rent, supplies, etc.) |
| 3 | Payment type | Cash or bank transfer — simple field on each transaction |
| 4 | Client list | Name, contact info, basic notes. Link transactions to clients. |
| 5 | Unpaid client tracking | Mark transactions as paid/unpaid, filter to see who owes money |
| 6 | Log of income/expenses | Detailed user-friendly log of income/expense entries that is filterable |
| 7 | Monthly summary dashboard | Income, expenses, net — at a glance for any month. Shows "unallocated" line for disposable income not yet assigned to a purpose. |
| 8 | Filters and search | By period (week/month/year), client, category, payment status |
| 9 | Google Sheets backend | Initial data store, swappable via repository pattern |
| 10 | PWA | Installable, mobile-friendly, works on phone during house calls |
| 11 | Single user | Sofía only, no auth system needed |

### v1.0 — "The full promise"

Built on top of MVP based on real feedback. Added in priority order.

| Priority | Feature | Detail |
|----------|---------|--------|
| 1 | Editable entries | Edit previous income/expense entries. Edited entries are marked as so. |
| 2 | Document attachments | Attach photo/PDF (receipt, payment confirmation, billing statement) to any transaction |
| 3 | Real-time profitability indicator | Running net income visible anytime during the month — no need to wait until month-end |
| 4 | Pending invoices & obligations dashboard | Dedicated view for unpaid client invoices (with aging: on-time, 7+, 30+ days) and upcoming obligation due dates |
| 5 | Net income breakdown + month closing | Auto-calculate obligations: seguridad social, tithe, loan, car (if applicable). Includes a "close the month" action where she allocates disposable income to predefined buckets (savings, loan, household, personal, etc.). Allocations are saved as part of the monthly summary, not as transactions. |
| 6 | PILA calculation (full) | IBC = gross income × 40%, salud (12.5%), pensión (16%), ARL by activity. Handles income < 2 SMLMV and variable monthly income. |
| 7 | Recurring expenses | Auto-create monthly entries for fixed costs (rent, internet, phone, insurance). Editable each month. |
| 8 | Reminders | Due dates for seguridad social (based on cédula or date), pending client payments, other obligations |
| 9 | Service catalog (basic) | List of services with standard pricing. Feeds into quick entry presets and future invoice generation. |
| 10 | Professional profile | Rates, contact info, professional e-card, uploaded PDFs. A section she can reference or share with clients. |

### v1 — Stretch

| # | Feature | Detail |
|---|---------|--------|
| S1 | Graphs and visualizations | Income vs expenses over time, category breakdown charts, trend lines |
| S2 | Patient list (basic) | Animal name, species, owner — linked to client. No clinical records yet. |
| S3 | Offline support | PWA works without internet, syncs when back online. Critical for farm/finca visits with no signal. |
| S4 | AI chat/voice entry | Send a voice message or text describing the expense/income, app parses it and creates the entry. |
| S5 | Travel log | Rate per distance/city/day. Clients tagged by location so travel surcharge can be auto-computed. |
| S6 | Appointment notes | Quick note per visit (not full clinical record). E.g. "Vacuna triple felina, next dose in 3 weeks." |
| S7 | Budget targets | Monthly expense ceiling per category. Warning when approaching limit. |

### Parked (v2+)

| # | Feature | Why not now |
|---|---------|------------|
| P1 | Invoice generation (cuenta de cobro) | Templates, PDF generation, prefilled items — high value but high complexity |
| P2 | Patient clinical records | Medical history, treatments, photos — different domain |
| P3 | Multi-user / shared access | Auth system, roles, permissions |
| P4 | Alegra/Siigo API integration | Only needed at scale or for facturación electrónica |
| P5 | Multi-currency | COP only for now |
| P6 | Income forecasting | Projections based on past months — valuable but variable income makes it tricky |
| P7 | Tax estimation | Annual renta estimate based on accumulated income |
| P8 | Export to CSV/Excel | Data dump for accountant or manual analysis |
| P9 | Analytics suite | Client notes, referral tracking, scheduled summary emails, smart alerts ("you spent 40% more on supplies this month") |
| P10 | Advanced dashboards | Year-over-year comparisons, client payment history summary, profitability per service type, seasonal patterns |
| P11 | Medication inventory | Track stock, low-stock alerts |
| P12 | Combined household finances | Sofía + partner finances merged view |
| P13 | WhatsApp integration | Payment reminders ("Tienes un saldo pendiente de $X"), share service catalog, professional profile, documents with clients via WhatsApp API |
| P14 | Document OCR & auto-tagging | Search receipt text, auto-categorize uploads |
| P15 | Monthly document bundle | One-click download of all docs for a month |
| P16 | Year-end tax summary | Everything needed for declaración de renta in one report |

### Questions for Sofía

> To be asked and answers recorded here before finalizing scope and user stories.

1. **Split transactions** — When a single visit involves multiple items (consultation + meds + travel), does she prefer logging one entry with line items, or separate entries? How does she handle it today?
2. **Client vs patient tracking** — Does she want to track at the pet level, the owner level, or both? What data matters for each?
3. **Third-party vets** — When a surgery requires an anesthesiologist or specialist, does Sofía pay them or does the client pay directly? Does she need to track those payments?
4. **Cuenta de cobro details** — What fields does she currently put on the Canva billing statement? (So we know what the eventual template needs)
5. **Payment methods** — Beyond cash and bank transfer, does she use Nequi, Daviplata, or other digital wallets? Should those be payment type options?
6. **Frequency of entries** — How many income/expense entries does she typically have per day? Per week? (Helps us prioritize speed of entry)
7. **Travel pricing** — How does she currently calculate travel surcharges? Flat rate per zone? Per km? Per city?

---

## 1.6 User Stories

Question to AI: Should I merge AV-01 and AV-02? Or have them separate?

### AV-01: Register an expense
As a vet, I want to quickly register an expense from my phone, 
so that I can log costs right after they happen without forgetting.

Acceptance criteria:
- [ ] I can find my app easily on my phone
- [ ] The app loads quickly
- [ ] I can tap a single button to start a new entry
- [ ] I select income or expense
- [ ] I enter amount, category, and optional description
- [ ] I can select payment type (cash / bank transfer)
- [ ] I can optionally link it to a client/business/recipient
- [ ] Entry is saved and visible in the log immediately
- [ ] I can optionally attach one or many documents to it
- [ ] I can modify an expense at anytime and it will reflect in the log and summaries/computations immediately

### AV-02: Register an income
As a vet, I want to quickly register income from my phone, so that I can log revenue after a house call occurs and don't forget about it.

Acceptance criteria:
- [ ] I can tap a single button to start a new entry
- [ ] I can mark the entry as an advancement (partial) or full payment
- [ ] I can mark the entry as paid or to be paid (pending)
- [ ] I enter amount, category, and optional description
- [ ] I can select payment type (cash / bank transfer)
- [ ] I can optionally link it to a client/business/recipient
- [ ] Entry is saved and visible in the log immediately
- [ ] I can optionally attach one or many documents to it
- [ ] I can modify an expense at anytime and it will reflect in the log and summaries/computations immediately

**To ask Sofia**: Can the payment be entered as full but pending and have a way to add a partial payment as an advanced? Let's say the consultation was 300.000 cop total but they gave an advance of 150.000 and then are pending to pay the rest? How would this look?

### AV-03: Create new client
As a vet, I want to easily create a new client, so I can link any expense or income to he/she.

Acceptance criteria:
- [ ] I can tap a single button to create a client
- [ ] The client must be easily accessible and selectable when creating an income/expense
- [ ] Clients should be unique. I shouldn't be able to create the same client twice
- [ ] Clients should have all necessary information (email, cellphone, location/address, name, patients/animals) which I can specify and/or retrieve easily, as well as update at any time
- [ ] Clients can have additional and optional information (preferences, preferred payment method, bank information, additional contact information, related people) which I can add and change at any time
- [ ] I can relate the client to already registered expenses/incomes after creating the client
- [ ] I can create a client while registering an expense/income

### AV-04: Mark entry as paid
As a vet, I want to quickly find an entry (income), and mark it as paid or partially paid, so that I can keep my payment history updated as soon as money comes in.

Acceptance criteria:
- [ ] I can easily search entries by client, date, patient, or any related info that can be used to filter and search
- [ ] I can quickly mark an entry as fully paid by a single button
- [ ] I can modify the entry to update the amount that has been paid in case the payment is partial and doesn't complete the full income entry
- [ ] I can update an entry and add various types of payments since clients can pay partially in cash and bank transfer
- [ ] If the update comes in in the upcoming month of the one that the payment was registered, the payment could be split in two and considered for the new month

### AV-05: I can query and filter expenses
As a vet, I want to accurately search/query and filter expenses by different criteria/categories (client, paid/unpaid, expense/income, dates, etc), so that I can find particular expenses and have filtered lists of them.

Acceptance criteria:
- [ ] I can find specific expenses/income by search criteria such as related keyword, client, date, expense/income type, etc
- [ ] I can find a list of expenses/income by filtering through criteria such as category, client, patient, dates, expense/income type, paid, unpaid, partially paid, etc.
- [ ] Searches and filters are accurate and don't leave out expenses/income that should be displayed. 
- [ ] All possible search/filter criteria can be used alone and in combination between each other

 
### AV-06: I bought supplies, I can register expense

### AV-07: I need to find a receipt

### AV-08: I want to compute balance at any time

### AV-09: I want to review the balance of the current month

### AV-10: I want to review the balance of last month, months, year

### AV-11: I want to know what are my obligations for the month that has passed (the ones I have to pay now)

### AV-12: I want to get reminders of upcoming obligations

### AV-13: I want to know which clients owe me

---

## 1.7 Constraints & Assumptions

> *To be defined in step 1.7*

> **Carry-forward:** Backend abstraction layer — see note in 1.4 Key Takeaways.