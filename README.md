# DATA602 Baltimore Arrest Data Classification

## Data = Arrests.csv
   Recieved from Baltimore Open City Database (https://data.baltimorecity.gov/datasets/619ec10c14b346f784a5a07bad4c43cd_0/about)
   
## Project Description:
The Baltimore Police Agency launched a massive overhaul to its new Records Management Systems back in May 2020. The improvement will enable the department to migrate from a paper-based system to a completely digital reporting environment. As a consequence of this major shift, we had significant difficulties in appropriately transferring data from the new records system to the previous Open Data Baltimore system. The "Arrests" dataset is one of many open datasets made publicly accessible by Baltimore's police department on the city's Open Data website. This information is provided to us in order to foster more openness and data exchange between the local administration and its residents. This dataset contains arrest records for offenses such as assault, theft, and property damage in the City of Baltimore. [1]

I want to use their database create a model that predicts which district a crime occurs based on various details related to the arrest of the perpetrator such as what he or she was charged with, their gender, etc. I will conseder the model to be a success if it can predict the Districe a crime occured at least 80% of the time. My hope with this model is that the department can then use this model to efficiently spread their resources tackling the more likely arrests that would be made in a certain district as well as modify their policing efforts to decrease bias in said policing efforts in certain districts (if any).

[1] “Baltimore Police Department.” Crime Stats | Baltimore Police Department, https://www.baltimorepolice.org/crime-stats. 


## EDA Findings
The following features will not contribute to my classification problem and was dropped.
* 'X', 'Y', 'RowID', and 'ArrestNumber': are all similar indexing variables that will provide nothing to the classification
* 'ArrestLocation', 'IncidentLocation', 'Neighborhood': This wouldnt fit my classification problem as my model shouldnt need location information to determine the district the crime happened
* 'Latitude', 'Longitude', and 'GeoLocation' all provide the same information. This wouldnt fit my classification problem as my model shouldnt need location information to determine the district the crime happened
* 'Shape': Shape is a useless feature column that provides no information and is mostly filled with NA values 
* Charge, post: Charge and Charge Description hold what is basically the same information. Post is a 3 digit code relating to someone's charge. Thus when I throw them into a OHE they will provide redundancy which is bad. 

I seem to have a couple NA values in Age, Gender, and Race (Ignore District for now). Due to the sheer size of the data, I can fill those with a value to denote that they were "unknown". But if they already have a value denoting "unknown" that would muddy the purity of the data. So before making that modification, I'll check for unique values for gender and race.
- If **age** has no "unknown" value already then substituting 0 for all
non positive age should suffice
  - I will check this by simply checking if all ages are positive numbers
- Gender should have 2 unique values. If it does then I know that NA values are most likely due to the gender being unknown at the time of the arrest according to my father who is a correctional officer
- Similarly I will check the unique values of each of the values in race. If the values all relate to a particular race and/or contain a letter denoting "other race", I will consider my "unknown" hypothesis to be true and will modify the NAs accordingly.

The initial NA check provided one more Row with an astonishing amount of NA values, My target column, District. Since the anount of NA values are so large and since its the target column for my classification, the resolution of this issue will require a more nuanced approach.

After discussing the issue with my professor, I've descided to take a semi-supervised approach to handling the NA values.

Arrest Data Is collected All day from: 
2014-01-01
To
2021-11-05

The 5 number summary, provided the following information:
- The most common way to get arrested is to not show up to your appointed court date. Whether its a major or minor offence, one should always try to make their court date.
- Most arrests seem to be made around 11am.
- The most amount of arrests made on a single day was on January 9th 2014
- In the 7 years this data spans, the black males are the most likely to be arrested. There are many articles backing up policing bias that contributed to this trend. I will leave some articles below.

The Spearman rank correlation test and the chi-squared test results showed that the data that I will be using for modeling had no multicolliniarity with each other
## Classification Results
The models were not able to an accuracy of over 25%. This model would ot be satisfactory to use in procuction if we wanted to try and predict where a crime occured

I initially determined that accuracy would be the most important metric because if the model could accurately determine where a crime will occur, the police department would be able to efficiently delegate their resources to prepare for the perceived crime. If the model was striving for high recall, this could cause bias in policing efforts leading to false incarcerations. This could also lead the department to using unneeded resources in one District while another District is left understaffed.

## Conclusion
In this project, I aimed to build a model that could estimate the District where a crime occurred based on the dataset's numerous non-location based variables. The methods that I used did not produce an accuracy rate of over 25%. This did not come anywhere near my 80% threshhold leading me to believe the following:
- The seven features alone are insufficient to adequately identify the District.

- Insufficient rows were utilized to train the data.

- the methodologies were insufficient to predict the District with just a few columns of data.

## Future plans
The following will be applied in the future
* Semi Supervised Learning
   * In this project, I attempted to create a model that could predict the District a crime occured from the various non-location based information in the dataset. However, in my attempt, I removed over half the instances by dropping all rows that had a NA value in the District column. Prior to the drop the dataframe had 175923 rows. After droping all rows that had an NA value, 89891 were the number of rows left. I lost 86032 arrests that could have been used to train the model. That is a significant loss in data that I can use to train the model. In the future, I will leave the district how it was prior to the row drop and take a semi-supervised approach during the Modeling, Processing, and Cleaning steps
* Another Method I will attempt in the future is to use the Support Vector Machine Methodology to see whether we can increase the accuracy score. SVM is a very costly and time consuming method, however with a more powerful computer and more time, I would perform the method similar to how I have it below then include it in my ensemble
* Another method I plan to use in the future is Adaptive Boosting or ADABOOST. The AdaBoost algorithm is a machine learning approach that is utilized as an Ensemble Method. It manipulates the weights of each of the columns to try and make up for improperly categorized instances. The purpose of ADA Boost in supervised learning is to decrease bias and variation. In layman's terms, the AdaBoost algorithm is a method available to me as a way to increase the accurracy score of the Logistic Regression, SVM, or whatever other method I may learn about in the future.
