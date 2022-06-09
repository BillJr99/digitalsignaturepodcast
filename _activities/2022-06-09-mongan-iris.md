---
layout: activity
title: "Classifying Flowers"
permalink: /activities/iris

gradelevel: elementary
category: r
subject: science

authors: "William M. Mongan"
date: 2022-06-09

summary: "In this activity, we will measure properties of different flower species and use those measurements to guess the species of an unknown flower."

thumbnail: "https://upload.wikimedia.org/wikipedia/commons/thumb/d/df/Blue_Iris_flower.jpg/640px-Blue_Iris_flower.jpg"
---

### Classifying Data from Features

Imagine holding some coins in your hand. Can you tell what they are without looking at them? How can you tell? You might look for some common &quot;features&quot; like the thickness or the diameter of the coin, or the weight, or even the ridges along the outside of the coin. If the coin is in rough shape, there might be some variance in the data that causes you to guess incorrectly from time to time, but the idea is that we can predict a classification of data by evaluating some known features against examples we’ve seen in the past.

We will use the Iris flower dataset to predict the species of a flower using a few features such as the length and width of the petal.  To try this out, navigate to this [flower classifier page](https://www.billmongan.com/Ursinus-CS173/Activities/FlowerClassifier) which is embedded below:

<iframe src="https://www.billmongan.com/Ursinus-CS173/Activities/FlowerClassifier" height="900px" width="100%"></iframe>

Fill in some numbers for the petal length, petal width, and sepal length for some imaginary flowers.  You can select the species for each flower click "Add" each time to plot the flowers by species on the 3D plot (which you can click to rotate and zoom).  Take a look at the plot: do your flowers group in a way that makes sense?  If the species data points are far away from each other when the species is different, but close together with those of the same species, there's a good chance you can classify a "mystery flower" between these species choices.  Enter numbers for a "mystery flower," select the species that you think it is, and click "Guess" to see if you can correctly predict them!  If not, try refreshing the page, and adding new example flowers whose measurements "group together" by species a bit more closely.  Do you have an easier time classifying between them now?

### Exploring the Iris Dataset Using R in the Browser
R.A. Fisher's Iris flower data is built into the R platform, and we have embedded an [R webassembly compiler](https://github.com/georgestagg/webR) into a [webpage](https://www.billmongan.com/Ursinus-CS173/assets/js/R/TutorialExercise) for exploration in our courses that use the R language (embedded below).  

<iframe src="https://www.billmongan.com/Ursinus-CS173/assets/js/R/TutorialExercise" height="900px" width="100%"></iframe>

We will begin by viewing the the Iris dataset for analysis, which is described in detail in [this article](https://gexijin.github.io/learnR/step-into-r-programmingthe-iris-flower-dataset.html).  To do this, you can type this command into the R console: 

```
head(iris)
```

This command displays the first few rows of the dataset.  It contains columns for the sepal length and width, and the petal length and width, as well as the species of that flower.  There are 50 Setosa flowers, 50 Versicolor flowers, and 50 Virginica flowers in the example dataset.  

The species column is not numeric, so we can't plot that.  However, we will use the species later to color code our plots.  For now, let's extract the first four columns (the numeric features) into a "dataframe" variable, by typing: 

```
df <- iris[, 1:4]
````

You can graph the columns of this dataset against each other.  We'll use two features at a time so we can more easily view the data on a 2D plot.  You can do this by typing: 

```
plot(iris$Petal.Length, iris$Petal.Width, col=iris$Species)
```

This plots the petal length against the petal width, and color codes them by species.  To see which color represents which species, you can add a legend to the plot by typing: 

```
legend("topleft", levels(iris$Species), fill=1:3)  
```

How well do you think the Petal Length and Petal Width can help you predict the species of a flower, and why?

Run these commands to see 2D plots of all of the columns against each other (this is called a "pairs plot").  Type this command to generate it: 

```
pairs(df, col = rainbow(3)[speciesID], labels=c("PetalLength", "PetalWidth", "SepalLength", "SepalWidth"))
```  

Which features do the best job separating the flowers by species?