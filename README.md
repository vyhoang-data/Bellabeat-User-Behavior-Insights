# Bellabeat-User-Behavior-Insights
Analyze the behaviors of Bellabeat users to uncover valuable insights into how consumers interact with their smart devices. Utilize these findings to guide the development of Bellabeat's products, including the app and wearable devices. This will help shape the company's marketing strategy and create new growth opportunities.

### **Company:** Bellabeat

### **Project Type:** Personal Business Case (Non-commercial)

### **Contributor:** Vy Hoang (Data Analyst)

---

## 📌 Executive Summary

Bellabeat designs health-first wearables and apps for women. This project analyzes device usage data from ~30 users to surface clear behavioral patterns and engagement gaps. The goal is to translate behavioral signals (when and how users move, when they disengage, which features they use) into product and marketing actions that increase long-term engagement and health outcomes.

**Problem:** Bellabeat lacks detailed, behavior-driven knowledge about how people actually use their trackers and app features. Without this knowledge, marketing and product decisions are often generic.

**Goal:** Discover how users interact with Bellabeat devices and the app, then use those insights to craft targeted product changes and marketing strategies that drive engagement and retention.

**Key stakeholders:** Urška Sršen (Co-founder & Chief Creative Officer), Sandro Mur (Co-founder), Marketing & Analytics teams.

---

## 🛠 Tools & Tech Stack

* **Data Cleaning & Transformation:** Google BigQuery (SQL)
* **Data Visualization:** Tableau Public
* **Data Source:** [FitBit Fitness Tracker Data (Kaggle)](https://www.kaggle.com/datasets/arashnic/fitbit)

---

## 📊 Key Findings & Insights

After exploring hourly and daily device logs, these patterns emerged and define the primary Bellabeat user persona(s).

### 1. The "9-to-5" Routine (Weekday Behavior)

* **Morning commute:** Steps rise between **7–9 AM**, indicating commuting or morning routines.
* **Lunch spike:** Notable increase around **12 PM (~525 steps)**.
* **Evening peak:** Highest activity at **7 PM (~575 steps)** — likely post-work movement.
* **Afternoon slump:** Lowest movement at **3 PM (~328 steps)** consistent with sedentary office/student activity.

### 2. The "Weekend Shifter" (Weekend Behavior)

* **Later start:** Users are less active in mornings and evenings on weekends.
* **Midday peak:** Activity concentrates between **10 AM–4 PM**, peaking at **2 PM (~572 steps)**.
* **Incidental movement:** Hourly peaks (~600 steps) are well below a 30-minute walking benchmark (≈3,000–4,000 steps) *(The Health News Team, 2022)*, indicating light or incidental movement rather than dedicated workouts.

### 3. The "Engagement Gap" (Feature Reliance)

* **Automated data (steps):** High coverage — users rarely miss these.
* **Semi-automated (sleep):** Moderately captured, but engagement is lower than automated metrics.
* **Manual (weight, manual activity logs):** Significant drop-off in recording frequency.

**Hypothesis:** Manual logging creates friction. Users may also have privacy concerns or low perceived value from manual entry.

### 4. The "Sedentary Reality"

* **71% of recorded time is sedentary.**
* This indicates either a true sedentary lifestyle, under-utilization of wearable features, or both.

---

## 💡 Strategic Recommendations

The product experience should focus on **reducing friction through automation**, ensuring that users no longer need to manually track essential health data. By enabling features such as **automatic sleep detection**, **smart scale integration**, and **automatic activity recognition**, Bellabeat can remove common engagement barriers and provide a more seamless user experience.

To deepen personalization, Bellabeat can leverage **machine learning to cluster users based on behavioral patterns**, such as identifying “weekday sitters” or “weekend movers.” This would allow the app to deliver **tailored notifications, goals, and micro-actions**, especially during known slump periods like **3 PM on weekdays**. Personalization should extend to subtle nudges, including brief activity suggestions and recovery reminders, which feel contextually relevant and supportive rather than intrusive.

To build long-term engagement, Bellabeat should incorporate **motivation loops** such as micro-goals, streaks, and badges that acknowledge even small achievements. Since many users are largely sedentary, celebrating incremental progress can create a strong sense of momentum. Weekend-focused challenges, when users have more flexibility, offer another opportunity to drive healthier habits.

Bellabeat must also reinforce **data privacy and transparency**, especially around sensitive health metrics. A simple, user-friendly privacy dashboard and clear messaging about encryption can help users feel secure while interacting with the platform.

### Marketing Strategy

Marketing efforts should emphasize **behavior-based communication**, especially targeting the predictable rhythm of 9–5 users. Timely nudges—such as a reminder during the afternoon slump or an encouragement before the evening activity peak—can drive meaningful increases in activity. Weekend campaigns should highlight longer, social, or guided wellness activities that match the natural weekend behavior shift.

Bellabeat can also strengthen its brand by offering **short, actionable wellness content** tied to the user’s real data, such as hydration reminders, mobility routines, or sleep hygiene tips. Finally, strategic partnerships with **smart scale manufacturers, wellness apps, or employers** can expand Bellabeat’s ecosystem and introduce new channels for user acquisition and engagement.

### Marketing Tactics

1. **Behavior-based messaging**

   * Target 9–5 customers with afternoon slump nudges and evening activity challenges.
   * Use weekend campaigns that promote longer, social or guided activities.

2. **Content & Education**

   * Produce short, actionable content: 5-minute mobility routines, hydration reminders, sleep hygiene tips tied to real metrics.

3. **Partnerships**

   * Explore integrations with smart scales, wellness apps, or employers (corporate wellness bundles).

---

## 📈 Data Analysis & SQL Overview

This project used BigQuery to compute participation, activity distributions, and hourly trends. Below are key SQL snippets (adapt for your dataset paths):

### Participation Rates (Feature Reliance)

```sql
SELECT 'daily_activites' AS category, COUNT(DISTINCT Id) AS num_users
FROM `project.dataset.daily_activity`

UNION ALL

SELECT 'sleep_minutes' AS category, COUNT(DISTINCT Id) AS num_users
FROM `project.dataset.sleep_minutes`

UNION ALL

SELECT 'weight_log_info' AS category, COUNT(DISTINCT Id) AS num_users
FROM `project.dataset.weight_log_info`;
```

### Activity Distribution (Sedentary vs Active)

```sql
SELECT
  ROUND(AVG(SedentaryMinutes),0) AS avg_sedentary_minutes,
  ROUND(AVG(LightlyActiveMinutes),0) AS avg_lightly_active_minutes,
  ROUND(AVG(FairlyActiveMinutes),0) AS avg_fairly_active_minutes,
  ROUND(AVG(VeryActiveMinutes),0) AS avg_very_active,
  ROUND(AVG(SedentaryMinutes)/1440*100,1) AS sedentary_pct,
  ROUND(AVG(LightlyActiveMinutes)/1440*100,1) AS lightly_active_pct,
  ROUND(AVG(FairlyActiveMinutes)/1440*100,1) AS fairly_active_pct,
  ROUND(AVG(VeryActiveMinutes)/1440*100,1) AS very_active_pct
FROM `project.dataset.daily_activity`;
```

### Weekday vs. Weekend Trends

```sql
SELECT
  CASE
    WHEN FORMAT_DATE('%A', PARSE_TIMESTAMP('%m/%d/%Y %I:%M:%S %p', ActivityHour))
         IN ('Saturday', 'Sunday') THEN 'Weekend'
    ELSE 'Weekday'
  END AS day_type,
  EXTRACT(HOUR FROM PARSE_TIMESTAMP('%m/%d/%Y %I:%M:%S %p', ActivityHour)) AS hour_of_day,
  ROUND(AVG(StepTotal), 0) AS avg_steps
FROM `project.dataset.hourly_steps`
GROUP BY day_type, hour_of_day
ORDER BY day_type, hour_of_day;
```

## 🎨 Dashboard & Visualizations

An interactive Tableau Public dashboard was built to explore weekday vs. weekend differences, hourly step trends, and engagement across features.

👉 **[View Dashboard on Tableau Public](https://public.tableau.com/app/profile/vy.hoang7117/viz/BellaBeatUserBehaviorInsights/BellaBeatAnalysis)**

---

## 💭 What I Learned

I learned that SQL queries are just the beginning. The real value comes from translating a query result (like "575 steps at 7 PM") into a human behavior (the post-work commute) and a business strategy.

Moreover, Using Tableau, I learned how to highlight the difference between datasets (Weekday vs. Weekend) rather than just plotting points, making the insights obvious to stakeholders immediately.

---

## 📚 References

* FitBit Fitness Tracker Data (Kaggle) — [https://www.kaggle.com/datasets/arashnic/fitbit](https://www.kaggle.com/datasets/arashnic/fitbit)
* Sharp Health News: How 7,000 Steps Can Save Your Life — [https://www.sharp.com/health-news/how-7-000-steps-per-day-can-save-your-life](https://www.sharp.com/health-news/how-7-000-steps-per-day-can-save-your-life)

---

## 🔒 License & Notes

This project is a **personal business case** and is non-commercial. If you reuse any part of this work, please credit the contributor: **Vy Hoang (Data Analyst)**.

---
**Contact**

**LinkedIn:** [Vy Hoang](https://www.linkedin.com/in/vyhoang-ussh/) | **Email:** vy.hoangphamuyen@gmail.com
