# Financial-Payback-Cash-and-Financing

## **Overview**

This script calculates **monthly and yearly electricity savings**, **loan repayment impact**, and **break-even analysis** for a solar-powered pump system. It integrates solar irradiation patterns, pump power requirements, climate variability, and financial structures (cash vs. financed purchase) to provide a full view of system performance and cost-effectiveness over a 15-year horizon.

---

## **Sections of the Script**

### 1. **Input Section**

#### **A. Energy Use & Technical Inputs**

These arrays are based on the actual energy consumption and technical system sizing:

* `actual_kwh`: Monthly kWh consumption from the user.
* `days_per_month`: Days in each calendar month (used to calculate daily usage).
* `climate_index_h`: Adjusts theoretical performance using climate-specific derating per month.
* `motor_kw`: The rated kW of the pump motor (here, 11 kW).
* `solar_kw`: The installed solar PV array size (here, 15.54 kW).
* `drive_min_pct`: Minimum drive output threshold (35%).
* `loss_factor_hourly`: Losses during hourly conversion (0.0 means none).
* `loss_factor_monthly`: Net energy loss assumed over a month (e.g., 20%).
* `kwh_cost`: The cost per kWh the user would pay using conventional electricity (R2.60).

#### **B. Sunshine Profile**

A stylized 13-point vector (`sunshine_percent`) from Veichi’s documentation simulates solar availability during hours of the day.

---

### 2. **Calculation Block 1: Pump Hours Per Day**

This section computes:

* `kwh_per_day`: Actual energy use per day.
* `monthly_pump_hours`: Divides daily energy by motor size to estimate hours the pump runs per day, per month.

---

### 3. **Calculation Block 2: Hourly Yield**

#### **Drive Output Simulation**

Simulates hourly solar yield:

* `sizing_factor`: Ratio of solar array to motor size.
* `drive_output`: Scales the hourly sunshine pattern against motor capacity.
* `G`: Actual kW output when above the drive minimum threshold.
* `H`: Adjusts for system losses.
* `I`: Efficiency as a proportion of motor output.

#### **Hourly Distribution Matching**

Function `build_excel_matched_distribution_fixed` mimics Excel's bell curve-based hourly spread of runtime.

* For each month, calculates how system output is spread over 13 hours.
* Multiplies the spread by hourly efficiencies to derive:

  * `hourly_yield_j`: Monthly average energy yield per hour of use.

---

### 4. **Calculation Block 3: Monthly and Yearly Savings**

This section calculates:

* `k = J × H`: Adjusted energy yield based on climate.
* `Expenditure`: Total monthly cost of electricity from the grid.
* `Theoretical Savings`: If the system ran unrestricted.
* `Capped Savings`: Limited by actual expenditure.
* `Actual Savings (L)`: Reduced by monthly loss factor.

The results are compiled into a pandas DataFrame `final_df`, and `total_savings` is summed.

---

### 5. **Merged Payback Block**

This section compares two purchasing methods:

#### **Loan-Based Purchase**

* Uses:

  * `system_cost`: Total system installation cost (R180,000).
  * `deposit`: Initial payment fraction (10%).
  * `interest_rate_monthly`: 1% monthly interest (12% annual).
  * `loan_period_years`: 10-year loan.
* Calculates:

  * Monthly and annual repayments.
  * Total financed cost (`deposit + total repayments`, negative cash outflow).
  * Builds a yearly cashflow table (`df`) including:

    * Loan repayments
    * Electricity and tax savings
    * Net cashflow and cumulative cashflow
    * Break-even tracking (`Cum Break Even`)
* **Loan Break-Even**: Determines the year and interpolated month where cumulative savings surpass total financed cost.

#### **Cash-Based Purchase**

* Simpler model.
* Compares cumulative savings to upfront system cost (R180,000).
* `calculate_cash_payback_interpolated`: Tracks when cumulative savings offset the capital expenditure.
* Builds a yearly DataFrame `df_cash` showing cumulative net position.

---

## **Output Explanation**

### **Formatted Summary Includes:**

#### **Key Metrics**

* `Loan Amount`: How much is financed after deposit.
* `Monthly/Annual Loan Repayment`: Amortized monthly and yearly costs.
* `Total Financed Cost`: Cumulative loan + deposit (negative cash position).
* `Cost per kWh`: Average over 15 years (cash vs. financed).
* `Break-even (Loan/Cash)`: Time it takes for savings to equal investment.

#### **Detailed Tables**

* **Loan-Based Table (`df`)**:

  * Breaks down annual cashflow and repayment structure.
  * Highlights cumulative benefit and cost balance.

* **Cash-Based Table (`df_cash`)**:

  * Tracks simple recovery of the capital cost over time.

---

## **Important Notes**

* **Assumption-Driven**: Outcomes are highly sensitive to assumptions like inflation rate, electricity cost, and drive behavior.

* **Hard-Coded Elements**:

  * Climate index
  * Sunshine profile (Veichi bell curve)
  * Loss factors and system specifications


* **Interpolation Logic**:

  * Break-even years and months are estimated by linear interpolation between years when cumulative cashflow crosses zero.

---

## **Recommendations for Users**

* Adjust `actual_kwh`, `motor_kw`, `solar_kw`, and `system_cost` to fit your scenario.
* Update `climate_index_h` with regional irradiation impact.
* If available, provide `first_year_savings_kWh` to enable meaningful LCOE comparison.
* Review cashflow tables to identify when system becomes financially beneficial.

## **Excel Test File**

https://webnicpremium-my.sharepoint.com/:x:/g/personal/christiaan_cedarsolar_com/EbQcBgJi0iBNhvJZVWxK9h8BJ7jBtBO5tsUbk9LRi80mqA?e=p8Jelg

