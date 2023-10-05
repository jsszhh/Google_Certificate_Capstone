# Methodology


As I am working through this capstone, I had a goal of doing a large chunk of my work in SQL to really dive into and immerse myself in all the skills and applications I learned throughout the Google Certificate. I will continue this methodology here as I explore the dataset for basic instincts, but there are some key weaknesses in SQL which incentivize one to use another analytical software package (e.g., Python, R, SPSS) or even Excel for most of the analytical tasks (even cleaning).

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


### Some relationships between IVs and the DV

The DV in this dataset is `CourseGrade`, which we've cleaned up and renamed to `CLEANED_INT_CourseGrade`. We'll use this 
