## Getting and Cleaning Data Course Project
## Goal
Companies like FitBit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked are collected from the accelerometers from the Samsung Galaxy S smartphone.

A full description is available at the site where the data was obtained:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

The data is available at:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

The aim of the project is to clean and extract usable data from the above zip file. R script called run_analysis.R that does the following:
The input data containts the following data files:

   * Item 1 Merges the training and the test sets to create one data set.
   * Item 2 Extracts only the measurements on the mean and standard deviation for each measurement.
   * Item 3 Uses descriptive activity names to name the activities in the data set
   * Item 4 Appropriately labels the data set with descriptive variable names.
   * Item 5 From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

In this repository, you find:


   * Item 1 run_analysis.R : the R-code run on the data set

   * Item 2 Tidy.txt : the clean data extracted from the original data using run_analysis.R

   * Item 3 CodeBook.md : the CodeBook reference to the variables in Tidy.txt

   * Item 4 README.md : the analysis of the code in run_analysis.R

## Getting Started
##Basic Assumption

The R code in run_analysis.R proceeds under the assumption that the zip file available at https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip is downloaded and extracted in the R Home Directory.
## Libraries Used
The libraries used in this operation are data.table and dplyr. We prefer data.table as it is efficient in handling large data as tables. dplyr is used to aggregate variables to create the tidy data.
library(data.table)
library(dplyr)

##Read Supporting Metadata
The supporting metadata in this data are the name of the features and the name of the activities. They are loaded into variables featureNames and activityLabels.
featureNames <- read.table("features.txt")
activityLabels <- read.table("activity_labels.txt", header = FALSE)

## Format training and test data sets
Both training and test data sets are split up into subject, activity and features. They are present in three different files. 
##Read training data
subjectTrain <- read.table("train/subject_train.txt", header = FALSE)
activityTrain <- read.table("train/y_train.txt", header = FALSE)
featuresTrain <- read.table("train/X_train.txt", header = FALSE)

## Read test data
subjectTest <- read.table("test/subject_test.txt", header = FALSE)
activityTest <- read.table("test/y_test.txt", header = FALSE)
featuresTest <- read.table("test/X_test.txt", header = FALSE)

## Part 1 - Merge the training and the test sets to create one data set

We can use combine the respective data in training and test data sets corresponding to subject, activity and features. The results are stored in subject, activity and features.
subject <- rbind(subjectTrain, subjectTest)
activity <- rbind(activityTrain, activityTest)
features <- rbind(featuresTrain, featuresTest)

##Naming the columns
The columns in the features data set can be named from the metadata in featureNames

colnames(features) <- t(featureNames[2])

##Merge the data
The data in features,activity and subject are merged and the complete data is now stored in completeData.

colnames(activity) <- "Activity"
colnames(subject) <- "Subject"
completeData <- cbind(features,activity,subject)

##Part 2 - Extracts only the measurements on the mean and standard deviation for each measurement
Extract the column indices that have either mean or std in them.

columnsWithMeanSTD <- grep(".*Mean.*|.*Std.*", names(completeData), ignore.case=TRUE)

Add activity and subject columns to the list and look at the dimension of completeData

requiredColumns <- c(columnsWithMeanSTD, 562, 563)
dim(completeData)

We create extractedData with the selected columns in requiredColumns. And again, we look at the dimension of requiredColumns.

extractedData <- completeData[,requiredColumns]
dim(extractedData)

##Part 3 - Uses descriptive activity names to name the activities in the data set
The V1 "activity" field in extractedData is originally of numeric type. We need to change its type to character so that it can accept activity names. The activity names are taken from metadata activityLabels.
extractedData$V1 <- as.character(extractedData$V1)
for (i in 1:6){
extractedData$V1[extractedData$V1 == i] <- as.character(activityLabels[i,2])
}

We need to factor the V1 "activity" variable, once the activity names are updated.

extractedData$V1 <- as.factor(extractedData$V1)

##Part 4 - Appropriately labels the data set with descriptive variable names
Here are the names of the variables in extractedData 
names(extractedData)
By examining extractedData, we can say that the following acronyms can be replaced:

     * Item 1 Acc can be replaced with Accelerometer

     * Item 2 Gyro can be replaced with Gyroscope

     * Item 3 BodyBody can be replaced with Body

     * Item 4 Mag can be replaced with Magnitude

     * Item 5 Character f can be replaced with Frequency

     * Item 6 Character t can be replaced with Time

     * Item 7 V1 is replaced by name Activity 

names(extractedData)<-gsub("Acc", "Accelerometer", names(extractedData))
names(extractedData)<-gsub("Gyro", "Gyroscope", names(extractedData))
names(extractedData)<-gsub("BodyBody", "Body", names(extractedData))
names(extractedData)<-gsub("Mag", "Magnitude", names(extractedData))
names(extractedData)<-gsub("^t", "Time", names(extractedData))
names(extractedData)<-gsub("^f", "Frequency", names(extractedData))
names(extractedData)<-gsub("tBody", "TimeBody", names(extractedData))
names(extractedData)<-gsub("-mean()", "Mean", names(extractedData), ignore.case = TRUE)
names(extractedData)<-gsub("-std()", "STD", names(extractedData), ignore.case = TRUE)
names(extractedData)<-gsub("-freq()", "Frequency", names(extractedData), ignore.case = TRUE)
names(extractedData)<-gsub("angle", "Angle", names(extractedData))
names(extractedData)<-gsub("gravity", "Gravity", names(extractedData))
names(extractedData)<-gsub("V1", "Activity", names(extractedData))

Here are the names of the variables in extractedData after they are edited

names(extractedData)

##Part 5 - From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject

Firstly, let us set Subject as a factor variable. 
extractedData$Subject <- as.factor(extractedData$Subject)
extractedData <- data.table(extractedData)

We create tidyData as a data set with average for each activity and subject. Then, we order the enties in tidyData and write it into data file Tidy.txt that contains the processed data.
tidyData <- aggregate(. ~Subject + Activity, extractedData, mean)
tidyData <- tidyData[order(tidyData$Subject,tidyData$Activity),]
write.table(tidyData, file = "Tidy.txt", row.names = FALSE)


