# ReadMe: Global Catering Productivity Dashboard

## 1. Data Model Sketch
The original flat JSON was transformed into a Star Schema via Power Query:
* **Dimensional Normalization:** Extracted a distinct `Region_Unit` table and a `Date_Table`.
* **Customer Hierarchy:** Extracted the 3-letter Customer code from the `Unit_Customer` column to enable Customer-level baseline toggle.
* **No external dependencies:** The dataset is embedded directly into the Power Query M-code (Advanced Editor) as a static table.

---

## 2. Key DAX Snippets

### Weighted Contribution (Waterfall Variance)
To ensure regional variances sum to the total global gap (since Productivity is a ratio), performance is weighted by TWH share:

```dax
-- 1. TWH_Weighted_Prod_YTD = 
VAR _AllRegTWH = CALCULATE(TOTALYTD([Sum_TWH], 'Date_Table'[Date]), ALL(Region_Unit[Region]))
RETURN DIVIDE([TWH_YTD], _AllRegTWH) * [Productivity_YTD]

-- 2. TWH_Weighted_Prod_LY = 
VAR _TWH_YTD_LY_All_Reg = CALCULATE([TWH_YTD_LY], ALL(Region_Unit[Region]))
VAR _share = DIVIDE([TWH_YTD_LY], _TWH_YTD_LY_All_Reg)
RETURN _share * [Productivity_YTD_LY]

-- 3. TWH_SHARE_DIFF = 
ROUND( [TWH_Weighted_Prod_YTD] - [TWH_Weighted_Prod_LY], 2 )
```

### Z-Score Logic
Calculated the Z-Score based on a unit's deviation from the regional median. 

```dax
-- 1. ZScore_Outlier_Deviation = 
VAR _CurrentDev = [Deviation_from_Median]
VAR _StdDev = CALCULATE( 
    STDEVX.P(VALUES(Region_Unit[Unit]), [Deviation_from_Median]),   
    REMOVEFILTERS(Region_Unit[Unit]), 
    VALUES(Region_Unit[Region])
)
RETURN DIVIDE(_CurrentDev, _StdDev)

-- 2. Zscore_Outlier_Flag = 
IF( ABS([ZScore_Outlier_Deviation]) > 2, 1, 0 )
```

---

## 3. Assumptions
* **Aggregate Division:** Regional/Global KPIs are calculated as `SUM(WFCE) / SUM(TWH)`. As per instructions, unit-level KPIs are never averaged to reach a regional total.
* **Rounding:** Standard KPIs use `ROUND([Measure], 2)`, while percentage measures use `ROUND([Measure], 4)` for accurate percentage display (e.g., 14.45%).
* **Visual Indicators:** The Heatmap uses a red-to-green scale for median deviations, while the Trend chart employs dynamic Y-axis max/min bounds to maximize readability in tight data ranges.
* **Trend Chart Dual-Line Logic:** Incorporates two reference lines:
  * **3-Month Rolling Average:** Shows the historical trend (reacts to geo-filters, but ignores the baseline toggle and Calculation Group).
  * **Dynamic Baseline (Median):** Interactive, adjusting to the selected aggregation toggle and Calculation Group.
* **Default Latest Month Logic:** KPI cards and the Waterfall chart default to the latest available month but update dynamically when a specific month is selected. The Heatmap displays the full historical period, while the Z-Score flag remains fixed on the latest month.
* **Outlier Definition:** Assumed that any unit exceeding a Z-Score of |2| standard deviations is a significant operational outlier, and flagged it accordingly.

---

## 4. Performance Notes
* **Time Intelligence:** Used a Calculation Group slicer for the Trend Chart, while maintaining explicit DAX for the Waterfall, KPI card, and Heatmap.
* **Optimized Architecture:** The data is organized into a clear structure (Fact and Dimension tables). All DAX measures are centralized in a Measure Table and organized into folders for easy maintenance.
* **Variable (VAR):** All complex measures use variables to cache intermediate results.
