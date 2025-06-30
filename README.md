# Patient-Healthcare-Analysis
This project focuses on analyzing healthcare operational data to uncover key performance insights across hospitals and departments. It demonstrates end-to-end data handling—from cleaning and modeling to visualization and storytelling.

--SQL Queries for the KPI's

-- select * FROM data_healthcare_lab;

-- Data Model 
SELECT P.`Patient ID`, P.FirstName,  P.Gender, P.DateOfBirth, P.Age, P.BloodType, P.InsuranceProvider, 
 P.State, P.City, P.Country, P.MedicalHistory, P.Race, P.Ethnicity, P.`Marital Status`, P.`Chronic Conditions`, 
 P.Allergies, D.`Doctor ID`, D.`Doctor Name`, D.Specialty, D.`Years Of Experience`, D.Specialization,
 V.`Visit ID`, V.`Visit Date`, V.`Visit Type`, V.`Reason for Visit`, V.`Visit Status`, V.`Follow Up Required`,
 T.`Treatment ID`,T.`Treatment Type`,T.`Treatment Name`, T.`Status`, T.`Treatment Cost`,
 L.`Lab Result ID`, L.`Test Name`, L.`Test Date`, L.Result, L.Units
 FROM patients P
 JOIN visits V ON V.`Patient ID` = P.`Patient ID` 
 JOIN doctors D ON D.`Doctor ID` = V.`Doctor ID`
 LEFT JOIN treatment T ON T.`Visit ID` = V.`Visit ID`
 LEFT JOIN lab_result L ON L.`Visit ID` = V.`Visit ID`
 ORDER BY `Visit Date` DESC;
 
 
 
 -- Total Patient count 
 SELECT COUNT(`Patient ID`) FROM patients;
 
 -- Treatment cost per visit 
SELECT ROUND(sum(`Treatment Cost`)/count(*),2) AS Average_cost from healthcare;
 
 -- Average Age
 SELECT Round(SUM(age)/count(Patient_ID),2) AS Average_age from healthcare;
 
-- Follow-up rate
SELECT CONCAT(ROUND((SUM(CASE WHEN `Follow Up Required`='YES' THEN 1 ELSE 0 END)/count(`Patient ID`)*100),2),"%") AS Followup_rate FROM data_healthcare_visit;

-- Age wise patient Distribution 
SELECT CASE 
 WHEN Age BETWEEN 0 AND 18 THEN "Young" 
 WHEN Age BETWEEN 19 AND 35 THEN "Adult" 
 WHEN Age BETWEEN 36 AND 55 THEN "Middle-Aged"
 ELSE "Senior-Citizen"
 END AS Age_Groups, COUNT(Patient_ID) AS Patient_Count FROM healthcare
 GROUP BY Age_Groups ORDER BY Patient_Count ASC;
 
-- Frequently Observed Conditions
SELECT Chronic_Conditn AS Chronic_Condition, COUNT(*) AS diagnosis_count
FROM healthcare
GROUP BY Chronic_Conditn
ORDER BY diagnosis_count DESC
LIMIT 5;
-- Diagnosis 
SELECT Diagnosis, count(*) AS Diagnosis_Count FROM healthcare 
GROUP BY Diagnosis;

-- location Wise Analysis
SELECT SUM(CASE WHEN `Follow Up Required`='YES' THEN 1 ELSE 0 END) AS Patient_count, City 
FROM healthcare V Join healthcare P on V.`Patient ID`= P.Patient_ID GROUP BY City; 

-- Visits over Time
SELECT 
    YEAR(`Visit Date`) AS visit_year,
    COUNT(*) AS visit_count
FROM data_healthcare_visit
GROUP BY visit_year
ORDER BY visit_year;
SELECT 
    DATE_FORMAT(`Visit Date`, '%Y-%m') AS visit_month,
    COUNT(*) AS visit_count
FROM healthcare
GROUP BY visit_month
ORDER BY visit_month;

-- Lab Result 
SELECT `Test Name`, `Reference Range`, count(`Lab Result ID`) AS Result_Count 
FROM data_healthcare_lab 
GROUP BY `Test Name`, `Reference Range`
ORDER BY `Test Name`, `Reference Range`;

-- Percentage of Abnormal test
SELECT 
concat(ROUND((sum(CASE WHEN Result = 'Abnormal' THEN 1 ELSE 0 END)/count(*)*100),2),'%') 
AS Abnormal_Test FROM healthcare;

-- Most Prescribed Medicines 
SELECT `Medication Prescribed`, count(`Visit ID`) AS Total_Prescribed 
FROM healthcare GROUP BY `Medication Prescribed`
ORDER BY Total_Prescribed DESC;

-- DOC workload
SELECT 
    `Doctor Name`,
    COUNT(`Visit ID`) AS visit_count
FROM healthcare D 
JOIN healthcare V 
ON D.`Doctor ID` = V.`Doctor ID`
GROUP BY `Doctor Name`
ORDER BY visit_count DESC
LIMIT 10;

-- DAX 
-- Average Patients per Doctor
AvgPatientsPerDoctor = 
DIVIDE(
    COUNT('PatientData'[PatientID]),
    DISTINCTCOUNT('PatientData'[DoctorID])
)

