# Global Energy Transition BI Engine

An enterprise-grade, automated Business Intelligence solution built to process millions of rows of historical energy, emissions, and economic records spanning from 1970 through the mid-2020s. This repository showcases the complete data architecture lifecycle: migrating noisy, siloed flat-file tables into a heavily optimized **Star Schema Data Model** and deploying a three-page interactive performance monitoring ecosystem.

---

## The Core Technical Challenge
Organizations and policymakers currently operate in an analytical blind spot due to the fragmented nature of global energy tracking. Transition datasets are split across isolated operational layers—grid-level production volumes, international greenhouse gas logs, and fluctuating macroeconomic indicators (GDP, population).

This project creates an automated, unified **Single Source of Truth** to:
1. **Model the Decoupling Thesis:** Mathematically isolate whether industrialized nations are successfully breaking the historical dependency loop between rising GDP and rising carbon footprints.
2. **Benchmark Geopolitical Velocity:** Separate real structural infrastructure overhauls from transient climate anomalies or natural market shifts.
3. **Audit Progress Against 2030 Mandates:** Track running performance metrics directly against international climate targets using interactive, non-breaking visuals.

---

## Architecture & Engineering Workflow

### 1. Ingestion & Power Query Ingestion Pipeline (M-Code)
* **Live Folder Ingestion:** Established programmatic folder links pointing directly to transactional data dumps[cite: 2]. This enables any subsequent annual CSV updates to automatically transform and self-load without manual copy-pasting[cite: 2].
* **Scale Normalization:** Handled extreme unit fragmentation across multi-source outputs, dynamically mapping all generation lines into a singular, cohesive base scale: **Terawatt-hours (TWh)**.
* **Macro Imputation:** Standardized economic data points down to base billion-dollar scales ($1B USD) to protect numeric precision during advanced divisions and gracefully handle early historical null rows.

### 2. High-Performance Relational Star Schema
To prevent performance lag, looping relationship chains, and many-to-many filtering traps, the data backend was built into an optimized **Star Schema** configuration:


```

              +--------------------------------+
              |         Dim_Calendar           |
              |     (True Date Data Type)      |
              +---------------+----------------+
                              |
                              | 1:N Join
                              v

+------------------+     +--------+-------+     +--------------------+
|  Dim_Geography   |1:N  |  Fact_Energy_  | N:1 | Dim_Energy_Source  |
| (Sanitized Text) +---->|  Annual_Core   |<----+  (Strict Fuel Mix  |
+------------------+     +----------------+     |   Classifications) |
+--------------------+


```
* **`Fact_Energy_Annual_Core`:** The central transaction engine holding absolute generation numbers, consumption volumes, and emission metrics.
* **`Dim_Geography`:** Standardized entity dimension holding sanitized regional lookup codes.
* **`Dim_Calendar`:** A continuous calendar ledger driving context-independent time intelligence.
* **`Dim_Energy_Source`:** Strict mapping tables organizing power assets directly into Fossil, Nuclear, and isolated Renewable branches.

```
---


## Comprehensive DAX Metrics Library

The solution uses custom Data Analysis Expressions (DAX) to enable advanced analytical capabilities on every dashboard page:

### Page 1: Macro Global Transition Baseline
* **Total Grid Consumption:** Summates macro consumption across the database layout.
  ```dax
  Total_Consumption_TWh = SUM('Fact_Energy_Annual_Core'[Consumption_Volume])

```

* **Renewable Share %:** Computes the true fraction of green energy powering the active filter context.


```dax
Renewable_Share = 
DIVIDE(
    CALCULATE([Total_Consumption_TWh], 'Dim_Energy_Source'[Category] = "Renewable"),
    [Total_Consumption_TWh],
    0
)

```


* **YoY Renewable Growth Rate:** Measures generation deployment speed via `SAMEPERIODLASTYEAR` time intelligence.


```dax
YoY_Renewable_Growth_Rate = 
VAR CurrentRenewable = CALCULATE([Total_Consumption_TWh], 'Dim_Energy_Source'[Category] = "Renewable")
VAR PrevYearRenewable = CALCULATE([Total_Consumption_TWh], 'Dim_Energy_Source'[Category] = "Renewable", SAMEPERIODLASTYEAR('Dim_Calendar'[Date]))
RETURN DIVIDE(CurrentRenewable - PrevYearRenewable, PrevYearRenewable, 0)

```



### Page 2: Policy & Geopolitical Profiling

* **Volumetric Consumer Scale Scaling:** Converts production TWh to standard consumer Kilowatt-hours ($1\text{ TWh} = 1,000,000,000\text{ kWh}$).
```dax
Total_Generated_Energy_kWh = SUM('Fact_Energy_Annual_Core'[Generation_Volume]) * 1000000000

```


* **Renewable Per Capita (kWh / Person):** Normalizes volume metrics against regional population scales to expose real infrastructure access.


```dax
Renewable_Per_Capita = 
VAR CleanPower_kWh = CALCULATE([Total_Generated_Energy_kWh], 'Dim_Energy_Source'[Category] = "Renewable")
VAR TotalPop = SUM('Fact_Energy_Annual_Core'[Population])
RETURN DIVIDE(CleanPower_kWh, TotalPop, 0)

```


* **Fossil Fuel Base Mix %:** Isolates exactly how dependent a filtered geographic boundary remains on carbon-heavy infrastructure.


```dax
Fossil_Mix_Percentage = DIVIDE(CALCULATE(SUM('Fact_Energy_Annual_Core'[Generation_Volume]), 'Dim_Energy_Source'[Category] = "Fossil"), SUM('Fact_Energy_Annual_Core'[Generation_Volume]), 0)

```



### Page 3: Sustainability Efficiency Trends

* **Macro Energy Intensity (Economic Efficiency):** Tracks structural waste by calculating the raw kWh consumed to output $1B of national GDP ($kWh / \$1B \text{ GDP}$).


```dax
Energy_Intensity_Per_GDP = 
VAR TotalPower_kWh = SUM('Fact_Energy_Annual_Core'[Consumption_Volume]) * 1000000000
VAR TotalGDP_Billion = SUM('Fact_Energy_Annual_Core'[GDP_Adjusted]) / 1000000000
RETURN DIVIDE(TotalPower_kWh, TotalGDP_Billion, 0)

```


* **Core Carbon Intensity (The Decoupling Benchmark):** Tracks true grid performance by measuring grams of greenhouse gases emitted per generated kWh ($\text{gCO}_2\text{e/kWh}$).


```dax
Carbon_Intensity = DIVIDE([Global_GHG_Emissions], [Total_Generated_Energy_kWh], 0)

```


* **10-Year Shifting Renewable CAGR:** Computes the compound annual growth rate of renewables over a shifting 10-year block to monitor policy acceleration velocity. Uses `ALLSELECTED` to safely preserve baseline historical boundaries regardless of active timeline filters.


```dax
Renewable_CAGR_10Yr = 
VAR CurrentYear = MAX('Dim_Calendar'[Year])
VAR StartYear = CurrentYear - 10
VAR CurrentValue = CALCULATE(SUM('Fact_Energy_Annual_Core'[Generation_Volume]), 'Dim_Energy_Source'[Category] = "Renewable")
VAR StartValue = CALCULATE(SUM('Fact_Energy_Annual_Core'[Generation_Volume]), FILTER(ALLSELECTED('Dim_Calendar'), 'Dim_Calendar'[Year] = StartYear), 'Dim_Energy_Source'[Category] = "Renewable")
RETURN IF(NOT(ISBLANK(StartValue)) && StartValue > 0, IFERROR((CurrentValue / StartValue) ^ (1 / 10) - 1, 0), BLANK())

```



---

## Strategic Production Insights

* **The 2030 Performance Gap:** While global renewable share has advanced to **25.27%**, target gauge mapping explicitly proves the global position is severely trailing the international 2030 climate mandate of **60.00%**.


* **Decoupling Mathematically Validated:** Cross-examining emissions directly against production volumes reveals that global carbon intensity is trending downward while generation scales upward. This confirms that industrial regions are successfully producing more net power with a smaller environmental footprint per kWh.


* **The Clean Supply Vulnerability:** Treemap analysis uncovers that **70.44% of total global renewable generation remains heavily anchored to legacy Hydroelectric assets**. While modern Wind and Solar curves are charting hockey-stick exponential paths, the grid's immediate heavy reliance on hydro leaves clean power highly vulnerable to localized, climate-driven water volatility.



---

## Deployment Instructions

1. Clone this repository to your local machine.
2. Place the source dataset updates inside your localized transaction folder path to let the **Power Query ETL engine** handle automated data cleaning.


3. Open `Global_Energy_Transition.pbix` in Power BI Desktop to inspect the live **Star Schema model** and interact with the dashboards.



---

### Technical Summary Matrix

* **ETL Pipeline Layer:** Built with Power Query (M-Code) for automated data cleaning, scale normalization, and future update processing.


* **Modeling Infrastructure:** Strictly engineered to a Star Schema configuration to ensure rapid query processing times and eliminate filtering traps.


* **Analytical Engine:** Advanced DAX library mapping economic metrics ($kWh/\$1\text{B GDP}$) alongside grid cleanliness benchmarks ($\text{gCO}_2\text{e/kWh}$).



```
