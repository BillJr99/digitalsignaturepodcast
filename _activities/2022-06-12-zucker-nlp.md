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
2. Read them into the machine learning algorithm (using a Natural Language Processing library called **spacy**) 
3. Use the algorithm to train a model which ones are legitimate and which ones are not so that it can build its model
4. Test the model by classifying new text as legitimate or fraudulent

#### Downloading the Training Examples

First, we'll download the example job description files.  After doing this, you can click on the folder icon on the left side of the Colab window, and you'll see the files inside.  You can click on them to read them if you like.  To download them, click the &quot;play&quot; button next to the first code snippet:

    <div class="cell markdown" id="D1pQ9Ls0_Iqp">

    # Text Classification Demo

    </div>

    <div class="cell markdown">

    ## Getting Repo from GitHub in Google Colab

    </div>

    <div class="cell code">

    ``` python
    # If in Google Colab, change to content directory
    %cd /content/
    # If project exists on local, pull the latest changes, otherwise clone it
    !if cd NLPDemo; then git pull; else git clone https://github.com/azucker99/NLPDemo.git NLPDemo; fi
    ```

    </div>

Next, click the play button next to the snippet that says `!pip install -r requirements.txt`.  This will install the spacy library and the other library functions from the Internet that will help us to build our model.  It will look like this, and when it is done, there might be a button to <strong>Restart</strong> at the bottom (go ahead and click on it if you see this).

    <div class="cell markdown" id="hi9HZskU_QFV">

    ## Imports and Downloads

    </div>

    <div class="cell code" data-execution_count="7" data-colab="{&quot;base_uri&quot;:&quot;https://localhost:8080/&quot;}" id="f0_GuXER_Ngg" data-outputId="7f7560f9-9925-4591-ea51-8a9904076b7d">

    ``` python
    # Installing all the required libraries 
    %pip install -r requirements.txt
    ```

    <div class="output stream stdout">

        Collecting spacy==3.0.6
          Downloading spacy-3.0.6.tar.gz (7.1 MB)
        ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 7.1/7.1 MB 3.4 MB/s eta 0:00:00m eta 0:00:010:01:01
        ents to build wheel ... etadata (pyproject.toml) ... ent already satisfied: pandas in ./nlpdemo/lib/python3.10/site-packages (from -r requirements.txt (line 2)) (1.4.2)
        Requirement already satisfied: numpy in ./nlpdemo/lib/python3.10/site-packages (from -r requirements.txt (line 3)) (1.22.4)
        Requirement already satisfied: matplotlib in ./nlpdemo/lib/python3.10/site-packages (from -r requirements.txt (line 4)) (3.5.2)
        Requirement already satisfied: sklearn in ./nlpdemo/lib/python3.10/site-packages (from -r requirements.txt (line 5)) (0.0)
        Requirement already satisfied: jinja2 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (3.1.2)
        Requirement already satisfied: blis<0.8.0,>=0.4.0 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (0.7.7)
        Requirement already satisfied: packaging>=20.0 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (21.3)
        Requirement already satisfied: cymem<2.1.0,>=2.0.2 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (2.0.6)
        Requirement already satisfied: spacy-legacy<3.1.0,>=3.0.4 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (3.0.9)
        Collecting typer<0.4.0,>=0.3.0
          Downloading typer-0.3.2-py3-none-any.whl (21 kB)
        Collecting pydantic<1.8.0,>=1.7.1
          Downloading pydantic-1.7.4-py3-none-any.whl (107 kB)
        ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 107.9/107.9 KB 6.9 MB/s eta 0:00:00
        ent already satisfied: setuptools in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (62.1.0)
        Requirement already satisfied: wasabi<1.1.0,>=0.8.1 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (0.9.1)
        Requirement already satisfied: preshed<3.1.0,>=3.0.2 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (3.0.6)
        Requirement already satisfied: thinc<8.1.0,>=8.0.3 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (8.0.17)
        Requirement already satisfied: requests<3.0.0,>=2.13.0 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (2.28.0)
        Requirement already satisfied: srsly<3.0.0,>=2.4.1 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (2.4.3)
        Requirement already satisfied: pathy>=0.3.5 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (0.6.1)
        Requirement already satisfied: murmurhash<1.1.0,>=0.28.0 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (1.0.7)
        Requirement already satisfied: catalogue<2.1.0,>=2.0.3 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (2.0.7)
        Requirement already satisfied: tqdm<5.0.0,>=4.38.0 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6->-r requirements.txt (line 1)) (4.64.0)
        Requirement already satisfied: python-dateutil>=2.8.1 in ./nlpdemo/lib/python3.10/site-packages (from pandas->-r requirements.txt (line 2)) (2.8.2)
        Requirement already satisfied: pytz>=2020.1 in ./nlpdemo/lib/python3.10/site-packages (from pandas->-r requirements.txt (line 2)) (2022.1)
        Requirement already satisfied: cycler>=0.10 in ./nlpdemo/lib/python3.10/site-packages (from matplotlib->-r requirements.txt (line 4)) (0.11.0)
        Requirement already satisfied: kiwisolver>=1.0.1 in ./nlpdemo/lib/python3.10/site-packages (from matplotlib->-r requirements.txt (line 4)) (1.4.2)
        Requirement already satisfied: pillow>=6.2.0 in ./nlpdemo/lib/python3.10/site-packages (from matplotlib->-r requirements.txt (line 4)) (9.1.1)
        Requirement already satisfied: pyparsing>=2.2.1 in ./nlpdemo/lib/python3.10/site-packages (from matplotlib->-r requirements.txt (line 4)) (3.0.9)
        Requirement already satisfied: fonttools>=4.22.0 in ./nlpdemo/lib/python3.10/site-packages (from matplotlib->-r requirements.txt (line 4)) (4.33.3)
        Requirement already satisfied: scikit-learn in ./nlpdemo/lib/python3.10/site-packages (from sklearn->-r requirements.txt (line 5)) (1.1.1)
        Requirement already satisfied: smart-open<6.0.0,>=5.0.0 in ./nlpdemo/lib/python3.10/site-packages (from pathy>=0.3.5->spacy==3.0.6->-r requirements.txt (line 1)) (5.2.1)
        Requirement already satisfied: six>=1.5 in ./nlpdemo/lib/python3.10/site-packages (from python-dateutil>=2.8.1->pandas->-r requirements.txt (line 2)) (1.16.0)
        Requirement already satisfied: urllib3<1.27,>=1.21.1 in ./nlpdemo/lib/python3.10/site-packages (from requests<3.0.0,>=2.13.0->spacy==3.0.6->-r requirements.txt (line 1)) (1.26.9)
        Requirement already satisfied: certifi>=2017.4.17 in ./nlpdemo/lib/python3.10/site-packages (from requests<3.0.0,>=2.13.0->spacy==3.0.6->-r requirements.txt (line 1)) (2022.5.18.1)
        Requirement already satisfied: charset-normalizer~=2.0.0 in ./nlpdemo/lib/python3.10/site-packages (from requests<3.0.0,>=2.13.0->spacy==3.0.6->-r requirements.txt (line 1)) (2.0.12)
        Requirement already satisfied: idna<4,>=2.5 in ./nlpdemo/lib/python3.10/site-packages (from requests<3.0.0,>=2.13.0->spacy==3.0.6->-r requirements.txt (line 1)) (3.3)
        Collecting click<7.2.0,>=7.1.1
          Downloading click-7.1.2-py2.py3-none-any.whl (82 kB)
        ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 82.8/82.8 KB 9.8 MB/s eta 0:00:00
        ent already satisfied: MarkupSafe>=2.0 in ./nlpdemo/lib/python3.10/site-packages (from jinja2->spacy==3.0.6->-r requirements.txt (line 1)) (2.1.1)
        Requirement already satisfied: threadpoolctl>=2.0.0 in ./nlpdemo/lib/python3.10/site-packages (from scikit-learn->sklearn->-r requirements.txt (line 5)) (3.1.0)
        Requirement already satisfied: scipy>=1.3.2 in ./nlpdemo/lib/python3.10/site-packages (from scikit-learn->sklearn->-r requirements.txt (line 5)) (1.8.1)
        Requirement already satisfied: joblib>=1.0.0 in ./nlpdemo/lib/python3.10/site-packages (from scikit-learn->sklearn->-r requirements.txt (line 5)) (1.1.0)
        Building wheels for collected packages: spacy
          Building wheel for spacy (pyproject.toml) ... e=spacy-3.0.6-cp310-cp310-linux_x86_64.whl size=32114650 sha256=5f27d54b22927f9a4e16d62cb22efc684fe87e2da4d6f72cedeca641ea55ac57
          Stored in directory: /home/azucker/.cache/pip/wheels/c3/db/f8/725ffb71b6583ede763307855ebffb14d8c8340d355b3a57ab
        Successfully built spacy
        Installing collected packages: pydantic, click, typer, spacy
          Attempting uninstall: pydantic
            Found existing installation: pydantic 1.8.2
            Uninstalling pydantic-1.8.2:
              Successfully uninstalled pydantic-1.8.2
          Attempting uninstall: click
            Found existing installation: click 8.1.3
            Uninstalling click-8.1.3:
              Successfully uninstalled click-8.1.3
          Attempting uninstall: typer
            Found existing installation: typer 0.4.1
            Uninstalling typer-0.4.1:
              Successfully uninstalled typer-0.4.1
          Attempting uninstall: spacy
            Found existing installation: spacy 3.3.1
            Uninstalling spacy-3.3.1:
              Successfully uninstalled spacy-3.3.1
        Successfully installed click-7.1.2 pydantic-1.7.4 spacy-3.0.6 typer-0.3.2
        WARNING: You are using pip version 22.0.4; however, version 22.1.2 is available.
        You should consider upgrading via the '/home/azucker/projects/NLPDemo/nlpdemo/bin/python -m pip install --upgrade pip' command.
        Note: you may need to restart the kernel to use updated packages.

    </div>

Similarly, click the next play button to tell our Python program that we're going to use the libraries we just installed:

    <div class="cell code" data-execution_count="117" id="euSM-PJ4-3TI">

    ``` python
    import pandas as pd
    import numpy as np
    import matplotlib.pyplot as plt
    import spacy
    from spacy.tokens import DocBin
    import random
    ```

    </div>
    
#### Reading the Training Data into an NLP Model

Now we're ready to create and train the model!  We'll read job description data from a website called [Kaggle](https://www.kaggle.com/datasets/shivamb/real-or-fake-fake-jobposting-prediction), which is a data science website that features tutorials, examples, and challenges that you can try.  With a Python notebook, that's as easy as hitting the play button again, which will look like the below!

    <div class="cell markdown" id="oeriNg7eBTZ0">

    ## Loading in the data

    Data comes from kaggle:
    <https://www.kaggle.com/datasets/shivamb/real-or-fake-fake-jobposting-prediction>

    </div>

    <div class="cell code" data-execution_count="118" data-colab="{&quot;height&quot;:73,&quot;resources&quot;:{&quot;http://localhost:8080/nbextensions/google.colab/files.js&quot;:{&quot;ok&quot;:true,&quot;status&quot;:200,&quot;data&quot;:&quot;Ly8gQ29weXJpZ2h0IDIwMTcgR29vZ2xlIExMQwovLwovLyBMaWNlbnNlZCB1bmRlciB0aGUgQXBhY2hlIExpY2Vuc2UsIFZlcnNpb24gMi4wICh0aGUgIkxpY2Vuc2UiKTsKLy8geW91IG1heSBub3QgdXNlIHRoaXMgZmlsZSBleGNlcHQgaW4gY29tcGxpYW5jZSB3aXRoIHRoZSBMaWNlbnNlLgovLyBZb3UgbWF5IG9idGFpbiBhIGNvcHkgb2YgdGhlIExpY2Vuc2UgYXQKLy8KLy8gICAgICBodHRwOi8vd3d3LmFwYWNoZS5vcmcvbGljZW5zZXMvTElDRU5TRS0yLjAKLy8KLy8gVW5sZXNzIHJlcXVpcmVkIGJ5IGFwcGxpY2FibGUgbGF3IG9yIGFncmVlZCB0byBpbiB3cml0aW5nLCBzb2Z0d2FyZQovLyBkaXN0cmlidXRlZCB1bmRlciB0aGUgTGljZW5zZSBpcyBkaXN0cmlidXRlZCBvbiBhbiAiQVMgSVMiIEJBU0lTLAovLyBXSVRIT1VUIFdBUlJBTlRJRVMgT1IgQ09ORElUSU9OUyBPRiBBTlkgS0lORCwgZWl0aGVyIGV4cHJlc3Mgb3IgaW1wbGllZC4KLy8gU2VlIHRoZSBMaWNlbnNlIGZvciB0aGUgc3BlY2lmaWMgbGFuZ3VhZ2UgZ292ZXJuaW5nIHBlcm1pc3Npb25zIGFuZAovLyBsaW1pdGF0aW9ucyB1bmRlciB0aGUgTGljZW5zZS4KCi8qKgogKiBAZmlsZW92ZXJ2aWV3IEhlbHBlcnMgZm9yIGdvb2dsZS5jb2xhYiBQeXRob24gbW9kdWxlLgogKi8KKGZ1bmN0aW9uKHNjb3BlKSB7CmZ1bmN0aW9uIHNwYW4odGV4dCwgc3R5bGVBdHRyaWJ1dGVzID0ge30pIHsKICBjb25zdCBlbGVtZW50ID0gZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgnc3BhbicpOwogIGVsZW1lbnQudGV4dENvbnRlbnQgPSB0ZXh0OwogIGZvciAoY29uc3Qga2V5IG9mIE9iamVjdC5rZXlzKHN0eWxlQXR0cmlidXRlcykpIHsKICAgIGVsZW1lbnQuc3R5bGVba2V5XSA9IHN0eWxlQXR0cmlidXRlc1trZXldOwogIH0KICByZXR1cm4gZWxlbWVudDsKfQoKLy8gTWF4IG51bWJlciBvZiBieXRlcyB3aGljaCB3aWxsIGJlIHVwbG9hZGVkIGF0IGEgdGltZS4KY29uc3QgTUFYX1BBWUxPQURfU0laRSA9IDEwMCAqIDEwMjQ7CgpmdW5jdGlvbiBfdXBsb2FkRmlsZXMoaW5wdXRJZCwgb3V0cHV0SWQpIHsKICBjb25zdCBzdGVwcyA9IHVwbG9hZEZpbGVzU3RlcChpbnB1dElkLCBvdXRwdXRJZCk7CiAgY29uc3Qgb3V0cHV0RWxlbWVudCA9IGRvY3VtZW50LmdldEVsZW1lbnRCeUlkKG91dHB1dElkKTsKICAvLyBDYWNoZSBzdGVwcyBvbiB0aGUgb3V0cHV0RWxlbWVudCB0byBtYWtlIGl0IGF2YWlsYWJsZSBmb3IgdGhlIG5leHQgY2FsbAogIC8vIHRvIHVwbG9hZEZpbGVzQ29udGludWUgZnJvbSBQeXRob24uCiAgb3V0cHV0RWxlbWVudC5zdGVwcyA9IHN0ZXBzOwoKICByZXR1cm4gX3VwbG9hZEZpbGVzQ29udGludWUob3V0cHV0SWQpOwp9CgovLyBUaGlzIGlzIHJvdWdobHkgYW4gYXN5bmMgZ2VuZXJhdG9yIChub3Qgc3VwcG9ydGVkIGluIHRoZSBicm93c2VyIHlldCksCi8vIHdoZXJlIHRoZXJlIGFyZSBtdWx0aXBsZSBhc3luY2hyb25vdXMgc3RlcHMgYW5kIHRoZSBQeXRob24gc2lkZSBpcyBnb2luZwovLyB0byBwb2xsIGZvciBjb21wbGV0aW9uIG9mIGVhY2ggc3RlcC4KLy8gVGhpcyB1c2VzIGEgUHJvbWlzZSB0byBibG9jayB0aGUgcHl0aG9uIHNpZGUgb24gY29tcGxldGlvbiBvZiBlYWNoIHN0ZXAsCi8vIHRoZW4gcGFzc2VzIHRoZSByZXN1bHQgb2YgdGhlIHByZXZpb3VzIHN0ZXAgYXMgdGhlIGlucHV0IHRvIHRoZSBuZXh0IHN0ZXAuCmZ1bmN0aW9uIF91cGxvYWRGaWxlc0NvbnRpbnVlKG91dHB1dElkKSB7CiAgY29uc3Qgb3V0cHV0RWxlbWVudCA9IGRvY3VtZW50LmdldEVsZW1lbnRCeUlkKG91dHB1dElkKTsKICBjb25zdCBzdGVwcyA9IG91dHB1dEVsZW1lbnQuc3RlcHM7CgogIGNvbnN0IG5leHQgPSBzdGVwcy5uZXh0KG91dHB1dEVsZW1lbnQubGFzdFByb21pc2VWYWx1ZSk7CiAgcmV0dXJuIFByb21pc2UucmVzb2x2ZShuZXh0LnZhbHVlLnByb21pc2UpLnRoZW4oKHZhbHVlKSA9PiB7CiAgICAvLyBDYWNoZSB0aGUgbGFzdCBwcm9taXNlIHZhbHVlIHRvIG1ha2UgaXQgYXZhaWxhYmxlIHRvIHRoZSBuZXh0CiAgICAvLyBzdGVwIG9mIHRoZSBnZW5lcmF0b3IuCiAgICBvdXRwdXRFbGVtZW50Lmxhc3RQcm9taXNlVmFsdWUgPSB2YWx1ZTsKICAgIHJldHVybiBuZXh0LnZhbHVlLnJlc3BvbnNlOwogIH0pOwp9CgovKioKICogR2VuZXJhdG9yIGZ1bmN0aW9uIHdoaWNoIGlzIGNhbGxlZCBiZXR3ZWVuIGVhY2ggYXN5bmMgc3RlcCBvZiB0aGUgdXBsb2FkCiAqIHByb2Nlc3MuCiAqIEBwYXJhbSB7c3RyaW5nfSBpbnB1dElkIEVsZW1lbnQgSUQgb2YgdGhlIGlucHV0IGZpbGUgcGlja2VyIGVsZW1lbnQuCiAqIEBwYXJhbSB7c3RyaW5nfSBvdXRwdXRJZCBFbGVtZW50IElEIG9mIHRoZSBvdXRwdXQgZGlzcGxheS4KICogQHJldHVybiB7IUl0ZXJhYmxlPCFPYmplY3Q+fSBJdGVyYWJsZSBvZiBuZXh0IHN0ZXBzLgogKi8KZnVuY3Rpb24qIHVwbG9hZEZpbGVzU3RlcChpbnB1dElkLCBvdXRwdXRJZCkgewogIGNvbnN0IGlucHV0RWxlbWVudCA9IGRvY3VtZW50LmdldEVsZW1lbnRCeUlkKGlucHV0SWQpOwogIGlucHV0RWxlbWVudC5kaXNhYmxlZCA9IGZhbHNlOwoKICBjb25zdCBvdXRwdXRFbGVtZW50ID0gZG9jdW1lbnQuZ2V0RWxlbWVudEJ5SWQob3V0cHV0SWQpOwogIG91dHB1dEVsZW1lbnQuaW5uZXJIVE1MID0gJyc7CgogIGNvbnN0IHBpY2tlZFByb21pc2UgPSBuZXcgUHJvbWlzZSgocmVzb2x2ZSkgPT4gewogICAgaW5wdXRFbGVtZW50LmFkZEV2ZW50TGlzdGVuZXIoJ2NoYW5nZScsIChlKSA9PiB7CiAgICAgIHJlc29sdmUoZS50YXJnZXQuZmlsZXMpOwogICAgfSk7CiAgfSk7CgogIGNvbnN0IGNhbmNlbCA9IGRvY3VtZW50LmNyZWF0ZUVsZW1lbnQoJ2J1dHRvbicpOwogIGlucHV0RWxlbWVudC5wYXJlbnRFbGVtZW50LmFwcGVuZENoaWxkKGNhbmNlbCk7CiAgY2FuY2VsLnRleHRDb250ZW50ID0gJ0NhbmNlbCB1cGxvYWQnOwogIGNvbnN0IGNhbmNlbFByb21pc2UgPSBuZXcgUHJvbWlzZSgocmVzb2x2ZSkgPT4gewogICAgY2FuY2VsLm9uY2xpY2sgPSAoKSA9PiB7CiAgICAgIHJlc29sdmUobnVsbCk7CiAgICB9OwogIH0pOwoKICAvLyBXYWl0IGZvciB0aGUgdXNlciB0byBwaWNrIHRoZSBmaWxlcy4KICBjb25zdCBmaWxlcyA9IHlpZWxkIHsKICAgIHByb21pc2U6IFByb21pc2UucmFjZShbcGlja2VkUHJvbWlzZSwgY2FuY2VsUHJvbWlzZV0pLAogICAgcmVzcG9uc2U6IHsKICAgICAgYWN0aW9uOiAnc3RhcnRpbmcnLAogICAgfQogIH07CgogIGNhbmNlbC5yZW1vdmUoKTsKCiAgLy8gRGlzYWJsZSB0aGUgaW5wdXQgZWxlbWVudCBzaW5jZSBmdXJ0aGVyIHBpY2tzIGFyZSBub3QgYWxsb3dlZC4KICBpbnB1dEVsZW1lbnQuZGlzYWJsZWQgPSB0cnVlOwoKICBpZiAoIWZpbGVzKSB7CiAgICByZXR1cm4gewogICAgICByZXNwb25zZTogewogICAgICAgIGFjdGlvbjogJ2NvbXBsZXRlJywKICAgICAgfQogICAgfTsKICB9CgogIGZvciAoY29uc3QgZmlsZSBvZiBmaWxlcykgewogICAgY29uc3QgbGkgPSBkb2N1bWVudC5jcmVhdGVFbGVtZW50KCdsaScpOwogICAgbGkuYXBwZW5kKHNwYW4oZmlsZS5uYW1lLCB7Zm9udFdlaWdodDogJ2JvbGQnfSkpOwogICAgbGkuYXBwZW5kKHNwYW4oCiAgICAgICAgYCgke2ZpbGUudHlwZSB8fCAnbi9hJ30pIC0gJHtmaWxlLnNpemV9IGJ5dGVzLCBgICsKICAgICAgICBgbGFzdCBtb2RpZmllZDogJHsKICAgICAgICAgICAgZmlsZS5sYXN0TW9kaWZpZWREYXRlID8gZmlsZS5sYXN0TW9kaWZpZWREYXRlLnRvTG9jYWxlRGF0ZVN0cmluZygpIDoKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ24vYSd9IC0gYCkpOwogICAgY29uc3QgcGVyY2VudCA9IHNwYW4oJzAlIGRvbmUnKTsKICAgIGxpLmFwcGVuZENoaWxkKHBlcmNlbnQpOwoKICAgIG91dHB1dEVsZW1lbnQuYXBwZW5kQ2hpbGQobGkpOwoKICAgIGNvbnN0IGZpbGVEYXRhUHJvbWlzZSA9IG5ldyBQcm9taXNlKChyZXNvbHZlKSA9PiB7CiAgICAgIGNvbnN0IHJlYWRlciA9IG5ldyBGaWxlUmVhZGVyKCk7CiAgICAgIHJlYWRlci5vbmxvYWQgPSAoZSkgPT4gewogICAgICAgIHJlc29sdmUoZS50YXJnZXQucmVzdWx0KTsKICAgICAgfTsKICAgICAgcmVhZGVyLnJlYWRBc0FycmF5QnVmZmVyKGZpbGUpOwogICAgfSk7CiAgICAvLyBXYWl0IGZvciB0aGUgZGF0YSB0byBiZSByZWFkeS4KICAgIGxldCBmaWxlRGF0YSA9IHlpZWxkIHsKICAgICAgcHJvbWlzZTogZmlsZURhdGFQcm9taXNlLAogICAgICByZXNwb25zZTogewogICAgICAgIGFjdGlvbjogJ2NvbnRpbnVlJywKICAgICAgfQogICAgfTsKCiAgICAvLyBVc2UgYSBjaHVua2VkIHNlbmRpbmcgdG8gYXZvaWQgbWVzc2FnZSBzaXplIGxpbWl0cy4gU2VlIGIvNjIxMTU2NjAuCiAgICBsZXQgcG9zaXRpb24gPSAwOwogICAgZG8gewogICAgICBjb25zdCBsZW5ndGggPSBNYXRoLm1pbihmaWxlRGF0YS5ieXRlTGVuZ3RoIC0gcG9zaXRpb24sIE1BWF9QQVlMT0FEX1NJWkUpOwogICAgICBjb25zdCBjaHVuayA9IG5ldyBVaW50OEFycmF5KGZpbGVEYXRhLCBwb3NpdGlvbiwgbGVuZ3RoKTsKICAgICAgcG9zaXRpb24gKz0gbGVuZ3RoOwoKICAgICAgY29uc3QgYmFzZTY0ID0gYnRvYShTdHJpbmcuZnJvbUNoYXJDb2RlLmFwcGx5KG51bGwsIGNodW5rKSk7CiAgICAgIHlpZWxkIHsKICAgICAgICByZXNwb25zZTogewogICAgICAgICAgYWN0aW9uOiAnYXBwZW5kJywKICAgICAgICAgIGZpbGU6IGZpbGUubmFtZSwKICAgICAgICAgIGRhdGE6IGJhc2U2NCwKICAgICAgICB9LAogICAgICB9OwoKICAgICAgbGV0IHBlcmNlbnREb25lID0gZmlsZURhdGEuYnl0ZUxlbmd0aCA9PT0gMCA/CiAgICAgICAgICAxMDAgOgogICAgICAgICAgTWF0aC5yb3VuZCgocG9zaXRpb24gLyBmaWxlRGF0YS5ieXRlTGVuZ3RoKSAqIDEwMCk7CiAgICAgIHBlcmNlbnQudGV4dENvbnRlbnQgPSBgJHtwZXJjZW50RG9uZX0lIGRvbmVgOwoKICAgIH0gd2hpbGUgKHBvc2l0aW9uIDwgZmlsZURhdGEuYnl0ZUxlbmd0aCk7CiAgfQoKICAvLyBBbGwgZG9uZS4KICB5aWVsZCB7CiAgICByZXNwb25zZTogewogICAgICBhY3Rpb246ICdjb21wbGV0ZScsCiAgICB9CiAgfTsKfQoKc2NvcGUuZ29vZ2xlID0gc2NvcGUuZ29vZ2xlIHx8IHt9OwpzY29wZS5nb29nbGUuY29sYWIgPSBzY29wZS5nb29nbGUuY29sYWIgfHwge307CnNjb3BlLmdvb2dsZS5jb2xhYi5fZmlsZXMgPSB7CiAgX3VwbG9hZEZpbGVzLAogIF91cGxvYWRGaWxlc0NvbnRpbnVlLAp9Owp9KShzZWxmKTsK&quot;,&quot;status_text&quot;:&quot;&quot;,&quot;headers&quot;:[[&quot;content-type&quot;,&quot;application/javascript&quot;]]}},&quot;base_uri&quot;:&quot;https://localhost:8080/&quot;}" id="V3O_9mkBDH5_" data-outputId="f87cf68c-c8eb-401c-adb7-62d9e57c80c9">

    ``` python
    # Reading in the data into a pandas dataframe object
    df = pd.read_csv("fake_job_postings.csv")
    ```

    </div>
    
##### Looking at the Data

It might seem like it, but <strong>Data Scientists</strong> don't just hit the play button to generate their models.  They could, but how would they know if it's really building the right model to make predictions later?  Fortunately, there are many techniques that we can apply to make sure we're generating models correctly.  Let's explore a few: you can click the play button next to each one to see what it does.  

First, we can see that our dataset has 17880 rows (job descriptions), and 18 columns.  What kind of columns do you see for each job description?  Which one contains the actual job description text that we'll be reading?

    <div class="cell markdown" id="uoDF3byWK-W9">

    ## Exploratory Data Analysis

    </div>

    <div class="cell code" data-execution_count="119" data-colab="{&quot;height&quot;:635,&quot;base_uri&quot;:&quot;https://localhost:8080/&quot;}" id="jSPe416jDruc" data-outputId="b62e00ff-a7ad-43c5-babb-5b263a7c3581" data-scrolled="true">

    ``` python
    print(f"Number of rows {df.shape[0]} \nNumber of columns: {df.shape[1]}")
    df.head()
    ```

    <div class="output stream stdout">

        Number of rows 17880 
        Number of columns: 18

    </div>

    <div class="output execute_result" data-execution_count="119">

    ``` 
       job_id                                      title            location  \
    0       1                           Marketing Intern    US, NY, New York   
    1       2  Customer Service - Cloud Video Production      NZ, , Auckland   
    2       3    Commissioning Machinery Assistant (CMA)       US, IA, Wever   
    3       4          Account Executive - Washington DC  US, DC, Washington   
    4       5                        Bill Review Manager  US, FL, Fort Worth   

      department salary_range                                    company_profile  \
    0  Marketing          NaN  We're Food52, and we've created a groundbreaki...   
    1    Success          NaN  90 Seconds, the worlds Cloud Video Production ...   
    2        NaN          NaN  Valor Services provides Workforce Solutions th...   
    3      Sales          NaN  Our passion for improving quality of life thro...   
    4        NaN          NaN  SpotSource Solutions LLC is a Global Human Cap...   

                                             description  \
    0  Food52, a fast-growing, James Beard Award-winn...   
    1  Organised - Focused - Vibrant - Awesome!Do you...   
    2  Our client, located in Houston, is actively se...   
    3  THE COMPANY: ESRI – Environmental Systems Rese...   
    4  JOB TITLE: Itemization Review ManagerLOCATION:...   

                                            requirements  \
    0  Experience with content management systems a m...   
    1  What we expect from you:Your key responsibilit...   
    2  Implement pre-commissioning and commissioning ...   
    3  EDUCATION: Bachelor’s or Master’s in GIS, busi...   
    4  QUALIFICATIONS:RN license in the State of Texa...   

                                                benefits  telecommuting  \
    0                                                NaN              0   
    1  What you will get from usThrough being part of...              0   
    2                                                NaN              0   
    3  Our culture is anything but corporate—we have ...              0   
    4                              Full Benefits Offered              0   

       has_company_logo  has_questions employment_type required_experience  \
    0                 1              0           Other          Internship   
    1                 1              0       Full-time      Not Applicable   
    2                 1              0             NaN                 NaN   
    3                 1              0       Full-time    Mid-Senior level   
    4                 1              1       Full-time    Mid-Senior level   

      required_education                   industry              function  \
    0                NaN                        NaN             Marketing   
    1                NaN  Marketing and Advertising      Customer Service   
    2                NaN                        NaN                   NaN   
    3  Bachelor's Degree          Computer Software                 Sales   
    4  Bachelor's Degree     Hospital & Health Care  Health Care Provider   

       fraudulent  
    0           0  
    1           0  
    2           0  
    3           0  
    4           0  
    ```

    </div>

    </div>
    
##### Looking for Invalid Data

When dealing with large datasets, and especially data that came from many people across the Internet, the data isn't always as &quot;clean&quot; as you might hope.  Entries might be missing, things can be misspelled, and so on.  Let's look for how many rows are missing column entries by checking for the sum of `isnull` entries (&quot;null&quot; is another way to say empty).  Looks like there are quite a few of them!  Fortunately, the worst columns don't look as important (for example, the department of the company offering the job):   

    <div class="cell code" data-execution_count="120" data-colab="{&quot;base_uri&quot;:&quot;https://localhost:8080/&quot;}" id="A3UJwyl4LiLu" data-outputId="0a183252-ec1c-44e1-8d62-33521616c7f9">

    ``` python
    # Checking for missing values
    df.isnull().sum()
    ```

    <div class="output execute_result" data-execution_count="120">

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

    </div>

    </div>

##### Looking at an Example Job Post

Sometimes, it's helpful to see an example row all by itself.  The `df.loc` code allows us to inspect the <strong>dataframe</strong> (the collection of rows, called `df`) at a location (`loc`) we choose.  Here, we're choosing row `0`, and the `company_profile` column:

    <div class="cell code" data-execution_count="121" data-colab="{&quot;height&quot;:123,&quot;base_uri&quot;:&quot;https://localhost:8080/&quot;}" id="cLlwh4uWNb-M" data-outputId="84619bdd-d0e5-4ad9-f0ca-e9b659d43f9f" data-scrolled="true">

    ``` python
    # Visualizing the first company profile 
    df.loc[0].company_profile
    ```

    <div class="output execute_result" data-execution_count="121">

        "We're Food52, and we've created a groundbreaking and award-winning cooking site. We support, connect, and celebrate home cooks, and give them everything they need in one place.We have a top editorial, business, and engineering team. We're focused on using technology to find new and better ways to connect people around their specific food interests, and to offer them superb, highly curated information about food and cooking. We attract the most talented home cooks and contributors in the country; we also publish well-known professionals like Mario Batali, Gwyneth Paltrow, and Danny Meyer. And we have partnerships with Whole Foods Market and Random House.Food52 has been named the best food website by the James Beard Foundation and IACP, and has been featured in the New York Times, NPR, Pando Daily, TechCrunch, and on the Today Show.We're located in Chelsea, in New York City."

    </div>

    </div>

##### Visualizing the Data

Sometimes, it's helpful to visualize the whole dataset together.  For example, here's a bar chart of the number of fraudulent rows and the number of legitimate rows in the data.

    <div class="cell code" data-execution_count="132">

    ``` python
    # Visualizing how many job postings are actually fraudulent 
    fraudulent_series = df.fraudulent.apply(lambda x: 'fraudlent' if x else 'not_fraudlent')
    fraudulent_series.value_counts(sort=True).plot(kind='barh')
    plt.xlabel('Count')
    plt.ylabel('Fraudulent');
    print(fraudulent_series.value_counts())
    ```

    <div class="output stream stdout">

        Not Fraudulent    17014
        Fraudulent          866
        Name: fraudulent, dtype: int64

    </div>

    <div class="output display_data">

    <p align="center">
    <img style="max-width:100%;" alt="Fraudulent and Non-Fraudulent Job Posting Counts" src="/images/activity-nlp/barchart.png">
    </p>

    </div>

    </div>
    
What do you notice about the number of fraudulent entries in the dataset?  Do you think our algorithm will do better at classifying job posts that are fraudulent or non-fraudulent?  Often, it is better to have about the same number of training examples for each category (called a <strong>class</strong>) that we want to train on.  What do you think we can do about that to help even things up?

#### Training a Model

We'll use the scapy library to read this dataset and look at the fraudulent/non-fraudulent column to find patterns among the job posting training data.  For example, it might find that jobs with very short or very long descriptions tend to be fraudulent (what would happen to legitimate jobs that happen to have long or short descriptions, if this were to occur?).  It might find that jobs with certain words in them tend to be fake.  Unfortunately, it might find that jobs from certain zip codes tend to be fake, even though that's not really the case, because it just happens to see those patterns in the training data.  What can we do to ensure that algorithms don't train on potentially discriminatory inputs?

For now, go ahead and run the training commands:

    <div class="cell markdown" id="ovS21b7dR1kv">

    ## Preparing Data for Training

    </div>

    <div class="cell code" data-execution_count="133" data-colab="{&quot;height&quot;:276,&quot;base_uri&quot;:&quot;https://localhost:8080/&quot;}" id="XLOh8l_LNuOs" data-outputId="103e66f3-e5f5-403e-82c6-d46d02cb38ca">

    ``` python
    # Our training data will come from the description column
    df2 = df[['description','fraudulent']].dropna() # Condenses the dataframe to the two columns and drops null values
    print(df2.shape)
    df2.head()
    ```

    <div class="output stream stdout">

        (17879, 2)

    </div>

    <div class="output execute_result" data-execution_count="133">

    ``` 
                                             description  fraudulent
    0  Food52, a fast-growing, James Beard Award-winn...           0
    1  Organised - Focused - Vibrant - Awesome!Do you...           0
    2  Our client, located in Houston, is actively se...           0
    3  THE COMPANY: ESRI – Environmental Systems Rese...           0
    4  JOB TITLE: Itemization Review ManagerLOCATION:...           0
    ```

    </div>

    </div>

    <div class="cell code" data-execution_count="125" data-colab="{&quot;base_uri&quot;:&quot;https://localhost:8080/&quot;}" id="otl8nQnbR5jw" data-outputId="cc736bfb-d6e9-4cfa-b6de-d2432b868947">

    ``` python
    # Converting the data from a dataframe to the appropriate training format

    # Adds a tuples column for each description and its label
    df2['tuples'] = df2.apply(lambda row: (row['description'], row['fraudulent']), axis = 1)
    # Converts that column to a list
    data = df2['tuples'].to_list()
    # Printing out the first two entries of our formatted dataset 
    data[:2]
    ```

    <div class="output execute_result" data-execution_count="125">

        [('Food52, a fast-growing, James Beard Award-winning online food community and crowd-sourced and curated recipe hub, is currently interviewing full- and part-time unpaid interns to work in a small team of editors, executives, and developers in its New York City headquarters.Reproducing and/or repackaging existing Food52 content for a number of partner sites, such as Huffington Post, Yahoo, Buzzfeed, and more in their various content management systemsResearching blogs and websites for the Provisions by Food52 Affiliate ProgramAssisting in day-to-day affiliate program support, such as screening affiliates and assisting in any affiliate inquiriesSupporting with PR &amp; Events when neededHelping with office administrative work, such as filing, mailing, and preparing for meetingsWorking with developers to document bugs and suggest improvements to the siteSupporting the marketing and executive staff',
          0),
         ("Organised - Focused - Vibrant - Awesome!Do you have a passion for customer service? Slick typing skills? Maybe Account Management? ...And think administration is cooler than a polar bear on a jetski? Then we need to hear you!\xa0We are the Cloud Video Production Service and opperating on a glodal level. Yeah, it's pretty cool. Serious about\xa0delivering a world class product and excellent customer service.Our rapidly expanding business is looking for a talented Project Manager to manage the successful delivery of video projects, manage client communications and drive the production process. Work with some of the coolest brands on the planet and learn from a global team that are representing NZ is a huge way!We are entering the next growth stage of our business and growing quickly internationally. \xa0Therefore, the position is bursting with opportunity for the right person entering the business at the right time.\xa090 Seconds, the worlds Cloud Video Production Service -\xa0http://90#URL_fbe6559afac620a3cd2c22281f7b8d0eef56a73e3d9a311e2f1ca13d081dd630#90 Seconds is the worlds Cloud Video Production Service enabling brands and agencies to get high quality online video content shot and produced anywhere in the world. Fast, affordable, and all managed seamlessly in the cloud from purchase to publish.\xa090 Seconds removes the hassle, cost, risk and speed issues of working with regular video production companies by managing every aspect of video projects in a beautiful online experience. \xa0With a growing network of over 2,000 rated video professionals in over 50 countries and dedicated production success teams in 5 countries guaranteeing video project success 100%. It's as easy as commissioning a quick google adwords campaign.90 Seconds has produced almost 4,000 videos in over 30 Countries for over 500 Global brands including some of the worlds largest including Paypal, L'oreal, Sony and Barclays and has offices in Auckland, London, Sydney, Tokyo &amp; Singapore.Our Auckland office is based\xa0right in the heart of the Wynyard Quarter Innovation Precinct - GridAKL!\xa0",
          0)]

    </div>

    </div>
    
You might notice that the job descriptions have &quot;bad data&quot; in them - funny looking characters and typos.  Data Scientists often write programs to correct or remove these from the data prior to training.  We won't do that today, for the sake of time.  What other things might we take out of the data?  For example, what kinds of words would occur in both fake and legitimate job descriptions (words that everyone uses everyday)?  Should we remove those, too?  These are called <strong>stop words</strong>, and we tend to remove them from the model (words like &quot;and&quot; or &quot;the&quot;).

At the very beginning, do you remember that we said it's a good idea to save a few rows from the dataset, so that we have some to test out the model with while still knowing the answers?  We'll do that right now.  Let's give 80 percent of the data to the model for training, and save the other 20 percent for testing (so it won't know the answers to those, even though we do for checking!).  

    <div class="cell code" data-execution_count="126" id="5E9zkl59WiVI">

    ``` python
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

    </div>

    <div class="cell code" data-execution_count="127">

    ``` python
    # Function to convert the data to spaCy's binary format 
    def create_docbin(dataset, output_name):
        # Creating a blank spacy pipeline to serve as a tokenizer
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

    </div>

    <div class="cell code" data-execution_count="128">

    ``` python
    # Creates the training and test files
    train_docbin = create_docbin(train, 'train')
    test_docbin = create_docbin(test, 'test')
    ```

    <div class="output stream stdout">

        train set contains 14303 documents
        test set contains 3576 documents

    </div>

    </div>

    <div class="cell markdown" id="ToPU5OCxR6Wn">

    ## Training the Model

    </div>

    <div class="cell code" data-execution_count="129">

    ``` python
    # Creating the configuration file for model training
    !python -m spacy init config config.cfg --lang en --pipeline textcat --optimize efficiency --force
    ```

    <div class="output stream stdout">

        ⚠ To generate a more effective transformer-based config (GPU-only),
        install the spacy-transformers package and re-run this command. The config
        generated now does not use transformers.
        ℹ Generated config template specific for your use case
        - Language: en
        - Pipeline: textcat
        - Optimize for: efficiency
        - Hardware: CPU
        - Transformer: None
        ✔ Auto-filled config with all values
        ✔ Saved config
        config.cfg
        You can now add your data and train your pipeline:
        python -m spacy train config.cfg --paths.train ./train.spacy --paths.dev ./dev.spacy

    </div>

    </div>

This last step actually performs the training.  There are a lot of records, so this might take a while.  Come back in 5 or 10 minutes and see if it is done.  You can hit the &quot;stop&quot; button (where the play button usually is) to quit early if you want to move ahead: it will still use whatever data it was able to train on, even if it's not the whole set.

    <div class="cell code" data-execution_count="130">

    ``` python
    # Training the model
    # Max epochs set to 1 epoch for training brevity
    # To complete training, remove "--training.max_epochs 1" from command
    !python -m spacy train config.cfg --output ./output --paths.train ./train.spacy \
    --paths.dev ./test.spacy --training.max_epochs 1
    ```

    <div class="output stream stdout">

        ℹ Saving to output directory: output
        ℹ Using CPU
        
        =========================== Initializing pipeline ===========================
        [2022-06-11 17:43:57,529] [INFO] Set up nlp object from config
        [2022-06-11 17:43:57,539] [INFO] Pipeline: ['textcat']
        [2022-06-11 17:43:57,542] [INFO] Created vocabulary
        [2022-06-11 17:43:57,542] [INFO] Finished initializing nlp object
        [2022-06-11 17:44:39,297] [INFO] Initialized pipeline components: ['textcat']
        ✔ Initialized pipeline
        
        ============================= Training pipeline =============================
        ℹ Pipeline: ['textcat']
        ℹ Initial learn rate: 0.001
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
    </div>

    </div>

#### Testing the Model

Finally, let's test the model on our remaining 20 percent that we saved earlier!  You'll see several scores.  See if you can find the section labeled `Textcat F`: this shows a few columns called `P` (for <strong>precision</strong>) and `R` (for <strong>recall</strong>), out of 100 percent.  Precision tells us how often the model correctly predicted that a job post was fraudulent when it really was fraudulent (or that it was not fraudulent when it really was not).  Recall tells us how often the model didn't give us a &quot;false positive&quot;, and incorrectly guess the answer.  Unlike a math test, when you get back your grade out of 100, we like to evaluate classification models in both directions.  For example, you could say that you can perfectly predict when it is going to rain by saying it's going to rain every day - you'd never be wrong!  Recall allows us to measure how often you guessed it was going to rain on days that it was sunny out.  These scores are kind of merged together into a single score called the <strong>Receiver Operating Characteristic</strong> (ROC), which is a score from 0 to 1 (with 1 being perfect).

    <div class="cell markdown" id="IHt5iNVGR8ZP">

    ## Evaluating the model

    </div>

    <div class="cell code" data-execution_count="131" id="C6jbN5apR9wQ">

    ``` python
    # Evaluating the model using common classification metrics (precision, recall, F1 score)
    !python -m spacy evaluate output/model-best test.spacy --output metrics.json
    ```

    <div class="output stream stdout">

        ℹ Using CPU
        
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
        
        ✔ Saved results to metrics.json

    </div>

    </div>
    
### Follow Up
How did they do?  For us, the model didn't do so well.  There are some things we can do to improve our results.  Can you think of some reasons why this might be, and what some of these things are?

1. How can we improve our current classification model?  For example, how can we improve the imbalance of examples between the fraudulent and non-fraudulent training classes?

2. Identify key features (e.g., entities, words, phrases) of job descriptions that belong to the fraudulent class

3. Perform Exploratory Data Analysis (EDA) to find interesting insights (e.g., most frequent words, what departments have the most fraudulent advertisements, etc.)

4. (Advanced): Create a classification model using all the features to predict fraudluent jobs (e.g, using logisitic regression, random forest, etc.)