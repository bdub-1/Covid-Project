select * 
from [Portfolio Project]..CovidDeaths
where continent is not null
;

select *
from [Portfolio Project]..CovidVaccinations
where continent is not null
;

select location, date, total_cases, new_cases, total_deaths, population 
from [Portfolio Project]..CovidDeaths
where continent is not null
;

-- Total Cases vs Total Deaths
-- Chance of dying after infection
select location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPct
from [Portfolio Project]..CovidDeaths
where location like '%states' 
order by date desc;

-- total cases vs population and death vs population
-- Chance of contracting covid 
-- Chance of dying from covid before infection 
select location, date, total_cases, population, (total_cases/population)*100 as PopulationCovidPct, (total_deaths/population)*100 as PopulationDeathPct
from [Portfolio Project]..CovidDeaths
where location like '%states'
order by date desc;

-- Countries with highest infrection rate vs population
select location, population, MAX(total_cases) as MaxInfected, MAX((total_cases/population)*100) as MaxPopulationCovidPct
from [Portfolio Project]..CovidDeaths
where continent is not null
Group by location, population
order by MaxPopulationCovidPct DESC;

-- Countries with highest death count
select location, population, MAX(cast(total_deaths as int)) as MaxDeaths
from [Portfolio Project]..CovidDeaths
where continent is not null
Group by location, population
order by MaxDeaths DESC;

-- Countries with highest death rate before infection
select location, population, MAX(cast(total_deaths as int)) as MaxDeaths, MAX(CAST(total_deaths as int))/population*100 as MaxDeathRate
from [Portfolio Project]..CovidDeaths
where continent is not null
Group by location, population
order by MaxDeathRate DESC;

--Analyzing by continents
select distinct continent
from [Portfolio Project]..CovidDeaths
where continent is not null
group by continent
order by continent;

-- Infection rate by continent
select distinct continent, MAX(total_cases) as MaxCases
from [Portfolio Project]..CovidDeaths 
where continent is not null
group by continent
order by MaxCases desc;

-- Global numbers for total deaths and death rate
select SUM(new_cases) as total_cases, SUM(CAST(new_deaths as int)) as total_deaths, SUM(CAST(new_deaths as int))/SUM(new_cases)*100 as DeathPct
from [Portfolio Project]..CovidDeaths
where continent is not null;

-- Total population vs vaccinations
-- Performing Joins 
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(cast(vac.new_vaccinations as bigint)) OVER (partition by dea.location) as RollingVac
From [Portfolio Project]..CovidDeaths dea
join [Portfolio Project]..CovidVaccinations vac
	on dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null;

-- Use CTE
with PopvsVac (continent, location, date, population, new_vaccinations, RollingVac)
as 
(
 select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(cast(vac.new_vaccinations as bigint)) OVER (partition by dea.location) as RollingVac
From [Portfolio Project]..CovidDeaths dea
join [Portfolio Project]..CovidVaccinations vac
	on dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null
)
select *, (RollingVac/population)*100 as VacPctPop
from PopvsVac
;

-- TEMP TABLE
Drop Table if exists #PercentPopulationVaccinated
Create Table #PercentPopulationVaccinated
(
Continent nvarchar(255),
location nvarchar(255),
date datetime,
population numeric,
new_vaccinations numeric,
RollingVac numeric
)

Insert Into #PercentPopulationVaccinated
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(cast(vac.new_vaccinations as bigint)) OVER (partition by dea.location) as RollingVac
From [Portfolio Project]..CovidDeaths dea
join [Portfolio Project]..CovidVaccinations vac
	on dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null

select *, (RollingVac/population)*100 as VacPctPop
from #PercentPopulationVaccinated


-- Creating View to store data for later visualizations
Create View PercentPopulationVaccinated as
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
, SUM(cast(vac.new_vaccinations as bigint)) OVER (partition by dea.location) as RollingVac
From [Portfolio Project]..CovidDeaths dea
join [Portfolio Project]..CovidVaccinations vac
	on dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null;


Select *
From PercentPopulationVaccinated
;

