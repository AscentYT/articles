> This is a high-level introduction to machine learning, based on the video below. We will cover what machine learning is, how models learn from data, the difference between supervised and unsupervised learning, and the Python tools used to build models. No prior experience is needed.

```video
src: https://www.youtube.com/watch?v=b9pGRXOC2bM
caption: A broad overview of machine learning — what it is, how it works, and the tools used to build models in Python.
```

If you showed a picture of a tiger to any person on the street, they would name it instantly. We recognize the orange body, the black stripes, the shape of the face — all of it in a fraction of a second.

For a computer, that same task is surprisingly hard.

A computer does not see an image the way we do. It sees a grid of numbers — each pixel is a set of values describing a color and a position. Nothing about those numbers obviously screams "tiger." So how does a computer ever get there?

It learns. And the field that makes that possible is called :vocab[machine learning]{definition="A branch of computer science where a program improves its performance on a task by learning from data, rather than following rules written by hand."}.

## What a Model Is

When a machine learning system learns from data, what it produces is called a :vocab[model]{definition="The end result of a machine learning algorithm learning from data. A model takes a new input and produces an output, like a prediction or a classification."}. Think of a model as a function: you give it an input, and it gives you an answer.

In the tiger example, the input is an image and the output might be "97% tiger, 3% lion." The model did not have that answer built in — it figured it out by studying thousands of labeled images until it learned what tigers tend to look like.

```image
src: tiger-prediction.png
alt: A photo of a tiger with a prediction bar showing 97% Tiger and 3% Lion overlaid on the bottom.
caption: The model assigns a confidence score to each category. It learned those associations from training data, not from hand-written rules.
fit: contain
```

## Two Types of Tasks: Classification and Regression

Not all machine learning problems are about sorting things into categories. There are two broad types of tasks.

The tiger example is a case of :vocab[classification]{definition="A machine learning task where the goal is to assign a label or category to an input. Examples include identifying whether an image is a cat or a dog, or whether an email is spam or not."}. The model picks from a fixed set of options — in this case, "tiger" or "lion."

The other type is :vocab[regression]{definition="A machine learning task where the goal is to predict a number rather than a category. Examples include estimating a house price or forecasting tomorrow's temperature."}. Instead of picking a label, the model predicts a value.

Take housing prices. If you give a model data about houses — square footage, number of bedrooms, neighborhood — it can learn the mathematical relationship between those inputs and the price. Once trained, give it a new square footage and it estimates a price.

```image
src: regression-graph.png
alt: A scatter plot with square footage on the x-axis and price on the y-axis. A best-fit line runs through the data points.
caption: Linear regression finds the line that best fits the data. That line becomes the model's rule for estimating new prices.
fit: contain
```

```quickcheck
q: What is the difference between classification and regression?
options:
  - Classification works with images; regression works with numbers
  - Classification predicts a category; regression predicts a number
  - Classification uses more data than regression
  - Classification is supervised; regression is unsupervised
answer: 1
explanation: Classification assigns a label from a fixed set of options, like "tiger" or "lion." Regression predicts a continuous value, like a house price. Both are common machine learning tasks.
```

## Where the Data Comes From: Training Data

Before a model can make any predictions, it needs to learn. That learning comes from :vocab[training data]{definition="The collection of examples a machine learning model learns from. Each example typically includes an input and the correct answer, which the model uses to figure out patterns."}. In the tiger example, training data might be thousands of labeled animal photos — each one tagged with the correct animal name.

The quality and size of the training data matters enormously. A model trained on blurry photos of only three tigers will not do well on new images. More data, more variety, and accurate labels all lead to better results.

One of the harder parts of building a machine learning model is getting that data into shape. Raw data is often messy — missing values, inconsistent formats, irrelevant columns. :vocab[Data preprocessing]{definition="The process of cleaning and transforming raw data before feeding it to a model. This can include handling missing values, normalizing numbers, and removing irrelevant information."} is the step where you fix all of that. It is unglamorous work, but skipping it leads to bad models.

```quickcheck
q: Why does the quality of training data matter?
options:
  - Low-quality data makes Python run slower
  - A model can only learn patterns that exist in its training data
  - Training data is only used to test the model, not train it
  - Better data reduces the number of features needed
answer: 1
explanation: A model learns by finding patterns in its training data. If the data is inaccurate, incomplete, or unrepresentative, the model will learn the wrong patterns and perform poorly on new inputs.
```

## Supervised vs. Unsupervised Learning

Machine learning algorithms generally fall into one of two categories depending on how their training data is structured.

:vocab[Supervised learning]{definition="A type of machine learning where the training data includes both inputs and the correct answers. The model learns to map inputs to outputs and makes predictions on new data."} uses labeled data — each training example has an input and the expected output. The tiger classifier is supervised: every image comes with a label saying what animal it is. The housing price predictor is also supervised: every house comes with its actual price. The model's job is to learn the pattern connecting inputs to outputs.

:vocab[Unsupervised learning]{definition="A type of machine learning where the training data has no labels. The goal is to find structure or patterns within the data on its own."} works differently. There are no correct answers provided — just raw data. The algorithm has to find structure on its own.

A common unsupervised technique is :vocab[clustering]{definition="An unsupervised learning method that groups data points together based on how similar they are to each other. The algorithm finds the groups itself — no labels required."}. Given a dataset of customer purchase histories, a clustering algorithm might group customers with similar buying habits together — without anyone telling it what the groups should be.

```image
src: clustering.png
alt: A scatter plot with unlabeled data points. Circles are drawn around three distinct clusters of points.
caption: Clustering finds natural groupings in data. The algorithm discovers the groups on its own — no labels needed.
fit: contain
```

## Training Data vs. Testing Data

There is a trap that is easy to fall into when building a machine learning model: the model gets so good at memorizing its training examples that it fails when it sees anything new. This is called :vocab[overfitting]{definition="When a model learns the training data too closely, including its noise and quirks, and fails to generalize to new, unseen examples."}.

Think about studying for a final exam. If you only memorize the exact answers from last year's practice tests without understanding the underlying concepts, you might score perfectly on those old tests — but struggle the moment a question is phrased differently. You have memorized, not learned.

To catch overfitting, the data is split into two parts: :vocab[training data and testing data]{definition="A standard split where the model learns from one portion of the data (training) and is evaluated on a separate portion it has never seen (testing). This checks whether the model generalizes, not just memorizes."}. The model learns from the training portion. Then it is evaluated on the testing portion — data it has never seen before.

If the model performs well on both, it has genuinely learned. If it does great on training but poorly on testing, it has overfit.

```image
src: train-test-split.png
alt: A data table being divided into two labeled sections: Training Data on the left and Testing Data on the right.
caption: The training set teaches the model. The test set checks whether it actually learned — or just memorized.
fit: contain
```

```quickcheck
q: What does it mean if a model performs well on training data but poorly on testing data?
options:
  - The model is accurate and ready to deploy
  - The training data was too small
  - The model has overfit — it memorized instead of learning
  - The testing data needs to be preprocessed
answer: 2
explanation: Strong training performance with weak testing performance is the signature of overfitting. The model learned the specific examples in the training set too closely and cannot generalize to new inputs.
```

## The Python Tools Behind Machine Learning

Most machine learning is built with :vocab[Python]{definition="A general-purpose programming language that has become the dominant language for machine learning and data science, largely because of its readable syntax and rich ecosystem of scientific libraries."}. It is the dominant language in the field for a good reason: the code is readable, and the ecosystem of libraries built for data and machine learning is enormous.

Here is a quick map of the most commonly used ones:

**Pandas** handles data loading and cleaning. When you pull in a raw CSV of housing records and need to filter out bad rows or rename columns, Pandas is the tool for it.

**NumPy** handles the math underneath. It makes working with large arrays of numbers fast and efficient. Most other libraries are built on top of it.

For building models, the three most common libraries are **Scikit-learn**, **TensorFlow**, and **PyTorch**. :vocab[Scikit-learn]{definition="A Python library for classical machine learning algorithms like decision trees, support vector machines, and K-Nearest Neighbors. It is the standard starting point for most beginner and intermediate ML tasks."} is the go-to for classical machine learning algorithms — decision trees, K-Nearest Neighbors, regression models. :vocab[TensorFlow]{definition="An open-source Python library developed by Google for building and training deep learning models, especially neural networks at scale."} and :vocab[PyTorch]{definition="An open-source Python library developed by Meta for building deep learning models. It is widely used in research because of its flexible, dynamic approach to building neural networks."} are for deep learning — neural networks and more complex architectures.

To understand what your data looks like and how your model is performing, **Matplotlib** and **Seaborn** let you build charts and visualizations directly in Python.

Together, these libraries form the foundation of almost every machine learning project you will encounter.

```quickcheck
q: Which Python library would you use to load and clean a raw dataset before training a model?
options:
  - NumPy
  - PyTorch
  - Pandas
  - Matplotlib
answer: 2
explanation: Pandas is designed for loading, exploring, and cleaning structured data. NumPy handles numerical operations, PyTorch is for deep learning, and Matplotlib is for visualization.
```

## What Comes Next

This lesson covered the big picture — what machine learning is, how models learn, the difference between classification and regression, supervised vs. unsupervised learning, and the tools used to build models in Python.

Each of those topics has a lot more depth to it. Future lessons will go deeper on specific algorithms, how to evaluate model performance, and how to build models from scratch. This is just the foundation.
