[READMED.md](https://github.com/EbraamMaher/Analyze-A-B-Test-Results-Udacity/files/10067934/READMED.md)
# Analyze-A-B-Test-Results-Udacity

{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Analyze A/B Test Results\n",
    "\n",
    "This project will assure you have mastered the subjects covered in the statistics lessons.  The hope is to have this project be as comprehensive of these topics as possible.  Good luck!\n",
    "\n",
    "## Table of Contents\n",
    "- [Introduction](#intro)\n",
    "- [Part I - Probability](#probability)\n",
    "- [Part II - A/B Test](#ab_test)\n",
    "- [Part III - Regression](#regression)\n",
    "\n",
    "\n",
    "<a id='intro'></a>\n",
    "### Introduction\n",
    "\n",
    "A/B tests are very commonly performed by data analysts and data scientists.  It is important that you get some practice working with the difficulties of these \n",
    "\n",
    "For this project, you will be working to understand the results of an A/B test run by an e-commerce website.  Your goal is to work through this notebook to help the company understand if they should implement the new page, keep the old page, or perhaps run the experiment longer to make their decision.\n",
    "\n",
    "**As you work through this notebook, follow along in the classroom and answer the corresponding quiz questions associated with each question.** The labels for each classroom concept are provided for each question.  This will assure you are on the right track as you work through the project, and you can feel more confident in your final submission meeting the criteria.  As a final check, assure you meet all the criteria on the [RUBRIC](https://review.udacity.com/#!/projects/37e27304-ad47-4eb0-a1ab-8c12f60e43d0/rubric).\n",
    "\n",
    "<a id='probability'></a>\n",
    "#### Part I - Probability\n",
    "\n",
    "To get started, let's import our libraries."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {},
   "outputs": [],
   "source": [
    "import pandas as pd\n",
    "import numpy as np\n",
    "import random\n",
    "import matplotlib.pyplot as plt\n",
    "%matplotlib inline\n",
    "#We are setting the seed to assure you get the same answers on quizzes as we set up\n",
    "random.seed(42)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "`1.` Now, read in the `ab_data.csv` data. Store it in `df`.  **Use your dataframe to answer the questions in Quiz 1 of the classroom.**\n",
    "\n",
    "a. Read in the dataset and take a look at the top few rows here:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {},
   "outputs": [],
   "source": [
    "ab_data = pd.read_csv(\"ab_data.csv\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>user_id</th>\n",
       "      <th>timestamp</th>\n",
       "      <th>group</th>\n",
       "      <th>landing_page</th>\n",
       "      <th>converted</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>851104</td>\n",
       "      <td>2017-01-21 22:11:48.556739</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>804228</td>\n",
       "      <td>2017-01-12 08:01:45.159739</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>661590</td>\n",
       "      <td>2017-01-11 16:55:06.154213</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>853541</td>\n",
       "      <td>2017-01-08 18:28:03.143765</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>864975</td>\n",
       "      <td>2017-01-21 01:52:26.210827</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   user_id                   timestamp      group landing_page  converted\n",
       "0   851104  2017-01-21 22:11:48.556739    control     old_page          0\n",
       "1   804228  2017-01-12 08:01:45.159739    control     old_page          0\n",
       "2   661590  2017-01-11 16:55:06.154213  treatment     new_page          0\n",
       "3   853541  2017-01-08 18:28:03.143765  treatment     new_page          0\n",
       "4   864975  2017-01-21 01:52:26.210827    control     old_page          1"
      ]
     },
     "execution_count": 3,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "ab_data.head()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "<class 'pandas.core.frame.DataFrame'>\n",
      "RangeIndex: 294478 entries, 0 to 294477\n",
      "Data columns (total 5 columns):\n",
      " #   Column        Non-Null Count   Dtype \n",
      "---  ------        --------------   ----- \n",
      " 0   user_id       294478 non-null  int64 \n",
      " 1   timestamp     294478 non-null  object\n",
      " 2   group         294478 non-null  object\n",
      " 3   landing_page  294478 non-null  object\n",
      " 4   converted     294478 non-null  int64 \n",
      "dtypes: int64(2), object(3)\n",
      "memory usage: 11.2+ MB\n"
     ]
    }
   ],
   "source": [
    "ab_data.info()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "b. Use the below cell to find the number of rows in the dataset."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "294478"
      ]
     },
     "execution_count": 5,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "ab_data.shape[0]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "c. The number of unique users in the dataset."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "290584"
      ]
     },
     "execution_count": 6,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#ab_data.head()\n",
    "ab_data.nunique()[0]\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "collapsed": true
   },
   "source": [
    "d. The proportion of users converted."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.11965919355605512"
      ]
     },
     "execution_count": 7,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "ab_data.converted.sum()/ab_data.shape[0]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "collapsed": true
   },
   "source": [
    "e. The number of times the `new_page` and `treatment` don't line up."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "1965"
      ]
     },
     "execution_count": 8,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#ab_data[(ab_data['landing_page']=='new_page') & (ab_data[ab_data['group']=='treatment'])]\n",
    "\n",
    "#ab_data.groupby(['landing_page']).count().group\n",
    "#ab_data.groupby(['group']).count()\n",
    "#ab_data[(ab_data['landing_page']=='new_page')]\n",
    "#(ab_data[ab_data['group']=='treatment'])   \n",
    "((ab_data.landing_page == 'old_page') & (ab_data.group == 'treatment')).sum()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "f. Do any of the rows have missing values?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "user_id         0\n",
       "timestamp       0\n",
       "group           0\n",
       "landing_page    0\n",
       "converted       0\n",
       "dtype: int64"
      ]
     },
     "execution_count": 9,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "ab_data[ab_data.isnull().any(axis=1)].count()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "`2.` For the rows where **treatment** is not aligned with **new_page** or **control** is not aligned with **old_page**, we cannot be sure if this row truly received the new or old page.  Use **Quiz 2** in the classroom to provide how we should handle these rows.  \n",
    "\n",
    "a. Now use the answer to the quiz to create a new dataset that meets the specifications from the quiz.  Store your new dataframe in **df2**."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "metadata": {},
   "outputs": [],
   "source": [
    "\n",
    "df2=ab_data[((ab_data.landing_page=='new_page') & (ab_data.group=='treatment'))| ((ab_data.landing_page=='old_page') & (ab_data.group=='control'))]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "<class 'pandas.core.frame.DataFrame'>\n",
      "Int64Index: 290585 entries, 0 to 294477\n",
      "Data columns (total 5 columns):\n",
      " #   Column        Non-Null Count   Dtype \n",
      "---  ------        --------------   ----- \n",
      " 0   user_id       290585 non-null  int64 \n",
      " 1   timestamp     290585 non-null  object\n",
      " 2   group         290585 non-null  object\n",
      " 3   landing_page  290585 non-null  object\n",
      " 4   converted     290585 non-null  int64 \n",
      "dtypes: int64(2), object(3)\n",
      "memory usage: 13.3+ MB\n"
     ]
    }
   ],
   "source": [
    "df2.info()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0"
      ]
     },
     "execution_count": 12,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Double Check all of the correct rows were removed - this should be 0\n",
    "df2[((df2['group'] == 'treatment') == (df2['landing_page'] == 'new_page')) == False].shape[0]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "`3.` Use **df2** and the cells below to answer questions for **Quiz3** in the classroom."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "a. How many unique **user_id**s are in **df2**?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "290584"
      ]
     },
     "execution_count": 13,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df2.user_id.nunique()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "collapsed": true
   },
   "source": [
    "b. There is one **user_id** repeated in **df2**.  What is it?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "2893    773192\n",
       "Name: user_id, dtype: int64"
      ]
     },
     "execution_count": 14,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df2[df2.user_id.duplicated()]['user_id']"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "c. What is the row information for the repeat **user_id**? "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>user_id</th>\n",
       "      <th>timestamp</th>\n",
       "      <th>group</th>\n",
       "      <th>landing_page</th>\n",
       "      <th>converted</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>2893</th>\n",
       "      <td>773192</td>\n",
       "      <td>2017-01-14 02:55:59.590927</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "      user_id                   timestamp      group landing_page  converted\n",
       "2893   773192  2017-01-14 02:55:59.590927  treatment     new_page          0"
      ]
     },
     "execution_count": 15,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df2[df2.user_id.duplicated()]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "d. Remove **one** of the rows with a duplicate **user_id**, but keep your dataframe as **df2**."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "metadata": {},
   "outputs": [],
   "source": [
    "df2 = df2.drop(df2[df2.duplicated(['user_id'])].index) "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "630836    1\n",
       "634271    1\n",
       "753093    1\n",
       "751044    1\n",
       "740803    1\n",
       "         ..\n",
       "805596    1\n",
       "803549    1\n",
       "809694    1\n",
       "807647    1\n",
       "630732    1\n",
       "Name: user_id, Length: 290584, dtype: int64"
      ]
     },
     "execution_count": 17,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#check\n",
    "df2.user_id.value_counts()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "`4.` Use **df2** in the below cells to answer the quiz questions related to **Quiz 4** in the classroom.\n",
    "\n",
    "a. What is the probability of an individual converting regardless of the page they receive?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 18,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.11959708724499628"
      ]
     },
     "execution_count": 18,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df2[df2['converted']==1].count()[0]/df2.shape[0]\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "b. Given that an individual was in the `control` group, what is the probability they converted?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 19,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.1203863045004612"
      ]
     },
     "execution_count": 19,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df2[ (df2['converted']==1) & (df2['group']=='control') ].count()[0]/df2[df2['group']=='control'].count()[0]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "c. Given that an individual was in the `treatment` group, what is the probability they converted?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 20,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.11880806551510564"
      ]
     },
     "execution_count": 20,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df2[ (df2['converted']==1) & (df2['group']=='treatment') ].count()[0]/df2[df2['group']=='treatment'].count()[0]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "d. What is the probability that an individual received the new page?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 21,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.5000619442226688"
      ]
     },
     "execution_count": 21,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df2[df2['landing_page']=='new_page'].count()[0]/df2.shape[0]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "e. Consider your results from a. through d. above, and explain below whether you think there is sufficient evidence to say that the new treatment page leads to more conversions.-"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "***since p(converting | treatment) = 0.11880806551510564 and p(converting | control) = 0.1203863045004612 \"nearly the same value\"\n",
    "so,further investigation is needed to draw conclusion.***\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "<a id='ab_test'></a>\n",
    "### Part II - A/B Test\n",
    "\n",
    "Notice that because of the time stamp associated with each event, you could technically run a hypothesis test continuously as each observation was observed.  \n",
    "\n",
    "However, then the hard question is do you stop as soon as one page is considered significantly better than another or does it need to happen consistently for a certain amount of time?  How long do you run to render a decision that neither page is better than another?  \n",
    "\n",
    "These questions are the difficult parts associated with A/B tests in general.  \n",
    "\n",
    "\n",
    "`1.` For now, consider you need to make the decision just based on all the data provided.  If you want to assume that the old page is better unless the new page proves to be definitely better at a Type I error rate of 5%, what should your null and alternative hypotheses be?  You can state your hypothesis in terms of words or in terms of **$p_{old}$** and **$p_{new}$**, **which are the converted rates for the old and new pages**."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "***null    hyposis     H0 : P*new*  - P*old*  <=0 ***\n",
    "\n",
    "***Alternative hyposis H1 : P*new*  - P*old*  > 0 ***"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "`2.` Assume under the null hypothesis, $p_{new}$ and $p_{old}$ both have \"true\" success rates equal to the **converted** success rate regardless of page - that is $p_{new}$ and $p_{old}$ are equal. Furthermore, assume they are equal to the **converted** rate in **ab_data.csv** regardless of the page. <br><br>\n",
    "\n",
    "Use a sample size for each page equal to the ones in **ab_data.csv**.  <br><br>\n",
    "\n",
    "Perform the sampling distribution for the difference in **converted** between the two pages over 10,000 iterations of calculating an estimate from the null.  <br><br>\n",
    "\n",
    "Use the cells below to provide the necessary parts of this simulation.  If this doesn't make complete sense right now, don't worry - you are going to work through the problems below to complete this problem.  You can use **Quiz 5** in the classroom to make sure you are on the right track.<br><br>"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "a. What is the **convert rate** for $p_{new}$ under the null? "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 22,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.11959708724499628"
      ]
     },
     "execution_count": 22,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "p_new = df2[df2['converted']==1].converted.shape[0]/df2.shape[0]\n",
    "p_new"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "b. What is the **convert rate** for $p_{old}$ under the null? <br><br>"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 23,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.11959708724499628"
      ]
     },
     "execution_count": 23,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "p_old = p_new\n",
    "p_old"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "c. What is $n_{new}$?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 24,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "145310"
      ]
     },
     "execution_count": 24,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "n_new = df2[df2['group']=='treatment'].group.shape[0]\n",
    "n_new"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "d. What is $n_{old}$?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 25,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "145274"
      ]
     },
     "execution_count": 25,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "n_old = df2[df2['group']=='control'].group.shape[0]\n",
    "n_old"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "e. Simulate $n_{new}$ transactions with a convert rate of $p_{new}$ under the null.  Store these $n_{new}$ 1's and 0's in **new_page_converted**."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 26,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([1, 0, 1, ..., 1, 1, 1])"
      ]
     },
     "execution_count": 26,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "new_page_converted = np.random.choice([0, 1], n_new, p = [p_new, 1-p_new])\n",
    "new_page_converted"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "f. Simulate $n_{old}$ transactions with a convert rate of $p_{old}$ under the null.  Store these $n_{old}$ 1's and 0's in **old_page_converted**."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 27,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([1, 0, 1, ..., 1, 1, 1])"
      ]
     },
     "execution_count": 27,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "old_page_converted = np.random.choice([0, 1], n_old, p = [p_old, 1-p_old])\n",
    "old_page_converted"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "g. Find $p_{new}$ - $p_{old}$ for your simulated values from part (e) and (f)."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 28,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.0018189396286096837"
      ]
     },
     "execution_count": 28,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "new_page_converted.mean() - old_page_converted.mean()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "h. Simulate 10,000 $p_{new}$ - $p_{old}$ values using this same process similarly to the one you calculated in parts **a. through g.** above.  Store all 10,000 values in a numpy array called **p_diffs**."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 29,
   "metadata": {},
   "outputs": [],
   "source": [
    "p_diffs = []\n",
    "for _ in range(10000):\n",
    "    \n",
    "    new_page_converted = np.random.choice([1, 0],size=n_new,p=[p_new, 1-p_new])\n",
    "    old_page_converted = np.random.choice([1, 0],size=n_old,p=[p_old, 1-p_new])\n",
    "    diff = new_page_converted.mean() - old_page_converted.mean()\n",
    "    p_diffs.append(diff)\n",
    "    \n",
    "\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "i. Plot a histogram of the **p_diffs**.  Does this plot look like what you expected?  Use the matching problem in the classroom to assure you fully understand what was computed here."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 30,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.hist(p_diffs);\n",
    "\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "its a normal distribution plot,so it meets my expectations "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "j. What proportion of the **p_diffs** are greater than the actual difference observed in **ab_data.csv**?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 31,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "-0.0015782389853555567"
      ]
     },
     "execution_count": 31,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "ac_diff = df2[(df2['converted']==1) & (df2['group']=='treatment')].shape[0] / df2[df2['group']=='treatment'].shape[0]- df2[(df2['converted']==1) & (df2['group']=='control')].shape[0] / df2[df2['group']=='control'].shape[0]\n",
    "ac_diff"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 32,
   "metadata": {},
   "outputs": [],
   "source": [
    "#converting the p_diffs array for faster and proper computation \n",
    "p_diffs = np.array(p_diffs)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 33,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.9012"
      ]
     },
     "execution_count": 33,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#computing the proportion of the p_diffs are greater than the actual difference observed \n",
    "(p_diffs > ac_diff).mean()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "k. In words, explain what you just computed in part **j.**  What is this value called in scientific studies?  What does this value mean in terms of whether or not there is a difference between the new and old pages?"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "in J. we had computed \" p-value\" , this value means we fail to  reject the null hypothesis since the p_vlue is very large \"91%\""
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "l. We could also use a built-in to achieve similar results.  Though using the built-in might be easier to code, the above portions are a walkthrough of the ideas that are critical to correctly thinking about statistical significance. Fill in the below to calculate the number of conversions for each page, as well as the number of individuals who received each page. Let `n_old` and `n_new` refer the the number of rows associated with the old page and new pages, respectively."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 34,
   "metadata": {},
   "outputs": [],
   "source": [
    "import statsmodels.api as sm\n",
    "\n",
    "convert_old = df2[(df2['converted']==1) & (df2['group']=='control')].shape[0]\n",
    "convert_new = df2[(df2['converted']==1) & (df2['group']=='treatment')].shape[0]\n",
    "n_old = df2[(df2['group']=='control')].shape[0]\n",
    "n_new = df2[(df2['group']=='treatment')].shape[0]\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "m. Now use `stats.proportions_ztest` to compute your test statistic and p-value.  [Here](http://knowledgetack.com/python/statsmodels/proportions_ztest/) is a helpful link on using the built in."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 35,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "z_score=-1.3109241984234394\n",
      "p_value=0.9050583127590245\n"
     ]
    }
   ],
   "source": [
    "z_score,p_value= sm.stats.proportions_ztest([convert_new, convert_old],[n_new, n_old], alternative = 'larger')\n",
    "print('z_score={}\\np_value={}'.format(z_score,p_value))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "n. What do the z-score and p-value you computed in the previous question mean for the conversion rates of the old and new pages?  Do they agree with the findings in parts **j.** and **k.**?"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "***since critical value\"1.64\" >  z-score \"-1.3109241984234394\" so,we fail to reject H0.\n",
    "so, we get the same conclusions***"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "<a id='regression'></a>\n",
    "### Part III - A regression approach\n",
    "\n",
    "`1.` In this final part, you will see that the result you acheived in the previous A/B test can also be acheived by performing regression.<br><br>\n",
    "\n",
    "a. Since each row is either a conversion or no conversion, what type of regression should you be performing in this case?"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**logistic regression (since they are binary values).**"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "b. The goal is to use **statsmodels** to fit the regression model you specified in part **a.** to see if there is a significant difference in conversion based on which page a customer receives.  However, you first need to create a column for the intercept, and create a dummy variable column for which page each user received.  Add an **intercept** column, as well as an **ab_page** column, which is 1 when an individual receives the **treatment** and 0 if **control**."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 36,
   "metadata": {},
   "outputs": [],
   "source": [
    "# ab_page column addition\n",
    "df2['ab_page'] = pd.get_dummies(df2['group'])['treatment']\n",
    "\n",
    "# intercept column addition\n",
    "df2['intercept'] = 1"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 37,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>user_id</th>\n",
       "      <th>timestamp</th>\n",
       "      <th>group</th>\n",
       "      <th>landing_page</th>\n",
       "      <th>converted</th>\n",
       "      <th>ab_page</th>\n",
       "      <th>intercept</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>160256</th>\n",
       "      <td>706616</td>\n",
       "      <td>2017-01-19 13:26:30.409177</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>200891</th>\n",
       "      <td>786715</td>\n",
       "      <td>2017-01-17 01:31:39.628299</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>89882</th>\n",
       "      <td>944274</td>\n",
       "      <td>2017-01-09 22:10:50.464224</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>246872</th>\n",
       "      <td>820360</td>\n",
       "      <td>2017-01-16 08:14:39.298259</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>281136</th>\n",
       "      <td>937820</td>\n",
       "      <td>2017-01-03 04:06:31.178577</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>202992</th>\n",
       "      <td>743854</td>\n",
       "      <td>2017-01-14 11:37:40.685232</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>19479</th>\n",
       "      <td>807274</td>\n",
       "      <td>2017-01-20 15:07:40.714388</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>288694</th>\n",
       "      <td>730919</td>\n",
       "      <td>2017-01-22 05:39:13.915103</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "        user_id                   timestamp      group landing_page  \\\n",
       "160256   706616  2017-01-19 13:26:30.409177    control     old_page   \n",
       "200891   786715  2017-01-17 01:31:39.628299    control     old_page   \n",
       "89882    944274  2017-01-09 22:10:50.464224  treatment     new_page   \n",
       "246872   820360  2017-01-16 08:14:39.298259  treatment     new_page   \n",
       "281136   937820  2017-01-03 04:06:31.178577    control     old_page   \n",
       "202992   743854  2017-01-14 11:37:40.685232    control     old_page   \n",
       "19479    807274  2017-01-20 15:07:40.714388    control     old_page   \n",
       "288694   730919  2017-01-22 05:39:13.915103  treatment     new_page   \n",
       "\n",
       "        converted  ab_page  intercept  \n",
       "160256          0        0          1  \n",
       "200891          0        0          1  \n",
       "89882           0        1          1  \n",
       "246872          0        1          1  \n",
       "281136          1        0          1  \n",
       "202992          0        0          1  \n",
       "19479           0        0          1  \n",
       "288694          1        1          1  "
      ]
     },
     "execution_count": 37,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#check columns addition \n",
    "df2.sample(8)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "c. Use **statsmodels** to import your regression model.  Instantiate the model, and fit the model using the two columns you created in part **b.** to predict whether or not an individual converts."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 38,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.366118\n",
      "         Iterations 6\n"
     ]
    }
   ],
   "source": [
    "import statsmodels.api as sm\n",
    "\n",
    "\n",
    "logitistic = sm.Logit(df2['converted'],df2[['intercept', 'ab_page']])\n",
    "results=logitistic.fit()\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "d. Provide the summary of your model below, and use it as necessary to answer the following questions."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 39,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<table class=\"simpletable\">\n",
       "<caption>Logit Regression Results</caption>\n",
       "<tr>\n",
       "  <th>Dep. Variable:</th>       <td>converted</td>    <th>  No. Observations:  </th>   <td>290584</td>   \n",
       "</tr>\n",
       "<tr>\n",
       "  <th>Model:</th>                 <td>Logit</td>      <th>  Df Residuals:      </th>   <td>290582</td>   \n",
       "</tr>\n",
       "<tr>\n",
       "  <th>Method:</th>                 <td>MLE</td>       <th>  Df Model:          </th>   <td>     1</td>   \n",
       "</tr>\n",
       "<tr>\n",
       "  <th>Date:</th>            <td>Thu, 16 Dec 2021</td> <th>  Pseudo R-squ.:     </th>  <td>8.077e-06</td> \n",
       "</tr>\n",
       "<tr>\n",
       "  <th>Time:</th>                <td>22:23:04</td>     <th>  Log-Likelihood:    </th> <td>-1.0639e+05</td>\n",
       "</tr>\n",
       "<tr>\n",
       "  <th>converged:</th>             <td>True</td>       <th>  LL-Null:           </th> <td>-1.0639e+05</td>\n",
       "</tr>\n",
       "<tr>\n",
       "  <th>Covariance Type:</th>     <td>nonrobust</td>    <th>  LLR p-value:       </th>   <td>0.1899</td>   \n",
       "</tr>\n",
       "</table>\n",
       "<table class=\"simpletable\">\n",
       "<tr>\n",
       "      <td></td>         <th>coef</th>     <th>std err</th>      <th>z</th>      <th>P>|z|</th>  <th>[0.025</th>    <th>0.975]</th>  \n",
       "</tr>\n",
       "<tr>\n",
       "  <th>intercept</th> <td>   -1.9888</td> <td>    0.008</td> <td> -246.669</td> <td> 0.000</td> <td>   -2.005</td> <td>   -1.973</td>\n",
       "</tr>\n",
       "<tr>\n",
       "  <th>ab_page</th>   <td>   -0.0150</td> <td>    0.011</td> <td>   -1.311</td> <td> 0.190</td> <td>   -0.037</td> <td>    0.007</td>\n",
       "</tr>\n",
       "</table>"
      ],
      "text/plain": [
       "<class 'statsmodels.iolib.summary.Summary'>\n",
       "\"\"\"\n",
       "                           Logit Regression Results                           \n",
       "==============================================================================\n",
       "Dep. Variable:              converted   No. Observations:               290584\n",
       "Model:                          Logit   Df Residuals:                   290582\n",
       "Method:                           MLE   Df Model:                            1\n",
       "Date:                Thu, 16 Dec 2021   Pseudo R-squ.:               8.077e-06\n",
       "Time:                        22:23:04   Log-Likelihood:            -1.0639e+05\n",
       "converged:                       True   LL-Null:                   -1.0639e+05\n",
       "Covariance Type:            nonrobust   LLR p-value:                    0.1899\n",
       "==============================================================================\n",
       "                 coef    std err          z      P>|z|      [0.025      0.975]\n",
       "------------------------------------------------------------------------------\n",
       "intercept     -1.9888      0.008   -246.669      0.000      -2.005      -1.973\n",
       "ab_page       -0.0150      0.011     -1.311      0.190      -0.037       0.007\n",
       "==============================================================================\n",
       "\"\"\""
      ]
     },
     "execution_count": 39,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "results.summary()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "e. What is the p-value associated with **ab_page**? Why does it differ from the value you found in **Part II**?<br><br>  **Hint**: What are the null and alternative hypotheses associated with your regression model, and how do they compare to the null and alternative hypotheses in the **Part II**?"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "part III:\n",
    "***null    hyposis     H0 : P*new*  - P*old*  =0***\n",
    "\n",
    "***Alternative hyposis H1 : P*new*  - P*old*  != 0 ***\n",
    "\n",
    "//////////////////////////////////////////////////////////////\n",
    "\n",
    "Part II :\n",
    "***null    hyposis     H0 : P*new*  - P*old*  <=0 ***\n",
    "\n",
    "***Alternative hyposis H1 : P*new*  - P*old*  > 0 ***\n",
    "\n",
    "we can get from the summary that p_value for ab_page is \"0.190\" which is different from the one in part 2 because of the difference in hypothesis"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "f. Now, you are considering other things that might influence whether or not an individual converts.  Discuss why it is a good idea to consider other factors to add into your regression model.  Are there any disadvantages to adding additional terms into your regression model?"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "***inculding other fators is a good idea because other factors like \"age\" may help us more to get more accurate results\n",
    "the disadvantages of adding additional terms is that we do not know how the factors are related \"or suitable\" for the regression model in general***"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "g. Now along with testing if the conversion rate changes for different pages, also add an effect based on which country a user lives. You will need to read in the **countries.csv** dataset and merge together your datasets on the approporiate rows.  [Here](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.join.html) are the docs for joining tables. \n",
    "\n",
    "Does it appear that country had an impact on conversion?  Don't forget to create dummy variables for these country columns - **Hint: You will need two columns for the three dummy variables.** Provide the statistical output as well as a written response to answer this question."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 40,
   "metadata": {},
   "outputs": [],
   "source": [
    "countries_df = pd.read_csv('countries.csv')\n",
    "df_new = countries_df.set_index('user_id').join(df2.set_index('user_id'), how='inner')\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 41,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>country</th>\n",
       "      <th>timestamp</th>\n",
       "      <th>group</th>\n",
       "      <th>landing_page</th>\n",
       "      <th>converted</th>\n",
       "      <th>ab_page</th>\n",
       "      <th>intercept</th>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>user_id</th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>797742</th>\n",
       "      <td>US</td>\n",
       "      <td>2017-01-05 15:42:38.613057</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>715967</th>\n",
       "      <td>US</td>\n",
       "      <td>2017-01-05 15:25:10.061717</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>812548</th>\n",
       "      <td>US</td>\n",
       "      <td>2017-01-23 06:59:51.663054</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>801287</th>\n",
       "      <td>US</td>\n",
       "      <td>2017-01-07 10:31:17.714388</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>770975</th>\n",
       "      <td>UK</td>\n",
       "      <td>2017-01-03 21:32:46.932833</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>759713</th>\n",
       "      <td>US</td>\n",
       "      <td>2017-01-12 04:26:46.499785</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>635592</th>\n",
       "      <td>US</td>\n",
       "      <td>2017-01-12 04:35:50.620058</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>803965</th>\n",
       "      <td>CA</td>\n",
       "      <td>2017-01-11 22:38:22.410112</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "        country                   timestamp      group landing_page  \\\n",
       "user_id                                                               \n",
       "797742       US  2017-01-05 15:42:38.613057  treatment     new_page   \n",
       "715967       US  2017-01-05 15:25:10.061717    control     old_page   \n",
       "812548       US  2017-01-23 06:59:51.663054  treatment     new_page   \n",
       "801287       US  2017-01-07 10:31:17.714388    control     old_page   \n",
       "770975       UK  2017-01-03 21:32:46.932833    control     old_page   \n",
       "759713       US  2017-01-12 04:26:46.499785  treatment     new_page   \n",
       "635592       US  2017-01-12 04:35:50.620058  treatment     new_page   \n",
       "803965       CA  2017-01-11 22:38:22.410112    control     old_page   \n",
       "\n",
       "         converted  ab_page  intercept  \n",
       "user_id                                 \n",
       "797742           0        1          1  \n",
       "715967           0        0          1  \n",
       "812548           0        1          1  \n",
       "801287           0        0          1  \n",
       "770975           0        0          1  \n",
       "759713           0        1          1  \n",
       "635592           0        1          1  \n",
       "803965           0        0          1  "
      ]
     },
     "execution_count": 41,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_new.sample(8)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 42,
   "metadata": {},
   "outputs": [],
   "source": [
    "### Create the necessary dummy variables\n",
    "df_new[['US', 'CA', 'UK']] = pd.get_dummies(df_new['country'])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "h. Though you have now looked at the individual factors of country and page on conversion, we would now like to look at an interaction between page and country to see if there significant effects on conversion.  Create the necessary additional columns, and fit the new model.  \n",
    "\n",
    "Provide the summary results, and your conclusions based on the results."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 43,
   "metadata": {},
   "outputs": [],
   "source": [
    "df_new['UK_X_ab_page'] = df_new['UK'] * df_new['ab_page']\n",
    "df_new['CA_X_ab_page'] = df_new['CA'] * df_new['ab_page']\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 44,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>country</th>\n",
       "      <th>timestamp</th>\n",
       "      <th>group</th>\n",
       "      <th>landing_page</th>\n",
       "      <th>converted</th>\n",
       "      <th>ab_page</th>\n",
       "      <th>intercept</th>\n",
       "      <th>US</th>\n",
       "      <th>CA</th>\n",
       "      <th>UK</th>\n",
       "      <th>UK_X_ab_page</th>\n",
       "      <th>CA_X_ab_page</th>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>user_id</th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>781013</th>\n",
       "      <td>US</td>\n",
       "      <td>2017-01-03 19:15:24.563386</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>701172</th>\n",
       "      <td>US</td>\n",
       "      <td>2017-01-06 11:00:22.875280</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>709390</th>\n",
       "      <td>US</td>\n",
       "      <td>2017-01-11 17:47:40.228456</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>869664</th>\n",
       "      <td>US</td>\n",
       "      <td>2017-01-13 18:56:41.851759</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>776085</th>\n",
       "      <td>US</td>\n",
       "      <td>2017-01-18 12:37:17.567281</td>\n",
       "      <td>treatment</td>\n",
       "      <td>new_page</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>844455</th>\n",
       "      <td>US</td>\n",
       "      <td>2017-01-22 16:49:28.026070</td>\n",
       "      <td>control</td>\n",
       "      <td>old_page</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "        country                   timestamp      group landing_page  \\\n",
       "user_id                                                               \n",
       "781013       US  2017-01-03 19:15:24.563386  treatment     new_page   \n",
       "701172       US  2017-01-06 11:00:22.875280  treatment     new_page   \n",
       "709390       US  2017-01-11 17:47:40.228456    control     old_page   \n",
       "869664       US  2017-01-13 18:56:41.851759  treatment     new_page   \n",
       "776085       US  2017-01-18 12:37:17.567281  treatment     new_page   \n",
       "844455       US  2017-01-22 16:49:28.026070    control     old_page   \n",
       "\n",
       "         converted  ab_page  intercept  US  CA  UK  UK_X_ab_page  CA_X_ab_page  \n",
       "user_id                                                                         \n",
       "781013           0        1          1   0   0   1             1             0  \n",
       "701172           0        1          1   0   0   1             1             0  \n",
       "709390           0        0          1   0   0   1             0             0  \n",
       "869664           0        1          1   0   0   1             1             0  \n",
       "776085           0        1          1   0   0   1             1             0  \n",
       "844455           0        0          1   0   0   1             0             0  "
      ]
     },
     "execution_count": 44,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_new.sample(6)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 45,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.366109\n",
      "         Iterations 6\n"
     ]
    }
   ],
   "source": [
    "### Fit Your Linear Model And Obtain the Results\n",
    "df_new['intercept'] = 1\n",
    "logit_mod = sm.Logit(df_new['converted'], df_new[['intercept', 'ab_page', 'UK', 'CA','UK_X_ab_page','CA_X_ab_page']])\n",
    "results = logit_mod.fit()\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 46,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<table class=\"simpletable\">\n",
       "<caption>Logit Regression Results</caption>\n",
       "<tr>\n",
       "  <th>Dep. Variable:</th>       <td>converted</td>    <th>  No. Observations:  </th>   <td>290584</td>   \n",
       "</tr>\n",
       "<tr>\n",
       "  <th>Model:</th>                 <td>Logit</td>      <th>  Df Residuals:      </th>   <td>290578</td>   \n",
       "</tr>\n",
       "<tr>\n",
       "  <th>Method:</th>                 <td>MLE</td>       <th>  Df Model:          </th>   <td>     5</td>   \n",
       "</tr>\n",
       "<tr>\n",
       "  <th>Date:</th>            <td>Thu, 16 Dec 2021</td> <th>  Pseudo R-squ.:     </th>  <td>3.482e-05</td> \n",
       "</tr>\n",
       "<tr>\n",
       "  <th>Time:</th>                <td>22:23:07</td>     <th>  Log-Likelihood:    </th> <td>-1.0639e+05</td>\n",
       "</tr>\n",
       "<tr>\n",
       "  <th>converged:</th>             <td>True</td>       <th>  LL-Null:           </th> <td>-1.0639e+05</td>\n",
       "</tr>\n",
       "<tr>\n",
       "  <th>Covariance Type:</th>     <td>nonrobust</td>    <th>  LLR p-value:       </th>   <td>0.1920</td>   \n",
       "</tr>\n",
       "</table>\n",
       "<table class=\"simpletable\">\n",
       "<tr>\n",
       "        <td></td>          <th>coef</th>     <th>std err</th>      <th>z</th>      <th>P>|z|</th>  <th>[0.025</th>    <th>0.975]</th>  \n",
       "</tr>\n",
       "<tr>\n",
       "  <th>intercept</th>    <td>   -2.0040</td> <td>    0.036</td> <td>  -55.008</td> <td> 0.000</td> <td>   -2.075</td> <td>   -1.933</td>\n",
       "</tr>\n",
       "<tr>\n",
       "  <th>ab_page</th>      <td>   -0.0674</td> <td>    0.052</td> <td>   -1.297</td> <td> 0.195</td> <td>   -0.169</td> <td>    0.034</td>\n",
       "</tr>\n",
       "<tr>\n",
       "  <th>UK</th>           <td>    0.0175</td> <td>    0.038</td> <td>    0.465</td> <td> 0.642</td> <td>   -0.056</td> <td>    0.091</td>\n",
       "</tr>\n",
       "<tr>\n",
       "  <th>CA</th>           <td>    0.0118</td> <td>    0.040</td> <td>    0.296</td> <td> 0.767</td> <td>   -0.066</td> <td>    0.090</td>\n",
       "</tr>\n",
       "<tr>\n",
       "  <th>UK_X_ab_page</th> <td>    0.0469</td> <td>    0.054</td> <td>    0.872</td> <td> 0.383</td> <td>   -0.059</td> <td>    0.152</td>\n",
       "</tr>\n",
       "<tr>\n",
       "  <th>CA_X_ab_page</th> <td>    0.0783</td> <td>    0.057</td> <td>    1.378</td> <td> 0.168</td> <td>   -0.033</td> <td>    0.190</td>\n",
       "</tr>\n",
       "</table>"
      ],
      "text/plain": [
       "<class 'statsmodels.iolib.summary.Summary'>\n",
       "\"\"\"\n",
       "                           Logit Regression Results                           \n",
       "==============================================================================\n",
       "Dep. Variable:              converted   No. Observations:               290584\n",
       "Model:                          Logit   Df Residuals:                   290578\n",
       "Method:                           MLE   Df Model:                            5\n",
       "Date:                Thu, 16 Dec 2021   Pseudo R-squ.:               3.482e-05\n",
       "Time:                        22:23:07   Log-Likelihood:            -1.0639e+05\n",
       "converged:                       True   LL-Null:                   -1.0639e+05\n",
       "Covariance Type:            nonrobust   LLR p-value:                    0.1920\n",
       "================================================================================\n",
       "                   coef    std err          z      P>|z|      [0.025      0.975]\n",
       "--------------------------------------------------------------------------------\n",
       "intercept       -2.0040      0.036    -55.008      0.000      -2.075      -1.933\n",
       "ab_page         -0.0674      0.052     -1.297      0.195      -0.169       0.034\n",
       "UK               0.0175      0.038      0.465      0.642      -0.056       0.091\n",
       "CA               0.0118      0.040      0.296      0.767      -0.066       0.090\n",
       "UK_X_ab_page     0.0469      0.054      0.872      0.383      -0.059       0.152\n",
       "CA_X_ab_page     0.0783      0.057      1.378      0.168      -0.033       0.190\n",
       "================================================================================\n",
       "\"\"\""
      ]
     },
     "execution_count": 46,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "###to get the summary results\n",
    "results.summary()\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "<a id='conclusions'></a>\n",
    "## Conclusions\n",
    "according to p_values we get , we conclude that there is no relation between countery & page \"we fail to reget null hypothesis\"\n",
    "\n",
    "based on the given information  my opnion for the company is to use the old version of the page.\n",
    "\n",
    "Congratulations on completing the project! \n",
    "\n",
    "### Gather Submission Materials\n",
    "\n",
    "Once you are satisfied with the status of your Notebook, you should save it in a format that will make it easy for others to read. You can use the __File -> Download as -> HTML (.html)__ menu to save your notebook as an .html file. If you are working locally and get an error about \"No module name\", then open a terminal and try installing the missing module using `pip install <module_name>` (don't include the \"<\" or \">\" or any words following a period in the module name).\n",
    "\n",
    "You will submit both your original Notebook and an HTML or PDF copy of the Notebook for review. There is no need for you to include any data files with your submission. If you made reference to other websites, books, and other resources to help you in solving tasks in the project, make sure that you document them. It is recommended that you either add a \"Resources\" section in a Markdown cell at the end of the Notebook report, or you can include a `readme.txt` file documenting your sources.\n",
    "\n",
    "### Submit the Project\n",
    "\n",
    "When you're ready, click on the \"Submit Project\" button to go to the project submission page. You can submit your files as a .zip archive or you can link to a GitHub repository containing your project files. If you go with GitHub, note that your submission will be a snapshot of the linked repository at time of submission. It is recommended that you keep each project in a separate repository to avoid any potential confusion: if a reviewer gets multiple folders representing multiple projects, there might be confusion regarding what project is to be evaluated.\n",
    "\n",
    "It can take us up to a week to grade the project, but in most cases it is much faster. You will get an email once your submission has been reviewed. If you are having any problems submitting your project or wish to check on the status of your submission, please email us at dataanalyst-project@udacity.com. In the meantime, you should feel free to continue on with your learning journey by beginning the next module in the program."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.8.8"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
