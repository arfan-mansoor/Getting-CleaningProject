## CodeBook
This document describes the data and transofrmations used by run_analysis.R and the definition of variables in Tidy.txt.

## Dataset Used
This data is obtained from "Human Activity Recognition Using Smartphones Data Set". The data linked are collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones.

The data set used can be downloaded from https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip. 

## Input Data Set

The input data containts the following data files:

   * Item 1 X_train.txt contains variable features that are intended for training.
   * Item 2 y_train.txt contains the activities corresponding to X_train.txt.
   * Item 3 subject_train.txt contains information on the subjects from whom data is collected.
   * Item 4 X_test.txt contains variable features that are intended for testing.
   * Item 5 y_test.txt contains the activities corresponding to X_test.txt.
   * Item 6 subject_test.txt contains information on the subjects from whom data is collected.
   * Item 7 activity_labels.txt contains metadata on the different types of activities.
   * Item 8 features.txt contains the name of the features in the data sets.

## Transformations
Following are the transformations that were performed on the input dataset:

  * Item 1  X_train.txt is read into featuresTrain.
  * Item 2  y_train.txt is read into activityTrain.
  * Item 3  subject_train.txt is read into subjectTrain.
  * Item 4  X_test.txt is read into featuresTest.
  * Item 5  y_test.txt is read into activityTest.
  * Item 6  subject_test.txt is read into subjectTest.
  * Item 7  features.txt is read into featureNames.
  * Item 8  activity_labels.txt is read into activityLabels.
  * Item 9  The subjects in training and test set data are merged to form subject.
  * Item 10 The activities in training and test set data are merged to form activity.
  * Item 11 The features of test and training are merged to form features.
  * Item 12 The name of the features are set in features from featureNames.
  * Item 13 features, activity and subject are merged to form completeData.
  * Item 14 Indices of columns that contain std or mean, activity and subject are taken into requiredColumns .
  * Item 15 extractedData is created with data from columns in requiredColumns.
  * Item 16 Activity column in extractedData is updated with descriptive names of activities taken from activityLabels. Activity column is expressed as a factor variable.
  * Item 17 Acronyms in variable names in extractedData, like 'Acc', 'Gyro', 'Mag', 't' and 'f' are replaced with descriptive labels such as 'Accelerometer', 'Gyroscpoe', 'Magnitude', 'Time' and 'Frequency'.
  * Item 18 tidyData is created as a set with average for each activity and subject of extractedData. Entries in tidyData are ordered based on activity and subject.
  * Item 19 Finally, the data in tidyData is written into Tidy.txt.

##Output Data Set
The output data Tidy.txt is a a space-delimited value file. The header line contains the names of the variables. It contains the mean and standard deviation values of the data contained in the input files. The header is restructued in an understandable manner. 

