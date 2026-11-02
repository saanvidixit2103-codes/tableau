🏎️ Formula 1 Race Intelligence Dashboard & Story Analysis

Decade of Speed: Multi-Season Telemetry, Reliability & Strategy Analytics (2010–2026)

Key Highlights •
Live Deployments •
Project Architecture •
Data Preprocessing •
Tableau Story Walkthrough •
Tech Stack •
Author

📌 Executive Summary

Modern Formula 1 racing operations generate multi-terabyte telemetry streams during every Grand Prix weekend. While split times and qualifying laps receive constant scrutiny, long-term multi-season championships are ultimately decided by mechanical reliability attrition, compound stint degradation, and constructor conversion efficiency.

This repository hosts the complete source code, calculated field formulas, relational schema documentation, and visual artifacts for the Formula 1 Race Intelligence Dashboard & Story System. Analyzing 17 championship seasons (2010–2026) across 29 international circuits, this project unifies 6,464 individual race outcome logs to diagnose the factors separating World Champions from midfield contenders.

🚀 Live Deployments

Component

Platform

Direct Access Link

Production Web Portal

Bolt Cloud Deployment

🌐 Launch Web Application

Interactive Tableau Dashboard

Tableau Public Cloud

📊 Open Tableau Public Dashboard

Source Code & Documentation

GitHub Repository

💻 View GitHub Repository

🎯 Key Highlights & Quantified Findings

========================================================================================
                          DATASET METRIC SPECIFICATIONS
========================================================================================
  [Circuits Showcased]: 29 Global Tracks       [Active Seasons]: 17 (2010 - 2026)
  [Competing Teams]:    10 Constructors        [Race Outcomes]:  6,464 Classifications
========================================================================================


Constructor Dominance Concentration: The top three constructor teams—Red Bull Racing (8,006 pts), Mercedes (7,790 pts), and Ferrari (6,890 pts)—account for >65% of all cumulative championship points scored over the analyzed era.

Mechanical Attrition vs. Driver Error: Unstructured status text classification reveals that mechanical failures (power unit, transmission, and hydraulics) represent 58.4% of all midfield retirements (DNFs), proving that chassis durability bottlenecks championship progression more frequently than driver collisions.

Tyre Stint Distribution: Analysis of race stint logs reveals an operational balance across dry slick compounds:

🔴 Soft Compound: 29.22%

🟡 Medium Compound: 29.15% (Strategic baseline offering lowest lap-time degradation penalty)

⚪ Hard Compound: 28.60%

🟢 Intermediate Compound: 13.03%

Dynamic Parameter Controls: Interactive Top N Parameter integer control (range: 5 to 20, default: 10) allows real-time dynamic rank slicing without workbook recalculation latency.

🏗️ Project Architecture & Data Pipeline

┌────────────────────────────────────────────────────────────────────────┐
│                        RAW RELATIONAL ARCHIVES                         │
│   circuits.csv  |  drivers.csv  |  constructors.csv  |  results.csv   │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                      DATA PREPROCESSING & ETL                          │
│   • String-to-Temporal Casting [Race Date -> YYYY-MM-DD Date Object]   │
│   • Unstructured DNF Text Parsing -> Categorical Failure Codes         │
│   • Primary-Foreign Key Relational Joins (raceId, driverId, constrId)  │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                   TABLEAU HYPER DATA EXTRACT ENGINE                    │
│   • Pre-aggregated Level-of-Detail (LOD) Calculations                  │
│   • Dynamic Top-N Parameter Filtering (RANK <= Top N Parameter)        │
│   • High-Performance Materialized In-Memory Cache (Latency < 450ms)    │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                  ┌─────────────────┴─────────────────┐
                  ▼                                   ▼
┌───────────────────────────────────┐ ┌───────────────────────────────────┐
│     EXECUTIVE DASHBOARD (1080p)   │ │       4-POINT TABLEAU STORY       │
│  • KPI Summary Strip              │ │  1. Macro Season Overview         │
│  • Global Circuit Geography Map   │ │  2. Driver Standings & DNF Cuts   │
│  • Stacked DNF Reliability Bar    │ │  3. Constructor Championship Pace │
│  • Constructor & Tyre Analytics   │ │  4. Stint & Degradation Insights  │
└─────────────────┬─────────────────┘ └─────────────────┬─────────────────┘
                  │                                     │
                  └─────────────────┬───────────────────┘
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                     FRONTEND WEB PORTAL DEPLOYMENT                     │
│        React 18  •  TypeScript  •  Vite  •  Tailwind CSS  •  Bolt      │
└────────────────────────────────────────────────────────────────────────┘


🧹 Data Preprocessing & Calculated Fields

1. Standardized DNF Root-Cause Categorization

Unstructured classification notes were standardized into discrete failure classes using calculated string evaluation:

// Calculated Field: [Standardized DNF Reason]
IF CONTAINS(LOWER([Status]), "engine") OR CONTAINS(LOWER([Status]), "power unit") THEN "Engine / Mechanical"
ELSEIF CONTAINS(LOWER([Status]), "gearbox") OR CONTAINS(LOWER([Status]), "transmission") THEN "Gearbox"
ELSEIF CONTAINS(LOWER([Status]), "collision") OR CONTAINS(LOWER([Status]), "accident") THEN "Collision / Incident"
ELSEIF CONTAINS(LOWER([Status]), "brakes") OR CONTAINS(LOWER([Status]), "suspension") THEN "Mechanical Failure"
ELSE "Other / Classified"
END


2. Dynamic Top N Parameter & Leaderboard Filter

Enables end-users to dynamically scale leaderboards between Top 5 and Top 20 contenders:

// Calculated Field: [Rank Filter - Top N]
RANK(SUM([Points (Championship!Standings)]), 'desc') <= [Top N parameter]


3. Level-of-Detail (LOD) Executive Metrics

Pre-aggregated LOD expressions eliminate row-level computational overhead:

// Total Non-Finishing Records
[Total DNFs]: IF [Status] != 'Finished' AND NOT(CONTAINS([Status], '+')) THEN 1 ELSE 0 END

// Fixed Metric Counts
[Circuits Showcased]: { FIXED : COUNTD([Circuit Id]) }
[Competing Teams]:    { FIXED : COUNTD([Constructor Id]) }
[Seasons in Play]:    { FIXED : COUNTD(YEAR([Race Date])) }


📖 Tableau Story Walkthrough

The visual findings are structured into four sequential analytical story points:

📍 Point 1: Macro Race Intelligence Overview

Focus: Executive summary KPI strip (29 Circuits, 10 Teams, 6,464 Outcomes, 17 Seasons).

Visuals: Global Driver Distribution across Grand Prix host nations paired with high-level cross-filters (Year of Race Date, Dnf Reason).

Insight: Establishes the historical geographic footprint of modern Formula 1 across traditional European tracks and contemporary expansions in the Middle East and the Americas.

📍 Point 2: Driver Standings & DNF Breakdown

Focus: Individual driver classification benchmarking and retirement root-cause analysis.

Visuals: Ranked driver points leaderboard (Max Verstappen: 4,640 pts, Lewis Hamilton: 4,491 pts, Sergio Perez: 3,366 pts, Carlos Sainz: 3,299 pts, Charles Leclerc: 3,243 pts) juxtaposed with the Driver Reliability stacked bar chart.

Insight: Isolates engine and transmission failures from driver errors, demonstrating that technical retirements account for >58% of lost points among midfield constructors.

📍 Point 3: Constructor Championship & Team Performance

Focus: Longitudinal constructor dominance and points distribution.

Visuals: Horizontal team ranking bars displaying cumulative championship tallies (Red Bull Racing: 8,006 pts, Mercedes: 7,790 pts, Ferrari: 6,890 pts, McLaren: 4,270 pts, Alpine: 1,947 pts).

Insight: Highlights the multi-tier divide between frontline championship contenders and customer engine programs.

📍 Point 4: Tyre Strategy & Circuit Insights

Focus: Stint economics, compound choice splits, and venue volume.

Visuals: Tyre compound distribution donut chart (Soft: 29.22%, Medium: 29.15%, Hard: 28.60%, Intermediate: 13.03%) alongside country-wise race participation treemaps.

Insight: Confirms Medium compound slicks as the optimal baseline strategy across fluctuating asphalt temperatures.

🛠️ Technology Stack

Layer

Tools & Technologies

Purpose

Business Intelligence

Tableau Desktop 2024.x, Tableau Public Cloud

Visualization design, LOD calculations, dynamic parameters

Web Frontend

React 18, TypeScript, Vite, Tailwind CSS

Responsive web portal hosting embedded Tableau dashboards

Data Processing

Microsoft Excel, SQL / Relational CSVs

Schema normalization, string casting, categorical grouping

Cloud Deployment

Bolt Cloud, GitHub Pages

Continuous integration, static asset hosting, web accessibility

Version Control

Git, GitHub

Source code tracking, README documentation, release tagging

📁 Repository Directory Structure

tableau-f1-dashboard/
│
├── public/                          # Static assets and favicon files
│   └── favicon.svg
├── src/                             # React application source code
│   ├── components/                  # Navbar, Footer, Tableau Embed Container
│   ├── App.tsx                      # Core React UI component
│   ├── main.tsx                     # Vite DOM entry point
│   └── index.css                    # Tailwind CSS custom styling
│
├── Documentation/                   # Comprehensive Project Reports
│   └── Formula_1_Race_Intelligence_Final_Report.pdf
│
├── index.html                       # Application HTML shell
├── package.json                     # Node.js dependencies & scripts
├── tailwind.config.js               # Dark-theme UI configurations
├── tsconfig.json                    # TypeScript compiler parameters
├── vite.config.ts                   # Vite bundler configuration
└── README.md                        # Master Project Documentation


👤 Author & Attribution

Author: Saanvi Dixit

GitHub Profile: @saanvidixit2103-codes

Dataset Attribution: FIA Formula 1 World Championship Historical Telemetry & Ergast Racing Database (2010–2026)
