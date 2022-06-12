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

    <div class="jp-Cell jp-MarkdownCell jp-Notebook-cell">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea"><div class="jp-InputPrompt jp-InputArea-prompt">
    </div><div class="jp-RenderedHTMLCommon jp-RenderedMarkdown jp-MarkdownOutput " data-mime-type="text/markdown">
    <h1 id="Text-Classification-Demo">Text Classification Demo<a class="anchor-link" href="#Text-Classification-Demo">&#182;</a></h1>
    </div>
    </div>
    </div>
    </div>
    <div class="jp-Cell jp-MarkdownCell jp-Notebook-cell">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea"><div class="jp-InputPrompt jp-InputArea-prompt">
    </div><div class="jp-RenderedHTMLCommon jp-RenderedMarkdown jp-MarkdownOutput " data-mime-type="text/markdown">
    <h2 id="Getting-Repo-from-GitHub-in-Google-Colab">Getting Repo from GitHub in Google Colab<a class="anchor-link" href="#Getting-Repo-from-GitHub-in-Google-Colab">&#182;</a></h2>
    </div>
    </div>
    </div>
    </div><div class="jp-Cell jp-CodeCell jp-Notebook-cell jp-mod-noOutputs  ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[&nbsp;]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># If in Google Colab, change to content directory</span>
    <span class="o">%</span><span class="n">cd</span> <span class="o">/</span><span class="n">content</span><span class="o">/</span>
    <span class="c1"># If project exists on local, pull the latest changes, otherwise clone it</span>
    <span class="err">!</span><span class="k">if</span> <span class="n">cd</span> <span class="n">NLPDemo</span><span class="p">;</span> <span class="n">then</span> <span class="n">git</span> <span class="n">pull</span><span class="p">;</span> <span class="k">else</span> <span class="n">git</span> <span class="n">clone</span> <span class="n">https</span><span class="p">:</span><span class="o">//</span><span class="n">github</span><span class="o">.</span><span class="n">com</span><span class="o">/</span><span class="n">azucker99</span><span class="o">/</span><span class="n">NLPDemo</span><span class="o">.</span><span class="n">git</span> <span class="n">NLPDemo</span><span class="p">;</span> <span class="n">fi</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    </div><div class="jp-Cell jp-CodeCell jp-Notebook-cell   ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[6]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Changing to the project directory</span>
    <span class="o">%</span><span class="n">cd</span> <span class="n">NLPDemo</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

Next, click the play button next to the snippet that says `!pip install -r requirements.txt`.  This will install the spacy library and the other library functions from the Internet that will help us to build our model.  It will look like this, and when it is done, there might be a button to <strong>Restart</strong> at the bottom (go ahead and click on it if you see this).

    <div class="jp-Cell jp-MarkdownCell jp-Notebook-cell">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea"><div class="jp-InputPrompt jp-InputArea-prompt">
    </div><div class="jp-RenderedHTMLCommon jp-RenderedMarkdown jp-MarkdownOutput " data-mime-type="text/markdown">
    <h2 id="Imports-and-Downloads">Imports and Downloads<a class="anchor-link" href="#Imports-and-Downloads">&#182;</a></h2>
    </div>
    </div>
    </div>
    </div><div class="jp-Cell jp-CodeCell jp-Notebook-cell   ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[7]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Installing all the required libraries </span>
    <span class="o">%</span><span class="n">pip</span> <span class="n">install</span> <span class="o">-</span><span class="n">r</span> <span class="n">requirements</span><span class="o">.</span><span class="n">txt</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    <div class="jp-Cell-outputWrapper">
    <div class="jp-Collapser jp-OutputCollapser jp-Cell-outputCollapser">
    </div>


    <div class="jp-OutputArea jp-Cell-outputArea">

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt"></div>


    <div class="jp-RenderedText jp-OutputArea-output" data-mime-type="text/plain">
    <pre>Collecting spacy==3.0.6
      Downloading spacy-3.0.6.tar.gz (7.1 MB)
         <span style="color: rgb(114,156,31)">━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━</span> <span class="ansi-green-fg">7.1/7.1 MB</span> <span class="ansi-red-fg">3.4 MB/s</span> eta <span class="ansi-cyan-fg">0:00:00</span>m eta <span class="ansi-cyan-fg">0:00:01</span>0:01:01
      Installing build dependencies ... done
      Getting requirements to build wheel ... done
      Installing backend dependencies ... done
      Preparing metadata (pyproject.toml) ... done
    Requirement already satisfied: pandas in ./nlpdemo/lib/python3.10/site-packages (from -r requirements.txt (line 2)) (1.4.2)
    Requirement already satisfied: numpy in ./nlpdemo/lib/python3.10/site-packages (from -r requirements.txt (line 3)) (1.22.4)
    Requirement already satisfied: matplotlib in ./nlpdemo/lib/python3.10/site-packages (from -r requirements.txt (line 4)) (3.5.2)
    Requirement already satisfied: sklearn in ./nlpdemo/lib/python3.10/site-packages (from -r requirements.txt (line 5)) (0.0)
    Requirement already satisfied: jinja2 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (3.1.2)
    Requirement already satisfied: blis&lt;0.8.0,&gt;=0.4.0 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (0.7.7)
    Requirement already satisfied: packaging&gt;=20.0 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (21.3)
    Requirement already satisfied: cymem&lt;2.1.0,&gt;=2.0.2 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (2.0.6)
    Requirement already satisfied: spacy-legacy&lt;3.1.0,&gt;=3.0.4 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (3.0.9)
    Collecting typer&lt;0.4.0,&gt;=0.3.0
      Downloading typer-0.3.2-py3-none-any.whl (21 kB)
    Collecting pydantic&lt;1.8.0,&gt;=1.7.1
      Downloading pydantic-1.7.4-py3-none-any.whl (107 kB)
         <span style="color: rgb(114,156,31)">━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━</span> <span class="ansi-green-fg">107.9/107.9 KB</span> <span class="ansi-red-fg">6.9 MB/s</span> eta <span class="ansi-cyan-fg">0:00:00</span>
    Requirement already satisfied: setuptools in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (62.1.0)
    Requirement already satisfied: wasabi&lt;1.1.0,&gt;=0.8.1 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (0.9.1)
    Requirement already satisfied: preshed&lt;3.1.0,&gt;=3.0.2 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (3.0.6)
    Requirement already satisfied: thinc&lt;8.1.0,&gt;=8.0.3 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (8.0.17)
    Requirement already satisfied: requests&lt;3.0.0,&gt;=2.13.0 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (2.28.0)
    Requirement already satisfied: srsly&lt;3.0.0,&gt;=2.4.1 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (2.4.3)
    Requirement already satisfied: pathy&gt;=0.3.5 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (0.6.1)
    Requirement already satisfied: murmurhash&lt;1.1.0,&gt;=0.28.0 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (1.0.7)
    Requirement already satisfied: catalogue&lt;2.1.0,&gt;=2.0.3 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (2.0.7)
    Requirement already satisfied: tqdm&lt;5.0.0,&gt;=4.38.0 in ./nlpdemo/lib/python3.10/site-packages (from spacy==3.0.6-&gt;-r requirements.txt (line 1)) (4.64.0)
    Requirement already satisfied: python-dateutil&gt;=2.8.1 in ./nlpdemo/lib/python3.10/site-packages (from pandas-&gt;-r requirements.txt (line 2)) (2.8.2)
    Requirement already satisfied: pytz&gt;=2020.1 in ./nlpdemo/lib/python3.10/site-packages (from pandas-&gt;-r requirements.txt (line 2)) (2022.1)
    Requirement already satisfied: cycler&gt;=0.10 in ./nlpdemo/lib/python3.10/site-packages (from matplotlib-&gt;-r requirements.txt (line 4)) (0.11.0)
    Requirement already satisfied: kiwisolver&gt;=1.0.1 in ./nlpdemo/lib/python3.10/site-packages (from matplotlib-&gt;-r requirements.txt (line 4)) (1.4.2)
    Requirement already satisfied: pillow&gt;=6.2.0 in ./nlpdemo/lib/python3.10/site-packages (from matplotlib-&gt;-r requirements.txt (line 4)) (9.1.1)
    Requirement already satisfied: pyparsing&gt;=2.2.1 in ./nlpdemo/lib/python3.10/site-packages (from matplotlib-&gt;-r requirements.txt (line 4)) (3.0.9)
    Requirement already satisfied: fonttools&gt;=4.22.0 in ./nlpdemo/lib/python3.10/site-packages (from matplotlib-&gt;-r requirements.txt (line 4)) (4.33.3)
    Requirement already satisfied: scikit-learn in ./nlpdemo/lib/python3.10/site-packages (from sklearn-&gt;-r requirements.txt (line 5)) (1.1.1)
    Requirement already satisfied: smart-open&lt;6.0.0,&gt;=5.0.0 in ./nlpdemo/lib/python3.10/site-packages (from pathy&gt;=0.3.5-&gt;spacy==3.0.6-&gt;-r requirements.txt (line 1)) (5.2.1)
    Requirement already satisfied: six&gt;=1.5 in ./nlpdemo/lib/python3.10/site-packages (from python-dateutil&gt;=2.8.1-&gt;pandas-&gt;-r requirements.txt (line 2)) (1.16.0)
    Requirement already satisfied: urllib3&lt;1.27,&gt;=1.21.1 in ./nlpdemo/lib/python3.10/site-packages (from requests&lt;3.0.0,&gt;=2.13.0-&gt;spacy==3.0.6-&gt;-r requirements.txt (line 1)) (1.26.9)
    Requirement already satisfied: certifi&gt;=2017.4.17 in ./nlpdemo/lib/python3.10/site-packages (from requests&lt;3.0.0,&gt;=2.13.0-&gt;spacy==3.0.6-&gt;-r requirements.txt (line 1)) (2022.5.18.1)
    Requirement already satisfied: charset-normalizer~=2.0.0 in ./nlpdemo/lib/python3.10/site-packages (from requests&lt;3.0.0,&gt;=2.13.0-&gt;spacy==3.0.6-&gt;-r requirements.txt (line 1)) (2.0.12)
    Requirement already satisfied: idna&lt;4,&gt;=2.5 in ./nlpdemo/lib/python3.10/site-packages (from requests&lt;3.0.0,&gt;=2.13.0-&gt;spacy==3.0.6-&gt;-r requirements.txt (line 1)) (3.3)
    Collecting click&lt;7.2.0,&gt;=7.1.1
      Downloading click-7.1.2-py2.py3-none-any.whl (82 kB)
         <span style="color: rgb(114,156,31)">━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━</span> <span class="ansi-green-fg">82.8/82.8 KB</span> <span class="ansi-red-fg">9.8 MB/s</span> eta <span class="ansi-cyan-fg">0:00:00</span>
    Requirement already satisfied: MarkupSafe&gt;=2.0 in ./nlpdemo/lib/python3.10/site-packages (from jinja2-&gt;spacy==3.0.6-&gt;-r requirements.txt (line 1)) (2.1.1)
    Requirement already satisfied: threadpoolctl&gt;=2.0.0 in ./nlpdemo/lib/python3.10/site-packages (from scikit-learn-&gt;sklearn-&gt;-r requirements.txt (line 5)) (3.1.0)
    Requirement already satisfied: scipy&gt;=1.3.2 in ./nlpdemo/lib/python3.10/site-packages (from scikit-learn-&gt;sklearn-&gt;-r requirements.txt (line 5)) (1.8.1)
    Requirement already satisfied: joblib&gt;=1.0.0 in ./nlpdemo/lib/python3.10/site-packages (from scikit-learn-&gt;sklearn-&gt;-r requirements.txt (line 5)) (1.1.0)
    Building wheels for collected packages: spacy
      Building wheel for spacy (pyproject.toml) ... done
      Created wheel for spacy: filename=spacy-3.0.6-cp310-cp310-linux_x86_64.whl size=32114650 sha256=5f27d54b22927f9a4e16d62cb22efc684fe87e2da4d6f72cedeca641ea55ac57
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
    <span class="ansi-yellow-fg">WARNING: You are using pip version 22.0.4; however, version 22.1.2 is available.
    You should consider upgrading via the &#39;/home/azucker/projects/NLPDemo/nlpdemo/bin/python -m pip install --upgrade pip&#39; command.</span><span class="ansi-yellow-fg">
    </span>Note: you may need to restart the kernel to use updated packages.
    </pre>
    </div>
    </div>

    </div>

    </div>

    </div>
    
Similarly, click the next play button to tell our Python program that we're going to use the libraries we just installed:

    <div class="jp-Cell jp-CodeCell jp-Notebook-cell jp-mod-noOutputs  ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[117]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="kn">import</span> <span class="nn">pandas</span> <span class="k">as</span> <span class="nn">pd</span>
    <span class="kn">import</span> <span class="nn">numpy</span> <span class="k">as</span> <span class="nn">np</span>
    <span class="kn">import</span> <span class="nn">matplotlib.pyplot</span> <span class="k">as</span> <span class="nn">plt</span>
    <span class="kn">import</span> <span class="nn">spacy</span>
    <span class="kn">from</span> <span class="nn">spacy.tokens</span> <span class="kn">import</span> <span class="n">DocBin</span>
    <span class="kn">import</span> <span class="nn">random</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    </div>
    
#### Reading the Training Data into an NLP Model

Now we're ready to create and train the model!  We'll read job description data from a website called [Kaggle](https://www.kaggle.com/datasets/shivamb/real-or-fake-fake-jobposting-prediction), which is a data science website that features tutorials, examples, and challenges that you can try.  With a Python notebook, that's as easy as hitting the play button again, which will look like the below!

    <div class="jp-Cell jp-MarkdownCell jp-Notebook-cell">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea"><div class="jp-InputPrompt jp-InputArea-prompt">
    </div><div class="jp-RenderedHTMLCommon jp-RenderedMarkdown jp-MarkdownOutput " data-mime-type="text/markdown">
    <h2 id="Loading-in-the-data">Loading in the data<a class="anchor-link" href="#Loading-in-the-data">&#182;</a></h2><p>Data comes from kaggle: <a href="https://www.kaggle.com/datasets/shivamb/real-or-fake-fake-jobposting-prediction">https://www.kaggle.com/datasets/shivamb/real-or-fake-fake-jobposting-prediction</a></p>

    </div>
    </div>
    </div>
    </div><div class="jp-Cell jp-CodeCell jp-Notebook-cell jp-mod-noOutputs  ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[118]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Reading in the data into a pandas dataframe object</span>
    <span class="n">df</span> <span class="o">=</span> <span class="n">pd</span><span class="o">.</span><span class="n">read_csv</span><span class="p">(</span><span class="s2">&quot;fake_job_postings.csv&quot;</span><span class="p">)</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    </div>
    
##### Looking at the Data

It might seem like it, but <strong>Data Scientists</strong> don't just hit the play button to generate their models.  They could, but how would they know if it's really building the right model to make predictions later?  Fortunately, there are many techniques that we can apply to make sure we're generating models correctly.  Let's explore a few: you can click the play button next to each one to see what it does.  

First, we can see that our dataset has 17880 rows (job descriptions), and 18 columns.  What kind of columns do you see for each job description?  Which one contains the actual job description text that we'll be reading?

    <div class="jp-Cell jp-MarkdownCell jp-Notebook-cell">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea"><div class="jp-InputPrompt jp-InputArea-prompt">
    </div><div class="jp-RenderedHTMLCommon jp-RenderedMarkdown jp-MarkdownOutput " data-mime-type="text/markdown">
    <h2 id="Exploratory-Data-Analysis">Exploratory Data Analysis<a class="anchor-link" href="#Exploratory-Data-Analysis">&#182;</a></h2>
    </div>
    </div>
    </div>
    </div><div class="jp-Cell jp-CodeCell jp-Notebook-cell   ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[119]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="nb">print</span><span class="p">(</span><span class="sa">f</span><span class="s2">&quot;Number of rows </span><span class="si">{</span><span class="n">df</span><span class="o">.</span><span class="n">shape</span><span class="p">[</span><span class="mi">0</span><span class="p">]</span><span class="si">}</span><span class="s2"> </span><span class="se">\n</span><span class="s2">Number of columns: </span><span class="si">{</span><span class="n">df</span><span class="o">.</span><span class="n">shape</span><span class="p">[</span><span class="mi">1</span><span class="p">]</span><span class="si">}</span><span class="s2">&quot;</span><span class="p">)</span>
    <span class="n">df</span><span class="o">.</span><span class="n">head</span><span class="p">()</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    <div class="jp-Cell-outputWrapper">
    <div class="jp-Collapser jp-OutputCollapser jp-Cell-outputCollapser">
    </div>


    <div class="jp-OutputArea jp-Cell-outputArea">

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt"></div>


    <div class="jp-RenderedText jp-OutputArea-output" data-mime-type="text/plain">
    <pre>Number of rows 17880 
    Number of columns: 18
    </pre>
    </div>
    </div>

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt">Out[119]:</div>



    <div class="jp-RenderedHTMLCommon jp-RenderedHTML jp-OutputArea-output jp-OutputArea-executeResult" data-mime-type="text/html">
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
          <th>job_id</th>
          <th>title</th>
          <th>location</th>
          <th>department</th>
          <th>salary_range</th>
          <th>company_profile</th>
          <th>description</th>
          <th>requirements</th>
          <th>benefits</th>
          <th>telecommuting</th>
          <th>has_company_logo</th>
          <th>has_questions</th>
          <th>employment_type</th>
          <th>required_experience</th>
          <th>required_education</th>
          <th>industry</th>
          <th>function</th>
          <th>fraudulent</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>1</td>
          <td>Marketing Intern</td>
          <td>US, NY, New York</td>
          <td>Marketing</td>
          <td>NaN</td>
          <td>We're Food52, and we've created a groundbreaki...</td>
          <td>Food52, a fast-growing, James Beard Award-winn...</td>
          <td>Experience with content management systems a m...</td>
          <td>NaN</td>
          <td>0</td>
          <td>1</td>
          <td>0</td>
          <td>Other</td>
          <td>Internship</td>
          <td>NaN</td>
          <td>NaN</td>
          <td>Marketing</td>
          <td>0</td>
        </tr>
        <tr>
          <th>1</th>
          <td>2</td>
          <td>Customer Service - Cloud Video Production</td>
          <td>NZ, , Auckland</td>
          <td>Success</td>
          <td>NaN</td>
          <td>90 Seconds, the worlds Cloud Video Production ...</td>
          <td>Organised - Focused - Vibrant - Awesome!Do you...</td>
          <td>What we expect from you:Your key responsibilit...</td>
          <td>What you will get from usThrough being part of...</td>
          <td>0</td>
          <td>1</td>
          <td>0</td>
          <td>Full-time</td>
          <td>Not Applicable</td>
          <td>NaN</td>
          <td>Marketing and Advertising</td>
          <td>Customer Service</td>
          <td>0</td>
        </tr>
        <tr>
          <th>2</th>
          <td>3</td>
          <td>Commissioning Machinery Assistant (CMA)</td>
          <td>US, IA, Wever</td>
          <td>NaN</td>
          <td>NaN</td>
          <td>Valor Services provides Workforce Solutions th...</td>
          <td>Our client, located in Houston, is actively se...</td>
          <td>Implement pre-commissioning and commissioning ...</td>
          <td>NaN</td>
          <td>0</td>
          <td>1</td>
          <td>0</td>
          <td>NaN</td>
          <td>NaN</td>
          <td>NaN</td>
          <td>NaN</td>
          <td>NaN</td>
          <td>0</td>
        </tr>
        <tr>
          <th>3</th>
          <td>4</td>
          <td>Account Executive - Washington DC</td>
          <td>US, DC, Washington</td>
          <td>Sales</td>
          <td>NaN</td>
          <td>Our passion for improving quality of life thro...</td>
          <td>THE COMPANY: ESRI – Environmental Systems Rese...</td>
          <td>EDUCATION: Bachelor’s or Master’s in GIS, busi...</td>
          <td>Our culture is anything but corporate—we have ...</td>
          <td>0</td>
          <td>1</td>
          <td>0</td>
          <td>Full-time</td>
          <td>Mid-Senior level</td>
          <td>Bachelor's Degree</td>
          <td>Computer Software</td>
          <td>Sales</td>
          <td>0</td>
        </tr>
        <tr>
          <th>4</th>
          <td>5</td>
          <td>Bill Review Manager</td>
          <td>US, FL, Fort Worth</td>
          <td>NaN</td>
          <td>NaN</td>
          <td>SpotSource Solutions LLC is a Global Human Cap...</td>
          <td>JOB TITLE: Itemization Review ManagerLOCATION:...</td>
          <td>QUALIFICATIONS:RN license in the State of Texa...</td>
          <td>Full Benefits Offered</td>
          <td>0</td>
          <td>1</td>
          <td>1</td>
          <td>Full-time</td>
          <td>Mid-Senior level</td>
          <td>Bachelor's Degree</td>
          <td>Hospital &amp; Health Care</td>
          <td>Health Care Provider</td>
          <td>0</td>
        </tr>
      </tbody>
    </table>
    </div>
    </div>

    </div>

    </div>

    </div>

    </div>

    <div class="jp-Cell jp-CodeCell jp-Notebook-cell   ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[120]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Checking for missing values</span>
    <span class="n">df</span><span class="o">.</span><span class="n">isnull</span><span class="p">()</span><span class="o">.</span><span class="n">sum</span><span class="p">()</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    <div class="jp-Cell-outputWrapper">
    <div class="jp-Collapser jp-OutputCollapser jp-Cell-outputCollapser">
    </div>


    <div class="jp-OutputArea jp-Cell-outputArea">

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt">Out[120]:</div>




    <div class="jp-RenderedText jp-OutputArea-output jp-OutputArea-executeResult" data-mime-type="text/plain">
    <pre>job_id                     0
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
    dtype: int64</pre>
    </div>

    </div>

    </div>

    </div>

    </div>

Below, what do you notice about the number of fraudulent entries in the dataset?  Do you think our algorithm will do better at classifying job posts that are fraudulent or non-fraudulent?  Often, it is better to have about the same number of training examples for each category (called a <strong>class</strong>) that we want to train on.  What do you think we can do about that to help even things up?
    
    <div class="jp-Cell jp-CodeCell jp-Notebook-cell   ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[121]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Visualizing the first company profile </span>
    <span class="n">df</span><span class="o">.</span><span class="n">loc</span><span class="p">[</span><span class="mi">0</span><span class="p">]</span><span class="o">.</span><span class="n">company_profile</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    <div class="jp-Cell-outputWrapper">
    <div class="jp-Collapser jp-OutputCollapser jp-Cell-outputCollapser">
    </div>


    <div class="jp-OutputArea jp-Cell-outputArea">

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt">Out[121]:</div>




    <div class="jp-RenderedText jp-OutputArea-output jp-OutputArea-executeResult" data-mime-type="text/plain">
    <pre>&#34;We&#39;re Food52, and we&#39;ve created a groundbreaking and award-winning cooking site. We support, connect, and celebrate home cooks, and give them everything they need in one place.We have a top editorial, business, and engineering team. We&#39;re focused on using technology to find new and better ways to connect people around their specific food interests, and to offer them superb, highly curated information about food and cooking. We attract the most talented home cooks and contributors in the country; we also publish well-known professionals like Mario Batali, Gwyneth Paltrow, and Danny Meyer. And we have partnerships with Whole Foods Market and Random House.Food52 has been named the best food website by the James Beard Foundation and IACP, and has been featured in the New York Times, NPR, Pando Daily, TechCrunch, and on the Today Show.We&#39;re located in Chelsea, in New York City.&#34;</pre>
    </div>

    </div>

    </div>

    </div>

    </div><div class="jp-Cell jp-CodeCell jp-Notebook-cell   ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[132]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Visualizing how many job postings are actually fraudulent </span>
    <span class="n">fraudulent_series</span> <span class="o">=</span> <span class="n">df</span><span class="o">.</span><span class="n">fraudulent</span><span class="o">.</span><span class="n">apply</span><span class="p">(</span><span class="k">lambda</span> <span class="n">x</span><span class="p">:</span> <span class="s1">&#39;fraudlent&#39;</span> <span class="k">if</span> <span class="n">x</span> <span class="k">else</span> <span class="s1">&#39;not_fraudlent&#39;</span><span class="p">)</span>
    <span class="n">fraudulent_series</span><span class="o">.</span><span class="n">value_counts</span><span class="p">(</span><span class="n">sort</span><span class="o">=</span><span class="kc">True</span><span class="p">)</span><span class="o">.</span><span class="n">plot</span><span class="p">(</span><span class="n">kind</span><span class="o">=</span><span class="s1">&#39;barh&#39;</span><span class="p">)</span>
    <span class="n">plt</span><span class="o">.</span><span class="n">xlabel</span><span class="p">(</span><span class="s1">&#39;Count&#39;</span><span class="p">)</span>
    <span class="n">plt</span><span class="o">.</span><span class="n">ylabel</span><span class="p">(</span><span class="s1">&#39;Fraudlent&#39;</span><span class="p">);</span>
    <span class="nb">print</span><span class="p">(</span><span class="n">fraudulent_series</span><span class="o">.</span><span class="n">value_counts</span><span class="p">())</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    <div class="jp-Cell-outputWrapper">
    <div class="jp-Collapser jp-OutputCollapser jp-Cell-outputCollapser">
    </div>


    <div class="jp-OutputArea jp-Cell-outputArea">

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt"></div>


    <div class="jp-RenderedText jp-OutputArea-output" data-mime-type="text/plain">
    <pre>Not Fraudlent    17014
    fraudlent          866
    Name: fraudulent, dtype: int64
    </pre>
    </div>
    </div>

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt"></div>




    <div class="jp-RenderedImage jp-OutputArea-output ">
    <img style="max-width: 100%;" src="data:image/png;base64,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
    "
    class="
    jp-needs-light-background
    "
    >
    </div>

    </div>

    </div>

    </div>

    </div>    

#### Training a Model

We'll use the scapy library to read this dataset and look at the fraudulent/non-fraudulent column to find patterns among the job posting training data.  For example, it might find that jobs with very short or very long descriptions tend to be fraudulent (what would happen to legitimate jobs that happen to have long or short descriptions, if this were to occur?).  It might find that jobs with certain words in them tend to be fake.  Unfortunately, it might find that jobs from certain zip codes tend to be fake, even though that's not really the case, because it just happens to see those patterns in the training data.  What can we do to ensure that algorithms don't train on potentially discriminatory inputs?

For now, go ahead and run the training commands:

    <div class="jp-Cell jp-MarkdownCell jp-Notebook-cell">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea"><div class="jp-InputPrompt jp-InputArea-prompt">
    </div><div class="jp-RenderedHTMLCommon jp-RenderedMarkdown jp-MarkdownOutput " data-mime-type="text/markdown">
    <h2 id="Preparing-Data-for-Training">Preparing Data for Training<a class="anchor-link" href="#Preparing-Data-for-Training">&#182;</a></h2>
    </div>
    </div>
    </div>
    </div><div class="jp-Cell jp-CodeCell jp-Notebook-cell   ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[133]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Our training data will come from the description column</span>
    <span class="n">df2</span> <span class="o">=</span> <span class="n">df</span><span class="p">[[</span><span class="s1">&#39;description&#39;</span><span class="p">,</span><span class="s1">&#39;fraudulent&#39;</span><span class="p">]]</span><span class="o">.</span><span class="n">dropna</span><span class="p">()</span> <span class="c1"># Condenses the dataframe to the two columns and drops null values</span>
    <span class="nb">print</span><span class="p">(</span><span class="n">df2</span><span class="o">.</span><span class="n">shape</span><span class="p">)</span>
    <span class="n">df2</span><span class="o">.</span><span class="n">head</span><span class="p">()</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    <div class="jp-Cell-outputWrapper">
    <div class="jp-Collapser jp-OutputCollapser jp-Cell-outputCollapser">
    </div>


    <div class="jp-OutputArea jp-Cell-outputArea">

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt"></div>


    <div class="jp-RenderedText jp-OutputArea-output" data-mime-type="text/plain">
    <pre>(17879, 2)
    </pre>
    </div>
    </div>

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt">Out[133]:</div>



    <div class="jp-RenderedHTMLCommon jp-RenderedHTML jp-OutputArea-output jp-OutputArea-executeResult" data-mime-type="text/html">
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
    </div>

    </div>

    </div>

    </div>

    </div>
    
    <div class="jp-Cell jp-CodeCell jp-Notebook-cell   ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[125]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Converting the data from a dataframe to the appropriate training format</span>

    <span class="c1"># Adds a tuples column for each description and its label</span>
    <span class="n">df2</span><span class="p">[</span><span class="s1">&#39;tuples&#39;</span><span class="p">]</span> <span class="o">=</span> <span class="n">df2</span><span class="o">.</span><span class="n">apply</span><span class="p">(</span><span class="k">lambda</span> <span class="n">row</span><span class="p">:</span> <span class="p">(</span><span class="n">row</span><span class="p">[</span><span class="s1">&#39;description&#39;</span><span class="p">],</span> <span class="n">row</span><span class="p">[</span><span class="s1">&#39;fraudulent&#39;</span><span class="p">]),</span> <span class="n">axis</span> <span class="o">=</span> <span class="mi">1</span><span class="p">)</span>
    <span class="c1"># Converts that column to a list</span>
    <span class="n">data</span> <span class="o">=</span> <span class="n">df2</span><span class="p">[</span><span class="s1">&#39;tuples&#39;</span><span class="p">]</span><span class="o">.</span><span class="n">to_list</span><span class="p">()</span>
    <span class="c1"># Printing out the first two entries of our formatted dataset </span>
    <span class="n">data</span><span class="p">[:</span><span class="mi">2</span><span class="p">]</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    <div class="jp-Cell-outputWrapper">
    <div class="jp-Collapser jp-OutputCollapser jp-Cell-outputCollapser">
    </div>


    <div class="jp-OutputArea jp-Cell-outputArea">

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt">Out[125]:</div>




    <div class="jp-RenderedText jp-OutputArea-output jp-OutputArea-executeResult" data-mime-type="text/plain">
    <pre>[(&#39;Food52, a fast-growing, James Beard Award-winning online food community and crowd-sourced and curated recipe hub, is currently interviewing full- and part-time unpaid interns to work in a small team of editors, executives, and developers in its New York City headquarters.Reproducing and/or repackaging existing Food52 content for a number of partner sites, such as Huffington Post, Yahoo, Buzzfeed, and more in their various content management systemsResearching blogs and websites for the Provisions by Food52 Affiliate ProgramAssisting in day-to-day affiliate program support, such as screening affiliates and assisting in any affiliate inquiriesSupporting with PR &amp;amp; Events when neededHelping with office administrative work, such as filing, mailing, and preparing for meetingsWorking with developers to document bugs and suggest improvements to the siteSupporting the marketing and executive staff&#39;,
      0),
     (&#34;Organised - Focused - Vibrant - Awesome!Do you have a passion for customer service? Slick typing skills? Maybe Account Management? ...And think administration is cooler than a polar bear on a jetski? Then we need to hear you!\xa0We are the Cloud Video Production Service and opperating on a glodal level. Yeah, it&#39;s pretty cool. Serious about\xa0delivering a world class product and excellent customer service.Our rapidly expanding business is looking for a talented Project Manager to manage the successful delivery of video projects, manage client communications and drive the production process. Work with some of the coolest brands on the planet and learn from a global team that are representing NZ is a huge way!We are entering the next growth stage of our business and growing quickly internationally. \xa0Therefore, the position is bursting with opportunity for the right person entering the business at the right time.\xa090 Seconds, the worlds Cloud Video Production Service -\xa0http://90#URL_fbe6559afac620a3cd2c22281f7b8d0eef56a73e3d9a311e2f1ca13d081dd630#90 Seconds is the worlds Cloud Video Production Service enabling brands and agencies to get high quality online video content shot and produced anywhere in the world. Fast, affordable, and all managed seamlessly in the cloud from purchase to publish.\xa090 Seconds removes the hassle, cost, risk and speed issues of working with regular video production companies by managing every aspect of video projects in a beautiful online experience. \xa0With a growing network of over 2,000 rated video professionals in over 50 countries and dedicated production success teams in 5 countries guaranteeing video project success 100%. It&#39;s as easy as commissioning a quick google adwords campaign.90 Seconds has produced almost 4,000 videos in over 30 Countries for over 500 Global brands including some of the worlds largest including Paypal, L&#39;oreal, Sony and Barclays and has offices in Auckland, London, Sydney, Tokyo &amp;amp; Singapore.Our Auckland office is based\xa0right in the heart of the Wynyard Quarter Innovation Precinct - GridAKL!\xa0&#34;,
      0)]</pre>
    </div>

    </div>

    </div>

    </div>

    </div>    
    
You might notice that the job descriptions have &quot;bad data&quot; in them - funny looking characters and typos.  Data Scientists often write programs to correct or remove these from the data prior to training.  We won't do that today, for the sake of time.  What other things might we take out of the data?  For example, what kinds of words would occur in both fake and legitimate job descriptions (words that everyone uses everyday)?  Should we remove those, too?  These are called <strong>stop words</strong>, and we tend to remove them from the model (words like &quot;and&quot; or &quot;the&quot;).

At the very beginning, do you remember that we said it's a good idea to save a few rows from the dataset, so that we have some to test out the model with while still knowing the answers?  We'll do that right now.  Let's give 80 percent of the data to the model for training, and save the other 20 percent for testing (so it won't know the answers to those, even though we do for checking!).  

    <div class="jp-Cell jp-CodeCell jp-Notebook-cell jp-mod-noOutputs  ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[126]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Splitting data into training and testing data </span>
    <span class="n">train_size</span> <span class="o">=</span> <span class="mf">0.8</span> <span class="c1"># Data will consist of 80% of the training set </span>
    <span class="n">split</span> <span class="o">=</span> <span class="nb">int</span><span class="p">(</span><span class="nb">len</span><span class="p">(</span><span class="n">data</span><span class="p">)</span><span class="o">*</span><span class="n">train_size</span><span class="p">)</span> <span class="c1"># The number of documents that will be in the training set</span>

    <span class="c1"># Setting seed for reproducibility</span>
    <span class="n">random</span><span class="o">.</span><span class="n">seed</span><span class="p">(</span><span class="mi">42</span><span class="p">)</span>
    <span class="n">random</span><span class="o">.</span><span class="n">shuffle</span><span class="p">(</span><span class="n">data</span><span class="p">)</span> <span class="c1"># Shuffling the data so the model does not learn from the order</span>

    <span class="c1"># Setting train and test set by indexing data to be before and after split </span>
    <span class="n">train</span> <span class="o">=</span> <span class="n">data</span><span class="p">[:</span><span class="n">split</span><span class="p">]</span>
    <span class="n">test</span> <span class="o">=</span> <span class="n">data</span><span class="p">[</span><span class="n">split</span><span class="p">:]</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    </div><div class="jp-Cell jp-CodeCell jp-Notebook-cell jp-mod-noOutputs  ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[127]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Function to convert the data to spaCy&#39;s binary format </span>
    <span class="k">def</span> <span class="nf">create_docbin</span><span class="p">(</span><span class="n">dataset</span><span class="p">,</span> <span class="n">output_name</span><span class="p">):</span>
        <span class="c1"># Creating a blank spacy pipeline to serve as a tokenizer</span>
        <span class="n">nlp</span> <span class="o">=</span> <span class="n">spacy</span><span class="o">.</span><span class="n">blank</span><span class="p">(</span><span class="s1">&#39;en&#39;</span><span class="p">)</span>
        <span class="c1"># Creating a DocBin object to hold spaCy documents</span>
        <span class="n">doc_bin</span> <span class="o">=</span> <span class="n">DocBin</span><span class="p">()</span>
        <span class="c1"># Converting each tuple into a spaCy Doc object</span>
        <span class="k">for</span> <span class="n">doc</span><span class="p">,</span> <span class="n">label</span> <span class="ow">in</span> <span class="n">nlp</span><span class="o">.</span><span class="n">pipe</span><span class="p">(</span><span class="n">dataset</span><span class="p">,</span> <span class="n">as_tuples</span><span class="o">=</span><span class="kc">True</span><span class="p">):</span>
            <span class="n">doc</span><span class="o">.</span><span class="n">cats</span> <span class="o">=</span> <span class="p">{</span><span class="s1">&#39;positive&#39;</span><span class="p">:</span> <span class="n">label</span><span class="p">,</span> <span class="s1">&#39;negative&#39;</span><span class="p">:</span> <span class="mi">1</span> <span class="o">-</span> <span class="n">label</span><span class="p">}</span> <span class="c1"># Setting the document&#39;s category to be its label</span>
            <span class="n">doc_bin</span><span class="o">.</span><span class="n">add</span><span class="p">(</span><span class="n">doc</span><span class="p">)</span> <span class="c1"># Adding the Doc to the DocBin</span>
        
        <span class="c1"># Writing the DocBin object to a file </span>
        <span class="n">doc_bin</span><span class="o">.</span><span class="n">to_disk</span><span class="p">(</span><span class="sa">f</span><span class="s2">&quot;./</span><span class="si">{</span><span class="n">output_name</span><span class="si">}</span><span class="s2">.spacy&quot;</span><span class="p">)</span>
        <span class="nb">print</span><span class="p">(</span><span class="sa">f</span><span class="s2">&quot;</span><span class="si">{</span><span class="n">output_name</span><span class="si">}</span><span class="s2"> set contains </span><span class="si">{</span><span class="nb">len</span><span class="p">(</span><span class="n">doc_bin</span><span class="p">)</span><span class="si">}</span><span class="s2"> documents&quot;</span><span class="p">)</span>

        <span class="k">return</span> <span class="n">doc_bin</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    </div><div class="jp-Cell jp-CodeCell jp-Notebook-cell   ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[128]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Creates the training and test files</span>
    <span class="n">train_docbin</span> <span class="o">=</span> <span class="n">create_docbin</span><span class="p">(</span><span class="n">train</span><span class="p">,</span> <span class="s1">&#39;train&#39;</span><span class="p">)</span>
    <span class="n">test_docbin</span> <span class="o">=</span> <span class="n">create_docbin</span><span class="p">(</span><span class="n">test</span><span class="p">,</span> <span class="s1">&#39;test&#39;</span><span class="p">)</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    <div class="jp-Cell-outputWrapper">
    <div class="jp-Collapser jp-OutputCollapser jp-Cell-outputCollapser">
    </div>


    <div class="jp-OutputArea jp-Cell-outputArea">

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt"></div>


    <div class="jp-RenderedText jp-OutputArea-output" data-mime-type="text/plain">
    <pre>train set contains 14303 documents
    test set contains 3576 documents
    </pre>
    </div>
    </div>

    </div>

    </div>

    </div>
    <div class="jp-Cell jp-MarkdownCell jp-Notebook-cell">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea"><div class="jp-InputPrompt jp-InputArea-prompt">
    </div><div class="jp-RenderedHTMLCommon jp-RenderedMarkdown jp-MarkdownOutput " data-mime-type="text/markdown">
    <h2 id="Training-the-Model">Training the Model<a class="anchor-link" href="#Training-the-Model">&#182;</a></h2>
    </div>
    </div>
    </div>
    </div><div class="jp-Cell jp-CodeCell jp-Notebook-cell   ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[129]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Creating the configuration file for model training</span>
    <span class="err">!</span><span class="n">python</span> <span class="o">-</span><span class="n">m</span> <span class="n">spacy</span> <span class="n">init</span> <span class="n">config</span> <span class="n">config</span><span class="o">.</span><span class="n">cfg</span> <span class="o">--</span><span class="n">lang</span> <span class="n">en</span> <span class="o">--</span><span class="n">pipeline</span> <span class="n">textcat</span> <span class="o">--</span><span class="n">optimize</span> <span class="n">efficiency</span> <span class="o">--</span><span class="n">force</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    <div class="jp-Cell-outputWrapper">
    <div class="jp-Collapser jp-OutputCollapser jp-Cell-outputCollapser">
    </div>


    <div class="jp-OutputArea jp-Cell-outputArea">

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt"></div>


    <div class="jp-RenderedText jp-OutputArea-output" data-mime-type="text/plain">
    <pre><span class="ansi-yellow-fg">⚠ To generate a more effective transformer-based config (GPU-only),
    install the spacy-transformers package and re-run this command. The config
    generated now does not use transformers.</span>
    <span class="ansi-blue-fg">ℹ Generated config template specific for your use case</span>
    - Language: en
    - Pipeline: textcat
    - Optimize for: efficiency
    - Hardware: CPU
    - Transformer: None
    <span class="ansi-green-fg">✔ Auto-filled config with all values</span>
    <span class="ansi-green-fg">✔ Saved config</span>
    config.cfg
    You can now add your data and train your pipeline:
    python -m spacy train config.cfg --paths.train ./train.spacy --paths.dev ./dev.spacy
    </pre>
    </div>
    </div>

    </div>

    </div>

    </div>

This last step actually performs the training.  There are a lot of records, so this might take a while.  Come back in 5 or 10 minutes and see if it is done.  You can hit the &quot;stop&quot; button (where the play button usually is) to quit early if you want to move ahead: it will still use whatever data it was able to train on, even if it's not the whole set.

    <div class="jp-Cell jp-CodeCell jp-Notebook-cell   ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[130]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Training the model</span>
    <span class="c1"># Max epochs set to 1 epoch for training brevity</span>
    <span class="c1"># To complete training, remove &quot;--training.max_epochs 1&quot; from command</span>
    <span class="err">!</span><span class="n">python</span> <span class="o">-</span><span class="n">m</span> <span class="n">spacy</span> <span class="n">train</span> <span class="n">config</span><span class="o">.</span><span class="n">cfg</span> <span class="o">--</span><span class="n">output</span> <span class="o">./</span><span class="n">output</span> <span class="o">--</span><span class="n">paths</span><span class="o">.</span><span class="n">train</span> <span class="o">./</span><span class="n">train</span><span class="o">.</span><span class="n">spacy</span> \
    <span class="o">--</span><span class="n">paths</span><span class="o">.</span><span class="n">dev</span> <span class="o">./</span><span class="n">test</span><span class="o">.</span><span class="n">spacy</span> <span class="o">--</span><span class="n">training</span><span class="o">.</span><span class="n">max_epochs</span> <span class="mi">1</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    <div class="jp-Cell-outputWrapper">
    <div class="jp-Collapser jp-OutputCollapser jp-Cell-outputCollapser">
    </div>


    <div class="jp-OutputArea jp-Cell-outputArea">

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt"></div>


    <div class="jp-RenderedText jp-OutputArea-output" data-mime-type="text/plain">
    <pre><span class="ansi-blue-fg">ℹ Saving to output directory: output</span>
    <span class="ansi-blue-fg">ℹ Using CPU</span>
    <span class="ansi-bold">
    =========================== Initializing pipeline ===========================</span>
    [2022-06-11 17:43:57,529] [INFO] Set up nlp object from config
    [2022-06-11 17:43:57,539] [INFO] Pipeline: [&#39;textcat&#39;]
    [2022-06-11 17:43:57,542] [INFO] Created vocabulary
    [2022-06-11 17:43:57,542] [INFO] Finished initializing nlp object
    [2022-06-11 17:44:39,297] [INFO] Initialized pipeline components: [&#39;textcat&#39;]
    <span class="ansi-green-fg">✔ Initialized pipeline</span>
    <span class="ansi-bold">
    ============================= Training pipeline =============================</span>
    <span class="ansi-blue-fg">ℹ Pipeline: [&#39;textcat&#39;]</span>
    <span class="ansi-blue-fg">ℹ Initial learn rate: 0.001</span>
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
    </pre>
    </div>
    </div>

    </div>

    </div>

    </div>
    <div class="jp-Cell jp-MarkdownCell jp-Notebook-cell">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea"><div class="jp-InputPrompt jp-InputArea-prompt">
    </div>

#### Testing the Model

Finally, let's test the model on our remaining 20 percent that we saved earlier!  You'll see several scores.  See if you can find the section labeled `Textcat F`: this shows a few columns called `P` (for <strong>precision</strong>) and `R` (for <strong>recall</strong>), out of 100 percent.  Precision tells us how often the model correctly predicted that a job post was fraudulent when it really was fraudulent (or that it was not fraudulent when it really was not).  Recall tells us how often the model didn't give us a &quot;false positive&quot;, and incorrectly guess the answer.  Unlike a math test, when you get back your grade out of 100, we like to evaluate classification models in both directions.  For example, you could say that you can perfectly predict when it is going to rain by saying it's going to rain every day - you'd never be wrong!  Recall allows us to measure how often you guessed it was going to rain on days that it was sunny out.  These scores are kind of merged together into a single score called the <strong>Receiver Operating Characteristic</strong> (ROC), which is a score from 0 to 1 (with 1 being perfect).

    <div class="jp-RenderedHTMLCommon jp-RenderedMarkdown jp-MarkdownOutput " data-mime-type="text/markdown">
    <h2 id="Evaluating-the-model">Evaluating the model<a class="anchor-link" href="#Evaluating-the-model">&#182;</a></h2>
    </div>
    </div>
    </div>
    </div><div class="jp-Cell jp-CodeCell jp-Notebook-cell   ">
    <div class="jp-Cell-inputWrapper">
    <div class="jp-Collapser jp-InputCollapser jp-Cell-inputCollapser">
    </div>
    <div class="jp-InputArea jp-Cell-inputArea">
    <div class="jp-InputPrompt jp-InputArea-prompt">In&nbsp;[131]:</div>
    <div class="jp-CodeMirrorEditor jp-Editor jp-InputArea-editor" data-type="inline">
         <div class="CodeMirror cm-s-jupyter">
    <div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Evaluating the model using common classification metrics (precision, recall, F1 score)</span>
    <span class="err">!</span><span class="n">python</span> <span class="o">-</span><span class="n">m</span> <span class="n">spacy</span> <span class="n">evaluate</span> <span class="n">output</span><span class="o">/</span><span class="n">model</span><span class="o">-</span><span class="n">best</span> <span class="n">test</span><span class="o">.</span><span class="n">spacy</span> <span class="o">--</span><span class="n">output</span> <span class="n">metrics</span><span class="o">.</span><span class="n">json</span>
    </pre></div>

         </div>
    </div>
    </div>
    </div>

    <div class="jp-Cell-outputWrapper">
    <div class="jp-Collapser jp-OutputCollapser jp-Cell-outputCollapser">
    </div>


    <div class="jp-OutputArea jp-Cell-outputArea">

    <div class="jp-OutputArea-child">

        
        <div class="jp-OutputPrompt jp-OutputArea-prompt"></div>


    <div class="jp-RenderedText jp-OutputArea-output" data-mime-type="text/plain">
    <pre><span class="ansi-blue-fg">ℹ Using CPU</span>
    <span class="ansi-bold">
    ================================== Results ==================================</span>

    TOK                 100.00
    TEXTCAT (macro F)   61.94 
    SPEED               195155

    <span class="ansi-bold">
    =========================== Textcat F (per label) ===========================</span>

                    P        R       F
    positive   100.00    14.94   26.00
    negative    95.83   100.00   97.87

    <span class="ansi-bold">
    ======================== Textcat ROC AUC (per label) ========================</span>

               ROC AUC
    positive      0.62
    negative      0.63

    <span class="ansi-green-fg">✔ Saved results to metrics.json</span>
    </pre>
    </div>
    </div>

    </div>

    </div>

    </div>
    
### Follow Up
How did they do?  For us, the model didn't do so well.  There are some things we can do to improve our results.  Can you think of some reasons why this might be, and what some of these things are?

1. How can we improve our current classification model?  For example, how can we improve the imbalance of examples between the fraudulent and non-fraudulent training classes?

2. Identify key features (e.g., entities, words, phrases) of job descriptions that belong to the fraudulent class

3. Perform Exploratory Data Analysis (EDA) to find interesting insights (e.g., most frequent words, what departments have the most fraudulent advertisements, etc.)

4. (Advanced): Create a classification model using all the features to predict fraudluent jobs (e.g, using logisitic regression, random forest, etc.)