# Methodology


As I am working through this capstone, I had a goal of doing a large chunk of my work in SQL to really dive into and immerse myself in all the skills and applications I learned throughout the Google Certificate. I will continue this approach here as I explore the dataset for basic insights, but there are some key weaknesses in SQL which incentivize one to use another analytical software package (e.g., Python, R, SPSS) or even Excel for most of their analytical tasks (even cleaning).

I will continue the goal I set out with and explore some of the relationships between variables in this dataset in SQL, but then I will transition to Python for the rest of my work.

I do not need to practice quantitative approaches in R or SPSS as I have substantial experience in those environments from my psychology coursework. Here, I'll focus on honing my Python and SQL implementation skills.


## What kinds of basic relationships exist in the data?


When analyzing any dataset, it's good to start broad and work our way in towards specificity. As there are a lot of independent variables (IVs; e.g., `SchoolType`, `Absences`, `Classroom`), we have a lot of potential relationships between independent variables **and** a lot of potential relationships between the IVs and the dependent variable (DV).

I'll first explore by eye what kinds of basic correlations exist among the IVs, then I'll explore how they might relate to the DV, `CourseGrade`.


### Some relationships among IVs

First, we can ask, **"Is there a relationship between _hand raising_ and a student's level of education (i.e., `SchoolType`)?"**

To answer this, we can get an `AVG` of hand raising across school type using the `GROUP BY` function:

```
SELECT
	CLEANED_SchoolType,
	AVG (NumRaisedHands) AS AVG_RaisedHands
FROM [dbo].[CLEANED_CAPSTONE_DATA]
GROUP BY CLEANED_SchoolType;
```

Our output shows us the following insights:

_Elementary schoolers_ raised their hands to participate **38 times** on average over the school year.  
_Middle schoolers_ raised their hands to participate **53 times** on average over the school year.  
_High schoolers_ raised their hands to participate **43 times** on average over the school year.  

We can see that on average it looks like elementary schoolers raise their hand fewer times than high schoolers, but both elementary and high schoolers raise their hands substantially fewer times than middle schoolers.

We can also ask, **"Is there a relationship between _number of class resources used_ in a school year and a student's level of education (i.e., `SchoolType`)?"**

To answer this, we can again follow the same process as above:

```
SELECT
	CLEANED_SchoolType,
	AVG (NumCourseResourcesUsed) AS AVG_CourseResourcesUsed
FROM [dbo].[CLEANED_CAPSTONE_DATA]
GROUP BY CLEANED_SchoolType;
```

Our output shows us the following insights:

Both _elementary and high schoolers_ used course resources _50 times_ on average over the school year.  
_Middle schoolers_ used course resources _58 times_ on average over the school year.  

Here again we see a similar trend as the first analysis, in that elementary schoolers and high schoolers are similar on educational outcomes, while middle schoolers seem to be more engaged.

We can ask more questions to see if this trend continues, such as, **"Is there a relationship between _number of announcements viewed_ and a student's level of education (i.e., `SchoolType`)?"**

We'll once again use the same kind of coding as above:

```
SELECT
	CLEANED_SchoolType,
	AVG (NumAnnouncementsViewed) AS AVG_NumAnnouncementsViewed
FROM [dbo].[CLEANED_CAPSTONE_DATA]
GROUP BY CLEANED_SchoolType;
```

Our output shows us the following insights:

On average, _elementary schoolers_ viewed **31 announcements** over the school year.  
On average, _middle schoolers_ viewed **43 announcements** over the school year.  
On average, _high schoolers_ viewed **36 announcements** over the school year.  

Here, we have another example of middle schoolers taking more initiative and being more attentive at school, moreso even than high schoolers.

How about another metric of course engagement? We can ask, **"Is there a relationship between the number of times a student participates in discussions over the school year and their level of education (i.e., `SchoolType`)?**

We'll use the same code as above again:

```
SELECT
	CLEANED_SchoolType,
	AVG (NumDiscussionParticipation) AS AVG_DiscussionParticipation
FROM [dbo].[CLEANED_CAPSTONE_DATA]
GROUP BY CLEANED_SchoolType;
```

Contrasting with our previous results, our output shows us these insights:

_Elementary schoolers_ participated in discussions **38 times** on average over the school year.  
_Middle schoolers_ participated in discussions **45 times** on average over the school year.  
_High schoolers_ participated in discussions **53 times** on average over the school year.  

Here, we see a general increase in the average number of discussion responses as a student advances through their education. At the onset of this analysis, we actually may have predicted precisely this outcome across all the relevant IVs measuring academic performance and engagement, as children may be expected to develop stronger language abilities, confidence, self-esteem, and other cognitive functions that would help them to engage and succeed in their academics as they grow older. Thus, high schoolers should have better engagement metrics than middle schoolers, and middle schoolers more than elementary schoolers. However, this does not appear to be entirely the case in this dataset.

Okay, great. The above basic analyses provide us an elementary understanding of how middle schoolers may be more engaged in their coursework, school resources, and academic journey than elementary and high schoolers. However, to really get a deeper understanding of these correlations (and all the other possible IV correlations), we'd need to run bivariate correlation analyses using a statistical software package. Beyond that, we could relate those basic insights to a more comprehensive understanding of how those variables impact course grades (i.e., school performance) with a regression analysis.

Let's move on to getting a basic look at the relationships between some of the interesting IVs and the DV.


### A relationship between an IV and a DV

The DV in this dataset is `CourseGrade`, which we've cleaned up and renamed to `CLEANED_INT_CourseGrade`. Let's dive into the DV a little bit here in SQL.

In the above analyses, we found a potential trend that middle schoolers may be more engaged in their academics than elementary and high schoolers. We may be able to pick this apart a little further now by seeing how many students fell into the _Low, Medium, and High_ course grade brackets in each school type.

```
BEGIN

-- STEP 1 --

DECLARE @NUM_EachSchool TABLE (School_Type NVARCHAR(50), Students_per_School DECIMAL(20, 7))
INSERT INTO @NUM_EachSchool (School_Type, Students_per_School)
SELECT CLEANED_SchoolType,
	COUNT (CLEANED_INT_SchoolType)
FROM [dbo].[CLEANED_CAPSTONE_DATA]
GROUP BY CLEANED_SchoolType;
SELECT * FROM @NUM_EachSchool;

-- STEP 2 --

DECLARE @NUM_EachSchool_Grade TABLE (Type_School NVARCHAR(50), Course_Grade DECIMAL(20, 7), Students_per_Grade DECIMAL(20, 7))
INSERT INTO @NUM_EachSchool_Grade (Type_School, Course_Grade, Students_per_Grade)
SELECT CLEANED_SchoolType,
	CLEANED_INT_CourseGrade,
	COUNT (CLEANED_INT_CourseGrade)
FROM [dbo].[CLEANED_CAPSTONE_DATA]
GROUP BY CLEANED_SchoolType, CLEANED_INT_CourseGrade;
SELECT * FROM @NUM_EachSchool_Grade;

-- STEP 3 --

SELECT Type_School, Course_Grade, Students_per_Grade, Students_per_School, CONVERT(DECIMAL(20, 7), ((Students_per_Grade*100)/Students_per_School)) AS Percentage_per_Grade
FROM @NUM_EachSchool_Grade
JOIN @NUM_EachSchool ON Type_School = School_Type

END
```

Now, this is a pretty complicated chunk of code, so let me pick it apart.

First, **Step 1**:

```
-- STEP 1 --

DECLARE @NUM_EachSchool TABLE (School_Type NVARCHAR(50), Students_per_School DECIMAL(20, 7))
INSERT INTO @NUM_EachSchool (School_Type, Students_per_School)
SELECT CLEANED_SchoolType,
	COUNT (CLEANED_INT_SchoolType)
FROM [dbo].[CLEANED_CAPSTONE_DATA]
GROUP BY CLEANED_SchoolType;
SELECT * FROM @NUM_EachSchool;
```

Here, all that's happening is I'm creating a **temporary table** (denoted with the @ symbol) which will tell me the name of the school in one column (elementary, middle, high) and the number of students in each school in the next column.

Here is the output table:

![Screenshot 2023-10-06 084508](https://github.com/jsszhh/Google_Certificate_Capstone/assets/146851092/fe18d97e-67f3-4e69-8330-25183b1fbe17)

Next, **Step 2**:

```
-- STEP 2 --

DECLARE @NUM_EachSchool_Grade TABLE (Type_School NVARCHAR(50), Course_Grade DECIMAL(20, 7), Students_per_Grade DECIMAL(20, 7))
INSERT INTO @NUM_EachSchool_Grade (Type_School, Course_Grade, Students_per_Grade)
SELECT CLEANED_SchoolType,
	CLEANED_INT_CourseGrade,
	COUNT (CLEANED_INT_CourseGrade)
FROM [dbo].[CLEANED_CAPSTONE_DATA]
GROUP BY CLEANED_SchoolType, CLEANED_INT_CourseGrade;
SELECT * FROM @NUM_EachSchool_Grade;
```

Here, I'm creating another temporary table which tells me, again, the name of the school in one column, but this time the course grade (denoted by 1, low, 2, medium, and 3, high) in the second column, and finally the number of students at each school type who received that grade.


Here is the table:

![Screenshot 2023-10-06 084747](https://github.com/jsszhh/Google_Certificate_Capstone/assets/146851092/ecc32f52-85d5-4a7c-b860-c1798c158d37)


At this point, we have the info we need, `Students_per_School` and `Students_per_Grade`. All we need to do now is to divide the number of students who received each grade in each school, (e.g., 65 elementary schoolers received a 1, divide 65 by the total number of 199 elementary schoolers) to receive a weighted percentage that we can analyze. This step is especially important because we don't have equal group sizes, so looking at the raw number of students to understand the data would be misleading.

This where **Step 3** comes into play:

```
-- STEP 3 --

SELECT Type_School, Course_Grade, Students_per_Grade, Students_per_School, CONVERT(DECIMAL(20, 7), ((Students_per_Grade*100)/Students_per_School)) AS Percentage_per_Grade
FROM @NUM_EachSchool_Grade
JOIN @NUM_EachSchool ON Type_School = School_Type
```

Here, I am creating a new temporary table JOINED ON the `School_Type` columns (named differently because they had to be) from each of the first two tables. The columns in this new table come from columns from the other two temporary tables (columns `Type_School`, `Course_Grade`, `Students_per_Grade`, and `Students_per_School`). Then, I added a new column which computes the percentage by simply dividing `Students_per_Grade` by `Students_per_School` and multiplying by 100.

**Note.** I multiplied the numerator, `Students_per_Grade` first by 100 and then divided by the denominator, `Students_per_School`.

This is the output table:

![Screenshot 2023-10-06 091201](https://github.com/jsszhh/Google_Certificate_Capstone/assets/146851092/a12eab9c-eb25-47ca-9926-2ddc8001a1f3)

At this point, we can stop and analyze the data. However, our role as a data analyst is to make things presentable, beautify them. Earlier, I liked 7 decimal points because it kept the fidelity of the percentage and got the total percentage (i.e., when we added all the percentages) closest to 100% (rather than rounding causing us to be at 101% or 99%. However, we never needed the decimal places in the whole numbers, as it gave us tables with seven zeroes after the decimal. Now, we can modify this table with a few extra lines of code using the FORMAT and ROUND functions to beautify our table.

We modify **Step 3** as follows:

```
-- STEP 3 --

SELECT
	Type_School,
	FORMAT (ROUND (Course_Grade, 0, 0), 'N0') AS Course_Grade,
	FORMAT (ROUND (Students_per_Grade, 0, 0), 'N0') AS Students_per_Grade,
	FORMAT (ROUND (Students_per_School, 0, 0), 'N0') AS Students_per_School,
	FORMAT (ROUND (CONVERT (DECIMAL(20, 7), ((Students_per_Grade*100)/Students_per_School)), 2, 0), 'N2') AS Percentage_per_Grade
FROM @NUM_EachSchool_Grade
JOIN @NUM_EachSchool ON Type_School = School_Type
```

And here is our final table with all our data:

![Screenshot 2023-10-06 093618](https://github.com/jsszhh/Google_Certificate_Capstone/assets/146851092/e7c47b5b-a5ee-4429-97e7-ac88da2f50a1)

Super clean!

Some basic insights from this data may be:

Elementary schoolers had a larger proportion of _Low_ course grades than middle and high schoolers.  
Middle schoolers had a larger proportion of _Medium_ course grades than elementary and high schoolers, but the percentages were close in distance (Middle = 47.58%, High = 42.42%, Elementary = 39.70%).  
High schoolers had the largest proportion of _High_ course grades, 33.33%.  

These results are surprising when compared to the earlier insights we received from our preliminary comparisons of the school types among other IVs.

### Next steps - Beyond SQL

We've done some basic exploration of the trends between the IVs and between an IV and a DV. In SQL, these analyses don't go much further, as statistical tests are not part of the functionality of this programming language.

We can only say that we can see differences between the numbers, but to get a deeper understanding of how the IVs in this dataset affect `Course_Grade`, we'd need to conduct statistical analyses (e.g., regressions, correlations).

Moving from here, we need to import the .csv dataset, `CLEANED_CAPSTONE_DATA`, into a statistical analysis package like R, Python, or SPSS.

In this course, we used R to conduct statistical analyses. However, I am going to use Python so that I can practice what I've learned in that language more, as I already am very confident in my skills in R from my psychology research.
