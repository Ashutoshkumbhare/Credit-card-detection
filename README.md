# Credit-Card-Defaulters


#Problem Statement

To build such model which identifies whether a customer is going to default for a credit card payment or not. People do not pay their bills or balance are referred as default. Taking interest on the dues is one of the way by which Banks earn, so by solving this problem banks will get a forecast of inflow of the money based on the ML model and it will become easy for banks to divide their strategy.  

#Data Description

The client will send data in multiple sets of files in batches at a given location.
The data contains 32561 instances which follows the attributes:
1.	Limit_Bal : datatype- continuous, Credit limit of a person.
2.	Sex : Categorical : 1 = male, 2 = female.
3.	Education : Categorical: 1 = graduate school ; 2 = university ; 3 = high school ; 4 = other
4.	Marrige: 1 = married ; 2 = single ; 3 = other
5.	Age : num: continuous
6.	Pay_0 to Pay_6 : History of past payment. We track the past monthly payment records (from April to Sep 2005)
7.	Bill ATM1 to Bill ATM6 : Amount of bill statements.
8.	Pay_ATM1 to Pay ATM6 : Amount of previous payments.
Table Label:
Whether a person shall default in the credit card payment or not 
9.	Default payment next months YES = 1, NO = 0
Apart from training files, we also require a "schema" file from the client, which contains all the relevant information about the training files such as:
Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns, and their datatype.

#Data Validation 

In this step, we perform different sets of validation on the given set of training files.  
1.	  Name Validation- We validate the name of the files based on the given name in the schema file. We have created a regex pattern as per the name given in the schema file to use for validation. After validating the pattern in the name, we check for the length of date in the file name as well as the length of time in the file name. If all the values are as per requirement, we move such files to "Good_Data_Folder" else we move such files to "Bad_Data_Folder."

2.	 Number of Columns - We validate the number of columns present in the files, and if it doesn't match with the value given in the schema file, then the file is moved to "Bad_Data_Folder."


3.	 Name of Columns - The name of the columns is validated and should be the same as given in the schema file. If not, then the file is moved to "Bad_Data_Folder".

4.	 The datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If the datatype is wrong, then the file is moved to "Bad_Data_Folder".


5.	Null values in columns - If any of the columns in a file have all the values as NULL or missing, we discard such a file and move it to "Bad_Data_Folder".



#Data Insertion in Database

Here we are using in memory databases i.e MySQL Lite
1) Database Creation and connection - Create a database with the given name passed. If the database is already created, open the connection to the database. 
2) Table creation in the database - Table with name - "Good_Data", is created in the database for inserting the files in the "Good_Data_Folder" based on given column names and datatype in the schema file. If the table is already present, then the new table is not created and new files are inserted in the already present table as we want training to be done on new as well as old training files.     
3) Insertion of files in the table - All the files in the "Good_Data_Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad_Data_Folder".
 
#Model Training 

1) Data Export from Db - The data in a stored database is exported as a CSV file to be used for model training.
2) Data Preprocessing -  We do data preprocessing as per the EDA created. 
3) Clustering - KMeans algorithm is used to create clusters in the preprocessed data. The optimum number of clusters is selected by plotting the elbow plot, and for the dynamic selection of the number of clusters, we are using "KneeLocator" function. The idea behind clustering is to implement different algorithms
To train data in different clusters. The Kmeans model is trained over preprocessed data and the model is saved for further use in prediction.
4) Model Selection - After clusters are created, we find the best model for each cluster. We are using two algorithms, "Naive Bayes " and "XGboost ". For each cluster, both the algorithms are passed with the best parameters derived from GridSearch. We calculate the AUC scores for both models and select the model with the best score. Similarly, the model is selected for each cluster. All the models for every cluster are saved for use in prediction.



#Prediction Data Description

Client will send the data in multiple set of files in batches at a given location. Data contain multiple columns “LIMIT_BAL”, “SEX”, “EDUCATION”, “MARRIAGE”, “AGE”, “PAY_0” to “PAY_6”, “BILL_AMT1” to “BILL_AMT6”, “PAY_AMT1” to “PAY_AMT6”. For prediction there will be 1 column less I.e of “default payment next month”.
Apart from prediction files, we also require a "schema" file from client which contains all the relevant information about the training files such as:
Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns and their datatype.
Prediction Data Validation  
In this step, we perform different sets of validation on the given set of Prediction files.  
1) Name Validation- We validate the name of the files on the basis of given Name in the schema file. We have created a regex pattern as per the name given in schema file, to use for validation. After validating the pattern in the name, we check for length of date in the file name as well as length of time in the file name. If all the values are as per requirement, we move such files to "Good_Data_Folder" else we move such files to "Bad_Data_Folder". 
2) Number of Columns - We validate the number of columns present in the files, if it doesn't match with the value given in the schema file then the file is moved to "Bad_Data_Folder". 
3) Name of Columns - The name of the columns is validated and should be same as given in the schema file. If not, then the file is moved to "Bad_Data_Folder". 
4) Datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If dataype is wrong then the file is moved to "Bad_Data_Folder". 
5) Null values in columns - If any of the columns in a file has all the values as NULL or missing, we discard such file and move it to "Bad_Data_Folder".  

#Prediction 

1) Data Export from Db - The data in the stored database is exported as a CSV file to be used for prediction.
2) Data Preprocessing – All Preprocessing steps that are applied while training data that all steps will apply here also.
3) Clustering - KMeans model created during training is loaded, and clusters for the preprocessed prediction data is predicted.
4) Prediction - Based on the cluster number, the respective model is loaded and is used to predict the data for that cluster.
5) Once the prediction is made for all the clusters, the predictions I.e. whether the client is default or not is saved in a CSV file at a given location and the location is returned to the client.
