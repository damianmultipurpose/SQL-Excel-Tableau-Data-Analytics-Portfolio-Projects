
## Table of Contents
- [Project Overview](#project-overview)
- [Excell Pivot Table](#excel-pivot-table)
- [Tableau Interactive Dashboard](#tableau-interactive-dashboard)
- [Data Sources](#data-sources)
- [Tools Used](#tools-used)
- [Data Cleaning/Preparation](#data-cleaningpreparation)
- [Exploratory Data Analysis ](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results/Findings](#resultsfindings)
- [ Reccomendations](#reccomendations)
- [  Limitations](#limitations)
- [References](#references)

---
### Project Overview
This project demonstrates a comprehensive analysis of multiple datasets using SQL, Excel, and Tableau to showcase data analysis and visualization skills. Each tool was applied to a separate dataset tailored for specific tasks, including:
- SQL Data Analysis: Focused on analyzing COVID-19 cases, deaths, and vaccination trends.
- SQL Data Cleaning: Addressed data inconsistencies and prepared a new dataset for advanced analysis.
- Excel: Used for creating pivot tables to summarize data trends.
- Tableau: Developed an interactive dashboard to visualize insights.

---

### Data Sources
 #### SQL Data Analysis:
 
- Dataset: CovidDeaths and CovidVaccinations from [Our World in Data](https://ourworldindata.org/covid-deaths)
- Process: Filtered in Excel to focus on key metrics like location, date, total_cases, new_cases, total_deaths, and population.
- Prepared Files: Imported into SQL Server as two tables (CovidDeaths and CovidVaccinations).

#### SQL Data Cleaning:
- Dataset: (Placeholder for the dataset to be specified).
- Process: Cleaned inconsistencies like null values, duplicates, and formatting issues.

#### Excel:
- Dataset: A trimmed dataset tailored for pivot table analysis (to be specified).
- Process: Organized and summarized data trends.
- Output: Creating Pivot tables and charts

#### Tableau:
- Dataset: Preprocessed dataset for visualization (to be specified).
- Process: Used for interactive dashboards showing COVID-19 insights.

---

### Tools Used
- SQL Server- For data analysis and cleaning. [See here](https://instagram.com/damianmultipurpose)
- Excell-For data preparation and pivot tables.  [See here](https://instagram.com/damianmultipurpose)
- Tableau-For creating interactive dashboards.  [See here](https://instagram.com/damianmultipurpose)

---
### Data Cleaning/Preparation
This section demonstrates SQL techniques used to clean and prepare datasets for analysis. (Placeholder for details on dataset, cleaning steps, and sample queries).

#### Key Techniques Used:
- Removing duplicates
- Handling null values
- Normalizing data formats

---
### Exploratory Data Analysis
This section includes queries and insights derived from analyzing the CovidDeaths and CovidVaccinations tables.

Key Questions Explored:

1.Total Cases vs. Total Deaths: What is the likelihood of dying if infected with COVID?

2.Infection Rate Per Population: Which locations have the highest infection rates?

3.Vaccination Impact: How do vaccination rates correlate with reduced deaths?

---
### Data Analysis
Include some interesting codes/features worked with
```sql
/*
Covid 19 Data Exploration 

Skills used: Joins, CTE's, Temp Tables, Windows Functions, Aggregate Functions,Aliasing with Aggregate Functions, Creating Views, Converting Data Types

*/
--select all data from  CovidDeaths Table  
Select *
From ProjectPortfolio..CovidDeaths
Where continent is not null 
order by 3,4
--select all data from  CovidVaccination Table  
Select *
From ProjectPortfolio..CovidVaccination
Where continent is not null 
order by 3,4

-- Select Data that we are going to be starting with

Select Location, date, total_cases, new_cases, total_deaths, population
From ProjectPortfolio..CovidDeaths
Where continent is not null 
order by 1,2


-- Total Cases vs Total Deaths
-- Shows likelihood of dying if you contract covid in your country

Select Location, date, total_cases,total_deaths, (total_deaths/total_cases)*100 as DeathPercentage
From ProjectPortfolio..CovidDeaths
Where location like '%Tanzania%'
and continent is not null 
order by 1,2


-- Total Cases vs Population
-- Shows what percentage of population infected with Covid

Select Location, date, Population, total_cases,  (total_cases/population)*100 as PercentPopulationInfected
From ProjectPortfolio..CovidDeaths
--Where location like '%Tanzania%'
order by 1,2


-- Countries with Highest Infection Rate compared to Population

Select Location, Population, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
From ProjectPortfolio..CovidDeaths
--Where location like '%Tanzania%'
Group by Location, Population
order by PercentPopulationInfected desc


-- Countries with Highest Death Count per Population

Select Location, MAX(cast(Total_deaths as int)) as TotalDeathCount
From ProjectPortfolio..CovidDeaths
--Where location like '%Tanzania%'
Where continent is not null 
Group by Location
order by TotalDeathCount desc



-- BREAKING THINGS DOWN BY CONTINENT

-- Showing contintents with the highest death count per population

Select continent, MAX(cast(Total_deaths as int)) as TotalDeathCount
From ProjectPortfolio..CovidDeaths
--Where location like '%%'
Where continent is not null 
Group by continent
order by TotalDeathCount desc



-- GLOBAL NUMBERS
--total_cases,total_deaths_Death percentage globally

Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
From ProjectPortfolio..CovidDeaths
--Where location like '%states%'
where continent is not null 
--Group By date
order by 1,2



-- Total Population vs Vaccinations
-- Shows Percentage of Population that has recieved at least one Covid Vaccine

Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From ProjectPortfolio..CovidDeaths dea
Join ProjectPortfolio..CovidVaccination vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 
order by 2,3


-- Using CTE to perform Calculation on Partition By in previous query

With PopvsVac (Continent, Location, Date, Population, New_Vaccinations, RollingPeopleVaccinated)
as
(
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From ProjectPortfolio..CovidDeaths dea
Join ProjectPortfolio..CovidVaccination vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 
--order by 2,3
)
Select *, (RollingPeopleVaccinated/Population)*100
From PopvsVac



-- Using Temp Table to perform Calculation on Partition By in previous query

DROP Table if exists #PercentPopulationVaccinated
Create Table #PercentPopulationVaccinated
(
Continent nvarchar(255),
Location nvarchar(255),
Date datetime,
Population numeric,
New_vaccinations numeric,
RollingPeopleVaccinated numeric
)

Insert into #PercentPopulationVaccinated
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From ProjectPortfolio..CovidDeaths dea
Join ProjectPortfolio..CovidVaccination vac
	On dea.location = vac.location
	and dea.date = vac.date
--where dea.continent is not null 
--order by 2,3

Select *, (RollingPeopleVaccinated/Population)*100
From #PercentPopulationVaccinated




-- Creating View to store data for later visualizations

Create View PercentPopulationVaccinated as
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
--, (RollingPeopleVaccinated/population)*100
From ProjectPortfolio..CovidDeaths dea
Join ProjectPortfolio..CovidVaccination vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 


```


---

### Excel
Details on using Excel to create pivot tables for data trends. (Placeholder for specifics on dataset and process).
#### Example Pivot Table:
![9239](https://github.com/user-attachments/assets/a4ddbb19-7408-4165-b219-9844d3eb1269)

---
### Tableau
Details on creating interactive dashboards in Tableau to visualize insights. (Placeholder for specifics on dataset and dashboard features).
#### Example Dashboard:
![9208](https://github.com/user-attachments/assets/06ffa572-7ec2-4610-8e5b-a68511f81392)

---


### Results/Findings
Key insights from the analysis:
1.Countries with higher vaccination rates had lower death rates.

2.Regions like Europe and the Americas were most impacted in terms of cases and deaths.

3.Vaccination campaigns significantly reduced new infections.

---

### Recomendations
1.Promote vaccination efforts in low-coverage areas.

2.Allocate resources to regions with high infection rates.

3.Develop long-term monitoring systems for better health interventions.


---
### Limitations
1.The analysis is based on publicly available data and may not reflect real-time scenarios.

2.Limited scope to key metrics; further insights require granular data (e.g., by age or comorbidities).

3.Data quality depends on accurate reporting from various countries.

---

### References

 [Our World in Data - COVID-19 Dataset](https://ourworldindata.org/covid-deaths)
 
 [@AlexTheAnalyst Youtube Channel](https://www.youtube.com/@AlexTheAnalyst)

 [@freeCodeCamp.Org Youtube Channel](https://www.youtube.com/watch?v=PSNXoAs2FtQ&t=8234s)
 
 
 
