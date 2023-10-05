# Cleaning my Data in SQL


First, I import the dataset I want to use (found at https://www.kaggle.com/datasets/aljarah/xAPI-Edu-Data) into Microsoft SQL Server Management Studio (MSSQL).

I save all my work under a database called "JACK_CAPSTONE", saved on my machine.


## Step 0 - Making a primary key


This is the first step before anything else happens because the students do not have unique identifiers (IDs) already assigned in the .csv file. In case we need the students to have unique IDs, I'll make this column now:

```
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD studentID integer IDENTITY(1,1);
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD PRIMARY KEY (studentID)
```

This will just assign each row (in this dataset, a student) a unique identifier, starting at 1 and increasing by 1 with each subsequent row (i.e., row 1 has ID 1, row 200 has ID 200, row 50 has ID 50).

## Step 1 - Updating column names


Here, I changed the names of some columns to make sure they were a little bit more descriptive, clearer, and more pleasing (for my eyes). I changed the names of the following columns:

`gender` to `Gender`  
`NationalITy` to `Nationality`  
`PlaceofBirth` to `BirthCountry`  
`StageID` to `SchoolType`  
`GradeID` to `GradeLevel`  
`SectionID` to `Classroom`  
`Topic` to `CourseSubject`  
`Relation` to `ResponsibleParent`  
`raisedhands` to `NumRaisedHands`  
`VisITedResources` to `NumCourseResourcesUsed`  
`AnnouncementsView` to `NumAnnouncementsViewed`  
`Discussion` to `NumDiscussionParticipation`  
`ParentschoolSatisfaction` to `ParentSatisfaction`  
`StudentAbsenceDays` to `StudentAbsences`

I did this very simply in MSSQL by right clicking each column (Database\Table\Columns) and clicking "Rename", as shown below.

![Screenshot 2023-10-04 125534](https://github.com/jsszhh/Google-Certificate-Capstone/assets/146851092/da327d74-5ea2-4386-ab8b-6324f4cb4380)

Other SQL environments (e.g., MySQL, bigQuery) may have a different approach for doing this, such as:

```
ALTER TABLE TableName
RENAME COLUMN OldColumnName TO NewColumnName;
```


## Step 2 - Fixing an incorrect data type


There are some data types which are incorrect in this dataset. Specifically:

`GradeLevel` is set to `money`

However, the reason this is the case is because the data is inputted in the .csv file as follows:


`G-01` in the original .csv dataset means `First Grade`  
`G-02` in the original .csv datasetmeans `Second Grade`

...and so on.

The problem here is that SQL removed the G but kept the - symbol when importing the dataset, leaving me with:

`-1` to mean `First Grade`  
`-2` to mean `Second Grade`

I'll adjust the values in a sort of roundabout way, just because I have to use SQL. First, I'll change the data type for this column from `money` to `smallint` using the following code:

```
ALTER TABLE [dbo].[xAPI-Edu-Data]
ALTER COLUMN GradeLevel tinyint NOT NULL
```

Now, I have a column with the following types of values:

| studentID | GradeLevel |
| --------- | ---------- |
| 1         | -4         |
| 2         | -4         |
| 3         | -4         |
| 4         | -4         |
| 5         | -4         |
| 6         | -4         |
| 7         | -7         |
| 8         | -7         |

I still need to remove the negative signs. To do this, I set each value in the column equal to the absolute value of the values already in that column. I used the following code:

```
UPDATE [dbo].[xAPI-Edu-Data]
SET GradeLevel=ABS(GradeLevel)
```

## Step 3 - Boolean columns out of nominal collected data


I set up code to transform all binary (i.e., boolean) variables from their native qualitative (i.e., string, nominal format) into dummy coded format (i.e., 0 for one condition, 1 for the other). I store the new data into new columns, following a general naming convention CLEANED_*variablename*.

```
USE [JACK_CAPSTONE]
GO

-- Absences --
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD CLEANED_Absences bit;
GO --this same format is applied across this entire section of queries, starting with CREATING THE NEW DUMMY COLUMN

UPDATE [dbo].[xAPI-Edu-Data]
SET [CLEANED_Absences] = 0
WHERE [StudentAbsences] = 'Under-7';
UPDATE [dbo].[xAPI-Edu-Data]
SET [CLEANED_Absences] = 1
WHERE [StudentAbsences] = 'Above-7'; --then I update EACH OF THE TWO POSSIBLE VALUES in that dummy column

ALTER TABLE [dbo].[xAPI-Edu-Data]
ALTER COLUMN CLEANED_Absences bit NOT NULL; --finally, I update the column I just made to have the NOT NULL CONSTRAINT

-- Gender --
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD CLEANED_Gender bit;
GO --CREATING THE NEW DUMMY COLUMN

UPDATE [dbo].[xAPI-Edu-Data]
SET [CLEANED_Gender]= 0
WHERE [Gender] = 'M';
UPDATE [dbo].[xAPI-Edu-Data]
SET [CLEANED_Gender]= 1
WHERE [Gender] = 'F'; --UPDATE VALUES IN DUMMY COLUMN

ALTER TABLE [dbo].[xAPI-Edu-Data]
ALTER COLUMN INTGender bit NOT NULL; --ADD NOT NULL CONSTRAINT

-- Semester --
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD CLEANED_Sem bit;
GO --CREATING THE NEW DUMMY COLUMN

UPDATE [dbo].[xAPI-Edu-Data]
SET [CLEANED_Sem] = 0
WHERE [Semester] = 'F';
UPDATE [dbo].[xAPI-Edu-Data]
SET [CLEANED_Sem] = 1
WHERE [Semester] = 'S'; --UPDATE VALUES IN DUMMY COLUMN

ALTER TABLE [dbo].[xAPI-Edu-Data]
ALTER COLUMN CLEANED_Sem bit NOT NULL; --ADD NOT NULL CONSTRAINT

-- Relationship to Parent --
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD CLEANED_Resp_Parent bit;
GO --CREATING THE NEW DUMMY COLUMN

UPDATE [dbo].[xAPI-Edu-Data]
SET [CLEANED_Resp_Parent] = 0
WHERE [ResponsibleParent]= 'Mum';
UPDATE [dbo].[xAPI-Edu-Data]
SET [CLEANED_Resp_Parent] = 1
WHERE [ResponsibleParent]= 'Father'; --UPDATE VALUES IN DUMMY COLUMN

ALTER TABLE [dbo].[xAPI-Edu-Data]
ALTER COLUMN CLEANED_Resp_Parent bit NOT NULL; --ADD NOT NULL CONSTRAINT

-- Parent Satisfaction --
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD CLEANED_Par_Satisfaction bit;
GO --this same format is applied across this entire section of queries, starting with CREATING THE NEW DUMMY COLUMN

UPDATE [dbo].[xAPI-Edu-Data]
SET [CLEANED_Par_Satisfaction] = 0
WHERE [ParentSatisfaction] = 'Bad';
UPDATE [dbo].[xAPI-Edu-Data]
SET [CLEANED_Par_Satisfaction] = 1
WHERE [ParentSatisfaction] = 'Good'; --then I update EACH OF THE TWO POSSIBLE VALUES in that dummy column

ALTER TABLE [dbo].[xAPI-Edu-Data]
ALTER COLUMN CLEANED_Par_Satisfaction bit NOT NULL; --finally, I update the column I just made to have the NOT NULL CONSTRAINT

-- Show me the newly updated table --
SELECT *
FROM [dbo].[xAPI-Edu-Data]
```

It isn't possible to do nested set/where statements within a single UPDATE statement, so I had to create separate UPDATE, SET, and WHERE statements for each variable I wanted to update (and, subsequently, each value within each of those variables).


## Step 4 - Tidying values in columns


There are a lot of values in this dataset that are still strangely formatted or misspelled, and will thus be a challenge to analyze later in Python/R/SPSS. To address these issues, I want to make new columns containing the cleaned versions of the problematic columns, following the earlier-established formatting approach of CLEANED_*variablename* for each new column.


### SchoolType


In the `SchoolType` column, we have 3 different values as follows:

| studentID | SchoolType |
| --------- | ---------- |
| 1         | lowerlevel |
| 2         | lowerlevel |
| 3         | lowerlevel |
| 4         | lowerlevel |
...
| 7         |middleschool|
| 8         |middleschool|
...
| 43        |highschool  |
| 44        |highschool  |

Here, I'll format `lowerlevel` to `Elementary`, and thus `middleschool` to `Middle` and `highschool` to `High` to make it easier for me to visually approach the data.

First, I'll make a new column called `CLEANED_SchoolType` for the cleaned version of `SchoolType`:

```
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD CLEANED_SchoolType nvarchar(50);
```

Then, I will update this new column with the transformed values from the initial `SchoolType` column, using the `UPDATE` and `CASE WHEN` functions:

```
UPDATE [dbo].[xAPI-Edu-Data]
SET CLEANED_SchoolType = 
		CASE
			WHEN SchoolType = 'lowerlevel' THEN 'Elementary'
			WHEN SchoolType = 'MiddleSchool' THEN 'Middle'
			WHEN SchoolType = 'HighSchool' THEN 'High'
		END
```


### Nationality


In the `Nationality` column, we have a lot that needs to be addressed.

First, we have `KW` to refer to `Kuwait` and `USA` to refer to the `United States of America` (i.e., country abbreviations), but then a large assortment of other countries all referred to by their full names, as in `Syria`, `Iran`, and others.

Furthermore, we have misspelled or poorly formatted countries:

`Tunis` is used to refer to `Tunisia`
`venzuela` is used to refer to `Venezuela`
`lebanon` is used to refer to `Lebanon`

I can continue to use the `UPDATE` and `CASE WHEN` functions, as I did with `SchoolType`, to make these changes.

First, I'll make a new column called `CLEANED_Nationality` for the cleaned version of `Nationality`:

```
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD CLEANED_Nationality nvarchar(50);
```

Then, I will update this new column with the transformed values from the initial `Nationality` column:

```
UPDATE [dbo].[xAPI-Edu-Data]
SET CLEANED_Nationality = 
		CASE
			WHEN Nationality = 'KW' THEN 'Kuwait'
			WHEN Nationality = 'lebanon' THEN 'Lebanon'
			WHEN Nationality = 'venzuela' THEN 'Venezuela'
			WHEN Nationality = 'Tunis' THEN 'Tunisia'
			ELSE Nationality
		END
```


### BirthCountry


A lot of the same issues from the `Nationality` column exist here in the `BirthCountry` column:

`Kuwait` is written as `KuwaIT`  
`Lebanon` is written as `lebanon` again  
`Venezuela` is written as `venzuela` again  
`Tunisia` is written as `Tunis` again  

I'll fix these with the same lines of code that I used to fix `Nationality` and `SchoolType`.

```
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD CLEANED_BirthCountry nvarchar(50);

UPDATE [dbo].[xAPI-Edu-Data]
SET CLEANED_BirthCountry = 
		CASE
			WHEN BirthCountry = 'KuwaIT' THEN 'Kuwait'
			WHEN BirthCountry = 'lebanon' THEN 'Lebanon'
			WHEN BirthCountry = 'venzuela' THEN 'Venezuela'
			WHEN BirthCountry = 'Tunis' THEN 'Tunisia'
			ELSE BirthCountry
		END
```



## Step 5 - Changing the rest of the nominal (categorical) columns to integers

Finally, to make the dataset the most approachable for analysis, I want to transform the remaining nominal variables (things like `Nationality` and `BirthCountry`) to numerical values (1, 2, 3, ...).

To retain the original data, I will make separate columns for the new cleaned variables, as I did above for the boolean transformations.
