# 📘 Shared Office Trial Conversion Analysis Report

This report analyses behavioural log data from a shared office provider offering a 3-day free trial. It aims to uncover key behavioural drivers behind trial-to-paid user conversion and proposes actionable strategies based on segmentation insights.

---

## 1. Project Background

This project analyses behavioural data from a shared office provider that offers a 3-day free trial to prospective users.     

The dataset encompasses user sign-up records, site visit histories, in-and-out access logs, and payment statuses. The data spans from **May 2021 to December 2023** and covers multiple physical office locations.

All data is fully anonymised and used solely for internal analytical purposes. It reflects real-world behavioural logs collected from a commercial shared office environment.

The core objective of this project is to leverage **log-level behavioural data** to determine which user actions are most strongly associated with successful **trial-to-paid conversions**. Through this, the project aims to deliver **practical and actionable insights** for service design and operational improvement.

The analysis and recommendations presented in this report focus on the following key areas:

- **Conversion Funnel Analysis**  
  Identifying which stages of the user journey experience the greatest drop-off and analysing the behavioural patterns associated with these losses.

- **Behavioural Drivers of Conversion**  
  Examining user traits such as:  
  └ How soon users visit after signing up  
  └ Which times of day they use the workspace  
  └ How long and how frequently they use the service during the trial period

- **Pandemic vs Post-Pandemic Behavioural Shifts**  
  Exploring how user behaviours and conversion drivers have changed over time, particularly in response to the easing of COVID-related restrictions.

- **High-Potential Segment Definition**  
  Identifying and classifying user segments with significantly higher likelihoods of converting to paid users, based on observable behaviours during the trial.


---

## 2. Data Structure & Initial Checks

This project draws from five raw data tables that represent the key stages of the shared office trial process — from initial registration to site access and payment outcomes.

### 2.1. Raw Data Schema

| Table Name       | Description                                                                                   | Row Count |
|------------------|-----------------------------------------------------------------------------------------------|-----------|
| `trial_register` | Trial sign-up records containing user ID and registration date                                | 9,659     |
| `trial_visit_info` | Session-level visit data including user ID, site ID, date, first entry time, exit time, and total stay duration | 11,477    |
| `trial_access_log` | Raw access logs capturing timestamp, access type (in/out), site ID, and user ID              | 63,708    |
| `trial_payment`  | Conversion status for each trial user (paid or not)                                           | 9,659     |
| `site_area`      | Metadata including site ID and physical size of each site (in pyeong)                         | 9         |

<img src="https://github.com/user-attachments/assets/d53442f0-b6d8-451b-80cd-65d2f52a22c0" width="600"/>


Each dataset underwent **rigorous validation and cleaning** to ensure consistency and analytical reliability. Key steps included:

- Verifying column consistency and enforcing primary key constraints  
- Identifying and resolving missing or duplicated records  
- Standardising all timestamp formats for compatibility  
- Engineering derived features for behavioural segmentation  

### 2.2. Feature Engineering & Data Mart Construction

Following validation, the five datasets were integrated into a single **user-level analytical dataset**, designed as a tailored data mart for behavioural segmentation and predictive modelling.

<img src="https://github.com/user-attachments/assets/5361a408-dbd1-4fbf-b232-1cdd3adabe08" width="600"/>

To reflect user intent and engagement patterns, the following features were engineered:

- `first_visit_delay`: Number of days between user sign-up and first site visit  
- `main_time_block`: Most frequently used time block during the trial (e.g., morning, afternoon)  
- `visit_day_type`: Usage pattern indicating weekday, weekend, or both  
- `stay_hours`: Total number of hours the user stayed during the trial  
- `avg_density_scaled`: Average hourly user density experienced (normalised by site area)

The final data mart includes **6,315 trial users** who recorded at least one site visit. It contains the following 13 user-level variables:

- `user_id`: Unique trial user identifier  
- `month`: Trial start year and month  
- `is_payment`: Conversion label (0 = not paid, 1 = paid)  
- `pandemic_phase`: Period categorisation (pandemic vs post-pandemic)  
- `stay_hours`: Total time spent across trial visits  
- `num_site_visited`: Number of distinct office sites visited  
- `site_id`: Site identifier for visit sessions  
- `visit_days`: Number of days the user visited during the trial  
- `visit_day_type`: Weekday / weekend / both  
- `avg_density_scaled`: Mean hourly user density during stay (normalised by site area)  
- `max_density_scaled`: Maximum hourly density experienced during any session  
- `main_time_block`: Most common time block used (midnight, early morning, morning, afternoon, evening, night)  
- `first_visit_delay`: Days between sign-up and first visit

---

## 3. Key Research Questions

This analysis is guided by five core research questions designed to uncover the behavioural dynamics behind user conversion and to inform practical, data-driven strategies.

1. **At which stages in the user journey do most trial users drop off**, and what behaviours are typically observed just before those drop-offs?  
   → This helps identify structural bottlenecks within the trial-to-payment funnel.

2. **Which behavioural patterns are most closely associated with successful conversion** from trial to paid user?  
   → Understanding these patterns enables the team to recognise high-intent behaviours early.

3. **How have user behaviours evolved between the pandemic and post-pandemic periods?**  
   → This explores whether behavioural trends shifted fundamentally due to external context and if older patterns remain relevant today.

4. **Which behavioural features are most predictive of payment conversion**, and how can these be leveraged to develop an effective targeting or onboarding strategy?  
   → The goal is to prioritise actions that are both measurable and modifiable.

5. **Which user segments strike the optimal balance between conversion likelihood and business impact?**  
   → Segment-level analysis reveals which groups are most strategic to focus on, considering both performance and user base size.

---
## 4. Funnel Overview and Business Context

To contextualise the trial-to-payment challenge, the full business flow was mapped — from user acquisition through trial engagement to payment conversion. This section outlines the overall funnel structure and performance trends, with a particular focus on the critical bottleneck between visit and payment.

### 4.1. Trial Funnel Overview

The dashboard below summarises the full trial-to-payment flow and conversion trends across a **32-month period (June 2021 – December 2023)**.


<img src="https://github.com/user-attachments/assets/070d1657-da8a-4884-9282-8c3408721e85" width="600"/>


**Key Figures**:
- **Total Registered**: 9,624  
- **Total Visited**: 6,534 → **Visit Churn**: 32.1%  
- **Total Converted**: 3,652 → **Conversion Churn (Visit → Pay)**: 44.1%

### 4.2. Conversion Trend by Year

Annual breakdown reveals a **structural shift** in the business funnel over time:

| Year | Avg. Monthly Sign-ups | Avg. Monthly Visitors | Avg. Monthly Conversion Rate | Key Change             |
|------|------------------------|------------------------|-------------------------------|------------------------|
| 2021 | 351                    | 218.6                  | 42.8%                         | Baseline               |
| 2022 | 290.3                  | 196.8                  | 39.1%                         | –3.7%p conversion      |
| 2023 | 277.7                  | 201.8                  | 33.6%                         | –5.53%p conversion     |

- **2022**: Sign-up volumes declined significantly, indicating reduced overall trial demand.
- **2023**: Sign-ups remained stable, but conversion dropped sharply, suggesting worsening post-visit performance.
- The primary driver of the 2023 performance decline was reduced conversion after trial engagement.

### 4.3. Monthly Conversion Trend

The **monthly conversion rate** fell from **52.96% in June 2021** to **23.17% in November 2023**, even though trial sign-ups remained relatively steady.

Key observations:
- A subtle decline begins in late 2022, intensifying in the second half of 2023.
- The widening gap between **trial sign-ups (grey area)** and **successful conversions (blue area)** reflects an ongoing erosion in conversion efficiency.

### 4.4. Pandemic vs Post-Pandemic Business Structure

To facilitate comparative analysis, the dataset was split into two periods using **May 2022** as the dividing point — coinciding with the easing of major COVID-related restrictions in South Korea (e.g. lifting of social distancing measures, return to office routines).

| Metric                 | Pandemic | Post-Pandemic | Change     |
|------------------------|----------|----------------|------------|
| Avg. Monthly Sign-ups  | 335.8    | 279.8          | ▼16.7%     |
| Avg. Monthly Visitors  | 214.6    | 188.5          | ▼12.1%     |
| Avg. Conversion Rate   | 41.1%    | 36.1%          | ▼5.0%p     |

**Key Observations**:
- All key metrics declined after the pandemic period.
- Despite lower trial volumes, the drop in conversion rate was comparatively modest — suggesting a **shift in user intent** rather than trial demand.
- The business appears to have transitioned from **high-volume, exploratory usage** to a **lower-volume, purpose-driven model** — requiring more tailored onboarding and engagement strategies.

### 4.5. Strategic Focus: Visit → Payment

While inefficiencies exist across the funnel, the most critical gap lies in the **post-visit stage**.

- In 2023, visit volume remained stable, yet conversion performance deteriorated significantly.
- This indicates a **behavioural or experience-related barrier** after trial engagement.

> **Why do 44.1% of trial visitors fail to convert, even after using the service?**

This question becomes the focal point of the behavioural analysis in the following section, which examines user patterns, segment profiles, and decision-making drivers in depth.

---

## 5. Behavioural Shift: Pandemic vs Post-Pandemic

Understanding **when** behavioural patterns changed is as important as understanding **what** changed. This section assesses whether user behaviour during the pandemic was structurally different from post-pandemic patterns, and whether those patterns remain valid as a strategic baseline today.

### 5.1. Purpose of Analysis

The objective is to determine which time period — pandemic or post-pandemic — offers a more reliable foundation for conversion strategy and behavioural modelling.

If behaviours during the pandemic were largely shaped by **temporary external constraints** (e.g. lockdowns, restricted hours), they may no longer reflect genuine user intent. Continued reliance on outdated patterns could lead to ineffective targeting or inaccurate modelling.

To verify whether a structural shift has occurred, we compared the following **seven behavioural dimensions** across both periods:

- Total number of visit days  
- Total stay hours during the trial  
- Number of access events (entries and exits)  
- Primary visit day type (weekday vs weekend)  
- Main usage time block (e.g. morning, night)  
- Average congestion level experienced  
- Feature importance rankings (via Random Forest)


### 5.2. Total Number of Visit Days

| Phase           | Avg. (Not Paid) | Avg. (Paid) | Difference | Significance |
|----------------|------------------|--------------|------------|---------------|
| Pandemic        | 1.48 days        | 1.59 days    | +0.11      | ✅ p = 0.0001 |
| Post-Pandemic   | 1.49 days        | 1.55 days    | +0.06      | ✅ p = 0.0028 |

Although statistically significant, the differences in visit frequency are minimal. Most users only visited once due to the 3-day trial limit, so this metric alone does not suggest meaningful behavioural divergence.



### 5.3. Total Stay Hours During the Trial

| Group           | T-statistic | P-value | Significance | Interpretation                    |
|----------------|-------------|---------|--------------|------------------------------------|
| Paid           | 2.401       | 0.0164  | ✅           | Slight decrease (6.48 → 5.96 hrs) |
| Not Paid       | –0.038      | 0.9698  | ❌           | No change observed                |

Stay hours among paying users decreased post-pandemic, but the change is under 1 hour and may not be practically meaningful.



### 5.4. Number of Access Events

| Group           | T-statistic | P-value | Significance |
|----------------|-------------|---------|--------------|
| Paid           | –0.6153     | 0.5448  | ❌           |
| Not Paid       | –0.0981     | 0.9224  | ❌           |

<img src="https://github.com/user-attachments/assets/d76cbb38-9867-4f98-b632-e87bddee835c" width="600"/>


While no statistically significant differences were found in the number of access events, **temporal patterns** offer qualitative insights:

- **Pandemic**: Paid and unpaid users exhibited similar rise-and-fall patterns in access activity, suggesting external controls (e.g. social distancing rules) influenced all users equally.
- **Post-Pandemic**: Patterns diverged by user type, reflecting **individual usage styles and motivations**.  
  → Despite being non-significant statistically, this shift signals a move from externally-driven to intent-driven behaviour.



### 5.5. Primary Visit Day Type (Weekday vs Weekend)

| Comparison                            | Chi² Stat. | P-value | Significance |
|--------------------------------------|------------|---------|--------------|
| Payment × Phase × Day Type (3-way)   | 42.94      | < 0.001 | ✅           |
| Paid (Pandemic vs Post-Pandemic)     | 5.165      | 0.0756  | ❌           |
| Not Paid (Pandemic vs Post-Pandemic) | 1.889      | 0.3888  | ❌           |

No strong behavioural shifts were observed in isolation. However, the **interaction effect** between payment status, time period, and day type was highly significant — suggesting that **behavioural shifts occurred within specific user subgroups** rather than across the board.

---

### 5.6. Main Usage Time Block

| Group      | T-statistic | P-value  | Significance |
|------------|-------------|----------|--------------|
| Paid       | 4.76        | 0.00077  | ✅           |
| Not Paid   | –4.68       | 0.00087  | ✅           |

**Key Observations from Hourly Usage Distribution**:
- From midnight to 16:00, usage rates were relatively stable across periods.
- After 16:00:
  - **Pandemic**: Paid users increased gradually in the evening.
  - **Post-Pandemic**: Paid user activity dropped sharply; unpaid users dominated evening/night hours.
- Among paying users:
  - Pandemic: Afternoon/evening use correlated with higher conversion.
  - Post-Pandemic: Preference shifted to earlier time blocks (e.g. morning, early afternoon).

→ This indicates a **statistically significant reversal** in usage behaviour between the two periods.

<img src="https://github.com/user-attachments/assets/aba83f21-56a1-40dd-8213-ec085d3961f9" width="600"/>



### 5.7. Average Congestion Level Experienced

<img src="https://github.com/user-attachments/assets/4d9f8637-ccd7-44aa-ac3e-81c24840ccc3" width="600"/>


**Key Observations**:
- **Pandemic**: Congestion levels were similar for both paid and unpaid users.
- **Post-Pandemic**:
  - Unpaid users experienced **significantly higher congestion**.
  - Paid users maintained relatively low congestion levels.

**Statistical Results**:
- Not Paid: ✅ Significant increase (p = 0.0169)  
- Paid: ❌ No meaningful change

→ Post-pandemic, congestion acts as a **conversion inhibitor**, influencing user satisfaction and decision-making.


### 5.8. Feature Importance Rankings (Random Forest)

To identify whether the most predictive behavioural features shifted, Random Forest models were trained separately on pandemic and post-pandemic data.

<img src="https://github.com/user-attachments/assets/cad3082b-d78f-4468-a165-d7dcc412cdd4" width="600"/>



**Key Shifts in Feature Importance**:

| Feature                   | Pandemic | Post-Pandemic | Change      | Interpretation                                      |
|---------------------------|----------|----------------|-------------|-----------------------------------------------------|
| `visit_day_type_weekend` | 0.0851   | 0.1345         | ▲ +0.0494   | Now the most important predictor                    |
| `main_time_block_evening`| 0.0085   | 0.0365         | ▲ +0.0280   | Strongly linked to non-conversion                   |
| `main_time_block_night`  | 0.0000   | 0.0211         | ▲ +0.0211   | Newly meaningful                                    |
| `main_time_block_afternoon`| 0.1164 | 0.0793         | ▼ –0.0371   | Declined in predictive value                        |
| `first_visit_delay`      | 0.0879   | 0.0706         | ▼ –0.0173   | Still relevant but less dominant                    |
| `visit_days`, `stay_hours`| High    | Lowered        | ▼           | No longer key predictors                            |

**Summary Table**:

| Period        | Top Predictors                         | Behavioural Focus             |
|---------------|----------------------------------------|-------------------------------|
| Pandemic      | `visit_days`, `stay_hours`, `density` | Quantity & frequency          |
| Post-Pandemic | `evening time`, `weekend use`, `first_visit_delay` | Timing, intent, planning |

→ Post-pandemic conversion is driven less by volume and more by **behavioural intent and timing**.


### 5.9. Strategic Implications

- **Feature importance is time-sensitive**  
  Predictors that worked in 2021 may no longer apply. Outdated rules can mislead targeting strategies.

- **Time and intent features now dominate**  
  Post-pandemic conversions are associated with **weekday daytime visits**, **low congestion**, and **early planning** (e.g. delayed first visit).

- **Unified modelling across time risks signal dilution**  
  Mixing data across periods may obscure key behavioural differences. Period-specific models or segmentation-aware strategies are necessary.


### 5.10. Summary & Strategic Direction

Based on the findings:

- **Pandemic-era behaviour is no longer a reliable basis** for strategy or modelling.
- All subsequent analysis — including segmentation, predictive modelling, and strategy development — will use **post-pandemic data only**.
- This ensures the analysis reflects **autonomous user behaviour**, not one shaped by external restrictions, and delivers more accurate and actionable insights.


