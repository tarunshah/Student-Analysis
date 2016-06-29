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

## Data Collection
For working on an analysis system, Data is the most integral part of it. We can collect data from different sources like we can collect data through survey paper or we can collect it from some open source data providing website. Also we can get it from specific university if they allow. So the first step to perform analysis is collection of raw data.

## Creating Data Warehouse
As defined, A data warehouse is a “subject oriented integrated, time varying, non-volatile, collection of data that is used primarily in organizational decision making”. Data warehouse are targeted for decision support. Historical, summarized and consolidated data is more important than detailed, individual records. To facilitate complex analyses and visualization, the data in a warehouse is typically modeled multidimensional. Here in this analysis we store the data computed after refining or ETL process into data warehouse and analysis it by creating multidimensional cube. The data stored in the warehouse is like below:

**Student Table**

Field |	Type | Size |	Null |	Key |	Description
--- | --- | --- | --- | --- | ---
Reg_No |	VARCHAR2 |	9 |	No |	PK |	Registration No. of the student
Branch |	VARCHAR2 |	6 |	No |		 | Branch of student
Total	| VARCHAR2 |	5 |	No |	 |	Aggregation
Total Description |	VARCHAR2 |	14 |	No |	 |	Aggregation Description 

**Semester Table**

Field |	Type | Size |	Null |	Key |	Description
--- | --- | --- | --- | --- | ---
Sem_Id |	NUMBER |	5 |	No |	PK |	Semester Id
Sem_Desc |	VARCHAR2 |	9 |	No |	| Semester Description
Year_of_sem |	NUMBER |	2 |	No |	| Year of semester
Total |	VARCHAR2 |	5 |	No |	|	Aggregation
Total Description |	VARCHAR2 |	14 |	No | | Aggregation Description 

**Subject Table**

Field |	Type | Size |	Null |	Key |	Description
--- | --- | --- | --- | --- | ---
Subject_Id |	NUMBER |	4 |	No |	PK |	Subject Id
Subject_Name |	VARCHAR2 |	43 |	No |	|	Name of Subject
Credit |	NUMBER |	2 |	No |	|	Credit of that subject
Subject_short_Name |	VARCHAR2 |	18 |	No |	|	Short name of subject
Total |	VARCHAR2 |	5 |	No |	|	Aggregation
Total Description |	VARCHAR2 |	14 |	No |	|	Aggregation Description 

**Grade Table**

Field |	Type | Size |	Null |	Key |	Description
--- | --- | --- | --- | --- | ---
Id |	NUMBER |	7 |	No |	|	Id
Reg_No |	VARCHAR2 |	9 |	No |	FK |	| Registration no. of student referencing to student table
SEM |	NUMBER |	3 |	No |	FK |	Semester referencing to semester table
Sub_id |	NUMBER |	3 |	No |	FK |	Subject id referencing to subject table
Grade_Value |	NUMBER |	4 |	No |	|	Grades of student

##Creation of OLAP Cubes through Oracle AWM
AWM considerably simplifies the process of creating the Analytic Workspace and enables anyone from a DBA to a business analyst to design the OLAP data model with the dimensions and hierarchies, map their data sources to the dimensions and cubes and then populate them.

[![ORACLE ANALYTICS WORKSPACE MANAGER](https://github.com/tarunshah/Student-Analysis/blob/master/docs/readme_images/AWM.JPG)](https://github.com/tarunshah/Student-Analysis/blob/master/docs/readme_images/AWM.JPG)

###Analysis to determine the analytic workspace content
Before the analytic workspace can be defined, as per the approach for creating a materialized view, some analysis work must first be performed to identify what information it must contain. We begin by looking at the type of queries our users will be running and our data and determine which dimensions we need; which will be Student, Semester and Subject and the physical tables where that data resides. Then we need to determine what cubes we require and the actual data we need to hold in the cubes.

Target AW object |	AW object Type |	Relational Source Table
--- | --- | ---
Student_Dim |	DIMENSION |	STUDENT
Semester_Dim |	DIMENSION |	SEMESTER
Subject_Dim |	DIMENSION |	SUBJECT
GRADE |	CUBE |	GRADE_FACT

###Creating the Analytic Workspace
The first step is to create the analytic workspace in which the dimensions and cube will reside. Creating the analytic workspace using AWM simply requires naming the analytic workspace and identifying the tablespace where it will be located.

###Creating the Dimension
An analytic workspace actually comprises a number of different types of objects, the principal ones we will be concentrating on are:

 - Dimensions including their hierarchies, levels and attributes,
 - Cubes

Dimensions are used to define the multi-dimensional data in a cube and must be created first in order to use them to create the cube. Dimensions in an analytic workspace can be either:

  - level based, the same as the relational dimension object,
  - Or can be value based (which is also known as a parent-child hierarchy).

A dimension is created by first creating the level information and then the hierarchies that group the levels in the correct order top to bottom. Finally, any additional attributes for the levels are created and assigned to the correct levels.

**Creating Semester Dimension**

<p align="center"><img src="https://github.com/tarunshah/Student-Analysis/blob/master/docs/readme_images/Sem_dim_general.JPG" ></p>

<p align="center"><img src="https://github.com/tarunshah/Student-Analysis/blob/master/docs/readme_images/Sem_dim_level.JPG" ></p>

###Populating the Dimension
In order to load the data into the dimension, mappings must be created to associate the columns in the tables holding the dimension source data and the attributes in the dimensions. Using AWM this is a simple task of dragging lines between the source table column and the target dimension attribute as shown in the figure below for the Product dimension.

<p align="center"><img src="https://github.com/tarunshah/Student-Analysis/blob/master/docs/readme_images/Mapping.JPG" ></p>

Here we can see columns in the SEMESTER table, such as YEAR_OF_SEM, linked to their attributes in the Semester analytic workspace dimension, for example, the long and short descriptions at the lowest Product level of the hierarchy.
The last step in creating the dimension is to populate it by executing the mappings and is performed by selecting the Maintain option from the right click menu for the dimension. This executes a process that:

- Transfers the data from the source object into the dimension object within the analytic workspace
- And implicitly validates the dimensional data.
- Compare this to SQL Dimensions which are metadata about the structure of a dimension held in the relational tables: when new data is   loaded into the tables used for the dimensions, it is not automatically validated to ensure that is conforms to the dimension
  structure. Consequently, SQL dimensions need the additional step of validating their structure by calling the VALIDATE_DIMENSION procedure in the DBMS_DIMENSION package.

###Creating Cubes
Once the dimensions have created, the cubes may be created and populated with equal ease by right clicking on the Cube sub-tree in the navigation pane and selecting the “Create” option. The dimensions that define the shape of the cube are selected. In our example, the three dimensions Student_Dim, Semester_Dim, and Subject_Dim define the GRADE cube.

At this point when the cube is being created, there are a number of important options that may be specified which define the cubes storage structure and that can considerably improve the time to build the cube. The most important of these are:

- **Dimension Order and Sparsity** - Specify the dimension order to improve build and aggregation performance by listing the dense dimensions first. Dense dimensions are those that have fact data values recorded against a high proportion of their dimension values.
- **Compression** - Select the compression option if the cube data is very sparse, i.e. the ratio of real data values to ‘null’ data values is extremely small. In most cases, this option is turned on.
- **Aggregation** - Specify the aggregation method and operators to use. In Oracle Database 11g there are now two methods: the new Cost Based Aggregation method and the previous level based method.
- **Partitioning** - Specify how the cube is to be partitioned in order to improve load performance.

New in Oracle Database 11g is the Cost Based Aggregation method where the OLAP Engine determines the most expensive cells to aggregate and stores the aggregated value for those cells. A value of 0 to 100 can be set – the higher the value then the larger the data set that will be pre-computed and stored. A good rule of thumb is to use a value of 35. The level based aggregation method can still be used where the levels are specified in the hierarchies at which the aggregations are to be physically stored. This provides more control over the levels that will be pre-computed and good strategy is to specify the aggregates to be calculated at alternate hierarchy levels.

Partitioning cubes should not be confused with the Database Partitioning option: for cubes it defines how the cube is stored as separate components within the analytic workspace. To partition your cube, choose a level within one hierarchy of one dimension: a different partition is then used for each data value at that level. For example, our GRADE cube uses a semester dimension that has semester as its lowest level and so specifying the cube partition strategy at either the semester or the Year level would be a good strategy. Partitioning cubes benefits the cube load and aggregation performance. It can also improve query performance because a query may be isolated to a single partition.

###Creating the Measures and the Cube MApping
Now we have defined the dimensions for our cube and its storage structure, we must specify the data it is to contain (the measures) and the mappings to the source data. When we define the measures for a cube, we define how the fact data is held in each cell of the cube. The cube mappings are defined just as we did for dimensions by dragging lines between the source relational table and the cube definition.

<p align="center"><img src="https://github.com/tarunshah/Student-Analysis/blob/master/docs/readme_images/Cube_mapping.JPG" ></p>

###Creating the Cube Materialized Views in Oracle Database 11g
Oracle Database 11g introduces the capability to create cube materialized views over the analytic workspace cubes and dimensions and enable query rewrite to transparently rewrite to them for user queries that are written in SQL against the base source tables. The new cube materialized views are controlled via AWM using a new Materialized View tab for the cube as shown below.

<p align="center"><img src="https://github.com/tarunshah/Student-Analysis/blob/master/docs/readme_images/materialized%20view.JPG"></p>

By selecting the “Enable Materialized View Refresh of the Cube” check box, the new cube materialized views are created for the cube and its dimensions when the cube is maintained. The cube materialized view naming convention uses the cube name with a “CB$” prefix. The new cube materialized views are also able to benefit from the refresh capabilities for loading data which is also controlled from this tab and is explored in more detail in a later section. Don’t forget to select the option “Enable Query Rewrite” otherwise the cube materialized views will not be made available for query rewrite to use.

###Populating the Cube
The final step in defining the analytic workspace on Oracle Database 11g is to maintain and populate the cube, after which it is available for querying. During this stage, data is loaded from the cube data source, for example the relational table GRADE_FACT in our case for the GRADE cube, and joined to its dimension values and stored in the cube. It is also at this point that the aggregations that were specified for the cube are performed and stored. The cube organized materialized views are also created for our cubes and dimensions and are refreshed. Once the data has been loaded then the analytic workspace is ready to be queried.

##Methodology

###Objective and Data Understanding

Understanding objective and data is a crucial in determining the performance of students as well as the faculty. It is used to determine the effectiveness and efficiency in terms of functionality. In this phase the student data is collected such as the grades from the past three semester in course “CSE”. Table 1 shows the attribute of dataset collected for analysis.

Attributes |	Values
--- | ---
Students |	Discrete
Subjects |	Categorical(OS, EM IV, RDBMS, MP, OOP, EM III, OOP, etc.)
Semester |	Categorical(I, II, III)
Grades | Categorical(A+, A, B, C, D, E, F)
Year | Categorical(I, II)
Branch | Categorical(CSE, IT, EC, Civil etc.)

###System Analysis and Design
In this phase, the overall flow of the system is planned, analysed and designed.

####System Requirment

Tools |	Product Used
--- | ---
Database |	Oracle 11g
OLAP cube |	Oracle AWM
Predictive | Analysis	WEKA

####User Requirment
Analysing student is the primary objective and to do so one need to have complete data of the students.

For collecting the student data we can use a survey paper or we can collect data from university itself. The data must contain the attribute listed above in the attribute table.

Further, we have to collect the subject data with their grade points in semester.

##Implementation and Testing
Analysis consist of 118 records of students in course CSE of MUJ. These data is analysed by using data mining technique to generate IF-THEN rules for prediction of student’s behaviour in selecting the open elective course. We will also apply other algorithms to match our best case. We will use an open source tool Weka for analysis. For analysis of performance we will use Oracle Analytical workspace manager i.e. also an open source toolkit. We will create a multidimensional cube to analyse the performance of student as well as faculties.

We had taken grades of student as a measure and store it in our tables like below format.

Grade Value |	Reference value
--- | ---
A+ |	1
A |	2
B |	3
C |	4
D |	5
E |	6
F |	7

We had taken grade A+ as 1 and F as 7, so the greater the average the less is the performance.

<p align="center"><img src="https://github.com/tarunshah/Student-Analysis/blob/master/docs/readme_images/TOTAL.JPG"></p>

From the above figure we can see that through the multidimensional cube having dimensions as students, semester and subjects the collective average grade of students are 3.33. Through this chart university can estimate the collective average performance of their institute.

Multidimensional cube have the functionality of drilling down and slicing up if we want to go further deep to visualize it in years.

<p align="center"><img src="https://github.com/tarunshah/Student-Analysis/blob/master/docs/readme_images/year_wise_grade.JPG"></p>

From the above figure we can see that the average performance of student in IIIrd year is slightly better that the previous year. We can further drill down if we want to visualize the performance of student’s semester wise.

<p align="center"><img src="https://github.com/tarunshah/Student-Analysis/blob/master/docs/readme_images/Stu_Sem_Performance.JPG"></p>

After drilling down to semester we can see individual average grade of each semester and we can analyze the students’ performance in each semester.

In all the above figure we have considered all the subjects and all the students to analyze the average grade.

Now we would drill down through subject wise to visualize the students’ performance in each and every subject.

<p align="center"><img src="https://github.com/tarunshah/Student-Analysis/blob/master/docs/readme_images/Sub_sem.JPG"></p>

Using above figure we can visualize average performance of all students in each subjects. Through this histogram we can also determine that which subject has higher average and we can consider it as below par performance of a faculty. With students, faculty is also responsible for the outcome i.e. grade. But taking it as average performance we can say that faculty is also involved in higher average or less performance. We can see through chart that the average of COA is very high and through this we can analyze that the performance of students as well as of faculty is not quite that well.

We can also determine the performance of a student by drilling down it to a particular student by selecting the student from student dimension.

<p align="center"><img src="https://github.com/tarunshah/Student-Analysis/blob/master/docs/readme_images/student%20wise%20subject%20grade.JPG"></p>

So we can see that by changing the queries through query builder we can analyze and visualize different perspective related to the student performance.

##Evaluation and Result
We can evaluate our open elective selection we will select K-fold cross validation method. The K- fold CV refers testing procedure where the database is randomly divided into K disjoints blocks of objects, then the data mining algorithm is trained using k-1 block and the remaining block is used to test the performance of the algorithm. This process is repeated k times. At the end we received measure are averaged.

###Performance Measure
There are some parameter on the basis of which we can evaluated the performance of the classifier such as true positive rate, false positive rate, accuracy and ROC area covered which are explained below.

The Accuracy of a classifier on a given test set is the percentage of test set tuples that are correctly classified by the classifier. The Error rate of a classifier, M, is the opposite of accuracy i.e. 1 – Acc (M), where Acc (M) is the accuracy of M.  The sensitivity and specificity measures can be used to calculate accuracy of classifiers. Sensitivity is also referred to as the true positive rate, while specificity is the true negative rate.

The measures are defined as follow:

	| Predicted = 0 |	Predicted = 1
	--- | --- | ---
Actual = 0 |	True Negative (TN) |	False Positive (FP)
Actual = 1 |	False Negative (FN) |	True Positive (TP)

Formulas | -
--- | --- 
Sensitivity = TP / (TP + FN) |	Specificity = TN / (TN + FP) 
Accuracy = (TN + TP) / (TN + FP + FN + FP) |	

Classification Algorithm |	A |	B |	Parametric Value |	Outcome
--- | --- | --- | --- | ---
FT |	31 |	30 |	A |	0
	 | 23 |	34 |	B |	1
AD Tree |	36 |	25 |	A |	0
	| 30 |	27 |	B |	1 
LAD Tree |	40 |	21 |	A |	0
	| 29 |	28 |	B |	1
J48 Tree |	37 |	24 |	A |	0
	| 36 |	21 |	B |	1 
SMO |	34 |	27 |	A |	0
	 |23 |	34 |	B |	1


Classification Algorithm |	TP Rate |	FP Rate |	Accuracy |	ROC Area
--- | --- | --- | --- | ---
FT |	0.60 |	0.51 |	0.55 |	0.4974
AD Tree |	0.47 |	0.59 | 0.53 |	0.547
LAD Tree |	0.49 |	0.65 |	0.57 | 0.5858
J48 Tree |	0.36 |	0.60 |	0.49 |	0.5361
SMO |	0.60 |	0.55 |	0.57 |	0.5769

#Result Analysis
The result analysis of this research is the success ratio achieved after comparing the actual and the predicted outcome. As the class is not defines for OE, whether the student is going to take that open elective or not, various decision tree classifier techniques are used such as FT (Functional Tree), ADT (Alternate Decision Tree) etc. The outcome of the analysis is mentioned above in table 11. The table shows that LAD (logical analysis of data) tree has an accuracy of 0.57, which is better than the other used algorithms. So we can classify the attribute with LAD tree with highest precision.

Also through stats we obtained using Oracle AWM toolkit, provide the insight about the performance of the student and the faculty. Thus we have achieved the objective in a successful manner to analyse the performance and to predict the behaviour of students.

#Conclusion

Using the result we obtained through our predictive analysis with WEKA, We can conclude the following things:

- The outcome of algorithm SMO (Support vector machine), WEKA, is same as that of the LAD. Therefore we don’t need to a machine to learn to classify the attribute i.e. Machine Learning is not needed for this analysis.
- For  the students, we can advise them to take open elective subject according to grades of their previous relative subject. For e.g. a student is advised to take Big Data Analysis as their open elective if they had gone through subjects like DBMS, DAA and Data Mining with higher grades obtained in these subjects.
- Further exploration can be possible for the parallel among the batches.
- Overall University performance can be obtained using the same scenario.


