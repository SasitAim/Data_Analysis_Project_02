# Data_Analysis_Project_02
Data Analysis  vehicle accident in SQL Server Management Studio (SSMS)

### Project Overview
This Data Analysis project will begin with the creation of a new database. And next, the data to be analyzed will be imported into SQL Server Management Studio (SSMS), and the analysis, along with answering all the questions, will be carried out.

### Tools
- SQL Server Management Studio (SSMS)

### Questions
1. How many accidents have occurred in urban areas versus rural areas?
2. Which day of the week has the highest number of accidents?
3. What is the average age of vehicles involved in accidents based on their type?
4. Can we identify any trends in accidents based on the age of vehicles involved?
5. Are there any specific weather conditions that contribute to severe accidents?
6. Do accidents often involve impacts on the left-hand side of vehicles?
7. Are there any relationships between journey purposes and the severity of accidents?
8. Calculate the average age of vehicles involved in accidents , considering Day light and point of impact

### Step 1 Create database and import data. 
```sql
-- First create database for analyze
CREATE DATABASE TESTDB ;
-- after created database import vehicle and accident files to database

USE TESTDB;
-- select database to query data
```
### Step 2 Perform an overall data check for this dataset.
```sql
SELECT * FROM accident ;
SELECT * FROM vehicle;
```
### Step 3 Data Analysis and answer a question.
```sql
--Question 1: How many accidents have occurred in urban areas versus rural areas?
SELECT Area, COUNT(AccidentIndex) AS Total_of_Accidents
FROM accident 
GROUP By Area;

--Question 2: Which day of the week has the highest number of accidents?
SELECT Day, COUNT(Day) AS 'Total Accident'
FROM accident 
GROUP BY Day
ORDER BY 'Total Accident' DESC;

--Question 3: What is the average age of vehicles involved in accidents based on their type?
SELECT VehicleType, COUNT(AccidentIndex) AS 'Total Accident'  ,AVG(AgeVehicle) AS 'Avg Year'
FROM vehicle
WHERE AgeVehicle IS NOT NULL
GROUP BY VehicleType
ORDER BY 'Total Accident'  DESC;


--Question 4: Can we identify any trends in accidents based on the age of vehicles involved?
SELECT AgeGroup,
	COUNT(AccidentIndex) AS 'Total Accident',
	AVG(AgeVehicle) AS 'Avg Year'
FROM (
	SELECT AccidentIndex, AgeVehicle,
		CASE
			WHEN[AgeVehicle] BETWEEN 0 AND 5 THEN'New'
			WHEN[AgeVehicle] BETWEEN 6 AND 10 THEN'Regular'
			ELSE 'Old'
		END AS 'AgeGroup'
	FROM vehicle
) AS SubQuery
GROUP BY AgeGroup 
ORDER BY 'Avg Year' DESC;

--Question 5: Are there any specific weather conditions that contribute to severe accidents?
DECLARE @Severity VARCHAR(50)
SET @Severity = 'Fatal'
--Change  @Severity to (Serious, Fatal, Slight) for show severity of accident in each weather conditions
SELECT 
	WeatherConditions,
	COUNT(Severity) AS 'Total Accident' 
FROM accident
WHERE Severity = @Severity
GROUP BY WeatherConditions
ORDER BY 'Total Accident' DESC ;

--Question 6: Do accidents often involve impacts on the left-hand side of vehicles?
SELECT LeftHand, COUNT(LeftHand)
FROM vehicle
GROUP BY LeftHand
HAVING LeftHand IS NOT NULL;

--Question 7: Are there any relationships between journey purposes and the severity of accidents?
-- EX.1
DECLARE @Severity VARCHAR(50)
SET @Severity = 'Serious'

SELECT JourneyPurpose, COUNT(JourneyPurpose) AS 'Total Accident'
FROM vehicle
LEFT JOIN accident
ON vehicle.AccidentIndex = accident.AccidentIndex
WHERE Severity = @Severity
GROUP BY JourneyPurpose;

-- EX.2 
SELECT 
	V.JourneyPurpose,
	COUNT(A.Severity) AS 'Total Accident',
	CASE
		WHEN COUNT(A.Severity) BETWEEN 0 AND 1000 THEN 'Low'
		WHEN COUNT(A.Severity) BETWEEN 1001 AND 3000 THEN 'Moderate'
		ELSE 'High'
	END AS 'Level '
FROM accident A
LEFT JOIN vehicle V
ON A.AccidentIndex = V.AccidentIndex
GROUP BY V.JourneyPurpose
ORDER BY 'Total Accident' DESC;


--Question 8: Calculate the average age of vehicles involved in accidents , considering Day light and point of impact
DECLARE @impact VARCHAR(50)
DECLARE @light VARCHAR(50)
SET @impact = 'Front'		-- Front, Nearside, Did not impact, Front, Offside, Nearside, Back, Back, Offside, Did not impact
SET @light = 'Daylight'		-- Daylight or Darkness

SELECT 
	A.LightConditions,
	V.PointImpact, 
	AVG(V.AgeVehicle) AS 'Avg Year'
FROM accident A
LEFT JOIN vehicle V
ON V.AccidentIndex = A.AccidentIndex
GROUP BY V.PointImpact, A.LightConditions
HAVING V.PointImpact = @impact AND  A.LightConditions = @light;

```

### Answer the question
What has been found from this dataset includes.

Ans 1. Total accidents in Urban is 58533 and  in Rutal is 21999 times.

Ans 2.Friday has the highest number of accidents, which is 12,937 times.

Ans 3.Vehicles with an age of 6 years have the highest number of accidents, totaling 182,954 times.

Ans 4.Analyzing the age of vehicles reveals that older vehicles are associated with more accidents.

Ans 5.The weather conditions most contributing to severe accidents are "fine on high winds" and "raining with no high winds."

Ans 6.No because  total accident of "not Left Hand" is much more. 

Ans 7. The most of journey purposes are "not known data." However, if we exclude these cases, the journey purposes "Journey as part of work" and "Commuting to/from work" have the highest accident rates.

Ans 8.Daylight and darkness have similar average vehicle ages.
