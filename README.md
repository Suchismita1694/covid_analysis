# COVID-19 
In this, Covid-19 Data Exploration is done using SQL and Tableau for Data Visualization. The updated dataset was downloaded from ourworldindata.org and the project was completed with online guidance

-- dataset preview
```
select *
from CovidProject..['covid death']
where continent is not null
order by 3,4
```
```
select *
from CovidProject..['covid vaccine']
order by 3,4
```
first 4 for visualization on tableau
--1-- Total cases in the world till Aug-01-2022
```
select SUM(new_cases) as total_cases,SUM(cast(new_deaths as int)) as total_deaths, 
SUM(cast(new_deaths as int))/SUM(new_cases)*100 as deathPercent
from CovidProject..['covid death']
where continent is not null
order by 1,2
```
Total cases = 577152412, Total_deaths = 6360394 , Death Percent = 1.10203022074523

-- 2     india total cases, deaths, and death percentage
```
select SUM(new_cases) as total_cases,SUM(cast(new_deaths as bigint)) as total_deaths,
SUM(cast(new_deaths as int))/SUM(new_cases)*100 as deathPercent
from CovidProject..['covid death']
where location = 'India'
order by 1,2
``` 
Total cases = 44050009, Total_deaths = 519058 , Death Percent = 1.17833801123627

--3-- total deaths per continent
```
select continent, SUM(cast(new_deaths as int)) as TotalDeaths
from CovidProject..['covid death']
where continent is not null
group by continent
order by TotalDeaths desc
```
![image](https://user-images.githubusercontent.com/107685839/201986121-b25b7032-ebfb-4209-ba2c-d6ae01ff9b24.png)


--4 -- --Highest infection rate compared to population
```
select location,population, MAX(total_cases) as highest_cases,MAX((total_cases/population))*100 as infected_percent_population
from CovidProject..['covid death']
where continent is not null
group by location,population
order by infected_percent_population desc
```
--only sql queries (not in visualization)
```
select location,population,date, MAX(total_cases) as highest_cases,MAX((total_cases/population))*100 as infected_percent_population
from CovidProject..['covid death']
--where continent is not null
group by location,population,date
order by infected_percent_population desc
```


-- for global- number of cases and deaths by each date from 2020-2022
```
select date, SUM(new_cases) as total_cases,SUM(cast(new_deaths as int)) as total_deaths, 
SUM(cast(new_deaths as int))/SUM(new_cases)*100 as deathPercent
from CovidProject..['covid death']
where continent is not null
group by date
order by 1,2
```        

--Joining death and vaccination table
```
select * 
from CovidProject..['covid death'] dea
join CovidProject..['covid vaccine'] vac
on dea.location = vac.location
and dea.date = vac.date
```
-- total population vs total vaccination
--toatl vaccinations provided
```
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
sum(cast(vac.new_vaccinations as bigint)) over (partition by dea.location order by dea.location, dea.date) as total_vaccination
from CovidProject..['covid death'] dea
join CovidProject..['covid vaccine'] vac
on dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
order by 2,3
```
-- total number of people fully vaccinated
```
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations, vac.people_fully_vaccinated
from CovidProject..['covid death'] dea
join CovidProject..['covid vaccine'] vac
on dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
order by 2,3
```
-- total percent of fully vaccinated people
```
select dea.continent, dea.location, dea.date, dea.population, vac.people_fully_vaccinated,
sum(cast(vac.people_fully_vaccinated as bigint)) over (partition by dea.location order by dea.location, dea.date) as total_fully_vaccinated,
(people_fully_vaccinated/population)*100 as total_vaccinated_percent
from CovidProject..['covid death'] dea
join CovidProject..['covid vaccine'] vac
on dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
order by 2,3
```
-- total vaccinations provided in india
```
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
sum(cast(vac.new_vaccinations as bigint)) over (partition by dea.location order by dea.location, dea.date) as total_vaccination
from CovidProject..['covid death'] dea
join CovidProject..['covid vaccine'] vac
on dea.location = vac.location
and dea.date = vac.date
where dea.location = 'India'
order by 2,3
```
-- total people vaccinated in India
```
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
sum(cast(vac.new_vaccinations as bigint)) over (partition by dea.location order by dea.location, dea.date) as fully_vaccinated,
(new_vaccinations/population)*100 as total_vaccinated_percent
from CovidProject..['covid death'] dea
join CovidProject..['covid vaccine'] vac
on dea.location = vac.location
and dea.date = vac.date
where dea.location = 'India'
order by 2,3
```

--Percent population vaccinated in INDIA
```
select dea.continent, dea.location, dea.date, dea.population, vac.people_fully_vaccinated,
sum(cast(vac.people_fully_vaccinated as bigint)) over (partition by dea.location order by dea.location, dea.date) as total_fully_vaccinated,
(people_fully_vaccinated/population)*100 as total_vaccinated_percent
from CovidProject..['covid death'] dea
join CovidProject..['covid vaccine'] vac
on dea.location = vac.location
and dea.date = vac.date
where dea.location = 'India'
order by 2,3
```
-- INDIA 66.3% has been vaccinated as of 1st august 2022.
