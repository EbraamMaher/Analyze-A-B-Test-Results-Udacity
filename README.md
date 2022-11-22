<div class="cell markdown">

## Analyze A/B Test Results



## Table of Contents

  - [Introduction](#intro)
  - [Part I - Probability](#probability)
  - [Part II - A/B Test](#ab_test)
  - [Part III - Regression](#regression)

<a id='intro'></a>

### Introduction

A/B tests are very commonly performed by data analysts and data
scientists. It is important that you get some practice working with the
difficulties of these


<a id='probability'></a>

#### Part I - Probability

To get started, let's import our libraries.

</div>

<div class="cell code" data-execution_count="1">

``` python
import pandas as pd
import numpy as np
import random
import matplotlib.pyplot as plt
%matplotlib inline
#We are setting the seed to assure you get the same answers on quizzes as we set up
random.seed(42)
```

</div>

<div class="cell markdown">

`1.` Now, read in the `ab_data.csv` data. Store it in `df`. **Use your
dataframe to answer the questions in Quiz 1 of the classroom.**

a. Read in the dataset and take a look at the top few rows here:

</div>

<div class="cell code" data-execution_count="2">

``` python
ab_data = pd.read_csv("ab_data.csv")
```

</div>

<div class="cell code" data-execution_count="3">

``` python
ab_data.head()
```

<div class="output execute_result" data-execution_count="3">

``` 
   user_id                   timestamp      group landing_page  converted
0   851104  2017-01-21 22:11:48.556739    control     old_page          0
1   804228  2017-01-12 08:01:45.159739    control     old_page          0
2   661590  2017-01-11 16:55:06.154213  treatment     new_page          0
3   853541  2017-01-08 18:28:03.143765  treatment     new_page          0
4   864975  2017-01-21 01:52:26.210827    control     old_page          1
```

</div>

</div>

<div class="cell code" data-execution_count="4">

``` python
ab_data.info()
```

<div class="output stream stdout">

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 294478 entries, 0 to 294477
    Data columns (total 5 columns):
     #   Column        Non-Null Count   Dtype 
    ---  ------        --------------   ----- 
     0   user_id       294478 non-null  int64 
     1   timestamp     294478 non-null  object
     2   group         294478 non-null  object
     3   landing_page  294478 non-null  object
     4   converted     294478 non-null  int64 
    dtypes: int64(2), object(3)
    memory usage: 11.2+ MB

</div>

</div>

<div class="cell markdown">

b. Use the below cell to find the number of rows in the dataset.

</div>

<div class="cell code" data-execution_count="5">

``` python
ab_data.shape[0]
```

<div class="output execute_result" data-execution_count="5">

    294478

</div>

</div>

<div class="cell markdown">

c. The number of unique users in the dataset.

</div>

<div class="cell code" data-execution_count="6">

``` python
#ab_data.head()
ab_data.nunique()[0]
```

<div class="output execute_result" data-execution_count="6">

    290584

</div>

</div>

<div class="cell markdown" data-collapsed="true">

d. The proportion of users converted.

</div>

<div class="cell code" data-execution_count="7">

``` python
ab_data.converted.sum()/ab_data.shape[0]
```

<div class="output execute_result" data-execution_count="7">

    0.11965919355605512

</div>

</div>

<div class="cell markdown" data-collapsed="true">

e. The number of times the `new_page` and `treatment` don't line up.

</div>

<div class="cell code" data-execution_count="8">

``` python
#ab_data[(ab_data['landing_page']=='new_page') & (ab_data[ab_data['group']=='treatment'])]

#ab_data.groupby(['landing_page']).count().group
#ab_data.groupby(['group']).count()
#ab_data[(ab_data['landing_page']=='new_page')]
#(ab_data[ab_data['group']=='treatment'])   
((ab_data.landing_page == 'old_page') & (ab_data.group == 'treatment')).sum()
```

<div class="output execute_result" data-execution_count="8">

    1965

</div>

</div>

<div class="cell markdown">

f. Do any of the rows have missing values?

</div>

<div class="cell code" data-execution_count="9">

``` python
ab_data[ab_data.isnull().any(axis=1)].count()
```

<div class="output execute_result" data-execution_count="9">

    user_id         0
    timestamp       0
    group           0
    landing_page    0
    converted       0
    dtype: int64

</div>

</div>

<div class="cell markdown">

`2.` For the rows where **treatment** is not aligned with **new\_page**
or **control** is not aligned with **old\_page**, we cannot be sure if
this row truly received the new or old page. Use **Quiz 2** in the
classroom to provide how we should handle these rows.

a. Now use the answer to the quiz to create a new dataset that meets the
specifications from the quiz. Store your new dataframe in **df2**.

</div>

<div class="cell code" data-execution_count="10">

``` python

df2=ab_data[((ab_data.landing_page=='new_page') & (ab_data.group=='treatment'))| ((ab_data.landing_page=='old_page') & (ab_data.group=='control'))]
```

</div>

<div class="cell code" data-execution_count="11">

``` python
df2.info()
```

<div class="output stream stdout">

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 290585 entries, 0 to 294477
    Data columns (total 5 columns):
     #   Column        Non-Null Count   Dtype 
    ---  ------        --------------   ----- 
     0   user_id       290585 non-null  int64 
     1   timestamp     290585 non-null  object
     2   group         290585 non-null  object
     3   landing_page  290585 non-null  object
     4   converted     290585 non-null  int64 
    dtypes: int64(2), object(3)
    memory usage: 13.3+ MB

</div>

</div>

<div class="cell code" data-execution_count="12">

``` python
# Double Check all of the correct rows were removed - this should be 0
df2[((df2['group'] == 'treatment') == (df2['landing_page'] == 'new_page')) == False].shape[0]
```

<div class="output execute_result" data-execution_count="12">

``` 
0
```

</div>

</div>

<div class="cell markdown">

`3.` Use **df2** and the cells below to answer questions for **Quiz3**
in the classroom.

</div>

<div class="cell markdown">

a. How many unique **user\_id**s are in **df2**?

</div>

<div class="cell code" data-execution_count="13">

``` python
df2.user_id.nunique()
```

<div class="output execute_result" data-execution_count="13">

    290584

</div>

</div>

<div class="cell markdown" data-collapsed="true">

b. There is one **user\_id** repeated in **df2**. What is it?

</div>

<div class="cell code" data-execution_count="14">

``` python
df2[df2.user_id.duplicated()]['user_id']
```

<div class="output execute_result" data-execution_count="14">

    2893    773192
    Name: user_id, dtype: int64

</div>

</div>

<div class="cell markdown">

c. What is the row information for the repeat **user\_id**?

</div>

<div class="cell code" data-execution_count="15">

``` python
df2[df2.user_id.duplicated()]
```

<div class="output execute_result" data-execution_count="15">

``` 
      user_id                   timestamp      group landing_page  converted
2893   773192  2017-01-14 02:55:59.590927  treatment     new_page          0
```

</div>

</div>

<div class="cell markdown">

d. Remove **one** of the rows with a duplicate **user\_id**, but keep
your dataframe as **df2**.

</div>

<div class="cell code" data-execution_count="16">

``` python
df2 = df2.drop(df2[df2.duplicated(['user_id'])].index) 
```

</div>

<div class="cell code" data-execution_count="17">

``` python
#check
df2.user_id.value_counts()
```

<div class="output execute_result" data-execution_count="17">

    630836    1
    634271    1
    753093    1
    751044    1
    740803    1
             ..
    805596    1
    803549    1
    809694    1
    807647    1
    630732    1
    Name: user_id, Length: 290584, dtype: int64

</div>

</div>

<div class="cell markdown">

`4.` Use **df2** in the below cells to answer the quiz questions related
to **Quiz 4** in the classroom.

a. What is the probability of an individual converting regardless of the
page they receive?

</div>

<div class="cell code" data-execution_count="18">

``` python
df2[df2['converted']==1].count()[0]/df2.shape[0]
```

<div class="output execute_result" data-execution_count="18">

    0.11959708724499628

</div>

</div>

<div class="cell markdown">

b. Given that an individual was in the `control` group, what is the
probability they converted?

</div>

<div class="cell code" data-execution_count="19">

``` python
df2[ (df2['converted']==1) & (df2['group']=='control') ].count()[0]/df2[df2['group']=='control'].count()[0]
```

<div class="output execute_result" data-execution_count="19">

    0.1203863045004612

</div>

</div>

<div class="cell markdown">

c. Given that an individual was in the `treatment` group, what is the
probability they converted?

</div>

<div class="cell code" data-execution_count="20">

``` python
df2[ (df2['converted']==1) & (df2['group']=='treatment') ].count()[0]/df2[df2['group']=='treatment'].count()[0]
```

<div class="output execute_result" data-execution_count="20">

    0.11880806551510564

</div>

</div>

<div class="cell markdown">

d. What is the probability that an individual received the new page?

</div>

<div class="cell code" data-execution_count="21">

``` python
df2[df2['landing_page']=='new_page'].count()[0]/df2.shape[0]
```

<div class="output execute_result" data-execution_count="21">

    0.5000619442226688

</div>

</div>

<div class="cell markdown">

e. Consider your results from a. through d. above, and explain below
whether you think there is sufficient evidence to say that the new
treatment page leads to more conversions.-

</div>

<div class="cell markdown">

***since p(converting | treatment) = 0.11880806551510564 and
p(converting | control) = 0.1203863045004612 "nearly the same value"
so,further investigation is needed to draw conclusion.***

</div>

<div class="cell markdown">

<a id='ab_test'></a>

### Part II - A/B Test

Notice that because of the time stamp associated with each event, you
could technically run a hypothesis test continuously as each observation
was observed.

However, then the hard question is do you stop as soon as one page is
considered significantly better than another or does it need to happen
consistently for a certain amount of time? How long do you run to render
a decision that neither page is better than another?

These questions are the difficult parts associated with A/B tests in
general.

`1.` For now, consider you need to make the decision just based on all
the data provided. If you want to assume that the old page is better
unless the new page proves to be definitely better at a Type I error
rate of 5%, what should your null and alternative hypotheses be? You can
state your hypothesis in terms of words or in terms of **\(p_{old}\)**
and **\(p_{new}\)**, **which are the converted rates for the old and new
pages**.

</div>

<div class="cell markdown">

***null hyposis H0 : P*new\* - P*old* \<=0 **\*

***Alternative hyposis H1 : P*new\* - P*old* \> 0 **\*

</div>

<div class="cell markdown">

`2.` Assume under the null hypothesis, \(p_{new}\) and \(p_{old}\) both
have "true" success rates equal to the **converted** success rate
regardless of page - that is \(p_{new}\) and \(p_{old}\) are equal.
Furthermore, assume they are equal to the **converted** rate in
**ab\_data.csv** regardless of the page. <br><br>

Use a sample size for each page equal to the ones in **ab\_data.csv**.
<br><br>

Perform the sampling distribution for the difference in **converted**
between the two pages over 10,000 iterations of calculating an estimate
from the null. <br><br>

Use the cells below to provide the necessary parts of this simulation.
If this doesn't make complete sense right now, don't worry - you are
going to work through the problems below to complete this problem. You
can use **Quiz 5** in the classroom to make sure you are on the right
track.<br><br>

</div>

<div class="cell markdown">

a. What is the **convert rate** for \(p_{new}\) under the null?

</div>

<div class="cell code" data-execution_count="22">

``` python
p_new = df2[df2['converted']==1].converted.shape[0]/df2.shape[0]
p_new
```

<div class="output execute_result" data-execution_count="22">

    0.11959708724499628

</div>

</div>

<div class="cell markdown">

b. What is the **convert rate** for \(p_{old}\) under the null? <br><br>

</div>

<div class="cell code" data-execution_count="23">

``` python
p_old = p_new
p_old
```

<div class="output execute_result" data-execution_count="23">

    0.11959708724499628

</div>

</div>

<div class="cell markdown">

c. What is \(n_{new}\)?

</div>

<div class="cell code" data-execution_count="24">

``` python
n_new = df2[df2['group']=='treatment'].group.shape[0]
n_new
```

<div class="output execute_result" data-execution_count="24">

    145310

</div>

</div>

<div class="cell markdown">

d. What is \(n_{old}\)?

</div>

<div class="cell code" data-execution_count="25">

``` python
n_old = df2[df2['group']=='control'].group.shape[0]
n_old
```

<div class="output execute_result" data-execution_count="25">

    145274

</div>

</div>

<div class="cell markdown">

e. Simulate \(n_{new}\) transactions with a convert rate of \(p_{new}\)
under the null. Store these \(n_{new}\) 1's and 0's in
**new\_page\_converted**.

</div>

<div class="cell code" data-execution_count="26">

``` python
new_page_converted = np.random.choice([0, 1], n_new, p = [p_new, 1-p_new])
new_page_converted
```

<div class="output execute_result" data-execution_count="26">

    array([1, 0, 1, ..., 1, 1, 1])

</div>

</div>

<div class="cell markdown">

f. Simulate \(n_{old}\) transactions with a convert rate of \(p_{old}\)
under the null. Store these \(n_{old}\) 1's and 0's in
**old\_page\_converted**.

</div>

<div class="cell code" data-execution_count="27">

``` python
old_page_converted = np.random.choice([0, 1], n_old, p = [p_old, 1-p_old])
old_page_converted
```

<div class="output execute_result" data-execution_count="27">

    array([1, 0, 1, ..., 1, 1, 1])

</div>

</div>

<div class="cell markdown">

g. Find \(p_{new}\) - \(p_{old}\) for your simulated values from part
(e) and (f).

</div>

<div class="cell code" data-execution_count="28">

``` python
new_page_converted.mean() - old_page_converted.mean()
```

<div class="output execute_result" data-execution_count="28">

    0.0018189396286096837

</div>

</div>

<div class="cell markdown">

h. Simulate 10,000 \(p_{new}\) - \(p_{old}\) values using this same
process similarly to the one you calculated in parts **a. through g.**
above. Store all 10,000 values in a numpy array called **p\_diffs**.

</div>

<div class="cell code" data-execution_count="29">

``` python
p_diffs = []
for _ in range(10000):
    
    new_page_converted = np.random.choice([1, 0],size=n_new,p=[p_new, 1-p_new])
    old_page_converted = np.random.choice([1, 0],size=n_old,p=[p_old, 1-p_new])
    diff = new_page_converted.mean() - old_page_converted.mean()
    p_diffs.append(diff)
    

```

</div>

<div class="cell markdown">

i. Plot a histogram of the **p\_diffs**. Does this plot look like what
you expected? Use the matching problem in the classroom to assure you
fully understand what was computed here.

</div>

<div class="cell code" data-execution_count="30">

``` python
plt.hist(p_diffs);

```

<div class="output display_data">

![](01b6daca15b711ee22d1180398b62601691fb224.png)

</div>

</div>

<div class="cell markdown">

its a normal distribution plot,so it meets my expectations

</div>

<div class="cell markdown">

j. What proportion of the **p\_diffs** are greater than the actual
difference observed in **ab\_data.csv**?

</div>

<div class="cell code" data-execution_count="31">

``` python
ac_diff = df2[(df2['converted']==1) & (df2['group']=='treatment')].shape[0] / df2[df2['group']=='treatment'].shape[0]- df2[(df2['converted']==1) & (df2['group']=='control')].shape[0] / df2[df2['group']=='control'].shape[0]
ac_diff
```

<div class="output execute_result" data-execution_count="31">

    -0.0015782389853555567

</div>

</div>

<div class="cell code" data-execution_count="32">

``` python
#converting the p_diffs array for faster and proper computation 
p_diffs = np.array(p_diffs)
```

</div>

<div class="cell code" data-execution_count="33">

``` python
#computing the proportion of the p_diffs are greater than the actual difference observed 
(p_diffs > ac_diff).mean()
```

<div class="output execute_result" data-execution_count="33">

    0.9012

</div>

</div>

<div class="cell markdown">

k. In words, explain what you just computed in part **j.** What is this
value called in scientific studies? What does this value mean in terms
of whether or not there is a difference between the new and old pages?

</div>

<div class="cell markdown">

in J. we had computed " p-value" , this value means we fail to reject
the null hypothesis since the p\_vlue is very large "91%"

</div>

<div class="cell markdown">

l. We could also use a built-in to achieve similar results. Though using
the built-in might be easier to code, the above portions are a
walkthrough of the ideas that are critical to correctly thinking about
statistical significance. Fill in the below to calculate the number of
conversions for each page, as well as the number of individuals who
received each page. Let `n_old` and `n_new` refer the the number of rows
associated with the old page and new pages, respectively.

</div>

<div class="cell code" data-execution_count="34">

``` python
import statsmodels.api as sm

convert_old = df2[(df2['converted']==1) & (df2['group']=='control')].shape[0]
convert_new = df2[(df2['converted']==1) & (df2['group']=='treatment')].shape[0]
n_old = df2[(df2['group']=='control')].shape[0]
n_new = df2[(df2['group']=='treatment')].shape[0]
```

</div>

<div class="cell markdown">

m. Now use `stats.proportions_ztest` to compute your test statistic and
p-value.
[Here](http://knowledgetack.com/python/statsmodels/proportions_ztest/)
is a helpful link on using the built in.

</div>

<div class="cell code" data-execution_count="35">

``` python
z_score,p_value= sm.stats.proportions_ztest([convert_new, convert_old],[n_new, n_old], alternative = 'larger')
print('z_score={}\np_value={}'.format(z_score,p_value))
```

<div class="output stream stdout">

    z_score=-1.3109241984234394
    p_value=0.9050583127590245

</div>

</div>

<div class="cell markdown">

n. What do the z-score and p-value you computed in the previous question
mean for the conversion rates of the old and new pages? Do they agree
with the findings in parts **j.** and **k.**?

</div>

<div class="cell markdown">

***since critical value"1.64" \> z-score "-1.3109241984234394" so,we
fail to reject H0. so, we get the same conclusions***

</div>

<div class="cell markdown">

<a id='regression'></a>

### Part III - A regression approach

`1.` In this final part, you will see that the result you acheived in
the previous A/B test can also be acheived by performing
regression.<br><br>

a. Since each row is either a conversion or no conversion, what type of
regression should you be performing in this case?

</div>

<div class="cell markdown">

**logistic regression (since they are binary values).**

</div>

<div class="cell markdown">

b. The goal is to use **statsmodels** to fit the regression model you
specified in part **a.** to see if there is a significant difference in
conversion based on which page a customer receives. However, you first
need to create a column for the intercept, and create a dummy variable
column for which page each user received. Add an **intercept** column,
as well as an **ab\_page** column, which is 1 when an individual
receives the **treatment** and 0 if **control**.

</div>

<div class="cell code" data-execution_count="36">

``` python
# ab_page column addition
df2['ab_page'] = pd.get_dummies(df2['group'])['treatment']

# intercept column addition
df2['intercept'] = 1
```

</div>

<div class="cell code" data-execution_count="37">

``` python
#check columns addition 
df2.sample(8)
```

<div class="output execute_result" data-execution_count="37">

``` 
        user_id                   timestamp      group landing_page  \
160256   706616  2017-01-19 13:26:30.409177    control     old_page   
200891   786715  2017-01-17 01:31:39.628299    control     old_page   
89882    944274  2017-01-09 22:10:50.464224  treatment     new_page   
246872   820360  2017-01-16 08:14:39.298259  treatment     new_page   
281136   937820  2017-01-03 04:06:31.178577    control     old_page   
202992   743854  2017-01-14 11:37:40.685232    control     old_page   
19479    807274  2017-01-20 15:07:40.714388    control     old_page   
288694   730919  2017-01-22 05:39:13.915103  treatment     new_page   

        converted  ab_page  intercept  
160256          0        0          1  
200891          0        0          1  
89882           0        1          1  
246872          0        1          1  
281136          1        0          1  
202992          0        0          1  
19479           0        0          1  
288694          1        1          1  
```

</div>

</div>

<div class="cell markdown">

c. Use **statsmodels** to import your regression model. Instantiate the
model, and fit the model using the two columns you created in part
**b.** to predict whether or not an individual converts.

</div>

<div class="cell code" data-execution_count="38">

``` python
import statsmodels.api as sm


logitistic = sm.Logit(df2['converted'],df2[['intercept', 'ab_page']])
results=logitistic.fit()
```

<div class="output stream stdout">

    Optimization terminated successfully.
             Current function value: 0.366118
             Iterations 6

</div>

</div>

<div class="cell markdown">

d. Provide the summary of your model below, and use it as necessary to
answer the following questions.

</div>

<div class="cell code" data-execution_count="39">

``` python
results.summary()
```

<div class="output execute_result" data-execution_count="39">

    <class 'statsmodels.iolib.summary.Summary'>
    """
                               Logit Regression Results                           
    ==============================================================================
    Dep. Variable:              converted   No. Observations:               290584
    Model:                          Logit   Df Residuals:                   290582
    Method:                           MLE   Df Model:                            1
    Date:                Thu, 16 Dec 2021   Pseudo R-squ.:               8.077e-06
    Time:                        22:23:04   Log-Likelihood:            -1.0639e+05
    converged:                       True   LL-Null:                   -1.0639e+05
    Covariance Type:            nonrobust   LLR p-value:                    0.1899
    ==============================================================================
                     coef    std err          z      P>|z|      [0.025      0.975]
    ------------------------------------------------------------------------------
    intercept     -1.9888      0.008   -246.669      0.000      -2.005      -1.973
    ab_page       -0.0150      0.011     -1.311      0.190      -0.037       0.007
    ==============================================================================
    """

</div>

</div>

<div class="cell markdown">

e. What is the p-value associated with **ab\_page**? Why does it differ
from the value you found in **Part II**?<br><br> **Hint**: What are the
null and alternative hypotheses associated with your regression model,
and how do they compare to the null and alternative hypotheses in the
**Part II**?

</div>

<div class="cell markdown">

part III: ***null hyposis H0 : P*new\* - P*old* =0**\*

***Alternative hyposis H1 : P*new\* - P*old* \!= 0 **\*

//////////////////////////////////////////////////////////////

Part II : ***null hyposis H0 : P*new\* - P*old* \<=0 **\*

***Alternative hyposis H1 : P*new\* - P*old* \> 0 **\*

we can get from the summary that p\_value for ab\_page is "0.190" which
is different from the one in part 2 because of the difference in
hypothesis

</div>

<div class="cell markdown">

f. Now, you are considering other things that might influence whether or
not an individual converts. Discuss why it is a good idea to consider
other factors to add into your regression model. Are there any
disadvantages to adding additional terms into your regression model?

</div>

<div class="cell markdown">

***inculding other fators is a good idea because other factors like
"age" may help us more to get more accurate results the disadvantages of
adding additional terms is that we do not know how the factors are
related "or suitable" for the regression model in general***

</div>

<div class="cell markdown">

g. Now along with testing if the conversion rate changes for different
pages, also add an effect based on which country a user lives. You will
need to read in the **countries.csv** dataset and merge together your
datasets on the approporiate rows.
[Here](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.join.html)
are the docs for joining tables.

Does it appear that country had an impact on conversion? Don't forget to
create dummy variables for these country columns - **Hint: You will need
two columns for the three dummy variables.** Provide the statistical
output as well as a written response to answer this question.

</div>

<div class="cell code" data-execution_count="40">

``` python
countries_df = pd.read_csv('countries.csv')
df_new = countries_df.set_index('user_id').join(df2.set_index('user_id'), how='inner')
```

</div>

<div class="cell code" data-execution_count="41">

``` python
df_new.sample(8)
```

<div class="output execute_result" data-execution_count="41">

``` 
        country                   timestamp      group landing_page  \
user_id                                                               
797742       US  2017-01-05 15:42:38.613057  treatment     new_page   
715967       US  2017-01-05 15:25:10.061717    control     old_page   
812548       US  2017-01-23 06:59:51.663054  treatment     new_page   
801287       US  2017-01-07 10:31:17.714388    control     old_page   
770975       UK  2017-01-03 21:32:46.932833    control     old_page   
759713       US  2017-01-12 04:26:46.499785  treatment     new_page   
635592       US  2017-01-12 04:35:50.620058  treatment     new_page   
803965       CA  2017-01-11 22:38:22.410112    control     old_page   

         converted  ab_page  intercept  
user_id                                 
797742           0        1          1  
715967           0        0          1  
812548           0        1          1  
801287           0        0          1  
770975           0        0          1  
759713           0        1          1  
635592           0        1          1  
803965           0        0          1  
```

</div>

</div>

<div class="cell code" data-execution_count="42">

``` python
### Create the necessary dummy variables
df_new[['US', 'CA', 'UK']] = pd.get_dummies(df_new['country'])
```

</div>

<div class="cell markdown">

h. Though you have now looked at the individual factors of country and
page on conversion, we would now like to look at an interaction between
page and country to see if there significant effects on conversion.
Create the necessary additional columns, and fit the new model.

Provide the summary results, and your conclusions based on the results.

</div>

<div class="cell code" data-execution_count="43">

``` python
df_new['UK_X_ab_page'] = df_new['UK'] * df_new['ab_page']
df_new['CA_X_ab_page'] = df_new['CA'] * df_new['ab_page']
```

</div>

<div class="cell code" data-execution_count="44">

``` python
df_new.sample(6)
```

<div class="output execute_result" data-execution_count="44">

``` 
        country                   timestamp      group landing_page  \
user_id                                                               
781013       US  2017-01-03 19:15:24.563386  treatment     new_page   
701172       US  2017-01-06 11:00:22.875280  treatment     new_page   
709390       US  2017-01-11 17:47:40.228456    control     old_page   
869664       US  2017-01-13 18:56:41.851759  treatment     new_page   
776085       US  2017-01-18 12:37:17.567281  treatment     new_page   
844455       US  2017-01-22 16:49:28.026070    control     old_page   

         converted  ab_page  intercept  US  CA  UK  UK_X_ab_page  CA_X_ab_page  
user_id                                                                         
781013           0        1          1   0   0   1             1             0  
701172           0        1          1   0   0   1             1             0  
709390           0        0          1   0   0   1             0             0  
869664           0        1          1   0   0   1             1             0  
776085           0        1          1   0   0   1             1             0  
844455           0        0          1   0   0   1             0             0  
```

</div>

</div>

<div class="cell code" data-execution_count="45">

``` python
### Fit Your Linear Model And Obtain the Results
df_new['intercept'] = 1
logit_mod = sm.Logit(df_new['converted'], df_new[['intercept', 'ab_page', 'UK', 'CA','UK_X_ab_page','CA_X_ab_page']])
results = logit_mod.fit()
```

<div class="output stream stdout">

    Optimization terminated successfully.
             Current function value: 0.366109
             Iterations 6

</div>

</div>

<div class="cell code" data-execution_count="46">

``` python
###to get the summary results
results.summary()
```

<div class="output execute_result" data-execution_count="46">

    <class 'statsmodels.iolib.summary.Summary'>
    """
                               Logit Regression Results                           
    ==============================================================================
    Dep. Variable:              converted   No. Observations:               290584
    Model:                          Logit   Df Residuals:                   290578
    Method:                           MLE   Df Model:                            5
    Date:                Thu, 16 Dec 2021   Pseudo R-squ.:               3.482e-05
    Time:                        22:23:07   Log-Likelihood:            -1.0639e+05
    converged:                       True   LL-Null:                   -1.0639e+05
    Covariance Type:            nonrobust   LLR p-value:                    0.1920
    ================================================================================
                       coef    std err          z      P>|z|      [0.025      0.975]
    --------------------------------------------------------------------------------
    intercept       -2.0040      0.036    -55.008      0.000      -2.075      -1.933
    ab_page         -0.0674      0.052     -1.297      0.195      -0.169       0.034
    UK               0.0175      0.038      0.465      0.642      -0.056       0.091
    CA               0.0118      0.040      0.296      0.767      -0.066       0.090
    UK_X_ab_page     0.0469      0.054      0.872      0.383      -0.059       0.152
    CA_X_ab_page     0.0783      0.057      1.378      0.168      -0.033       0.190
    ================================================================================
    """

</div>

</div>

<div class="cell markdown">

<a id='conclusions'></a>

## Conclusions

according to p\_values we get , we conclude that there is no relation
between countery & page "we fail to reget null hypothesis"

based on the given information my opnion for the company is to use the
old version of the page.


</div>

<div class="cell code">

``` python
```

</div>
