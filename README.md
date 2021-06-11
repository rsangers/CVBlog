# Elastic deformation invariance in medical imaging

In the field of medical imaging, acquiring training data is in general costly, especially for rare conditions or when data labeling needs to be done by medical specialist. It is therefore important to use available data in an efficient way when training a model. Existing methods often apply data augmentation, such as image transformation, to increase the amount of data. More recent literature is focussed on semi-supervised learning techniques to increase model accuracy by increasing the data efficiency. In this project, we have applied a siamese neural network [1] to learn elastic deformation invariance on medical data, with the goal of thereby increasing the prediction accuracy for image classification with limited data availability. 

## Methods

### MedMNIST
MedMNIST is a collection of ten medical imaging datasets. Following the approach by the MNIST dataset, the images in this dataset have been standardized and preprocessed to 28x28 pixel images with the goal of making a lightweight standardized classification dataset. These ten datasets are of different modalities, ranging from organ classification (OrganMNIST) to disease classification in chest x-ray images (ChestMNIST). This thereby makes it possible to test a classification approach on a wide range of data. We have chosen this collection of datasets because it allows for rapid prototyping and training without the need of day-long computations. Moreover, the field of medical imaging is a natural application domain for our proposed method: consistency under elastic deformation.
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


## References
[1] Chicco, Davide. "Siamese neural networks: An overview." Artificial Neural Networks (2021): 73-94.

[3] LeCun, Y. Cortes, C. and Burges, C.J.C. "The MNIST database of handwritten digits". http://yann.lecun.com/exdb/mnist/
