# Workforce_Resource_Optimization
http://localhost:5173 

## What We Are Building

We are building a **browser-based Workforce Resource Optimization Platform** that helps organizations answer one core business question:

> **“Given the demand we expect, how many people do we need, when do we need them, and how can we deploy them at the lowest practical cost while maintaining service levels?”**

The platform combines **demand forecasting + workforce planning + mathematical optimization + scenario modeling + executive analytics** into one tool.

### The end-to-end flow

```text
                    BUSINESS DATA
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
       Historical      Employee       Business
        Demand        Availability     Rules
          │              │              │
          └──────────────┼──────────────┘
                         ▼
                1. DEMAND FORECAST
                         │
              "How much demand?"
                         ▼
              2. WORKFORCE NEED
                         │
           "How many people needed?"
                         ▼
               3. OPTIMIZATION
                         │
        "Who should work, when & where?"
                         ▼
                4. SCHEDULE
                         │
              "Actual roster"
                         ▼
               5. WHAT-IF ENGINE
                         │
       "What happens if demand changes?"
                         ▼
             6. EXECUTIVE DASHBOARD
                         │
            "What is the business impact?"
```

---

## 1. Demand Forecasting

The system takes historical demand and predicts future demand.

For example:

**Customer Service**

| Hour  | Historical | Forecast |
| ----- | ---------: | -------: |
| 8 AM  |        120 |      135 |
| 9 AM  |        150 |      165 |
| 10 AM |        190 |      205 |
| 11 AM |        220 |      235 |

The forecasting engine evaluates different models and uses the existing forecasting logic to determine the appropriate model.

It measures:

* **WAPE**
* Forecast Bias
* MAPE where appropriate
* Forecast accuracy
* Peak demand periods

---

## 2. Workforce Requirement

The forecast is converted into **required headcount**.

For example:

> Forecast demand = 200 transactions/hour
> Productivity = 10 transactions/employee/hour
> Shrinkage = 20%

The system calculates the workforce actually required rather than simply dividing demand by productivity.

The result becomes:

**Required Workforce by Hour**

```text
8 AM     ████████       8 people
9 AM     ██████████    10 people
10 AM    █████████████ 13 people
11 AM    ███████████████ 15 people
```

This tells management **where capacity gaps exist**.

---

## 3. Workforce Optimization

This is one of the most important parts of the platform.

The system takes:

* Required headcount
* Employee availability
* Skills
* Working hours
* Shift rules
* Maximum hours
* Overtime
* Break requirements
* Employee preferences
* Coverage requirements

and uses **OR-Tools optimization** to generate an employee-level schedule.

Instead of saying:

> "You need 15 employees."

the system answers:

> **"These 15 employees should work these shifts to provide the required coverage while respecting the constraints."**

---

## 4. Manager Schedule Experience

A manager can then see the generated roster.

For example:

| Employee    | Shift | Hours | Status |
| ----------- | ----- | ----: | ------ |
| Employee 01 | 8–4   |     8 | ✓      |
| Employee 02 | 9–5   |     8 | ✓      |
| Employee 03 | 10–6  |     8 | ✓      |
| Employee 04 | 11–7  |     8 | ⚠      |
| Employee 05 | 12–8  |     8 | ✓      |

The manager can review/edit schedules while the system checks constraints.

It can show:

* Coverage
* Overtime
* Labor cost
* Understaffing
* Overstaffing
* Schedule violations

---

## 5. What-If Scenario Modeling

This makes the application much more useful for business leaders.

A manager can ask:

> **"What happens if demand increases by 20%?"**

or:

> **"What happens if absenteeism increases by 10%?"**

or:

> **"What if I reduce staffing by 5 people?"**

The system recalculates the scenario and compares:

**Baseline vs Scenario**

```text
                    BASELINE       +20% DEMAND
Demand              10,000          12,000
Required HC             50              62
Scheduled HC             52              62
Coverage               104%            100%
Labor Cost          $42,000         $48,500
Overtime              $1,200          $2,100
```

This turns the tool from a scheduling application into a **workforce decision-support platform**.

---

## 6. Executive Dashboard

The executive layer answers:

> **"What is the business impact?"**

Rather than forcing executives to understand the optimization algorithm, the dashboard surfaces:

### Workforce

* Required Headcount
* Scheduled Headcount
* Available Headcount
* Coverage %

### Demand

* Forecast
* WAPE
* Forecast Bias
* Demand outlook
* Peak periods

### Cost

* Labor Cost
* Overtime
* Labor utilization
* Estimated savings

### Operational Risk

* Understaffed hours
* Overstaffed hours
* Coverage gaps
* Overtime alerts

### Optimization Impact

For example:

> **12% reduction in avoidable labor cost**

> **18% reduction in understaffed hours**

> **95%+ demand coverage**

The numbers will come from the actual underlying calculations—not hardcoded demo numbers.

---

# The Technology Strategy

The important architectural decision we made is that **we are not rebuilding the business engine**.

You already have a tested Python backend containing:

* Forecasting
* Workforce requirement
* OR-Tools optimization
* Schedule management
* Scenario engine
* Executive dashboard calculations
* Data import/export

That engine currently has **179 tests passing**.

We are putting a **Streamlit web interface on top of that existing engine**.

```text
                 USER / EXECUTIVE
                        │
                        ▼
              ┌──────────────────┐
              │    STREAMLIT     │
              │    WEB APP       │
              └────────┬─────────┘
                       │
                       ▼
             EXISTING PYTHON ENGINE
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
    Forecast       Workforce       OR-Tools
     Engine        Requirement      Optimizer
        │              │              │
        └──────────────┼──────────────┘
                       ▼
                  SQLite Data
```

### Why Streamlit?

Because your immediate goal is:

> **"I want a working web application that I can open in a browser without asking users to install Python, Node, OR-Tools, Docker, PostgreSQL, etc."**

So once deployed to a Streamlit-compatible host:

**User → URL → Web App**

No local installation for the end user.


# The Final Product in One Sentence

> **An intelligent workforce planning and optimization platform that forecasts demand, calculates workforce requirements, automatically creates optimized employee schedules, evaluates what-if scenarios, and translates workforce decisions into measurable cost, coverage, and productivity outcomes.**

### And commercially, the pitch becomes:

**Forecast Demand → Optimize Capacity → Deploy the Right Workforce → Reduce Cost → Protect Service Levels**

## What We're Building

**Workforce Resource Optimization Tool** — a browser-based application that helps operations leaders (retail, contact centers, warehouses, any shift-based workforce) figure out exactly how many people they need, when, and build an optimal staff schedule automatically — backed by real forecasting and mathematical optimization, not guesswork or spreadsheets.

It's built as two things sharing the same core engine:
- **A Streamlit web app** (the one you're deploying) — open a URL, no installation needed
- **A React + FastAPI app** — the original architecture, kept as a future production path

Both call the exact same Python business logic underneath, so the numbers are identical no matter which front end you use.

## The Business Problem It Solves

Most workforce planning today is reactive and manual: a manager eyeballs last week's numbers, guesses at next week's staffing, and finds out they're understaffed (angry customers, burned-out staff) or overstaffed (wasted labor cost) only after the fact. This tool replaces that guesswork with a connected, end-to-end pipeline:

```
How busy will we be?              → Demand Forecasting
How many people does that need?   → Workforce Requirement Engine
Who should actually work when?    → OR-Tools Optimization (real constraint solver)
What if a manager needs to tweak it?  → Editable Schedule with validation
What if demand changes?           → What-If Scenario Modeling
So what does this mean for the business?  → Executive Dashboard
```

Every number on every screen is a real calculation — nothing is hardcoded or fabricated. If the optimizer genuinely can't build a feasible schedule (not enough skilled staff, say), it says so honestly instead of inventing one.

## Step-by-Step Guide

### Getting there
Open the URL your app is deployed at (`https://your-app-name.streamlit.app` once live on Streamlit Community Cloud). Nothing to install — just a browser.

### 1. Launch Demo
On the **Home** page, click **Launch Demo**. This generates a realistic synthetic dataset — 2 locations, 3 departments, ~30 employees, 90 days of hourly demand with real patterns (busier midday, quieter weekends, occasional spikes) — in your own private session. (Or use **Data Management** to upload your own CSV/Excel data instead.)

### 2. Demand Forecast
Go to **Demand Forecast**. Pick a location, department, and horizon (7 or 14 days), then click **Generate Forecast**. The app backtests three statistical models against your actual history and automatically picks the most accurate one — you'll see WAPE (accuracy), bias, and which model won, plus a chart of actual vs. forecast demand.

### 3. Workforce Requirement
Go to **Workforce Requirement**. Set your productivity assumption (how much one employee handles per hour) and shrinkage (absence, breaks, training %), then click **Calculate**. This converts the forecast into "how many people do I need, hour by hour" — fully transparent math shown on screen.

### 4. Optimized Schedule
Go to **Optimized Schedule** and click **Generate Optimized Schedule**. A real constraint solver (Google OR-Tools) builds the lowest-cost schedule that respects every employee's availability, skills, and hour limits. You can then:
- Edit any shift directly in the grid, see the cost/coverage impact before saving
- **Save** or **Cancel** staged changes
- **Undo** the last edit, or **Revert to Optimized** to throw away all manual edits

### 5. What-If Scenarios
Go to **What-If Scenarios**. Try a preset ("Demand +20%") or set your own combination of demand/absenteeism/productivity/cost changes, click **Run Scenario**. You get a real before/after comparison — required headcount, labor cost, coverage — because the app actually re-runs the forecast-to-schedule pipeline with your adjusted numbers, not an estimate.

### 6. Executive Dashboard
Go to **Executive Dashboard** — the one-screen summary for leadership: forecast accuracy, coverage %, labor cost, overtime, estimated savings, automated alerts ("understaffed by 4 employees 2–4pm tomorrow"), and concrete recommendations, all pulled together from everything you just did.

### 7. Export
From **Data Management**, download any result — forecast, requirement, schedule, scenario, or the executive summary — as a CSV for a spreadsheet or slide deck.

**One thing worth knowing**: each browser session gets its own private workspace automatically — if colleagues open the same URL at the same time, none of you will see or affect each other's data.
