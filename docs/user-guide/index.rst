========
Overview
========

In this section, we’d like to go over the end-to-end Machine Learning model development using Kubeflow’s features. The overall model development workflow could be split into two major parts: Training and Inference. During the Model Training phase, we want to build a champion model that can fulfill the problem we need to address and the performance to our satisfaction. 


Model Training
--------------

Data Processing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Data Exploration
""""""""""""""""

Let’s start with the data exploration where we can learn more about the data to understand the possible solution to the problem we need to address. **Jupyter Notebooks** is equipped in the Kubeflow core components to provide such feature. In Jupyter Notebook the user could share data, code, and experiments.

Data Preparation
""""""""""""""""

In order for ML algorithms to be effective, the traditional ETL (Extract, Transfer, Load) method can be applied to raw data to assure the quality of the data suitable for the models. Kubeflow offers some tools to support this:

- Apache **Spark** (the most popular tool to handle big data): Spark can handle a variety of formats and data sizes and are designed to scope with user’s data exploration environment.

- TensorFlow Transform (**TFT**): TFT helps to process the raw data efficiently in the context of the entire dataset.

Feature Selection
"""""""""""""""""

Feature engineering is the process to transforming the raw data into features that the ML model can use.Often time, it is the most time-consuming part of the data processing and results in high amount of development and repeated features across projects. Kubeflow offers the feature store (feast) to user 
overcoming this time inefficiency and effort duplication.

Model Development
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After the data processing tasks are done and a good set of data and features is identified, the user is ready to build and train the model. Kubeflow offers rich features to cover this process.

Model Training
""""""""""""""

Once the user has constructed a model from the algorithm, he can employ the Kubeflow **Training Operators** to perform the model training. The list of the Training Operators offered by Kubeflow is growing from coming releases. Here is the highlights:

- TensorFlow (TFjob)

- PyTorch (PyTorchjob)

- Apache MXNet (MXjob)

- XGBoost (XGBoostjob)

- MPI (MPIjob)

- PaddlePaddle (Paddlejob)


By employing these operators, users can effectively manage the model training process, monitor progress, and scale their experiments to find the best algorithm. With Kubeflow’s operators, users have the flexibility to tackle complex machine learning tasks while minimizing infrastructure complexities.

Model Tuning
""""""""""""

Hyperparameters are the variables that control the model training process. The examples for hyperparameters are: 

- The learning rate in a neural network

- The numbers of layers and nodes in a neural network

- Regularization

- Type of loss function

Hyperparameter tuning is the process of optimizing the hyperparameter values to maximize the model metrics 
such as accuracy in validation phase.

Kubeflow offers **Katib** to automate the hyperparameter tuning process by automatically tuning the target variable which user specifies in the configuration. Katib offers exploration algorithms such as random search, grid search and Bayesian to perform the hyperparameter evaluation and tries to achieve the optimal set of hyperparameters for the given model.

Model Validation
""""""""""""""""

We can use Kubeflow’s **Experiments** and **Runs** to compare the metrics of a given model across multiple models. For example, these may be the same model trained on different datasets, or two models with different hyperparameters trained on the same dataset. By using the Kubeflow’s **Pipeline**, uses can automate these processes to report whether a model ran smoothly or encountered some problems.


Data Storage
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Shared Storage
""""""""""""""

To host data used in the common data access during the creation of the model such as using pipeline and saving results of the experiments, some sort of external and distributed storage server can be the solution. Different cloud providers have different storage offerings, for example Amazon S3, Azure Data Storage, Google Cloud Storage. Due to the complexity to deal with many storage offerings, Kubeflow ships with **MinIO** to reduce the dependency to rely on the storage offerings from different cloud providers by acting as a common gateway to public cloud storage APIs. This gateway option is the most flexible, and allows the users to create cloud independent implementation without scale limits.

Model Registry
""""""""""""""

This is a storage unit that holds model specific data (classes) or weights. Its purpose is to hold trained models for fast retrieval by other applications. Without the model registry, the model classes and weights would be saved to the source code repository and hard to retrieve.

Metadata Database
"""""""""""""""""

Metadata of a model is to hold the collection of the datasets and the transformation of these datasets during the data exploration. Capturing the metadata lets the users to understand the variations during the model experiments phase. This understanding can help the users iteratively develop and efficiently improve the models. Kuebflow employs ML Matadata (**MLMD**) library to faciliate this enhancement.



Model Inference (Model Serving)
-------------------------------
Once the model is selected from the validation where the metrics are met, we can deploy the model to the 
production environment. This trained and then deployed model acts as a service that can handle prediction 
requests. 

Kubeflow simplifies the model deployment by dealing the given model‘s different model format using **Seldon Core**, **TFServe** and **KFServe**. The model-as-data methodology is used by these implementations to leverage an intermediate model format and Kubeflow allows the swapping between model frameworks as smoothly as possible.

Kubeflow also handles the infrastructure complexities such as modeling monitoring, scaling, revisioning during the model serving. The hosted models could be updated with newer version to fit the current dataset better and therefore increase the performance metrics. They can be rollback to previous version if certain problems are encountered after deployment. These kinds of model management can be handled smoothly and automatically with Kubeflow without much of the human involvement.