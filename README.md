# Elastic deformation invariance in medical imaging

Introductiestukje over doel van het project en motivatie

## Methods

### MedMNIST

### Elastic deformation

## Results

### Training loop

### Hyperparameter search
In our current architecture and network training method we have two hyperparameters: \sigma and \alpha. \sigma denotes the degree to which we deform input images when training for consistency. More specifically the elastic deformation method we use requires an input of normally distributed noise, and \sigma denotes the standard deviation of this normal distribution. A higher value for \sigma results in more strongly deformed input images, which means that our network will try to learn invariance to strong deformations. On the other hand, lower \sigma values will teach the network invariance to more subtle deformations. \alpha is a measure for how large the influence of the consistency loss is relative to the supervised loss based on the training labels. Higher values for \alpha will make the network prioritize consistency under elastic deformation more, over correct predictions. For lower \alpha values it's the other way around.

In order to find the right combination of \alpha and \sigma values, we perform a grid search. In a grid search we train and test our model with \alpha values ranging from \[0.2, 0.4, ..., 2.0\] and \sigma values ranging from \[0.05, 0.10, ..., 0.50\]. Each model is trained on the train set (80% ? of the data) and validated on the validation set (5% ? of the data). The accuracies achieved by each trained model are shown below:

-- GRID SEARCH MATRIX --

From this grid we chose the optimal values for \sigma and \alpha an we tested a model with these hyperparameter settings on the test set (15% ? of the data).

### Comparison with baseline

## Conclusions



