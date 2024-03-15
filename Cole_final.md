# Katherine Cole's Final Project- DataScience 223


* The link to the jupyter notebook for the course project can be found here [Link](https://github.com/Kcole3/datasci_223/blob/Neural-networks/exercises/final_project.ipynb)

## The project
* This project will consist of a survival analysis using both standard approaches (Kaplan Meier analysis and Cox proportional hazard models) as well as survival analysis using various neural network methods.  I will apply these methods to a heterogenous population of solid tumor cancer patients of all stages diagnosed between 2015-2018 who received at least 1 dose of checkpoint inhibitor therapy (Valero et al, Nature Communications, 2021). 
* The article and data are publically avaliable from [Link](https://www.nature.com/articles/s41467-021-20935-9)
* The code is adapted from Mbotwa et al., 2021, and is available from [GitHub repository](https://github.com/omariosc/survival-analysis). Please note that the helper functions were provided in the reference code, which I did not modify.
* The main object of the analysis is to test different model structures to determine which algorithm is most predictive of overall survival, as assessed via the C-statistic in a hold-out dataset. Cox proportional hazards analysis will serve as the standard against which other neural network (NN) models will be assessed.
* The context of this project stems from the fact that checkpoint inhibitor clinical trials frequently have survival curves which have crossover and long tails, thus violating the proportional hazards assumption necessary for accurate estimation of risk in CoxPH analysis.
* Skills that were applied in this project include data cleaning and exploratory analysis, semi-parameter survival analysis, and NN model training. For the helper functions, I also invested time in annotating the code to ensure that I understood the different components of the function. By slowly building on the architecture of the NN model, I now feel that I have a better understanding of the different components of these algorithsms. 

## Different algorithms utilized in this project with corresponding C statistics

| Model | C Statistic|
|-----------------|-----------------|
| CoxPH Model #1 | 0.767, 3 fold CV 757 (std: 0.019) |
| CoxPH Model #2- Train | 0.83 |
| CoxPH Model #2- Test |0.663|
| CoxNNet |0.767 |
| Randomized Linear Weights | 0.767 |
| SNN (15,5,1) | 0.768 |
| SNN (15,20,1) | 0.799|
| GNN (30,15,1) | 0.807|
| LCA (10 latent classes) | 0.845|
| LCA10 +GNN Train* (30,15,1)| 0.859 |
| LCA10 +GNN Test (30,15,1)| 0.489 |
| CNN| 0.446 |

*Best performing model

## Specific Models Considered 

* Kaplan Meier and Cox Proportional Hazards Analysis
* Cox Neural Networks
* Randomized linear weights model (adapted from fixed linear weight structure)
* Single neural network
* Generalized neural networks with >1 hidden layer
* Latent class analysis (to identify hidden populations) +/- the addition of GNN architecture
* Convolutional neural network

## Package requirements
* %pip install pandas==1.5.3
* %pip install ydata-profiling==4.6.4
* %pip install scikit-learn==1.2.2
* %pip install scikit-survival==0.22.2
* %pip install matplotlib==3.7.1
* %pip install matplotlib-inline==0.1.6
* %pip install numpy==1.23.5
* %pip install torch==2.0.0
* %pip install lifelines==0.27.4


## The data
* The original data (prior to data cleaning) consisted of a tabular dataset including 2037 individuals with 20 clinical features 
* Features included demographic data (eg. patient age, functional status), cancer-specific data (eg. cancer subtype, stage, tumor mutation burden, neutrophil to lymphocyte ratio), treatment specific data (eg. type of checkpoint inhibitor therapy, line of treatment, response to treatment), and survival data (vital status, progression free survival, and overall survival). Overall survival was defined as death by any cause. Patients who were alive at the time of final review were censored at last contact.
* All features were categorical excluding tumor_mutational burden and neutrophil to lymphocyte ratio, which were continuous.

## Project components

### Step 1- Data Cleaning
* Column names were renamed, and analysis was restricted to adults who have non-gynecologic solid organ tumors with subtypes generally responsive to checkpoint inhibitor therapy, and who had a sufficient number of observations. Cancer subtypes included lung, genitourinary, lower gastrointestinal, upper gastrointestinal, head and neck, and melanoma cancers.
* Age was recategorized into 10 year blocks until age 80, which included remaining participant age groups. Recategorization was pursued to reduce the number of category levels, which was causing challenges with CoxPH convergence.
* Category levels were collapsed for instances where specified levels had low counts (eg. variables with low counts were collapsed with other categories in the stage and ECOG/functional status categories)

### Step 2- Exploratory Analysis and Data Processing

* A correlation matrix was performed for the continuous variables to assess for potential collinearity issues, and correlations between the continuous variable and the vital status variable were also pursued.
* Categorical variables with no inherent order were converted to numeric dummy variables via one hot encoding.
* Categorical variables with an inherent order were converted to numerical variables using Ordinal Encoding 
* Continuous variables were scaled
* A YData_Profiling summary was created
* For simplicity, complete case analysis was conducted, and missing data was removed
* The final dataset included 1378 individuals, and 15 features were selected for inclusion in the model (see below)

### Step 3- Generate Kaplan Meier Curves and Cox Proportional Hazard Models

* Data was split into train (85%) and test (15%) data. The test data was kept as a holdout to assess predictive capacity in both the CoxPH model, and the NN model with the best predictive accuracy. Of note, Valero et al. did have their own train and validate data as part of the dataset, however, I preferred to resample to limit confounding by factors such as time effect.
* The probability of survival was initially estimated via Kaplan Meier survival curves
* Cox Proportional Hazard analysis was then pursued- Two methods of CoxPH were evaluated: CoxPHSurvivalAnalysis through sksurv.linear.model AND CoxPHFitter through via lifelines
* The final model included sex (2 levels), drug_class (2 levels), progression (2 levels yes/no), cancer_type (6 levels), overall survival (continuous), vital_status(2 levels- dead(1) vs alive(0))
#neutrophil to lymphocyte ratio (continuous), tumor mutational burden (continuous), age (10 year categories), best response to treatment (4 categories- progressive disease, stable disease, partial response, complete response), cancer stage (2 categories- local vs advanced), functional status (ECOG- 3 categories, 0, 1, 2+)
#and microsatellite instability (3 levels)
* The C statistic for the CoxPH through CoxPHSurvivalAnalysis was 0.767 Following 3 fold CV, the C statistic was 0.757 (std: 0.019)

* A series of helper functions were then developed by Mbotwa et al., 2021 which helped to implement the negative partial log likelihood as a loss function. THis will be the loss function in all subsequent models
* A second method of CoxPH was fit using CoxPHFitter. The beta coefficients are slightly different between the two methods, which has been flagged as an inconsistency by other authors. [Link](https://gist.github.com/jackyko1991/bd0e605fa03b2c3e244d08db2b68edd8)
* For this approach, the C statistic was 0.83. It is beyond the scope of this project to deeply delve into the reasons for the differences between these two methods, however, in subsequent analysis, implementation of simple neural networks using Cox methodology (eg. CoxNN) yielded C-statistic scores which were compatible with the score obtained through CoxPHSurvival Analysis (method #1). I also experimented with L1 and L2 penelties using the CoxPHFitter approach, which did not contribute to an improved model fit.

* The C statistic in the test holdeover (done using the lifelines method for simplicity as there is a built in function) was 0.663, which will be used to the holdout score from the best performing NN.

### Step 4- Survival Analysis with Cox Neural Net

* CoxNN is a 2 layer neural network (one hidden layer) which uses the hidden layer activation level to perform Cox regression.
* Gradient descent is then utilized to maximize the partial log likelihood, for which a helper function was created by Mbotwa et al. A function generalizable to other NN's was also created.
* I also generated a plot which examines the risk of individuals over time who did/did not have the event. We do see some seperation of risk between those with/without the event, and individuals who were at the highest risk had the event early on, which makes sense clinically.

* The C-statistic for a simple initialized mCox NN is 0.767, which is equivalent to the CoxPH estimated through CoxPHSurvival Analysis. This suggests that it was implemented correctly.

### Step 5- Variable Weights Model

* A fixed weight model was then initialized which achieved a comparable C-statistic to CoxPH 0.767
* This model was then adapted to enable randomized weights which were then trained through gradient descent.
* I left the learning rate at 0.0001, and the number of epochs at 4000
* The resulting C statistic from the trained model was the same as the COX model 0.767

### Step 6- Single Neural Network

* At this point, a simple fully connected neural network with a single hidden layer and single output was initialized.
* The ReLu activation function was used which returns the value of itself when the input is positive, and zero otherwise. It helps to introduce non-linearity into the model.
* The initial model was specified as input = # of features, hidden layer = sqrt (# features) and 1 output layer
* We now see the first improvement in the C statistic of 0.768
* A plot was generated which examined the relationship between the number of EPOCHS and the change in the negative partial log likelihood loss. We note that the loss decreases with increasing # of EPOCHS, however, we do not see a plateauing of the curve, suggesting that more EPOCHS would be helpful in improving the loss. Unfortunately, the kernal kept "dying" with EPOCHS more than 2000. THe next step would be to see how modifying the depth of the hidden layer changes the loss.
* The depth of the hidden layer was then tuned up to a depth of 2X the input layer to mitigate risk of overfitting and "dead" neurons.
* We note an improvement in both the loss and the C-statistic with an increase in the depth of 20 for the hidden layer 
* The C statistic has now improved further to 0.799, with an optimal depth of 20 for the hidden layer
* We note the majority of the weights are near zero

### Step 7- Generalized Neural Networks (GNN) with > 1 Hidden Layer

* The model was further generalized to allow the presence of more than 1 hidden layer, again using ReLu activation functions
* The first hidden layer was arbitrarily selected to be 2X the input layer, with the 2nd hidden layer selected to be equal to the input layer (ie 15, 30, 15, 1). The number of EPOCHS was set to 1000
* We now see that increasing both the depth AND the number of hidden layers leads to an improvement in the C-statistic, now 0.807. The EPOCH vs loss curve reveals increased variability in loss beginning at an EPOCH of 600. We again note the absence of pleateuing of the curve


### Step 8- Latent Class Analysis

* A clustering approach to identify hidden subgroups was pursued via LCA, which uses a KMeans clustering algorithm. The C statistic for LCA alone was 0.845
* LCA was performed on 10 latent classes. The number of classes were determined based on the number of distinct cancer subtypes represented in the dataset (Small cell lung cancer, non-small cell lung cancer, melanoma, hepatobiliary cancer, gastric cancer, pancreatic cancer, renal cancer, bladder, esophgeal, and head and neck cancer). The C statistic was 0.845
* Each subgroup was then individually trained in the architecture of the best GNN identified above. The resulting C statistic was 0.859.


### Step 9- (Simple) Convolutional Neural Networks

* The purpose of this part of the project was primarily to understand the structure of the CNN in greater detail, of which I have annotated the reference code.
* Patient features were converted to images in a fully connected model, and ReLu activation functions are again used. 
* In this relatively small and simple dataset, the model performed quite poorly, which is not surprising. 
* The C statistic was essentially no better than chance = 0.446


## Discussion

* Overall, the model with the best performance was the LCA (classes=10) +GNN model, with a C- statistic of 0.859 in the training data
* In the testing data, however, the C statistic was only 0.489, essentially no better than random chance. This indicates significant overfitting of the training data, with poor predictive capability of the model to estimate survival in the test population.
* This finding may also be due to too few EPOCHS being run on the training data (as mentioned above, we did not yet reach a plateau in EPOCH vs loss curve), indicating that additional EPOCHs could be helpful, and may improve performance in the test data. Moreover, additional hidden layers did seem to improve predictive capabilities, and this is something that could be assessed in future projects.
* For now, the predictive capabilities of the CoxPH model was superior in the test data. (C statistic = 0.663)


## Next Steps

* Tuning the best model with different activation functions, ranges of hidden layers, learning rates, EPOCHS, etc.
* Incorporating raw image data from staging scans with tabular clinical data into a hybrid NN model.