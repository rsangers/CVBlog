# Elastic deformation invariance in medical imaging
*By Ruben Sangers and Jurrian de Boer*

In the field of medical imaging, acquiring training data is in general costly, especially for rare conditions or when data labeling needs to be done by medical specialist. It is therefore important to use available data in an efficient way when training a model. Existing methods often apply data augmentation, such as image transformation, to increase the amount of data. More recent literature is focussed on semi-supervised learning techniques to increase model accuracy by increasing the data efficiency. In this project, we have applied a siamese neural network [1] to learn elastic deformation invariance on medical data, with the goal of thereby increasing the prediction accuracy for image classification with limited data availability. 

## Methods

### MedMNIST
MedMNIST [2] is a collection of ten medical imaging datasets. Following the approach by the MNIST dataset [3], the images in this dataset have been standardized and preprocessed to 28x28 pixel images with the goal of making a lightweight standardized classification dataset. These ten datasets are of different modalities, ranging from organ classification (OrganMNIST) to disease classification in chest x-ray images (ChestMNIST). This thereby makes it possible to test a classification approach on a wide range of data. We have chosen this collection of datasets because it allows for rapid prototyping and training without the need of day-long computations. Moreover, the field of medical imaging is a natural application domain for our proposed method: consistency under elastic deformation.
<br />
<br />
<img src="medmnist_overview.PNG" alt="medmnist_overview" width="600"/>

### Elastic deformation
The choice for learning equivariance under elastic deformation was based on promising results by [4] on the JSRT X-ray dataset. While this research implements equivariance under elastic deformation in a segmentation task, we want to see if this can be useful as well for enforcing consistency in classification. We hypothesize that with elastic deformation we can model anatomical variations that are present between patients or even between images of the same patient in subtly different positions: these deformed images should still receive the same classification label as in an 'undeformed' situation. For this reason, we expect that such a siamese network learning equivariance under elastic deformation would also increase performance on other medical imaging datasets, such as MedMNIST. We especially expect good results in the OrganMNIST sub-dataset, since in CT scans the organs might be deformed a little due to the way a patient is positioned or, again, due to anatomical difference between patients.

### Architecture
In the paper by [2] the authors provide performance of baseline models, among others ResNet-18 or ResNet-50, on their datasets. In order to be able to compare our results with the baseline, we will also use a ResNet-18 as our model architecture. The only changes we make are in the way the model is trained and the way the loss function is defined, namely as a Siamese Network. We feed the network two samples at the same time. The first is a sample from the dataset and the second is that same image but slightly elastically deformed. Now, we define the loss function as a weigted sum of two distances: the distance between the prediction and the label, and the distance between the predictions on both samples, which we measure using the Kullback-Leiber (KL) divergence.

## Results

### Training curve
In order to determine how many epochs were necessary to converge, we plotted a learning curve on the breast sub-dataset of the MedMNIST (see figure below). We chose this dataset as it was limited in size and thus less computationally expensive to experiment on. The red line indicates the accuracy of the model and the blue line the ROC AUC value. We found that 25 epochs was enough to reach convergence. We used this number of epochs across our experiments in this blog and across various sub-datasets. Of course, the assumption that this optimal number of epochs generalizes across the various datasets is unlikely to hold perfectly. Ideally you would want to plot learning curves for the other datasets as well, but this was out of the scope of the present study.

<img src="learning_curve_siamese.png" alt="grid search breast avg and auc" width="450"/>

### Hyperparameter search
In our current architecture and network training method we have two hyperparameters: ?? and ??. ?? denotes the degree to which we deform input images when training for consistency. More specifically the elastic deformation method we use requires an input of normally distributed noise, and ?? denotes the standard deviation of this normal distribution. A higher value for ?? results in more strongly deformed input images, which means that our network will try to learn invariance to strong deformations. On the other hand, lower ??-values will teach the network invariance to more subtle deformations. ?? is a measure for how large the influence of the consistency loss is relative to the supervised loss based on the training labels. Higher values for ?? will make the network prioritize consistency under elastic deformation more, over correct predictions. For lower ?? values it's the other way around.

In order to find the right combination of ?? and ?? values, we perform a grid search on the BreastMNIST dataset. In a grid search we train and test our model with ?? values ranging from \[0.2, 0.4, ..., 2.0\] and ??-values ranging from \[0.30, 0.35, ..., 0.50\]. ??-values from \[0.05, 0.10, ..., 0.25\] were considered suboptimal based on a previous experiment (data not shown) and excluded from the grid-search. Each model is trained on the train set (70% of the data) and validated on the validation set (10% of the data). The accuracies and ROC AUC values achieved by each trained model are shown below:

<img src="breast_grid_search_avg_auc.png" alt="grid search breast avg and auc" width="600"/>

Both the accuracies as well as the AUC values are important measures for the performance of our model. For this reason we determine the average between the accuracy and the AUC for each model (data not shown), and subsequently the best model is chosen based on which average is the highest. The optimal hyperparameters are the ones of the best model, which turns out to be ?? = 1.0 and ?? = 0.35. We will use these hyperparameter settings for other datasets as well.


### Learning curve
We have the hypothesis that our semi-supervised training method will work especially well in the low-data regime. Using the Siamese network approach, each image is used in two ways; supervised using the label and unsupervised by enforcing consistency of classification under elastic deformation. Therefore, this method will be especially helpful in situations where there is only a limited amount of training data available. We have tested this by training our model with various amounts of training data available, ranging from 20% to 100% of the BreastMNIST training dataset. We then compare the results achieved using the original training method and our siamese network approach, to see how their performance degrades when there is a smaller amount of data available. These results are shown in the figures below, where we take the average over 5 training runs to counteract stochasticity in training.

<img src="data_curve_accuracies_confidence.png" alt="training data acc" width="300"/>
<img src="data_curve_aucs_confidence.png" alt="training data auc" width="300"/>

In the figure, we can see that the performance of both methods naturally degrades for smaller amounts of data. We do however see a significant difference in the relative degradation of both methods: while the original training model achieves an accuracy of only 36% on 20% of the training data, our proposed method still scores 'okay' with an accuracy of 70%. A similar difference can be seen for the Area Under Curve performance. Moreover, it seems that enforcing the elastic deform consistency using our chosen parameters in general achieves a slightly higher accuracy and AUC than without this method, regardless of the amount of training data available.

### Comparison with baseline
We have also looked if our elastic deformation consistency method outperforms the original training method on all four datasets. In the previous subchapter, we have already seen that this holds for the BreastMNIST dataset, but because the four datasets are of different modalities, it might be interesting to see if we can come to any general conclusions. We have therefore trained models following the original or elastic deformation method on 20% of the training data available, which is the data-regime on which we think our method might be the most beneficial. The 5-run accuracy and AUC performance have been summarized in the table below.

<img src="Baseline comparison.PNG" alt="baseline comparison" width="600"/>

From this table, we see that our method achieves the highest improvement on the BreastMNIST dataset. Moreover, it achieves a slight improvement on the PneumoniaMNIST and OrganMNIST dataset, while it is slightly outperformed on the ChestMNIST dataset. It is however hard to say conclude what causes these performance differences: this could be because elastic deformation consistency in general holds the most for the BreastMNIST dataset, although this is not what we hypothesized. Another explanation could be that we have tuned the ?? and ?? hyperparameters specifically on the BreastMNIST dataset, and that these values do not generalize to the other datasets.

### Discussion
In our elastic deformation approach, we use a KL-divergence loss for the supervised as well as the unsupervised loss, while the original MedMNIST paper uses a cross-entropy loss. We wanted to evaluate what effect using this different loss function has on our results, and for this sake we have also compared the original approach (as in the MedMNIST paper) with a similar approach that only differs in using a KL divergence loss function on the BreastMNIST dataset. Here, we saw that the use of a KL divergence loss actually resulted in a few percentage decrease in the performance of the model (acc 0.818, auc 0.867) compared to a cross-entropy loos (acc 0.859, auc 0.897). This means that there is a small drawback of using our elastic deformation method, although this is compensated if we use the right parameters for ?? and ?? as we saw in the previous section.

## Conclusions
After seeing some promising results by [4] on the JSRT X-ray dataset, we set out to see whether such results would generalize to other medical imaging datasets, such as MedMNIST [2]. We did find some promising results, especially on the BreastMNIST subset of the data. We also identified some topics for further exploration, such as exploring the effect of invariance to elastic deformation on any of the remaining six datasets, or for example hyperparameter tuning on other subsets of the data. All in all we hope that the our research provides more insight in the domain of medical imaging and siamese networks.

## Code availability
All code used for the present research is available via the following links:
- [Training ResNet-18 on MedMNIST](https://drive.google.com/file/d/1Np-hT4Tcqanhe2JT6iODx01CVFVVM0bN/view?usp=sharing)
- [Training Siamese ResNet-18 on MedMNIST](https://drive.google.com/file/d/1WuoyE3mSQ_NPc2KZJNgcsdAl4F_skvZk/view?usp=sharing)

## References
[1] Chicco, Davide. "Siamese neural networks: An overview." Artificial Neural Networks (2021): 73-94.

[2] Yang, J., Shi, R., Ni, B., 2021. "MedMNIST Classification Decathlon: A Lightweight AutoML Benchmark for Medical Image Analysis", in: 2021 IEEE 18th International Symposium on Biomedical Imaging (ISBI).

[3] LeCun, Y. Cortes, C. and Burges, C.J.C. "The MNIST database of handwritten digits". http://yann.lecun.com/exdb/mnist/

[4] Bortsova G., Dubost F., Hogeweg L., Katramados I., de Bruijne M. (2019), "Semi-supervised Medical Image Segmentation via Learning Consistency Under Transformations." In: Shen D. et al. (eds) Medical Image Computing and Computer Assisted Intervention ??? MICCAI 2019. MICCAI 2019. Lecture Notes in Computer Science, vol 11769. Springer, Cham. https://doi.org/10.1007/978-3-030-32226-7_90
