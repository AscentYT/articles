> This is a high-level introduction to K-Nearest Neighbors, based on the video below, one of our earlier lessons that still covers the core ideas well. We will walk through how the algorithm works, what the K parameter means, and how to implement it in Python. No prior machine learning experience is needed.

```video
src: https://www.youtube.com/watch?v=nf-8ed80cPY&t=2s
caption: Our original KNN walkthrough, covering classification, distance, and the effect of K
```

Machine learning is about teaching computers to make decisions by showing them examples. One of the simplest ways to do this is an algorithm called **K-Nearest Neighbors (KNN)**.

KNN is a :vocab[supervised machine learning algorithm]{definition="A type of algorithm that learns from labeled examples. Each example includes an input and the correct answer, and the model uses those examples to make predictions on new data it has never seen."} used to sort new data into categories. To do that, it looks at the examples it has already seen and asks: which ones are most similar to this new piece of data? Whatever category most of those similar examples belong to, that is the category it assigns.

## Plotting Data as Points on a Graph

Before KNN can do anything, the data needs to be turned into a format a computer can work with. That format is numbers, and those numbers can be plotted as points on a graph.

Here is how it works. Say you want to teach a computer to tell the difference between cats and dogs based on two measurements: tail length and ear shape (scored on a scale from pointy to floppy).

For each animal in your dataset, you have two numbers. Tail length becomes the position on the horizontal axis (left to right), and ear shape becomes the position on the vertical axis (up and down). That gives every animal a specific spot on the graph. A dog with a long tail and floppy ears ends up in the top right. A cat with a short tail and pointy ears ends up in the bottom left. Each animal is now a single dot.

These measurements are called :vocab[features]{definition="The numbers that describe a piece of data. Each feature is one measurement, like tail length or ear shape. Together, the features place a data point at a specific spot on the graph."}.

```image
src: feature-plot.png
alt: A 2D scatter plot with Tail Length on the x-axis and Ear Shape on the y-axis. Cat dots cluster in the bottom-left, dog dots in the top-right. One dot has dotted lines showing how its x and y position map to its tail length and ear shape measurements.
caption: Each animal becomes a dot. Its tail length sets how far right it goes, its ear shape sets how far up it goes.
fit: contain
```

Once every animal is plotted, animals of the same type tend to cluster together. Cats, with their shorter tails and pointier ears, group near other cats. Dogs, with longer tails and floppier ears, group near other dogs. That clustering is what KNN takes advantage of.

This collection of labeled examples is called the :vocab[training data]{definition="The set of examples the model learns from. Each example has both its measurements (features) and the correct label, like cat or dog."}.

```quickcheck
q: How does KNN use a data point's features?
options:
  - It uses them to place the point at a specific position on a graph
  - It uses them to choose the value of K
  - It uses them to calculate accuracy
  - It stores them but does not use them during prediction
answer: 0
explanation: Each feature becomes a coordinate on the graph. Together they determine exactly where the point is plotted, which is what KNN uses to find similar examples nearby.
```

## How KNN Makes a Prediction

When a new, unlabeled animal comes in, KNN measures its tail length and ear shape, then plots it on the same graph. Then it looks at the K closest labeled points around it. Those are its :vocab[nearest neighbors]{definition="The labeled examples closest in distance to the new point on the graph. KNN uses them to vote on what label the new point should get."}.

Each neighbor gets one vote. Whichever label gets the most votes wins, and that becomes the prediction.

```image
src: knn-vote.png
alt: The same scatter plot with a new unlabeled point marked with a question mark. A dashed circle surrounds its 5 nearest neighbors: 3 cats and 2 dogs. A vote tally shows cats 3, dogs 2. The new point is labeled predicted: cat.
caption: The new animal is surrounded by 3 cats and 2 dogs, so KNN predicts cat.
fit: contain
```

## Choosing K

The number K controls how many neighbors get a vote.

If K is small, like 1, the prediction is based entirely on whichever single point happens to be closest. That one point might be an unusual outlier, which can throw off the result. This is called :vocab[overfitting]{definition="When a model is too influenced by specific examples in the training data, including noisy or unusual ones, causing it to make poor predictions on new data."}.

If K is large, many points vote, which smooths out those mistakes. But if K is too large, the model starts pulling in points that are not really that similar, and the predictions become too general.

```image
src: k-small.png
alt: Scatter plot showing K=1 for a KNN classifier. The decision boundary is extremely jagged and tightly wraps around individual data points, creating small islands around outliers.
caption: With a very small K (like K=1), the model overfits and creates a highly jagged boundary that reacts too strongly to noise and individual points.
fit: contain
```

```image
src: k-big.png
alt: Scatter plot showing K=50 for a KNN classifier. The decision boundary is overly smooth and almost linear, ignoring local clusters and misclassifying several edge points.
caption: With a very large K, the model underfits and over-smooths the boundary, ignoring local structure and blending distinct groups together.
fit: contain
```

```quickcheck
q: What is the risk of setting K = 1?
options:
  - The model becomes too slow
  - One unusual example can control the whole prediction
  - The model ignores training data
  - Distance calculations break down
answer: 1
explanation: With K = 1, a single neighbor determines the prediction. If that neighbor is an outlier or mislabeled, the prediction will be wrong. Using more neighbors evens this out.
```

## How KNN Measures Distance

To find the nearest neighbors, KNN needs to measure how far apart two points are on the graph. The most common way to do this is :vocab[Euclidean distance]{definition="The straight-line distance between two points on a graph, the same as what you would measure with a ruler."}, which is just the straight-line distance between them, the same as if you put a ruler on the graph.

The closer two points are, the more similar the algorithm considers them to be.

## Implementing KNN in Python

Python's Scikit-learn library makes it easy to build a KNN classifier. The example below uses the Iris dataset, a classic beginner dataset with measurements of three types of flowers.

Start by loading the data and splitting it into a training set and a test set:

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

The scaling step adjusts all the measurements to a similar range. This matters because KNN uses distance. If one measurement is in the thousands and another is in the tens, the larger one will always dominate the distance calculation, even if both measurements are equally important.

Next, train the model and check how well it performs:

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

To find the best K, test a range of values and see which one performs best:

```python
accuracies = {}

for k in range(1, 21):
    model = KNeighborsClassifier(n_neighbors=k)
    model.fit(X_train, y_train)
    accuracies[k] = accuracy_score(y_test, model.predict(X_test))

best_k = max(accuracies, key=accuracies.get)
print(f"Best K: {best_k} ({accuracies[best_k]:.2%} accuracy)") # 0.9
```

```quickcheck
q: Why do we scale the features before training KNN?
options:
  - To reduce the number of neighbors considered
  - So no single measurement dominates the distance calculation
  - To convert labels into numbers
  - To speed up training
answer: 1
explanation: KNN measures distance between points. If one feature has much larger values than another, it will have an outsized effect on every distance calculation. Scaling brings all features to a comparable range.
```

## What About More Than Two Features?

The examples above use two measurements so they are easy to draw on a graph. Real datasets often have dozens or hundreds of measurements per example. You cannot draw a 100-dimensional graph, but KNN works the same way regardless of how many features there are.

For any number of features, the steps are always:

1. Calculate the distance between the new point and every training example.
2. Pick the K closest ones.
3. Count which label appears most among them.
4. Assign that label to the new point.

## Conclusion

K-Nearest Neighbors works by turning data into points on a graph and finding the labeled examples closest to a new, unlabeled point. Whatever label most of those neighbors have, that is the prediction.

It is a great first algorithm to learn because the logic mirrors how people naturally think: if something looks like a duck and walks like a duck, it is probably a duck. The same intuition powers a lot of more advanced machine learning as well.
