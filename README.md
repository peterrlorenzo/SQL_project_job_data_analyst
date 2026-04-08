# SQL Job Data Analyst Project

## Introduction

This project analyzes job market data for Data Analyst and Data Engineer positions using SQL queries. The analysis explores salary trends, in-demand skills, and job opportunities in different locations.

For queries, check out here: [project_sql folder](/project_sql/) 

## Background

The dataset contains:
- Job postings fact table with salary, location, and job title information
- Company dimension table with company names
- Skills dimension table with skill names and IDs
- Skills-to-job mapping table linking jobs to required skills

The analysis focuses on **Data Analyst** and **Data Engineer** positions, with particular emphasis on remote ("Anywhere") and Australia-based opportunities.

## Tools I used

- **SQL** - Data querying and analysis
- **PostgreSQL/SQL Database** - Database management system
- **CSV Files** - Raw data sources (company_dim.csv, job_postings_fact.csv, skills_dim.csv, skills_job_dim.csv)

## The Analysis

### Query 1: Top 10 Highest Paying Jobs
**File:** `first_problem.sql`

Identifies the top 10 highest-paying Data Analyst and Data Engineer positions available anywhere with non-null salary information.

**Key Columns:**
- Job ID, Title, Location, Schedule Type
- Annual Salary, Posted Date
- Company Name

---

### Query 2: Top Paying Jobs & Required Skills
**File:** `second_query.sql`

Uses a CTE to find the top 10 paying jobs, then lists all required skills for each position. Provides insight into which skills are demanded in high-paying roles.

**Result:** Top 10 jobs with their associated skill requirements

---

### Query 3: Top 5 Most Demanded Skills (Australia)
**File:** `third_problem.sql`

Analyzes job demand for skills specifically in Australia, showing which skills are most frequently requested.

**Result:** Top 5 skills by demand count in Australia

---

### Query 4: Average Salary by Skill (Australia)
**File:** `fourth_question.sql`

Calculates the average salary for each skill in Australia, helping identify which skills command higher compensation.

**Result:** Skills ranked by average salary in Australia (top 25)

---

### Query 5: Skills Analysis - Demand & Salary (With CTEs)
**File:** `last_query.sql`

Advanced query using two CTEs:
1. **skills_demand CTE** - Counts job postings for each skill
2. **average_salary CTE** - Calculates average salary per skill

Focuses on remote ("Anywhere") Data Analyst and Data Engineer positions with skills appearing in 10+ job postings.

```sql
WITH skills_demand AS (
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short IN ('Data Analyst', 'Data Engineer') AND
        salary_year_avg IS NOT NULL
    GROUP BY
        skills_dim.skill_id
), average_salary AS (
    SELECT
        skills_dim.skill_id,
        ROUND(AVG(salary_year_avg), 2) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short IN ('Data Analyst', 'Data Engineer') AND
        salary_year_avg IS NOT NULL
    GROUP BY
        skills_dim.skill_id
)

SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 2) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim
    ON
    job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON
    skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short IN ('Data Analyst', 'Data Engineer')
    AND salary_year_avg IS NOT NULL
    AND job_location = 'Anywhere'
GROUP BY
    skills_dim.skill_id
HAVING
    COUNT(skills_job_dim.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```

**Result:** Skills ranked by salary then demand, with filtering for popular skills

---

## What I Learned

- **CTE (Common Table Expression) Usage** - Organizing complex queries with multiple aggregations
- **JOIN Operations** - Combining data across multiple tables efficiently
- **WHERE vs HAVING** - Filtering rows vs filtering aggregated results
- **GROUP BY with Multiple Tables** - Avoiding ambiguous column references
- **Data Analysis Insights:**
  - Remote Data roles command competitive salaries
  - Certain skills have higher average salaries than others
  - Skill demand varies significantly by location
  - Top-paying positions require multiple complementary skills

## Conclusion

This project demonstrates the power of SQL for real-world data analysis. By combining multiple queries and advanced SQL techniques (CTEs, JOINs, aggregations), we can uncover valuable insights about job market trends and skill valuations. The analysis shows that location matters significantly in salary and demand, and that certain skills are consistently valued higher across the job market.