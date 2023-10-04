# Cleaning my Data in SQL


First, I import the dataset I want to use (found at https://www.kaggle.com/datasets/aljarah/xAPI-Edu-Data) into Microsoft SQL Server Management Studio (MSSQL).

I save all my work under a database called "JACK_CAPSTONE", saved on my machine.

## Step 1 - Boolean variables out of nominal collected data


I set up code to transform all binary (i.e., boolean) variables from their native qualitative (i.e., string, nominal format) into dummy coded format (i.e., 0 for one condition, 1 for the other).

```
USE [JACK_CAPSTONE]
GO

-- Absences --
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD INTAbsences bit;
`GO --this same format is applied across this entire section of queries, starting with CREATING THE NEW DUMMY COLUMN`

UPDATE [dbo].[xAPI-Edu-Data]
SET [INTAbsences] = 0
WHERE [StudentAbsenceDays] = 'Under-7';
UPDATE [dbo].[xAPI-Edu-Data]
SET [INTAbsences] = 1
WHERE [StudentAbsenceDays] = 'Above-7'; --then I update EACH OF THE TWO POSSIBLE VALUES in that dummy column

ALTER TABLE [dbo].[xAPI-Edu-Data]
ALTER COLUMN INTAbsences bit NOT NULL; --finally, I update the column I just made to have the NOT NULL CONSTRAINT

-- Gender --
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD INTgender bit;
GO --CREATING THE NEW DUMMY COLUMN

UPDATE [dbo].[xAPI-Edu-Data]
SET [INTgender]= 0
WHERE [gender] = 'M';
UPDATE [dbo].[xAPI-Edu-Data]
SET [INTgender]= 1
WHERE [gender] = 'F'; --UPDATE VALUES IN DUMMY COLUMN

ALTER TABLE [dbo].[xAPI-Edu-Data]
ALTER COLUMN INTGender bit NOT NULL; --ADD NOT NULL CONSTRAINT

-- Semester --
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD INTSem bit;
GO --CREATING THE NEW DUMMY COLUMN

UPDATE [dbo].[xAPI-Edu-Data]
SET [INTSem] = 0
WHERE [Semester] = 'F';
UPDATE [dbo].[xAPI-Edu-Data]
SET [INTSem] = 1
WHERE [Semester] = 'S'; --UPDATE VALUES IN DUMMY COLUMN

ALTER TABLE [dbo].[xAPI-Edu-Data]
ALTER COLUMN INTSem bit NOT NULL; --ADD NOT NULL CONSTRAINT

-- Relationship to Parent --
ALTER TABLE [dbo].[xAPI-Edu-Data]
ADD INTRelation bit;
GO --CREATING THE NEW DUMMY COLUMN

UPDATE [dbo].[xAPI-Edu-Data]
SET [INTRelation] = 0
WHERE [Relation]= 'Mum';
UPDATE [dbo].[xAPI-Edu-Data]
SET [INTRelation] = 1
WHERE [Relation]= 'Father'; --UPDATE VALUES IN DUMMY COLUMN

ALTER TABLE [dbo].[xAPI-Edu-Data]
ALTER COLUMN INTRelation bit NOT NULL; --ADD NOT NULL CONSTRAINT

-- Show me the newly updated table --
SELECT *
```
FROM [dbo].[xAPI-Edu-Data]`
