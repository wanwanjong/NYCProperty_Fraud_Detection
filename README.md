# NYC Property Fraud Detection

**Principle Component Analysis & Autoencoder**

**Tools: Python (Keras/scikit-learn/pandas/numpy/matplotlib)**

The goal of this project is to detect anomalies and potentail fraud events by analyzing over one million New York property data within year 2010 and 2011. The following are the outline of the analysis. For a detailed description of the project, please refer to [NY_FraudAnalysis_Report.pdf](https://github.com/wanwanjong/NYCProperty_Fraud_Detection/blob/master/NY_FraudAnalysis_FinalReport.pdf).


## 1. Exploratory Data Analysis and Data Quality Report

1.1 Exploratory data analysis: [1.Explore_data_Amy.ipynb](https://github.com/wanwanjong/NYCProperty_Fraud_Detection/blob/master/1.Explore_data_Amy.ipynb)

1.2 [Data Quality Report](https://github.com/wanwanjong/NYCProperty_Fraud_Detection/blob/master/DataQualityReport_Amy.pdf): includes exploratory analysis and detailed description of each field.


## 2. Data Cleaning - Fill Missing Values
[2.DataCleaning_Amy.ipynb](https://github.com/wanwanjong/NYCProperty_Fraud_Detection/blob/master/2.DataCleaning_Amy.ipynb)

We only used the following fields in the original dataset:
*LTFRONT, LTDEPTH, BLDFRONT, BLDDEPTH, FULLVAL, AVLAND, AVTOT, STORIES, B, TAXCLASS, ZIP*

## 3. Created 45 Expert Variables
To detect abnormalities efficiently, we needed further information beyond the original dataset. So, we created 45 variables according to the following method.
#### 3.1 Create 3 size variables
- Lot Area (lotarea) = LTFRONT * LTDEPTH
- Building Area (bldarea) = BLDFRONT * BLDDEPTH
- Building Volume (bldvol) = bldarea * STORIES

#### 3.2 Divide FULLVAL, AVLAND, and AVTOT by each size variables
After dividing FULLVAL, AVLAND, and AVTOT by every size variables created in the previous stage, we have a total of 9 variables, noted as r1 to r9.
#### 3.3 Divide r1 to r9 by 5 groups average
After this stage, we will have 45 variables ready for the rest of the analysis.

## 4. Performed Principle Component Analysis to Reduce Dimensionality
[3.PCA&Autoencoder_Amy.ipynb](https://github.com/wanwanjong/NYCProperty_Fraud_Detection/blob/master/3.PCA%26Autoencoder_Amy.ipynb)

After performing PCA to the 45 expert variables, I chose the top 8 principle components (accounted for over 95% of the variance) and standardized them for the remaining analysis.

<p align="center">
  <img width="600" height="450" src="https://github.com/wanwanjong/NYCProperty_Fraud_Detection/blob/master/Graphs/PCA.png">
</p>

## 5. Calculated 2 fraud scores using Heuristic Function and an Autoencoder Model
#### 5.1 Heuristic Function
For each record, there are z-scores of PC1, PC2, ..., to PC8. The fruad score_1 for a record is defined as:

<p align="center">
  <img width="212" height="92" src="https://github.com/wanwanjong/NYCProperty_Fraud_Detection/blob/master/Graphs/score_1.png">
</p>

I then created a rank column for score_1, named as rank_1.

#### 5.2 The Autoencoder
I used Keras package in Python to train an autoencoder model. The structure of the auencoder is shown below, which consists of 3 layers. Both the input layer and output layer have 8 neurons, and the hidden layer has 4 neurons.

<p align="center">
  <img width="600" height="400" src="https://github.com/wanwanjong/NYCProperty_Fraud_Detection/blob/master/Graphs/autoencoder.png">
</p>

After fitting the trained autoencoder to the z-scores of PC1 to PC8, we got the reconstructed z-scores from the output of the autoencoder model. Set the input z-scores as X1 to X8, and the reconstructed z-scores as xNew1 to xNew8. For each record, the fraud score_2 is defined as:

<p align="center">
  <img width="295" height="92" src="https://github.com/wanwanjong/NYCProperty_Fraud_Detection/blob/master/Graphs/score_2.png">
</p>

Similarly, I then created a rank column for score_2, named as rank_2.

It turned out that the top ten records ranked by the two methods are highly overlapped.

## 6. Ranked all records using the weighted average of rank_1 and rank_2

<p align="center">
  <img width="319" height="32" src="https://github.com/wanwanjong/NYCProperty_Fraud_Detection/blob/master/Graphs/rankfinal.png">
</p>

According to FinalRank, the following tables show the top 10 anomalous records with 11 original variables in the NY property dataset.

**Table 1**

<p align="center">
  <img width="600" height="400" src="https://github.com/wanwanjong/NYCProperty_Fraud_Detection/blob/master/Graphs/top10_1.png">
</p>

**Table 2**

<p align="center">
  <img width="600" height="400" src="https://github.com/wanwanjong/NYCProperty_Fraud_Detection/blob/master/Graphs/top10_2.png">
</p>

## Conclusion
Through further investigation of these 10 records, I concluded the following causes of abnormalities. 
1.	Incorrect data inputs
2.	Mortgage fraud 
3.	Tax avoidance


