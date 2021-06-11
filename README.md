# Elastic deformation invariance in medical imaging

In the field of medical imaging, acquiring training data is in general costly, especially for rare conditions or when data labeling needs to be done by medical specialist. It is therefore important to use available data in an efficient way when training a model. Existing methods often apply data augmentation, such as image transformation, to increase the amount of data. More recent literature is focussed on semi-supervised learning techniques to increase model accuracy by increasing the data efficiency. In this project, we have applied a siamese neural network [1] to learn elastic deformation invariance on medical data, with the goal of thereby increasing the prediction accuracy for image classification with limited data availability. 

## Methods

### MedMNIST

### Elastic deformation

## Results

### Training loop

### Hyperparameter search
In our current architecture and network training method we have two hyperparameters: σ and α. σ denotes the degree to which we deform input images when training for consistency. More specifically the elastic deformation method we use requires an input of normally distributed noise, and σ denotes the standard deviation of this normal distribution. A higher value for σ results in more strongly deformed input images, which means that our network will try to learn invariance to strong deformations. On the other hand, lower σ-values will teach the network invariance to more subtle deformations. α is a measure for how large the influence of the consistency loss is relative to the supervised loss based on the training labels. Higher values for α will make the network prioritize consistency under elastic deformation more, over correct predictions. For lower α values it's the other way around.

In order to find the right combination of α and σ values, we perform a grid search on the BreastMNIST dataset. In a grid search we train and test our model with α values ranging from \[0.2, 0.4, ..., 2.0\] and σ-values ranging from \[0.30, 0.35, ..., 0.50\]. σ-values from \[0.05, 0.10, ..., 0.25\] were considered suboptimal based on a previous experiment (data not shown) and excluded from the grid-search. Each model is trained on the train set (70% of the data) and validated on the validation set (10% of the data). The accuracies achieved by each trained model are shown below:

-- GRID SEARCH MATRIX --
![grid_search_breast](grid_search_breast.jpg)

From this grid we chose the optimal values for σ and α an we tested a model with these hyperparameter settings on the test set (20% of the data).

### Comparison with baseline

## Conclusions


## References
[1] Chicco, Davide. "Siamese neural networks: An overview." Artificial Neural Networks (2021): 73-94.
