# TidyData
Getting and Cleaning Data -- Final Project

## Step 0: Load the proper library, download, and unzip the file.

#### Make sure the library is there
library(dplyr)

#### Check if the file already exist or not.
filename <-"getdata-projectfiles-UCI HAR Dataset.zip"

if (!file.exists(filename)){
    filreURL <-"https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
    download.file(fileURL, filename, method = "curl")
}

#### Check if the unzipped folder exist
if (!file.exists("UCI HAR Daaset")){
    unzip(filename)
}

## Step 1: Read and Merge the data


#### read all data. Make sure that X_aaaaa.txt and y_aaaaaa.txt (case sensitive)!!!!! 

features <- read.table("UCI HAR Dataset/features.txt", col.names = c("n", "functions"))

##### This contains the functions of each vairables in X_test/train.

activities <- read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("code", "activity"))

##### This contains the activities codes in y_test/train

subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "subject")

##### subject IDs for test

x_test <- read.table("UCI HAR Dataset/test/X_test.txt", col.names = features$functions)

##### values of functions in test

y_test <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "code")

##### avtivity ID for test

subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names = "subject")

##### subject ID for train

x_train <- read.table("UCI HAR Dataset/train/X_train.txt",col.names = features$functions)

##### values of variables in train

y_train <- read.table("UCI HAR Dataset/train/y_train.txt",col.names = "code")

##### activity ID for train

#### Prepare the pre-tidy data by mergering test and training sets together.

#### Then, extract the "mean" and "std" from the data set.

#### In terms of naming, think about B-cell development. Pro-B -> Pre-B -> B lol



DataSet <- rbind(x_train, x_test)

##### combined x_train and x_test

Activity <- rbind(y_train, y_test)

##### combined y_train and y_test

Subject <- rbind(subject_train, subject_test)

##### combined from the aboved two

Pro_TidyData <- cbind(Subject, Activity, DataSet)
##### Prototype data (Pro_Tidy), all merged data

## Step 2: Extract the Mean/Std, and Organize the naming of each activiy/subject

Pre_TidyData <- Pro_TidyData %>% select(subject, code, contains("mean"), contains("std"))

##### Priliminary data (Pre_Tidy), inclues selected mean and std.

#### Organzie the naming of the data to make it tidier

Pre_TidyData$code <- activities[Pre_TidyData$code, 2]

names(Pre_TidyData)[2] = "Activity"

names(Pre_TidyData)<-gsub("Acc", "Accelerometer", names(Pre_TidyData))

names(Pre_TidyData)<-gsub("Gyro", "Gyroscope", names(Pre_TidyData))

names(Pre_TidyData)<-gsub("BodyBody", "Body", names(Pre_TidyData))

names(Pre_TidyData)<-gsub("Mag", "Magnitude", names(Pre_TidyData))

names(Pre_TidyData)<-gsub("^t", "Time", names(Pre_TidyData))

names(Pre_TidyData)<-gsub("^f", "Frequency", names(Pre_TidyData))

names(Pre_TidyData)<-gsub("tBody", "TimeBody", names(Pre_TidyData))

names(Pre_TidyData)<-gsub("-mean()", "Mean", names(Pre_TidyData), ignore.case = TRUE)

names(Pre_TidyData)<-gsub("-std()", "STD", names(Pre_TidyData), ignore.case = TRUE)

names(Pre_TidyData)<-gsub("-freq()", "Frequency", names(Pre_TidyData), ignore.case = TRUE)

names(Pre_TidyData)<-gsub("angle", "Angle", names(Pre_TidyData))

names(Pre_TidyData)<-gsub("gravity", "Gravity", names(Pre_TidyData))

##### These are just renaming some of the abbreviations in the names of activities and functions to make them more readable

## Step 3 Take the Average of each activity/subject and export the TidyData

#### Take the average of each activity and subject.

#### Finally, our data is mature enough to be the real TidyData. 

TidyData <- Pre_TidyData %>% group_by(subject, Activity) %>% summarise_all(list(mean=mean))

##### Final version of the TidyData

write.table(TidyData, "TidyData.txt", row.name=FALSE)

##### export the tidy data as a txt file.




