# 🔥 Zero Risk - Business Automation System

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Status](https://img.shields.io/badge/status-active%20development-success.svg)]()
[![Platform](https://img.shields.io/badge/platform-Google%20Sheets-34A853)]()

> Automated business management system that reduces administrative workload by 87%

---

## 🎯 About This Project

**Zero Risk** is a fire safety services company in Ecuador with 18 corporate clients. This project automates the complete administrative cycle, from quotations to financial statements.

### The Problem

As the owner and administrator of Zero Risk, I faced the classic small business dilemma:
- ⏰ **15+ hours per week** on manual administrative tasks
- 📊 **No real-time financial visibility**
- 🔄 **Frequent data entry errors**
- 📈 **Unable to scale the business**

### The Solution

End-to-end automated system that integrates:
- **Smart Quotations** - Dropdown lists, auto-fill, automatic calculations
- **Automated Invoicing** - 1-click conversion from quotation to invoice
- **Automatic Accounting** - Double-entry bookkeeping with zero manual input
- **Real-time Dashboard** - KPIs and financial metrics updated instantly

---

## 📊 Results

| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
| **Weekly admin time** | 15 hours | 2 hours | **-87%** |
| **Accounting errors/month** | 8 | 0 | **-100%** |
| **Time to generate reports** | 4 hours | 5 minutes | **-98%** |
| **Software cost/month** | $0 | $0 | N/A |

### Business Impact:
- ✅ **3 new clients** signed with freed-up time
- ✅ **+40% revenue** (Q4 2025 vs Q3 2025)
- ✅ Significantly reduced operational stress

---

## 🛠️ Tech Stack

- **Frontend:** Google Sheets (intelligent forms)
- **Backend:** Google Apps Script (JavaScript)
- **Database:** Google Sheets with relational architecture
- **Integration:** IMPORTRANGE for cross-file references
- **Visualization:** Google Charts

**Why this stack?**
- ✅ Zero infrastructure costs
- ✅ Accessible from any device (cloud-based)
- ✅ Collaborative (easy to share with accountant)
- ✅ Fast implementation and iteration
- ✅ Familiar interface for non-technical users

---

## 🏗️ System Architecture
```
┌─────────────────────────────────┐
│   QUOTATION TEMPLATE            │
│   • Dropdown lists              │
│   • Auto-fill client data       │
│   • Auto-calculate totals       │
└──────────┬──────────────────────┘
           │
           │ 1-CLICK
           ↓
┌──────────────────────────────────┐
│   INVOICE TEMPLATE               │
│   • Automatic numbering          │
│   • Data copied from quotation   │
│   • Editable before finalizing   │
└──────────┬───────────────────────┘
           │
           │ AUTOMATIC
           ↓
┌──────────────────────────────────┐
│   ACCOUNTING SYSTEM              │
│   • Journal entries (3 auto)     │
│   • General ledger update        │
│   • Accounts receivable          │
│   • Income statement update      │
└──────────┬───────────────────────┘
           │
           ↓
┌──────────────────────────────────┐
│   EXECUTIVE DASHBOARD            │
│   • Real-time KPIs               │
│   • Automated alerts             │
└──────────────────────────────────┘
```

---

## 📂 Project Structure
```
zero-risk-business-automation/
├── README.md
├── docs/
│   ├── architecture.md
│   ├── functional-requirements.md
│   └── user-guide.md
├── src/
│   └── apps-script/
└── data/
    └── sample-data/
```

---

## 🚀 Features

### 1. Intelligent Quotations
- Dropdown lists for all fields
- Auto-fill client information (Tax ID, phone, email, address)
- Auto-fill product prices from database
- Automatic calculations (subtotal, tax, total)
- Sequential numbering

### 2. Automated Invoicing
- Create invoice from quotation with 1 click
- Automatic invoice numbering
- Ability to edit before finalizing
- Option to create invoice from scratch

### 3. Automatic Accounting
- 1 invoice = 3 automatic journal entries
- Real-time general ledger updates
- Accounts receivable tracking with aging
- Income statement auto-updated
- Inventory updates (if applicable)

### 4. Executive Dashboard
- Real-time financial KPIs
- Total invoiced (day/month/year)
- Outstanding receivables
- Overdue invoice alerts
- Trend charts

---

## 📈 Roadmap

- [x] MVP with core functionality
- [x] Automated accounting registration
- [x] Dashboard with main KPIs
- [ ] Integration with Ecuador SRI API (electronic invoicing)
- [ ] Machine Learning for cash flow forecasting
- [ ] Mobile app for expense tracking
- [ ] Bank API integration

---

## 📖 Documentation

- [System Architecture](docs/architecture.md) - Technical design and data flow
- [Functional Requirements](docs/functional-requirements.md) - Detailed specifications
- [User Guide](docs/user-guide.md) - How to use the system *(coming soon)*

---

## 👤 Author

**Emilio Pérez**

Business Manager & Data Analyst

- 💼 Owner @ Zero Risk (Fire safety services)
- 🎓 Codebasics Data Analytics Bootcamp | Google Project Management | Lean Six Sigma Green Belt
- 📍 Zermatt, Switzerland
- 🔗 www.linkedin.com/in/emilio-perez-aviles
- 📧 emilacho@hotmail.com

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- My accountant for validating all accounting calculations
- Google Apps Script community for valuable resources
- My clients for their patience during implementation

---

⭐ If you find this project useful, please consider giving it a star on GitHub!

---

**Last updated:** February 2026

