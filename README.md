# Student performance and Predicive analysis Using Oracle AWM and WEKA

The idea behind this analysis is to predict the behavior of students in terms of selection of their open electives. A student performance can be measured through grades in their respective subjects. Also the subjects in the curriculum are interrelated with each other. The concept behind this is if a student performance is better in related subjects then he/she should select the open elective related to their previous performance. But generally student selects open elective that can get them better grades to enhance their overall GPA. Also through grades we can determine overall performance of students in a year and the performance of students in particular subjects. To achieve this we need a section of students from different backgrounds of their Branches (i.e. IT, CSE, EC, etc.) and of a particular year.

Through this project we can:-
- Determine the overall performance of students in year and semester wise.
-	Determine the performance of student according to their subjects.
-	Determine the performance of faculty with respective to their subject of teaching.
-	Predict the open elective selected by the student in upcoming semester.

Above points can give insights of the performance of the university indeed. Through these points the university can determine the performance of the students. They can determine that to which semester the performance of the students raise or fall and find out the cause. University can check the performance of their faculty through finding out that the subject faculty are teaching are up to the mark or not. Finally we have to predict the open elective selected by the student so that we can determine the mentality of the student and the quality of our predicting model.

# Table of Contents

- [Data Collection](#Data-Collection)
- [Creating Data Warehouse](#Creating-Data-Warehouse)
- [Creation of OLAP Cubes through Oracle AWM](#Creation-of-OLAP-Cubes-through-Oracle-AWM)
  - [Analysis to determine the analytic workspace content](#Analysis-to-determine-the-analytic-workspace-content)
  - [Creating the Analytic Workspace](#Creating-the-Analytic-Workspace)
  - [Creating the Dimensions](#Creating-the-Dimensions)
  - [Populating the Dimension](#Populating-the-Dimension)
  - [Creating Cube](#Creating-Cube)
  - [Creating the Measures and the Cube Mapping](#Creating-the-Measures-and-the-Cube-Mapping)
  - [Creating the Cube Materialized Views in Oracle Database 11g](#Creating-the-Cube-Materialized-Views-in-Oracle-Database-11g)
  - [Populating the Cube](#Populating-the-Cube)
- [Methodology](#Methodology)
  - [Objective and data understanding](#Objective-and-data-understanding)
  - [System Analysis and Design](#System-Analysis-and-Design)
    - [System Requirement](#System-Requirement)
    - [User Requirement](#User-Requirement)
  - [Implementation and Testing](#Implementation-and-Testing)
  - [Evaluation and Result](#Evaluation-and-Result)
    - [Performance Measure](#Performance-Measure)
- [Result Anaysis](#Result-Anaysis)
- [Conclusion](#Conclusion)

# Data Collection
For working on an analysis system, Data is the most integral part of it. We can collect data from different sources like we can collect data through survey paper or we can collect it from some open source data providing website. Also we can get it from specific university if they allow. So the first step to perform analysis is collection of raw data.

# Creating Data Warehouse
As defined, A data warehouse is a “subject oriented integrated, time varying, non-volatile, collection of data that is used primarily in organizational decision making”. Data warehouse are targeted for decision support. Historical, summarized and consolidated data is more important than detailed, individual records. To facilitate complex analyses and visualization, the data in a warehouse is typically modeled multidimensional. Here in this analysis we store the data computed after refining or ETL process into data warehouse and analysis it by creating multidimensional cube. The data stored in the warehouse is like below:

Student Table

Field |	Type | Size |	Null |	Key |	Description
--- | --- | --- | --- | --- | ---
Reg_No |	VARCHAR2 |	9 |	No |	PK |	Registration No. of the student
Branch |	VARCHAR2 |	6 |	No |		 | Branch of student
Total	| VARCHAR2 |	5 |	No |	 |	Aggregation
Total Description |	VARCHAR2 |	14 |	No |	 |	Aggregation Description 

Semester Table

Field |	Type | Size |	Null |	Key |	Description
--- | --- | --- | --- | --- | ---
Sem_Id |	NUMBER |	5 |	No |	PK |	Semester Id
Sem_Desc |	VARCHAR2 |	9 |	No |	| Semester Description
Year_of_sem |	NUMBER |	2 |	No |	| Year of semester
Total |	VARCHAR2 |	5 |	No |	|	Aggregation
Total Description |	VARCHAR2 |	14 |	No | | Aggregation Description 

Subject Table

Field |	Type | Size |	Null |	Key |	Description
--- | --- | --- | --- | --- | ---
Subject_Id |	NUMBER |	4 |	No |	PK |	Subject Id
Subject_Name |	VARCHAR2 |	43 |	No |	|	Name of Subject
Credit |	NUMBER |	2 |	No |	|	Credit of that subject
Subject_short_Name |	VARCHAR2 |	18 |	No |	|	Short name of subject
Total |	VARCHAR2 |	5 |	No |	|	Aggregation
Total Description |	VARCHAR2 |	14 |	No |	|	Aggregation Description 
