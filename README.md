# Global Catering Productivity Dashboard

## 1. Project Overview & Analysis Scope
This project is an executive-level Power BI dashboard developed for a global airline catering network. It is designed to analyze monthly operational productivity across different regions, operating units, and customer hierarchies. 

The core focus of the analysis is evaluating performance efficiency, tracking Year-Over-Year (YOY) and Month-Over-Month (MOM) growth, and identifying operational deviations. The data model was built from raw JSON files and transformed into a clean Star Schema to support fast and scalable analytics.

##  Live Demo  
  Check out the dashboard here: [Productivity Analysis Dashboard](https://app.powerbi.com/view?r=eyJrIjoiZjI4NzU3NTgtMWYxZC00Yzg2LWEyMGQtNTE2YTk5MGNjNTI4IiwidCI6ImJkMGQ4ZDNmLTJjODYtNGRhMC04Y2FhLWZlNjFlNzNlNGQ5MyIsImMiOjEwfQ%3D%3D)

---

## 2. Key Visuals & Analytical Methods
To translate complex data into actionable insights, the dashboard utilizes the following visuals and underlying methods:

* **Smart KPI Cards:** Provide an instant snapshot of top-level productivity metrics. 
  * *Method:* Uses advanced DAX with intelligent error handling. Instead of breaking or showing blanks when previous year/month data is missing, it dynamically displays user-friendly text like *"No LY Data"*.
* **Interactive Trend Chart:** Visualizes historical performance and trajectory.
  * *Method:* Employs a dual-line logic comparing a **3-Month Rolling Average** against a dynamically shifting **Median Baseline**. It uses Calculation Groups to allow users to seamlessly switch between Time Intelligence views (MTD, QTD, YTD) without cluttering the model.
* **Waterfall Variance Chart:** Explains the "why" behind performance shifts.
  * *Method:* Uses a **Weighted Contribution Method**. Because productivity is a ratio, regional performance gaps are weighted by their Total Working Hours (TWH) share to ensure they sum perfectly to the total global variance.
* **Conditionally Formatted Heatmap:** Highlights unit-level performance over time.
  * *Method:* Applies **Z-Score Statistical Logic**. It calculates each unit's deviation from the regional median and automatically flags extreme outliers (deviations >2 standard deviations) with clear visual indicators.

---

## 3. Business Value: What the User Gains
By using this dashboard, executives and regional managers receive a clear, noise-free view of their operations. The key benefits include:

* **Instant Outlier Detection:** Management no longer needs to dig through spreadsheets. The system automatically highlights which specific units are underperforming and require immediate attention.
* **Accurate Root-Cause Analysis:** The weighted waterfall chart clearly illustrates exactly which regions are driving growth or dragging down global productivity.
* **Dynamic Benchmarking:** Users can interactively toggle baselines (Global, Regional, or Customer-specific) to see how individual units stack up against their relevant peers.
* **Fast, Data-Driven Decisions:** With a clean UI/UX and an optimized backend, decision-makers get immediate, reliable answers to guide their operational strategies.
