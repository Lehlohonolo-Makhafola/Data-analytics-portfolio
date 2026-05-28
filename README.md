## Project Overview
Analysis of 1,020 employee records across 
6 departments and 6 regions. Project covers 
the full data analytics pipeline from raw 
messy CSV data through to cleaned dataset, 
pivot table analysis and interactive dashboard.

## Tools Used
Microsoft Excel — Data Cleaning, Pivot Tables,
Text to Columns, Power Query, Dashboard Design,
Conditional Formatting, Slicers

## Dataset Source
Messy Employee Dataset — CSV format converted 
to Excel for analysis

## Data Quality Issues Found and Fixed
- Negative phone numbers corrected across 
  all 1,020 rows
- Department and Region split from single 
  combined column into two separate columns 
  using Text to Columns
- Missing age values standardised as Unknown 
  across 211 affected rows
- Salary column converted from text to numeric 
  to enable financial analysis
- Employee first and last names combined into 
  single Full Name column
- Phone numbers reformatted as text to prevent 
  numeric manipulation

## Key Insights
- Sales and Finance departments carry the 
  highest average salaries
- Over 34% of employees are in Pending status 
  representing a significant workforce 
  management gap
- Headcount is evenly distributed across 
  6 departments with Cloud Tech marginally 
  the smallest team
- Remote and office based employees are split 
  almost exactly 50/50 across all departments

## Project Structure
- Messy_Employee_Dataset — original raw CSV 
  data preserved and untouched
- Employee_Dataset — cleaned and transformed 
  working data
- Pivot_Tables — analysis across headcount, 
  salary, performance and status metrics
- Dashboard — final interactive visualisation 
  with slicers

## Dashboard Features
- KPI cards — Total Employees, Active Employees,
  Average Salary, Remote Workers
- Headcount by Department — horizontal bar chart
- Employee Status breakdown — donut chart
- Average Salary by Department — bar chart
- Performance Score distribution — bar chart
- Interactive slicers — Department, Region, 
  Status, Remote Work
