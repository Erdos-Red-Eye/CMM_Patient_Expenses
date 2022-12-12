## Project: CoverMyMeds prediction of patient copayment for prescribed drugs  
### Project Team Members
Craig Franze PhD; Charles D Ruggiero PhD; Mandy Cheung PhD; Shirali Obul PhD;
## Project Overview
Using a set of pharmacy data-billing claims that were run from a pharmacy to a third-party payer (insurance plan) who covers some portion of the prescription drug price on behalf of a patient. As part of the claim process the amount that the payer reimburses the pharmacy and copayments required of the patient are set by complicated negotiations and contracts between the drug manufacturer, the payer, and the pharmacy. Those negotiations also often cover decisions on what drug claims will ultimately be approved (preferred / non-preferred / non-covered formulary status of each drug) based on the relative discounts that the payer can secure relative to other drugs in the same class that may treat the similar types of medical conditions. Therefore, using this claim billing data into machine learning algorithms to build a method of predicting the copayments required of patients ahead of time will be useful for doctors to presribe drugs based on patient affordability. 
### Project Aim:
- Main goal of the project is to provide doctors information about the costs that their patient would expect to see in the pharmacy in case affordability of the medication was an issue.

### Data Source and Description 
[Data Source](https://drive.google.com/drive/folders/1ARlKaPluI1mNB7A-Vd7eHrddBdLt60m4) 
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
#### [Cleaning the data](https://github.com/Erdos-Red-Eye/CMM_Patient_Expenses/blob/main/CMM_Copayment_EDA.ipynb)
- Drop duplicated rows across all the columns in the dataset.
#### Percentage of Accepted and Rejected claims in the dataset:
<p align="center">
<img src="https://user-images.githubusercontent.com/65901034/203902584-384e719c-5af3-4bfd-af36-e17d9a8e5b88.png"/>
</p>

#### The distribution of patient copayment in the dataset:

<p align="center">
<img src="https://user-images.githubusercontent.com/65901034/203912993-476304f7-47db-48c2-b7f4-6739f22f289c.png"/>

<img src="https://user-images.githubusercontent.com/65901034/203914342-5cd96312-a790-467e-af46-8ca5a3ee2230.png"/>
</p>

[EDA](https://github.com/Erdos-Red-Eye/CMM_Patient_Expenses/blob/main/CMM_Copayment_EDA1.ipynb)
- Patient payment distribution by drug name:
<p align="center">
<img width="1064" alt="Screenshot 2022-12-12 at 2 24 03 PM" src="https://user-images.githubusercontent.com/65901034/207135971-95c0105f-60f3-49b2-a2dd-5ce95cbf9a8a.png"/>
- Most of the copayment range in the dataset is 5-50 US dollars, 9,325,049 out of 12,561,845 claims is in this range.
</p> 

- In this grapgh 1 is generic (orange color), 0 is branded (blue):
<p align="center">
<img width="1033" alt="Screenshot 2022-12-12 at 2 21 25 PM" src="https://user-images.githubusercontent.com/65901034/207135377-26b60663-5438-4998-a6e8-b9831b81a636.png"/>
- Generic drugs are generally cheaper. Branded drugs are represented at a higher rate in the data set.
 </p>




 

# Machine Learning models:  
## Regressors: 
### In order to predict patient payment with optimal accuracy, we have tried several regressor models as following:
    - RidgeRegressor
    - RandomForestRegressor
    - GradientBoostingRegressor
    - HistGradientBoostingRegressor
    - CatBootRegressor
Since we have mostly categorical data in our dataset, we tried models that deals with engineered numerical features and original categorical features.
### Models with engineered numerical features
#### [Ridge Regression Model, RandomForestRegressor and GradientBoostingRegressor](https://github.com/Erdos-Red-Eye/CMM_Patient_Expenses/blob/main/regression_with_engineered_features.ipynb)):
##### Feature analysis
  - Taking only accepted claims for the model and splitting data into training and test sets (test size is 20% of the total dataset)
##### Feature engineering
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
<p align="center">
<img width="1041" alt="Screenshot 2022-12-08 at 9 01 30 AM" src="https://user-images.githubusercontent.com/65901034/206465567-b445b026-7238-4762-994a-b2f0376e3067.png">
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
#### [HistGradientBoostingRegressor](https://github.com/Erdos-Red-Eye/CMM_Patient_Expenses/blob/main/HGBR_Final.ipynb): 
- In sklearn HistGradientBoostingRegressor has native support for categorical variables allowing for minimal preprocessing of the features in the data set. The categorical variables for insurance (bin, pcn, and group) and drug are transformed into ordinal values using the label encoder.
- Five models are trained to imporve performace from baseline algorithm by changing  defining categorical features, no bound on the number of leaf nodes, adding day of the year or adding month to the features. The comparison of models showed in the following table with performance matrixes:

<p align="center">
<img src="https://user-images.githubusercontent.com/65901034/205551699-cd7b04fe-a8d5-43f9-ba89-e50268010b58.png"/>
</p>
- Model 4 and Model 5 show very similar statistics. Model 5 outperforms Model 4 in two key areas training time and mean MAPE.
- Cross validation of model fitted on the insurance information, drug name and month show the best outcome with a mean average precentage error of ~1.4%.

#### [Demonstration of pickled HGBR Model](https://github.com/Erdos-Red-Eye/CMM_Patient_Expenses/blob/main/DrugComparrison.ipynb)
- Shows a simple application of the HGBR patient pay predicting model that allows a doctor to compare the predicted patient pay for mulitple drugs that have been prescribed for a particular diagnosis. Giving the features to the pickled pipeline in the order of diagnosis, month, bin_num, pcn_num, group_num:
<p align="center">
<img src="https://user-images.githubusercontent.com/65901034/206856460-c8b847eb-e80f-42bc-bc96-e159b51b9799.png"/>
</p>

#### CatBootRegressor:

## Classifiers
### In order to predict claims acceptance or rejection, we have tried follwoing classfier models:
- As we have unbalanced data for accepted and rejected claims (9:1 ratio), we need to deal with data balancing for our models. BalancedRandomForestClassifier can balance the data so we directly apply the model, but we did downsampling of accepted claims with XGBoost classifier to optimize performance.

    - [BalancedRandomForestClassifier](https://github.com/Erdos-Red-Eye/CMM_Patient_Expenses/blob/main/Rejection_BalancedRFClassifier.ipynb)
        -  We have 87.7% accuracy (0 is accepted, 1 is rejected) the confusion matrix, classification report and feature importance for the model are shown below:  
 <p align="center">   
 <img width="241" alt="Screen Shot 2022-12-08 at 3 32 07 PM" src="https://user-images.githubusercontent.com/65901034/206561662-a9063f66-a5e6-416f-82ed-d22d572760df.png"> 
 <img width="683" alt="Screen Shot 2022-12-08 at 3 26 01 PM" src="https://user-images.githubusercontent.com/65901034/206560801-1a372856-a97c-4c94-a9d7-40a3f7cba366.png">
 </p>
 
 

 
    - XGboostClassifier
    We did not have good classification as we got from RandomForestClassifier. 

## Future perspective
- Build separate models for the generic and branded drugs.
- Examine the outliers where the models perform poorly and see if there is a particular commonality amongst them that could explain this.
- Combine engineered features, such as the ones used in the Random Forest model, or rejection rate for example, with the HistGradientBoosting model.
- Develop the app further by integrating these improvements to help doctors easily obtain predicted prices of medications ahead of time.


## Presentation and Webapp(prototype)
- Presentation [GoogleSlide](https://docs.google.com/presentation/d/1fUMjhsC_x6y-02GUdo7BRB4d-LaayGNRFovKLvo0F7w/edit?usp=sharing)
- [Video Presentation](https://www.erdosinstitute.org/project-database) by Search (Red Eye or Team 89) Cohort (Fall 2022)
 
- [Webapp](https://shiraliobul.github.io/CMM_Copayment_Predictor/)
    - This is the prototype of the webapp that we are trying to build in order to provide doctors to type in patient information and show the predicted  copaymen. Moreover, doctors or user be able to compare the predicted price in the original dataset by filtering the table.  
<p align="center">
<img width="1512" alt="Screenshot 2022-12-07 at 9 19 01 AM" src="https://user-images.githubusercontent.com/65901034/206203954-a50236cf-a2f4-46f3-9308-099e8f08f351.png">
</p>







