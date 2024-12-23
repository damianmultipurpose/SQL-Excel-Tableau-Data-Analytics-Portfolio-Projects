
## Table of Contents
- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools Used](#tools-used)
- [Data Cleaning/Preparation](#data-cleaningpreparation)
- [Exploratory Data Analysis ](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Excel Pivot](#excel-pivot)
- [Tableau Dashboard](#tableau-dashboard)
- [Results/Findings](#resultsfindings)
- [Recomendations](#recomendations)
- [Limitations](#limitations)
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
- Dataset: Nashville Housing Data for Data Cleaning.[damianmultipurpose-github](https://github.com/damianmultipurpose/SQL-Excel-Tableau-Data-Analytics-Portfolio-Projects/blob/main/Nashville%20Housing%20Data%20for%20Data%20Cleaning.xlsx)
- Process: Cleaned inconsistencies like null values, duplicates, and formatting issues.

#### Excel:
- Dataset: A trimmed dataset tailored for pivot table analysis (to be specified).
- Process: Organized and summarized data trends.
- Output: Creating Pivot tables and charts

#### Tableau:
- Dataset: Preprocessed dataset for visualization [kaggle-dataset](https://public.tableau.com/app/profile/damian.kimaro/viz/AirBnBFullProject_17349102691680/Dashboard1).
- Process: Used for interactive dashboards showing AirBnb listings and trends.

---

### Tools Used
- SQL Server- For data analysis and cleaning. [Explore My SQL Data Cleaning and Analysis Project](https://github.com/damianmultipurpose/SQL-Excel-Tableau-Data-Analytics-Portfolio-Projects/tree/main/SQL_Projects)
- Excell-For data preparation and pivot tables.  [See here](https://instagram.com/damianmultipurpose)
- Tableau-For creating interactive dashboards.  [Explore AirBnb Dashboard here](https://public.tableau.com/app/profile/damian.kimaro/viz/AirBnBFullProject_17349102691680/Dashboard1)

---
### Data Cleaning/Preparation
Include some interesting codes/features worked with
```sql
/*

Cleaning Data in SQL Queries

*/


Select *
From ProjectPortfolio.dbo.NashvilleHousing

--------------------------------------------------------------------------------------------------------------------------

-- Standardize Date Format


Select SaleDateConverted, CONVERT(Date,SaleDate)
From ProjectPortfolio.dbo.NashvilleHousing


Update NashvilleHousing
SET SaleDate = CONVERT(Date,SaleDate)

-- If it doesn't Update properly

ALTER TABLE NashvilleHousing
Add SaleDateConverted Date;

Update NashvilleHousing
SET SaleDateConverted = CONVERT(Date,SaleDate)


 --------------------------------------------------------------------------------------------------------------------------

-- Populate Property Address data

Select *
From ProjectPortfolio.dbo.NashvilleHousing
--Where PropertyAddress is null
order by ParcelID



Select a.ParcelID, a.PropertyAddress, b.ParcelID, b.PropertyAddress, ISNULL(a.PropertyAddress,b.PropertyAddress)
From ProjectPortfolio.dbo.NashvilleHousing a
JOIN ProjectPortfolio.dbo.NashvilleHousing b
	on a.ParcelID = b.ParcelID
	AND a.[UniqueID ] <> b.[UniqueID ]
Where a.PropertyAddress is null


Update a
SET PropertyAddress = ISNULL(a.PropertyAddress,b.PropertyAddress)
From ProjectPortfolio.dbo.NashvilleHousing a
JOIN ProjectPortfolio.dbo.NashvilleHousing b
	on a.ParcelID = b.ParcelID
	AND a.[UniqueID ] <> b.[UniqueID ]
Where a.PropertyAddress is null




--------------------------------------------------------------------------------------------------------------------------

-- Breaking out Address into Individual Columns (Address, City, State)


Select PropertyAddress
From ProjectPortfolio.dbo.NashvilleHousing
--Where PropertyAddress is null
--order by ParcelID

SELECT
SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress) -1 ) as Address
, SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress) + 1 , LEN(PropertyAddress)) as Address

From ProjectPortfolio.dbo.NashvilleHousing


ALTER TABLE NashvilleHousing
Add PropertySplitAddress Nvarchar(255);

Update NashvilleHousing
SET PropertySplitAddress = SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress) -1 )


ALTER TABLE NashvilleHousing
Add PropertySplitCity Nvarchar(255);

Update NashvilleHousing
SET PropertySplitCity = SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress) + 1 , LEN(PropertyAddress))




Select *
From ProjectPortfolio.dbo.NashvilleHousing





Select OwnerAddress
From ProjectPortfolio.dbo.NashvilleHousing


Select
PARSENAME(REPLACE(OwnerAddress, ',', '.') , 3)
,PARSENAME(REPLACE(OwnerAddress, ',', '.') , 2)
,PARSENAME(REPLACE(OwnerAddress, ',', '.') , 1)
From ProjectPortfolio.dbo.NashvilleHousing

Update NashvilleHousing
SET OwnerSplitAddress = PARSENAME(REPLACE(OwnerAddress, ',', '.') , 3)


ALTER TABLE NashvilleHousing
Add OwnerSplitCity Nvarchar(255);

Update NashvilleHousing
SET OwnerSplitCity = PARSENAME(REPLACE(OwnerAddress, ',', '.') , 2)



ALTER TABLE NashvilleHousing
Add OwnerSplitState Nvarchar(255);

Update NashvilleHousing
SET OwnerSplitState = PARSENAME(REPLACE(OwnerAddress, ',', '.') , 1)



Select *
From ProjectPortfolio.dbo.NashvilleHousing




--------------------------------------------------------------------------------------------------------------------------


-- Change Y and N to Yes and No in "Sold as Vacant" field


Select Distinct(SoldAsVacant), Count(SoldAsVacant)
From ProjectPortfolio.dbo.NashvilleHousing
Group by SoldAsVacant
order by 2




Select SoldAsVacant
, CASE When SoldAsVacant = 'Y' THEN 'Yes'
	   When SoldAsVacant = 'N' THEN 'No'
	   ELSE SoldAsVacant
	   END
From ProjectPortfolio.dbo.NashvilleHousing


Update NashvilleHousing
SET SoldAsVacant = CASE When SoldAsVacant = 'Y' THEN 'Yes'
	   When SoldAsVacant = 'N' THEN 'No'
	   ELSE SoldAsVacant
	   END






-----------------------------------------------------------------------------------------------------------------------------------------------------------

-- Remove Duplicates

WITH RowNumCTE AS(
Select *,
	ROW_NUMBER() OVER (
	PARTITION BY ParcelID,
				 PropertyAddress,
				 SalePrice,
				 SaleDate,
				 LegalReference
				 ORDER BY
					UniqueID
					) row_num

From ProjectPortfolio.dbo.NashvilleHousing
--order by ParcelID
)
Select *
From RowNumCTE
Where row_num > 1
Order by PropertyAddress



Select *
From ProjectPortfolio.dbo.NashvilleHousing




---------------------------------------------------------------------------------------------------------

-- Delete Unused Columns



Select *
From ProjectPortfolio.dbo.NashvilleHousing


ALTER TABLE PortfolioProject.dbo.NashvilleHousing
DROP COLUMN OwnerAddress, TaxDistrict, PropertyAddress, SaleDate















-----------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------

--- Importing Data using OPENROWSET and BULK INSERT	

--  More advanced and looks cooler, but have to configure server appropriately to do correctly
--  Wanted to provide this in case you wanted to try it


--sp_configure 'show advanced options', 1;
--RECONFIGURE;
--GO
--sp_configure 'Ad Hoc Distributed Queries', 1;
--RECONFIGURE;
--GO


--USE PortfolioProject 

--GO 

--EXEC master.dbo.sp_MSset_oledb_prop N'Microsoft.ACE.OLEDB.12.0', N'AllowInProcess', 1 

--GO 

--EXEC master.dbo.sp_MSset_oledb_prop N'Microsoft.ACE.OLEDB.12.0', N'DynamicParameters', 1 

--GO 


---- Using BULK INSERT

--USE PortfolioProject;
--GO
--BULK INSERT nashvilleHousing FROM 'C:\Temp\SQL Server Management Studio\Nashville Housing Data for Data Cleaning Project.csv'
--   WITH (
--      FIELDTERMINATOR = ',',
--      ROWTERMINATOR = '\n'
--);
--GO


---- Using OPENROWSET
--USE PortfolioProject;
--GO
--SELECT * INTO nashvilleHousing
--FROM OPENROWSET('Microsoft.ACE.OLEDB.12.0',
--    'Excel 12.0; Database=C:\Users\alexf\OneDrive\Documents\SQL Server Management Studio\Nashville Housing Data for Data Cleaning Project.csv', [Sheet1$]);
--GO
```


















#### Key Techniques Used:
- Removing Duplicates: Identified and removed duplicate rows using ROW_NUMBER() in combination with a Common Table Expression (CTE).
- Handling Null Values: Addressed missing PropertyAddress fields by leveraging ISNULL and self-joins.
- Normalizing Data Formats: Standardized date formats and split composite fields like PropertyAddress and OwnerAddress into separate columns (e.g., Address, City, State).
- Transforming Categorical Data: Converted SoldAsVacant values from 'Y'/'N' to 'Yes'/'No' for better readability.
- Dropping Unused Columns: Removed columns like OwnerAddress and TaxDistrict to optimize the dataset.
- Importing Data: Included advanced methods for loading data using BULK INSERT and OPENROWSET.

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

### Excel Pivot
Details on using Excel to create pivot tables for data trends. (Placeholder for specifics on dataset and process).
#### Example Pivot Table:
![9239](https://github.com/user-attachments/assets/a4ddbb19-7408-4165-b219-9844d3eb1269)

---
### Tableau Dashboard
Details on creating interactive dashboards in Tableau to visualize insights. (Placeholder for specifics on dataset and dashboard features).
#### Example Dashboard:
[Click to view Tableau Dashboard](https://public.tableau.com/app/profile/damian.kimaro/viz/AirBnBFullProject_17349102691680/Dashboard1)

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
 
 [@kaggle dataset for Tableau](https://www.kaggle.com/datasets/dcschmidt/airbnbcopenhagen?select=calendar.csv)
 
 
 
 
