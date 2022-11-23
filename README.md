# Project Name
CoverMyMeds prediction of patient copayment for prescribed drugs  
## Project Team Members
Craig Franze; Charles D Ruggiero; Mandy Cheung; Shirali Obul;
## Project Overview
Using a set of (SIMULATED) pharmacy data-billing claims that were run from a pharmacy to a third-party payer (insurance plan) who covers some portion of the prescription drug price on behalf of a patient. As part of the claim process the amount that the payer reimburses the pharmacy and copayments required of the patient are set by complicated negotiations and contracts between the drug manufacturer, the payer, and the pharmacy. Those negotiations also often cover decisions on what drug claims will ultimately be approved (preferred / non-preferred / non-covered formulary status of each drug) based on the relative discounts that the payer can secure relative to other drugs in the same class that may treat the similar types of medical conditions. Therefore, using this claim billing data into machine learning algorithms to build a method of predicting the copayments required of patients ahead of time will be useful for doctors to presribe drugs based on patient affordability. 
## Project Aims:
- Initial goal is to provide doctors information about the costs that their patient would expect to see in the pharmacy in case affordability of the medication was an issue.
- Secondary goal is to also provide information about the potential formulary status of the medication on each insurance plan. 
- Third goal is to develop a method of grouping similar medications together so that all the options under a patient’s insurance could be compared together based on their relative formulary statuses and copayment requirements.
## Data Description 
About 14 million pharmacy transactions from a handful of pharmacies taken over the course of a year 2022 as you can see in the screenshot below:

![Screen Shot 2022-11-22 at 11 31 21 PM](https://user-images.githubusercontent.com/65901034/203469662-3520bf76-1bd7-4cbf-a83a-2c4850bd7df8.png)

A brief description of the identifiers included in this dataset:
- tx_date – The date on which the pharmacy transaction was attempted
- pharmacy – The particular pharmacy where the transaction was attempted
- diagnosis – The diagnosis of the patient associated with the transaction
- drug – The drug that the patient was prescribed that the pharmacy is attempting to bill
- bin – The broadest identifier of a patient’s insurance plan (banking identification number) 
- pcn – An identifier that more narrowly specifies a plan underneath the broader “bin”
- group – Another identifier that more narrowly specifies a plan underneath the broader “bin” rejected – Whether the billing transaction was rejected by the plan
- patient_pay – The amount of copayment for which the patient is responsible

## Data EDA Process

