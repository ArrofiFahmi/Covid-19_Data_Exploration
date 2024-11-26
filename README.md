# Covid-19 Data Exploration

## About the Project
This project involves exploring and analyzing COVID-19. The main focus is to derive meaningful insigths such as case percentage, death rates, and trends across coutries, with a special emphasis Indonesia. The queries analyze the relationship between COVID-19 metrics and population, helping us understand the pandemic's global and regional impacts.

----

## Objective
The goal of this project is to:
1. Explore and clean COVID-19 Data
2. Calculate and compare COVID-19 metrics (cases, deaths) relative to population.
3. Analyze regional trends, with a focus on Indonesia

---

## Features
- Analyze total cases and deaths compared to population.
- Identify trends over time in specific regions.
- SQL queries are structured for clarity and reusability.

## Tools
**Database Management System**: SQL Server

## Project Highlights
### Global Number
#### Total Cases vs Death in the world
```sql
SELECT SUM(new_cases) as TotalCases, SUM(new_deaths) as TotalDeath,  SUM(new_deaths)/SUM(new_cases)*100 as DeathPercentage
FROM Covid19Explo..CovidDeath
WHERE location is not null
ORDER BY 1,2
```

#### Breaking Down by Continents
Showing contintents with the highest death count per population:
```sql
SELECT continent, MAX(cast(total_deaths as int)) as TotalDeathCount
FROM Covid19Explo..CovidDeath
WHERE continent is not null 
GROUP BY continent
ORDER BY TotalDeathCount desc
```
#### Total Cases vs Population 
Calculates the percentage of cases relative to the population for each country:
```sql
SELECT location, date, total_cases, population,
  (total_cases/population)*100 as CasesPercentage
FROM Covid19Explo..CovidDeath
WHERE continent  is not null
ORDER BY 1,2
```

#### Countries with Highest Death Count per Population
Knowing which countries having the highest death count per population:
```sql
SELECT location, Population,  MAX(cast(total_deaths as int)) as TotalDeathCount
FROM Covid19Explo..CovidDeath
WHERE location is not null 
GROUP BY location, population
ORDER BY TotalDeathCount desc
```

#### Countries with Death Percentage
Showing Percentage of Death per population by location:
```sql
SELECT location, max(population) as Population, MAX(cast(total_deaths as int)) as TotalDeathCount, MAX(cast(total_deaths as int)/population)*100 as PercentDeath
FROM Covid19Explo..CovidDeath
WHERE location is not null 
GROUP BY location
ORDER BY TotalDeathCount desc
```

#### Total Population vs Vaccinations
Showing Percentage of Population that has recieved at least one Covid Vaccine:
```sql
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
FROM Covid19Explo..CovidDeath dea
	INNER JOIN Covid19Explo..CovidVac vac
	ON dea.date = vac.date and
	dea.location = vac.location	
WHERE dea.continent is not null
ORDER BY 2,3
```
#### Rolling People Vaccinated
Showing how many people vaccinated each day:
```
WITH PopvsVac
AS
(SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(CAST(vac.new_vaccinations as bigint)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) as RollingPeopleVaccinated
FROM Covid19Explo..CovidDeath dea
	INNER JOIN Covid19Explo..CovidVac vac
	ON dea.date = vac.date and
	dea.location = vac.location	
WHERE dea.continent is not null)
 
SELECT*, (RollingPeopleVaccinated/population)*100 AS RollingPeopleVac_Percent
FROM PopvsVac
```
### Focus on Indonesia
Filters data to analyze trends specific to Indonesia, including death and infection rates:
#### Total Cases vs Total Deaths in Indonesia
Calculating how many cases and deaths in Indonesia:
```sql
SELECT location, date,total_cases, total_deaths, population, (total_deaths/population)*100 as TotalDeathPercentage
FROM Covid19Explo..CovidDeath
WHERE location like '%Indo%'
and continent  is not null
ORDER BY 1,2
```
#### Total Cases vs Population in Indonesia
Showing total cases per population in Indonesia:
```sql
SELECT location, date, total_cases, population, (total_cases/population)*100 as PersonPeopleInfected
FROM Covid19Explo..CovidDeath
WHERE location like '%Indo%' 
and continent is not null
ORDER BY 1,2
```
#### The Highest Infection Rate in Indonesia compared to Population
```sql
SELECT location, population, max(total_cases) as HighestInfectionCount,
  max((total_cases/population))*100 as PercentPopulationInfected
FROM Covid19Explo..CovidDeath
WHERE location like '%Indo%' 
GROUP BY location,population
```
