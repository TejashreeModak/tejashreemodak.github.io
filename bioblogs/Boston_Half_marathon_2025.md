---
layout: post
title: Data science on the run 
date: 2025-06-09
---

## Data Science on the Run: Analyzing the 2025 Boston Run to Remember Half Marathon


<img align="left" src="/img/marathon/IMG_6677.jpeg">

I recently finished my fourth Half Marathon this time in Boston! As a data enthusiast and recreational runner, I combined my passions by diving into the results of the **2025 Boston Run to Remember Half Marathon**. I downloaded and cleaned the dataset, focused on *female participants*, and explored performance trends using both R and a corresponding python notebook showcasing the same analysis using different languages/methods. Analyzing real-world race data like this was incredibly satisfying — it’s a great example of how data science intersects with daily life and hobbies.

This post is a walkthrough of my analysis — covering data wrangling, visualizations, statistical modeling, and some fun insights on pacing strategies.

### The Data
The data came from the [official results page](https://results.raceroster.com/v2/en-US/results/36vs6pxhw573fv4c/results?subEvent=227956) and included names, finish times, age groups, and split times (5K, 10K, 10M, etc). I filtered the dataset for female runners only since I was interested in finding more insights in this group. Maximum time to finish the race was 3 hours. 

### Data Preparation
- Always start with data cleaning steps. Here I removed rows with empty pace or split times and rows with any other fields that were NA.
- Runners' times were in a HH:MM:SS format. I wrote a helper function to convert all times (finish and splits) into total minutes for easy computation and plotting. This conversion made the rest of the analysis straightforward.
- Outliers: taking a look at the outliers is very important, here I perform outlier analysis using the Interquartile Range (IQR) method. These turned out to be outlier slow times that might reflect pacing issues, injuries, or walkers. 
```r
# Calculate IQR for finish time
Q1 <- quantile(df_age$FinishTime_numeric, 0.25)
Q3 <- quantile(df_age$FinishTime_numeric, 0.75)
IQR <- Q3 - Q1
# Identify outliers (outside of 1.5*IQR range)
outliers <- df %>%
  filter(FinishTime_numeric < (Q1 - 1.5 * IQR) | FinishTime_numeric > (Q3 + 1.5 * IQR))
# View outliers
outliers
```

### Exploratory Visualizations

#### Participation Stats: Number of Runners per Age Group
Age group 20-24, had the highest participation, it was inspiring to see runners in 70-74 age group still participating.

<img align="left" src="/img/marathon/Num_runners_per_age_grp2.png">

#### Finish Time by Age Group
Using ggplot2, I created a boxplot to show the spread of finish times across age divisions.
Plotly is super useful in making the plot interactive. 

<img align="left" src="/img/marathon/Boxplot_Finish_Time_by_Age_Group.png">

#### Finish Time Distribution
I also explored how finish times are distributed. A histogram faceted by age group shows the trend of frequency of runners across finish times.You can see that while most runners finish around 110–140 minutes, younger runners tend to skew faster. 

<img align="left" src="/img/marathon/Histogram_Finish_times.png">

#### Top 5 Finishers per Division
I highlighted the top 5 finishers per age group:
This clearly showed how middle-aged runners tended to dominate top finishes.

<img align="left" src="/img/marathon/Top_5_runners_per_age_grp.png">

#### Normalized Finish Times
To compare across divisions more fairly, I normalized finish times (z-scores):

<img align="left" src="/img/marathon/Normalized_Finish_Time_by_Age_Group.png">

This makes it easier to identify who outperformed their peers, regardless of age.


### Performance Modeling: Is Age correlated with Finish Time?
I parsed age from the division field and found a weak positive correlation (r ≈ 0.13) between age and finish time.
But when I fit a quadratic model, the curve was U-shaped — middle-aged runners were fastest, with slower times in younger and older groups:
```r
lm(FinishTime_numeric ~ poly(AgeGroup, 2), data = df_age)

```

<img align="left" src="/img/marathon/Quadratic_model.png">

#### Multivariate Regression
I built a multiple regression model using pace, 10K split, and age group. Result?
```r
R-squared = 0.9998

```
Pace was the dominant predictor. Age and splits didn’t matter much once pace was known — a good reminder that "pace is king."

#### Cluster Analysis
Using K-means clustering on finish times and age, I identified three distinct performance clusters:

This visualization helps identify groups like elites, mid-packers, and back-of-the-pack runners.

### Split-Wise Pacing
#### Pace Dynamics by Split
I decomposed runners’ performances into four key splits:
-0–5K
-5K–10K
-10K–10M
-10M–Finish

Then visualized how their pace varied across splits:


We can see the blue line (mean pace) — runners generally slowed down in the later stages.

#### Consistent pace
How evenly a runner paces across splits — and correlate with performance
Test the idea that more consistent pacing leads to better outcomes.

#### Negative Splits
I also examined who ran a negative split (second half faster than the first). 

#### Cumulative Progression
To get a sense of how runners progressed through the course, I reconstructed their cumulative time over each split:


This paints a picture of group pacing strategy — many runners start conservatively and fade in the second half.

### Key takeaways

- Pace is king — it explains almost all variance in finish times.
- Middle-aged runners tend to perform best overall.
- Even pacing is a strong performance indicator.
- Clustering, regression, and progression plots reveal hidden race dynamics.
- Runners who ran negative splits had better results.

### Code
You can find the full code on my [GitHub](https://github.com/TejashreeModak/Blog_Boston_Half_Marathon/tree/main). Feel free to fork, adapt, or use it in your own running analytics!

## 🏁 Wrapping Up

From finish times to pacing consistency, age trends to cluster analysis—this half marathon dataset offered a rich field for exploration. What began as a personal race turned into a deep dive into real-world data, revealing the stories numbers can tell when we visualize and model them with care.

Whether you're a runner, a data scientist, or both, one thing is clear:

**The finish line is just the beginning when you're chasing insights.**

**Run data. Uncover patterns. Go the distance. 🏃📊**

