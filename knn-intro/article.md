# Understanding the K-Nearest Neighbors (KNN) Algorithm

> This article is a beginner-friendly introduction to KNN. No prior machine learning experience is required, just a basic comfort with numbers and curiosity about how AI works.

```video
src: https://www.youtube.com/watch?v=nf-8ed80cPY&t=2s
caption: A visual walkthrough of how the KNN algorithm classifies data points
```

Machine learning algorithms allow computers to make decisions by finding patterns in data. One of the simplest and most intuitive algorithms used for this purpose is **K-Nearest Neighbors (KNN)**.

KNN is a :vocab[supervised machine learning algorithm]{definition="A type of algorithm that learns from labeled training data, where each example includes both an input and the correct answer. The model uses these examples to make predictions on new, unseen data."} used for both classification and regression tasks. In classification problems, its goal is to determine which category a new data point belongs to based on examples it has seen before.

Despite its simplicity, KNN demonstrates many of the fundamental ideas behind machine learning: representing data with features, measuring similarity, and making predictions from previous observations.

## How Computers Represent Data

Before a machine learning model can make predictions, data must be represented numerically.

Each piece of data is described using one or more :vocab[features]{definition="Measurable properties or characteristics of the data used as input to a machine learning model. For example, the height and weight of an animal are both features."}, which are measurable characteristics of an observation. For example, if we were building a model to classify animals, features might include weight, height, tail length, or ear size.

```image
src: feature-space.png
alt: A 2D grid showing animals plotted by weight on the x-axis and height on the y-axis, colored by species
caption: Each animal becomes a point in a 2D feature space defined by its measurements
fit: contain
```

Each observation can then be represented as a point in a mathematical space. A dataset containing many observations forms a collection of points, where each point corresponds to a known example.

Because these examples already have labels, they become the :vocab[training data]{definition="A labeled dataset used to teach a machine learning model. Each example includes both the input features and the correct output label."} that KNN uses to make future predictions.

```quickcheck
q: What is a "feature" in a machine learning dataset?
options:
  - A measurable characteristic used as input to a model
  - The final prediction made by an algorithm
  - The number of neighbors considered by KNN
  - A type of distance metric
answer: 0
explanation: Features are the measurable properties of each observation, the inputs a model uses to make predictions.
```

## The Core Idea Behind KNN

The central assumption of KNN is simple:

> Similar data points are likely to belong to the same category.

When a new, unlabeled observation is introduced, the algorithm examines the examples that are most similar to it. These nearby examples are its :vocab[nearest neighbors]{definition="The training examples closest in distance to a new data point. KNN uses these neighbors to vote on what label the new point should receive."}.

```image
src: knn-voting.png
alt: A new unlabeled point surrounded by 5 neighbors, 3 labeled class A and 2 labeled class B, with the new point assigned class A
caption: With K = 5, the new point is classified as class A because 3 of its 5 neighbors belong to that class
fit: contain
```

KNN predicts the label of the new observation by looking at the labels of these neighbors and choosing the category that appears most frequently.

For example, suppose a new data point has five nearest neighbors. If three belong to one class and two belong to another, the algorithm will classify the new point as belonging to the majority class.

This voting process forms the basis of KNN classification.

## Understanding the Value of K

The parameter **K** determines how many neighbors are considered when making a prediction. Choosing an appropriate value for K is important because it directly affects the behavior of the model.

### Small Values of K

When K is very small, such as K = 1, predictions depend heavily on the closest example. This can make the model highly sensitive to noise or unusual data points, often leading to :vocab[overfitting]{definition="When a model learns the training data too precisely, including its noise, and performs poorly on new, unseen data."}.

### Large Values of K

As K increases, predictions are influenced by a larger portion of the dataset. This generally makes the model more stable, but if K becomes too large, the model may overlook important local patterns and produce overly generalized predictions.

Finding the right value of K usually requires experimentation and testing.

```image
src: k-comparison.png
alt: Side-by-side decision boundaries for K=1, K=5, and K=20, showing how the boundary becomes smoother as K grows
caption: A small K produces jagged, sensitive boundaries. A larger K produces smoother, more generalized ones.
fit: contain
```

```quickcheck
q: What is the risk of using a very small value of K (e.g. K = 1)?
options:
  - The model becomes too slow to run
  - The model may overfit and be sensitive to noise
  - The model ignores all training data
  - Euclidean distance can no longer be computed
answer: 1
explanation: With K = 1, a single unusual or mislabeled neighbor can determine the prediction, making the model fragile and prone to overfitting.
```

## Measuring Similarity

To determine which neighbors are closest, KNN must calculate the distance between data points. The most common distance metric is :vocab[Euclidean distance]{definition="The straight-line distance between two points in space, calculated as the square root of the sum of squared differences across all dimensions."}, which measures the straight-line distance between two points.

The smaller the distance, the more similar the points are considered to be. Other distance metrics, such as Manhattan distance and Minkowski distance, can also be used depending on the problem.

## Implementing KNN in Python

Python's Scikit-learn library makes it straightforward to train and evaluate a KNN classifier. Below is a complete example using the classic Iris dataset.

Start by loading the data and splitting it into training and testing sets:

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

iris = load_iris()
X, y = iris.data, iris.target

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)
```

Feature scaling is applied here because KNN relies on distance. Without it, a feature measured in large numbers would dominate the distance calculation over one measured in small numbers.

Next, train the classifier and evaluate it:

```python
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import accuracy_score

knn = KNeighborsClassifier(n_neighbors=5)
knn.fit(X_train, y_train)

predictions = knn.predict(X_test)
accuracy = accuracy_score(y_test, predictions)

print(f"Accuracy: {accuracy:.2%}")
# Accuracy: 100.00%
```

To find the best value of K, loop over several candidates and compare their accuracy on the test set:

```python
accuracies = {}

for k in range(1, 21):
    model = KNeighborsClassifier(n_neighbors=k)
    model.fit(X_train, y_train)
    accuracies[k] = accuracy_score(y_test, model.predict(X_test))

best_k = max(accuracies, key=accuracies.get)
print(f"Best K: {best_k} ({accuracies[best_k]:.2%} accuracy)")
```

```image
src: k-accuracy-plot.png
alt: A line chart showing test accuracy on the y-axis against values of K from 1 to 20 on the x-axis
caption: Plotting accuracy against K helps identify the point where adding more neighbors stops improving performance
fit: contain
```

```quickcheck
q: Why is feature scaling applied before training a KNN model?
options:
  - To reduce the number of neighbors considered
  - To ensure no feature dominates distance calculations due to its scale
  - To convert labels into numbers
  - To speed up the voting process
answer: 1
explanation: If one feature has much larger values than another, it will dominate the distance calculation. Scaling puts all features on a comparable range so each contributes fairly.
```

## Working in Higher Dimensions

Many introductory examples use only two features because they are easy to visualize. Real-world datasets, however, often contain dozens or even hundreds of features.

Whether a dataset contains three features, ten features, or hundreds of features, the algorithm follows the same process:

1. Calculate distances between points.
2. Identify the K nearest neighbors.
3. Determine the most common label.
4. Assign that label to the new observation.

## Conclusion

K-Nearest Neighbors is one of the most intuitive machine learning algorithms. Rather than learning a complex mathematical model, it makes predictions by comparing new observations to examples it has already seen.

Although more advanced algorithms exist, KNN provides an excellent foundation for the fundamental concepts of machine learning: feature representation, similarity measurement, classification, and model evaluation. Understanding KNN lays the groundwork for exploring more sophisticated techniques used throughout modern AI.