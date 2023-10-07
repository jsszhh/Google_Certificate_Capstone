During the data analysis phase of this project, I created several visualizations which were interpretable and others which would not be of much relevance to the majority of potential stakeholders of this project.

Here is one which requires more finesse to interpret:


![Screenshot 2023-10-07 111147](https://github.com/jsszhh/Google_Certificate_Capstone/assets/146851092/4d36f534-e535-478b-a3f4-254b2495df10)


***Note.*** This visualization is also available on my TableauPublic profile, along with the visualizations I made in Tableau Desktop for this project.

This is a set of pairwise correlations plots. In it, students' Grade Brackets (Low, Medium, and High) are plotted as points along the scale of the ratio-level variables. They are:

`Number of Participations in Class Discussions`  
`Number of Announcements Viewed`  
`Number of Course Resources Used`  
`Number of Raised Hands`  

Later in the data analysis phase, my ML models (specifically the random forest classifier model) found these variables to be the most impactful on the dataset.

In reality, there is a lot of useful information in these pairwise plots. Just a few insights include:

* Students with fewer observations (i.e., closer to the left side of the chart, or zero observations of the target behavior) tended to be in the LOW `Grade Bracket`.
  * This trend was starkly evident in all of the target behaviors.
* Conversely, students with higher observations (i.e., closer to the right side of the chart, or 100 observations of the target behavior) tended to be in the HIGH `Grade Bracket`.
  * This trend was clearest for `Number of Raised Hands` and `Number of Course Resources Used`.
* Students in the MEDIUM `Grade Bracket` tended to skew towards fewer observations of target behaviors.
  * This trend was clearest for `Number of Announcements Viewed` and `Number of Participations in Class Discussions`.
  * However, MEDIUM `Grade Bracket` students were skewed towards higher observations of `Number of Course Resources Used`.
  * This highlights an ambiguous relationship between the grade bracket a student is in and certain behaviors potentially associated with academic engagement, such as classroom participation and viewing announcements.


## Machine Learning Visuals


### Confusion Matrix


The above relationships were picked apart further in the machine learning models. In an effort of maintaining brevity while not sacrificing a great amount of comprehensiveness in this analysis, I will show one confusion matrix and explain it in sufficient detail to understand how machine learning models can be interpreted.

Here is the confusion matrix for the random forest classifier (RFC) model.


![Screenshot 2023-10-07 125920](https://github.com/jsszhh/Google_Certificate_Capstone/assets/146851092/215b496f-ca82-48fc-92aa-c98d137d0a8b)


The confusion matrix plots the model's accuracy in categorizing observations based on thresholds it learns from "training" itself the dataset.

(_Note._ In this section, "observations" denotes a **single student**, a sample from the dataset; in the above section, "observations" referred to an instance of a student engaging in a specified behavior.)

In this dataset, I loaded all the IVs (e.g., `Gender`, `Number of Raised Hands`, etc.) into a single composite function that represents the variables from the dataset which we think might predict `Grade Bracket`, the DV.

I loaded the DV into another composite function.

Now, recall the goal. I want the computer to pick a student, look at that student's scores across all the IVs, then figure some kind of way to use that information to predict the student's Grade Bracket (the DV).

Across many, many students, it learns how best to use the IV information to predict the DV. In fact, _many, many students_ is not arbitrary; I told the model to use 80% of the students' data to train (i.e., _learn_) its accuracy, then test itself on the remaining 20%.

The confusion matrix is a visualized result of that train-test process. It visually shows the number of students from the testing dataset (i.e., the remaining 20% left over after training) who were correctly categorized into their real-life grade bracket.

Across the diagonals are the students' actual grade brackets. For instance:

* 17 students were correctly categorized into the LOW `Grade Bracket`
* 25 students were correctly categorized into the MEDIUM `Grade Bracket`
* 41 students were correctly categorized into the HIGH `Grade Bracket`

The rest of the table shows where the model failed to correctly categorize a student. For instance:

* 3 students were incorrectly categorized into the HIGH `Grade Bracket` when they actually were in the LOW `Grade Bracket`
* 4 students were incorrectly categorized into the HIGH `Grade Bracket` when they actually were in the MEDIUM `Grade Bracket`
* 5 students were incorrectly categorized into the LOW `Grade Bracket` when they actually were in the HIGH `Grade Bracket`
* 1 student was incorrectly categorized into the MEDIUM `Grade Bracket` when they actually were in the HIGH `Grade Bracket`

This shows that the model was ***86% accurate*** in categorizing its testing students. Meanwhile, the other machine learning models that I ran were less accurate (as low as 80% for the logistic regression).


### Ranked Feature Importances


After running the model and looking at its accuracy, the next step in interpreting a machine learning model is extrapolating the IVs that the model "felt" (for lack of a simpler word) helped it categorize the student into their grade bracket the best.

Such information can be best analogized to regression coefficients in traditional statistics, which essentially show the extent to which (i.e., how strongly) each IV contributed to the change in the DV.

In a machine learning model, however, ranking the IVs roughly means highlighting the variables that most contributed to a student's success or failure in their studies that school year.

Generally, the model which performed the best would be the most accurate in telling us the IVs. In this case, therefore, we need to look at the most important variables (or features) from the RFC model.

Here is that feature importances chart:


![Screenshot 2023-10-07 133915](https://github.com/jsszhh/Google_Certificate_Capstone/assets/146851092/7decf509-e680-4e10-bea8-0c97aafbcdbb)


_Note._ The variables have different names here than the correlations plot above. There was an issue with some of the variable names in Python, so in an effort to save time, I reverted to an earlier version of the dataset which had these variable names. Interpretations remain analogous for the variables.

This chart shows that the most important IV for predicting a student's grade bracket was the number of times they accessed and used course resources.

After that, how many times the student raised their hands, viewed announcements, and participated in classroom discussions were the most impactful IVs.

It may be the case that academic engagement behaviors such as using course materials and raising hand in class to participate may be best at determining a student's success in their studies. Future studies would be necessary to determine this relationship.
