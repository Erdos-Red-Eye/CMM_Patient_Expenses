## Project: CoverMyMeds prediction of patient copayment for prescribed drugs  
### Project Team Members
Craig Franze PhD; Charles D Ruggiero PhD; Mandy Cheung PhD; Shirali Obul PhD;
## Project Overview
Using a set of pharmacy data-billing claims that were run from a pharmacy to a third-party payer (insurance plan) who covers some portion of the prescription drug price on behalf of a patient. As part of the claim process the amount that the payer reimburses the pharmacy and copayments required of the patient are set by complicated negotiations and contracts between the drug manufacturer, the payer, and the pharmacy. Those negotiations also often cover decisions on what drug claims will ultimately be approved (preferred / non-preferred / non-covered formulary status of each drug) based on the relative discounts that the payer can secure relative to other drugs in the same class that may treat the similar types of medical conditions. Therefore, using this claim billing data into machine learning algorithms to build a method of predicting the copayments required of patients ahead of time will be useful for doctors to presribe drugs based on patient affordability. 
### Project Aim:
- Main goal of the project is to provide doctors information about the costs that their patient would expect to see in the pharmacy in case affordability of the medication was an issue.

### Data Source and Description 
[Data](https://drive.google.com/drive/folders/1ARlKaPluI1mNB7A-Vd7eHrddBdLt60m4) 
: About 14 million pharmacy transactions from a handful of pharmacies taken over the course of a year 2022 as you can see in the screenshot below:
<p align="center">
<img src= "https://user-images.githubusercontent.com/65901034/203469662-3520bf76-1bd7-4cbf-a83a-2c4850bd7df8.png"  />
</p>

A brief description of the identifiers included in this dataset:
- tx_date – The date on which the pharmacy transaction was attempted
- pharmacy – The particular pharmacy where the transaction was attempted
- diagnosis – The diagnosis of the patient associated with the transaction
- drug – The drug that the patient was prescribed that the pharmacy is attempting to bill
- bin – The broadest identifier of a patient’s insurance plan (banking identification number) 
- pcn – An identifier that more narrowly specifies a plan underneath the broader “bin”
- group – Another identifier that more narrowly specifies a plan underneath the broader “bin” rejected – Whether the billing transaction was rejected by the plan
- patient_pay – The amount of copayment for which the patient is responsible

### Tools 
- ##### Python packages and libraries: Numpy, Pandas, Skitlearn
- ##### Machine learning algorithms (see in machine learning section)


## Data EDA Process
#### Cleaning the data
- Drop duplicatesd rows across all the columns in the dataset.
#### Percentage of Accepted and Rejected claims in the dataset:
<p align="center">
<img src="https://user-images.githubusercontent.com/65901034/203902584-384e719c-5af3-4bfd-af36-e17d9a8e5b88.png"/>
</p>

#### The distribution of patient copayment in the dataset, both linear and log10 scale:

<p align="center">
 <img src="https://user-images.githubusercontent.com/65901034/203912922-e2c61aa1-e3af-4c08-a24e-42deacaa20ac.png"/> <img src="https://user-images.githubusercontent.com/65901034/203912993-476304f7-47db-48c2-b7f4-6739f22f289c.png"/>
<img src="https://user-images.githubusercontent.com/65901034/203914911-89da7c98-ce95-451f-a496-de7b1fce7baa.png"/>
<img src="https://user-images.githubusercontent.com/65901034/203914342-5cd96312-a790-467e-af46-8ca5a3ee2230.png"/>
</p>
- Most of the copayment range in the dataset is 5-60 US dollars.

 

# Machine Learning models:  
## In order to predict patient payment with optimal accuracy, we have tried several models as following:
    - RidgeRegressor
    - RandomForestRegressor
    - GradientBoostingRegressor
    - HistGradientBoostingRegressor
    - CatBootRegressor
Since we have mostly categorical data in our dataset, we tried models that deals with engineered numerical features and original categorical features.
### Models with engennered numerical features:
#### Ridge Regression Model, RandomForestRegressor and GradientBoostingRegressor: 
- ##### Feature analysis
  - Taking only accepted claims for the model and splitting data into training and test sets (test size is 20% of the total dataset)
##### Feature engennering
  - introduce new features on our training data. We introduce 8 features by aggregating over various columns. These features will have two applications :
     - Help us build a model to predict patient pay.
     - Help us impute claim entries when some of the features like drug, group, pcn or pharmacy are missing.
  - correlation among features

<p align="center">
<img src="https://user-images.githubusercontent.com/65901034/204189933-fac0886d-baec-4d9b-a547-5d1202145d79.png"/>
</p>

##### Metrics to determine which model performs better:
    - R2 score
    - Mean absolute error (MAE)
    - Mean squared error (RMSE)
    - Mean absolute percent error(MAPE)
    
##### Train models with training data and comaprision of their performace is below:

<p align="center">
<img src="https://user-images.githubusercontent.com/65901034/205443660-09056a12-fc81-4984-aa9f-0b70e9f637bb.png"/>
</p>

We can clearly see Random Forest and Gradient Boost Regressor Models performed better than ridge regression models. We will choose Random Forests as our final model as it took lesser time to train.

##### Test Random Forests model with test data(unseen data) and results. There are two steps that we have to take to prepare our data for the model:
    - Use the feature engineered variables from the training data and introde these features on the test data.
    - Some of the features would not have been seen during training, so we will carefully fill null entries before testing on the test data.
<p align="center">    
<img src="https://user-images.githubusercontent.com/65901034/205444349-f45f9ca5-df1d-457f-86a6-1ffeaacff311.png"/>
</p>

##### Conclusions: 
  - After doing some error analysis we found that our prediction differs more from actual patient pay for entries where the deductible of the patient pay hasn't been fulfilled yet as you can see below table the claim on Janurary:
<p align="center"> 
<img src="https://user-images.githubusercontent.com/65901034/205444673-cb0d13f9-81b0-4998-87be-70a60e227867.png"/>
</p>

##### Recommendations: 
  - One might train two models with two amounts predicting copay_with_deductible and copay_without_deductible; so that doctors might ask the patient whether they have met the deductible or not and prescribe the drug accordingly.

### Models with Categorical features: 
#### HistGradientBoostingRegressor: 
- In sklearn HistGradientBoostingRegressor has native support for categorical variables allowing for minimal preprocessing of the features in the data set. The categorical variables for insurance (bin, pcn, and group) and drug are transformed into ordinal values using the label encoder.
- Five models are trained to imporve performace from baseline algorithm by changing  defining categorical features, no bound on the number of leaf nodes, adding day of the year or adding month to the features. The comparison of models showed in the following table with performance matrixes:

<p align="center">
<img src="https://user-images.githubusercontent.com/65901034/205551699-cd7b04fe-a8d5-43f9-ba89-e50268010b58.png"/>
</p>

- Model 4 and Model 5 show very similar statistics. Model 5 outperforms Model 4 in two key areas training time and mean MAPE.
- Cross validation of model fitted on the insurance information, drug name and month show the best outcome with a mean average precentage error of ~1.4%.
#### CatBootRegressor:

### In order to predict claims acceptance or rejection, we have tried follwoing classfier models:
- As we have unbalanced data for accepted and rejected claims (9:1 ratio), we need to deal with data balancing for our models. BalancedRandomForestClassifier can balance the data so we directly apply the model, but we did downsampling of accepted claims with XGBoost classifier to optimize performance.
    - BalancedRandomForestClassifier
    - XGboostClassifier


## Conclusions 
-
-
-

## Future perspective
- To develop a method of grouping similar medications together so that all the options under a patient’s insurance could be compared together based on their relative formulary statuses and copayment requirements.

## Presentation and Webapp(prototype)
- [GoogleSlide](https://docs.google.com/presentation/d/1fUMjhsC_x6y-02GUdo7BRB4d-LaayGNRFovKLvo0F7w/edit?usp=sharing)
- [Webapp](https://shiraliobul.github.io/CMM_Copayment_Predictor/)
- This is the prototype of the webapp that we are trying to build in order to provide doctors to type in patient information and show the predicted  copayment and also be able to compare it in the original dataset by filtering the table.  
<p align="center">
<img width="1512" alt="Screenshot 2022-12-07 at 9 19 01 AM" src="https://user-images.githubusercontent.com/65901034/206203954-a50236cf-a2f4-46f3-9308-099e8f08f351.png">
</p>







