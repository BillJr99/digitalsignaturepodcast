---
layout: activity
title: "Classifying Legitimate Text with Natural Language Processing"
permalink: /activities/zucker-nlp

gradelevel: secondary
category: python
subject: computing

gitrepo: https://github.com/azucker99/NLPDemo

authors: "Adam Zucker and William M. Mongan"
date: 2022-06-12

summary: "In this activity, we will use Natural Language Processing to read job descriptions and determine which ones are for legitimate job opportunities using Python and Google Colab."

thumbnail: "/images/activity-nlp/textgraph.png"
---

One field of computing studies the application of <strong>Machine Learning</strong> to process the written word.  That could include text like the words on this webpage, or in a book, or generated on social networks.  This is a field called <strong>Natural Language Processing</strong>, or NLP.  For example, you might have read someone's social media page, or comment on a web article, and wondered if it was real, or if it was generated automatically by a &quot;bot&quot;.  Today, we will use NLP to help determine if job descriptions are legitimate (that is, do they actually represent real job opportunities).  

Most machine learning algorithms begin by <strong>training</strong> from existing data: that is, by reading existing text that we already know is legitimate (or not), and telling the algorithm which category it is.  Over time, the algorithm will formulate a <strong>model</strong> that will help it infer in general whether text falls into one category or the other by identifying patterns in the training data.  Hopefully, this will allow us to run the algorithm on unknown data (like a new piece of text) and decide which category it most likely falls into.  One way we can see how effective our model is at doing this is to use other examples for which we already know the answer: instead of training on it, we ask the algorithm to predict with it, and we check its work.  If it does a good job accurately classifying these, we can infer that it will do a good job in general.

### Getting Started

We will use the Python programming language in an environment called [Google Colab](https://colab.research.google.com/), which is a free tool to run interactive code in small steps.

To get started, log into [Google Colab](https://colab.research.google.com/).  Google Colab uses something called a <strong>notebook</strong> to write and experiment with code.  This way, we can write our code in small steps, and try each one as we go.  This is often easier than trying to write an entire program from scratch and running it all at once.  

Go to the <strong>File</strong> menu and select <strong>Open Notebook</strong>.  You'll see a tab called <strong>GitHub</strong> at the top.  GitHub is a platform for sharing code, and we'll be using Adam Zucker's GitHub repository to get started.  Enter his repository URL: [https://github.com/azucker99/NLPDemo.git](https://github.com/azucker99/NLPDemo.git) into the open menu window.  

You might see a few repository choices: if you do, select the one called <strong>azucker99/NLPDemo</strong>.  That's the one that contains the notebook and the sample job description files that we're going to train our natural language processing algorithm with.  
If it asks you to select a <strong>branch</strong>, select the one called <strong>main</strong>.  It's probably the only choice you'll see!  Finally, you'll see an available file to open called <strong>nlp_demo.ipynb</strong>: this is the notebook!  Go aheand and select it to open the notebook.

### The Plan

Now that we have the notebook open, we'll use the notebook to:

1. Download the training examples of job descriptions (some of which are legitimate and some of which are fraudulent)
2. Read them into the machine learning algorithm (using a Natural Language Processing library called **spaCy**) 
3. Use the algorithm to train a model which ones are legitimate and which ones are not so that it can build its model
4. Test the model by classifying new text as legitimate or fraudulent

#### Downloading the Training Examples

First, we'll download the example job description files.  After doing this, you can click on the folder icon on the left side of the Colab window, and you'll see the files inside.  You can click on them to read them if you like.  To download them, click the &quot;play&quot; button next to the first code snippet:

```python
# If in Google Colab, change to content directory
%cd /content/
# If project exists on local, pull the latest changes, otherwise clone it
!if cd NLPDemo; then git pull; else git clone https://github.com/azucker99/NLPDemo.git NLPDemo; fi
```


```python
# Changing to the project directory
%cd NLPDemo
```

Next, click the play button next to the snippet that says `!pip install -r requirements.txt`.  This will install the spaCy library and the other library functions from the Internet that will help us to build our model.  It will look like this, and when it is done, there might be a button to <strong>Restart</strong> at the bottom (go ahead and click on it if you see this).

```python
# Installing all the required libraries 
%pip install -r requirements.txt
```
    
Similarly, click the next play button to tell our Python program that we're going to use the libraries we just installed:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import spacy
from spacy.tokens import DocBin
import random
```
    
#### Reading the Training Data into an NLP Model

Now we're ready to create and train the model!  We'll read job description data from a website called [Kaggle](https://www.kaggle.com/datasets/shivamb/real-or-fake-fake-jobposting-prediction), which is a data science website that features tutorials, examples, and challenges that you can try.  With a Python notebook, that's as easy as hitting the play button again, which will look like the below!

```python
# Reading in the data into a pandas dataframe object
df = pd.read_csv("fake_job_postings.csv")
```
    
##### Looking at the Data

It might seem like it, but <strong>Data Scientists</strong> don't just hit the play button to generate their models.  They could, but how would they know if it's really building the right model to make predictions later?  Fortunately, there are many techniques that we can apply to make sure we're generating models correctly.  Let's explore a few: you can click the play button next to each one to see what it does.  

First, we can see that our dataset has 17880 rows (job descriptions), and 18 columns.  What kind of columns do you see for each job description?  Which one contains the actual job description text that we'll be reading?

```python
print(f"Number of rows {df.shape[0]} \nNumber of columns: {df.shape[1]}")
df.head()
```

    Number of rows 17880 
    Number of columns: 18





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>title</th>
      <th>location</th>
      <th>company_profile</th>
      <th>requirements</th>
      <th>fraudulent</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Marketing Intern</td>
      <td>US, NY, New York</td>
      <td>We're Food52, and we've created a groundbreaki...</td>
      <td>Experience with content management systems a m...</td>
      <td>0</td>
    </tr>
    <tr>
      <td>Customer Service - Cloud Video Production</td>
      <td>NZ, , Auckland</td>
      <td>90 Seconds, the worlds Cloud Video Production ...</td>
      <td>What we expect from you:Your key responsibilit...</td>
      <td>0</td>
    </tr>
    <tr>
      <td>Commissioning Machinery Assistant (CMA)</td>
      <td>US, IA, Wever</td>
      <td>Valor Services provides Workforce Solutions th...</td>
      <td>Implement pre-commissioning and commissioning ...</td>
      <td>0</td>
    </tr>
    <tr>
      <td>Account Executive - Washington DC</td>
      <td>US, DC, Washington</td>
      <td>THE COMPANY: ESRI – Environmental Systems Rese...</td>
      <td>EDUCATION: Bachelor’s or Master’s in GIS, busi...</td>
      <td>0</td>
    </tr>
    <tr>
      <td>Bill Review Manager</td>
      <td>US, FL, Fort Worth</td>
      <td>SpotSource Solutions LLC is a Global Human Cap...</td>
      <td>QUALIFICATIONS:RN license in the State of Texa...</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Checking for missing values
df.isnull().sum()
```




    job_id                     0
    title                      0
    location                 346
    department             11547
    salary_range           15012
    company_profile         3308
    description                1
    requirements            2695
    benefits                7210
    telecommuting              0
    has_company_logo           0
    has_questions              0
    employment_type         3471
    required_experience     7050
    required_education      8105
    industry                4903
    function                6455
    fraudulent                 0
    dtype: int64




```python
# Visualizing the first company profile 
df.loc[0].company_profile
```




    "We're Food52, and we've created a groundbreaking and award-winning cooking site. We support, connect, and celebrate home cooks, and give them everything they need in one place.We have a top editorial, business, and engineering team. We're focused on using technology to find new and better ways to connect people around their specific food interests, and to offer them superb, highly curated information about food and cooking. We attract the most talented home cooks and contributors in the country; we also publish well-known professionals like Mario Batali, Gwyneth Paltrow, and Danny Meyer. And we have partnerships with Whole Foods Market and Random House.Food52 has been named the best food website by the James Beard Foundation and IACP, and has been featured in the New York Times, NPR, Pando Daily, TechCrunch, and on the Today Show.We're located in Chelsea, in New York City."

##### Exploring the Dataset as a Whole

Often, it is helpful to explore the data in its entirety rather than one row at a time.  Let's see how many fraudulent job postings there are in the example set.

```python
# Visualizing how many job postings are actually fraudulent 
fraudulent_series = df.fraudulent.apply(lambda x: 'fraudlent' if x else 'not_fraudlent')
fraudulent_series.value_counts(sort=True).plot(kind='barh')
plt.xlabel('Count')
plt.ylabel('Fraudlent');
print(fraudulent_series.value_counts())
```

    Not Fraudlent    17014
    fraudlent          866
    Name: fraudulent, dtype: int64  

<p align="center">
<img style="max-width:100%;" alt="Hide and Seek Flowchart" src="/images/activity-nlp/barchart.png">
</p>    

What do you notice about the number of fraudulent entries in the dataset?  Do you think our algorithm will do better at classifying job posts that are fraudulent or non-fraudulent?  Often, it is better to have about the same number of training examples for each category (called a <strong>class</strong>) that we want to train on.  What do you think we can do about that to help even things up?

#### Training a Model

We'll use the spaCy library to read this dataset and look at the fraudulent/non-fraudulent column to find patterns among the job posting training data.  For example, it might find that jobs with very short or very long descriptions tend to be fraudulent (what would happen to legitimate jobs that happen to have long or short descriptions, if this were to occur?).  It might find that jobs with certain words in them tend to be fake.  Unfortunately, it might find that jobs from certain zip codes tend to be fake, even though that's not really the case, because it just happens to see those patterns in the training data.  What can we do to ensure that algorithms don't train on potentially discriminatory inputs?

For now, go ahead and run the training commands:

```python
# Our training data will come from the description column
df2 = df[['description','fraudulent']].dropna() # Condenses the dataframe to the two columns and drops null values
print(df2.shape)
df2.head()
```

    (17879, 2)





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>description</th>
      <th>fraudulent</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Food52, a fast-growing, James Beard Award-winn...</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Organised - Focused - Vibrant - Awesome!Do you...</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Our client, located in Houston, is actively se...</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>THE COMPANY: ESRI – Environmental Systems Rese...</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>JOB TITLE: Itemization Review ManagerLOCATION:...</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Converting the data from a dataframe to the appropriate training format

# Adds a tuples column for each description and its label
df2['tuples'] = df2.apply(lambda row: (row['description'], row['fraudulent']), axis = 1)
# Converts that column to a list
data = df2['tuples'].to_list()
# Printing out the first two entries of our formatted dataset 
data[:2]
```




    [('Food52, a fast-growing, James Beard Award-winning online food community and crowd-sourced and curated recipe hub, is currently interviewing full- and part-time unpaid interns to work in a small team of editors, executives, and developers in its New York City headquarters.Reproducing and/or repackaging existing Food52 content for a number of partner sites, such as Huffington Post, Yahoo, Buzzfeed, and more in their various content management systemsResearching blogs and websites for the Provisions by Food52 Affiliate ProgramAssisting in day-to-day affiliate program support, such as screening affiliates and assisting in any affiliate inquiriesSupporting with PR &amp; Events when neededHelping with office administrative work, such as filing, mailing, and preparing for meetingsWorking with developers to document bugs and suggest improvements to the siteSupporting the marketing and executive staff',
      0),
     ("Organised - Focused - Vibrant - Awesome!Do you have a passion for customer service? Slick typing skills? Maybe Account Management? ...And think administration is cooler than a polar bear on a jetski? Then we need to hear you!\xa0We are the Cloud Video Production Service and opperating on a glodal level. Yeah, it's pretty cool. Serious about\xa0delivering a world class product and excellent customer service.Our rapidly expanding business is looking for a talented Project Manager to manage the successful delivery of video projects, manage client communications and drive the production process. Work with some of the coolest brands on the planet and learn from a global team that are representing NZ is a huge way!We are entering the next growth stage of our business and growing quickly internationally. \xa0Therefore, the position is bursting with opportunity for the right person entering the business at the right time.\xa090 Seconds, the worlds Cloud Video Production Service -\xa0http://90#URL_fbe6559afac620a3cd2c22281f7b8d0eef56a73e3d9a311e2f1ca13d081dd630#90 Seconds is the worlds Cloud Video Production Service enabling brands and agencies to get high quality online video content shot and produced anywhere in the world. Fast, affordable, and all managed seamlessly in the cloud from purchase to publish.\xa090 Seconds removes the hassle, cost, risk and speed issues of working with regular video production companies by managing every aspect of video projects in a beautiful online experience. \xa0With a growing network of over 2,000 rated video professionals in over 50 countries and dedicated production success teams in 5 countries guaranteeing video project success 100%. It's as easy as commissioning a quick google adwords campaign.90 Seconds has produced almost 4,000 videos in over 30 Countries for over 500 Global brands including some of the worlds largest including Paypal, L'oreal, Sony and Barclays and has offices in Auckland, London, Sydney, Tokyo &amp; Singapore.Our Auckland office is based\xa0right in the heart of the Wynyard Quarter Innovation Precinct - GridAKL!\xa0",
      0)]  
    
You might notice that the job descriptions have &quot;bad data&quot; in them - funny looking characters and typos.  Data Scientists often write programs to correct or remove these from the data prior to training.  We won't do that today, for the sake of time.  What other things might we take out of the data?  For example, what kinds of words would occur in both fake and legitimate job descriptions (words that everyone uses everyday)?  Should we remove those, too?  These are called <strong>stop words</strong>, and we tend to remove them from the model (words like &quot;and&quot; or &quot;the&quot;).

At the very beginning, do you remember that we said it's a good idea to save a few rows from the dataset, so that we have some to test out the model with while still knowing the answers?  We'll do that right now.  Let's give 80 percent of the data to the model for training, and save the other 20 percent for testing (so it won't know the answers to those, even though we do for checking!).  

```python
# Splitting data into training and testing data 
train_size = 0.8 # Data will consist of 80% of the training set 
split = int(len(data)*train_size) # The number of documents that will be in the training set

# Setting seed for reproducibility
random.seed(42)
random.shuffle(data) # Shuffling the data so the model does not learn from the order

# Setting train and test set by indexing data to be before and after split 
train = data[:split]
test = data[split:]
```


```python
# Function to convert the data to spaCy's binary format 
def create_docbin(dataset, output_name):
    # Creating a blank spaCy pipeline to serve as a tokenizer
    nlp = spacy.blank('en')
    # Creating a DocBin object to hold spaCy documents
    doc_bin = DocBin()
    # Converting each tuple into a spaCy Doc object
    for doc, label in nlp.pipe(dataset, as_tuples=True):
        doc.cats = {'positive': label, 'negative': 1 - label} # Setting the document's category to be its label
        doc_bin.add(doc) # Adding the Doc to the DocBin
    
    # Writing the DocBin object to a file 
    doc_bin.to_disk(f"./{output_name}.spacy")
    print(f"{output_name} set contains {len(doc_bin)} documents")

    return doc_bin
```


```python
# Creates the training and test files
train_docbin = create_docbin(train, 'train')
test_docbin = create_docbin(test, 'test')
```

    train set contains 14303 documents
    test set contains 3576 documents

##### Allowing the Algorithm to Generate the Model

Now, we're ready to generate the model itself.  This involves reading all the training documents, so it takes a little while.  Come back in 5 to 10 minutes and it should be ready to continue.  You can hit the &quot;stop&quot; button (which is where the play button usually is on the left while the code is running) to quit this process at any time - you can move on with the training model it has generated so far if you don't want to wait for it to finish.

```python
# Creating the configuration file for model training
!python -m spacy init config config.cfg --lang en --pipeline textcat --optimize efficiency --force
```

    You can now add your data and train your pipeline:
    python -m spacy train config.cfg --paths.train ./train.spacy --paths.dev ./dev.spacy

```python
# Training the model
# Max epochs set to 1 epoch for training brevity
# To complete training, remove "--training.max_epochs 1" from command
!python -m spacy train config.cfg --output ./output --paths.train ./train.spacy \
--paths.dev ./test.spacy --training.max_epochs 1
```

    ============================= Training pipeline =============================
    E    #       LOSS TEXTCAT  CATS_SCORE  SCORE 
    ---  ------  ------------  ----------  ------
      0       0          0.25       48.75    0.49
      0     200          9.94       48.75    0.49
      0     400          9.31       50.47    0.50
      0     600         12.24       53.20    0.53
      0     800          7.23       59.67    0.60
      0    1000          5.84       59.67    0.60
      0    1200          6.81       61.94    0.62
      0    1400         10.27       61.94    0.62

#### Testing the Model

Finally, let's test the model on our remaining 20 percent that we saved earlier!  You'll see several scores.  See if you can find the section labeled `Textcat F`: this shows a few columns called `P` (for <strong>precision</strong>) and `R` (for <strong>recall</strong>), out of 100 percent.  Precision tells us how often the model correctly predicted that a job post was fraudulent when it really was fraudulent (or that it was not fraudulent when it really was not).  Recall tells us how often the model didn't give us a &quot;false positive&quot;, and incorrectly guess the answer.  Unlike a math test, when you get back your grade out of 100, we like to evaluate classification models in both directions.  For example, you could say that you can perfectly predict when it is going to rain by saying it's going to rain every day - you'd never be wrong!  Recall allows us to measure how often you guessed it was going to rain on days that it was sunny out.  These scores are kind of merged together into a single score called the <strong>Receiver Operating Characteristic</strong> (ROC), which is a score from 0 to 1 (with 1 being perfect).

```python
# Evaluating the model using common classification metrics (precision, recall, F1 score)
!python -m spacy evaluate output/model-best test.spacy --output metrics.json
```

    ================================== Results ==================================
    
    TOK                 100.00
    TEXTCAT (macro F)   61.94 
    SPEED               195155
    
    =========================== Textcat F (per label) ===========================
    
                    P        R       F
    positive   100.00    14.94   26.00
    negative    95.83   100.00   97.87
    

    ======================== Textcat ROC AUC (per label) ========================
    
               ROC AUC
    positive      0.62
    negative      0.63
    
### Follow Up
How did they do?  For us, the model didn't do so well.  There are some things we can do to improve our results.  Can you think of some reasons why this might be, and what some of these things are?

1. How can we improve our current classification model?  For example, how can we improve the imbalance of examples between the fraudulent and non-fraudulent training classes?

2. Identify key features (e.g., entities, words, phrases) of job descriptions that belong to the fraudulent class

3. Perform Exploratory Data Analysis (EDA) to find interesting insights (e.g., most frequent words, what departments have the most fraudulent advertisements, etc.)

4. (Advanced): Create a classification model using all the features to predict fraudluent jobs (e.g, using logisitic regression, random forest, etc.)
