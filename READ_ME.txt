{\rtf1\ansi\ansicpg1252\cocoartf1347\cocoasubrtf570
{\fonttbl\f0\fswiss\fcharset0 Helvetica;}
{\colortbl;\red255\green255\blue255;}
\margl1440\margr1440\vieww10800\viewh8400\viewkind0
\pard\tx566\tx1133\tx1700\tx2267\tx2834\tx3401\tx3968\tx4535\tx5102\tx5669\tx6236\tx6803\pardirnatural

\f0\fs24 \cf0 ## Getting and Cleaning Data Course Project\
\

\b Purpose
\b0 :\
\
The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit:\
\
a tidy data set as described below;\
a link to a Github repository with your script for performing the analysis; and\
a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md.\
You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.\
\
\

\b Objectives
\b0 :\
\
run_analysis.R performs the following:\
\
1. Merges the training and the test sets to create one data set.\
2. Extracts only the measurements on the mean and standard deviation for each measurement.\
3. Uses descriptive activity names to name the activities in the data set\
4. Appropriately labels the data set with descriptive activity names.\
5. Creates a second, independent tidy data set with the average of each variable for each activity and each subject.\
\
\

\b run_analysis.R:
\b0 \
\
1. It downloads the UCI HAR Dataset data set from https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip and puts the zip file working directory. \
\
2. After it is downloaded, it unzips the file into the UCI HAR Dataset folder.\
It loads the train and test data sets and appends the two datasets into one data frame. This is done using rbind.\
\
3. It extracts just the mean and standard deviation from the features data set. This is done using grep.\
\
4. After cleaning the column names, these are applied to the x data frame.\
5. After loading the activities data set, it converts it to lower case using tolower and removes underscore using gsub. Activity and subject column names are named as y and subj data sets, respectively.\
\
6. The three data sets, x, y and subj, are merged. Then, it is exported as a txt file into the Project folder in the same working directory, named merged.txt.\
\
7. The mean of activities and subjects are created into a separate tidy data set which is exported into the Project folder as txt file; this is named average.txt.\
The R code contains str for easier preview of the two final data sets.\
\
\

\b Code:\

\b0 \
library(RCurl)\
\
if (!file.info('UCI HAR Dataset')$isdir=FALSE) \{\
  dataFile <- 'https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip'\
  dir.create('UCI HAR Dataset')\
  download.file(dataFile, 'UCI-HAR-dataset.zip', method='curl')\
  unzip('./UCI-HAR-dataset.zip')\
\}\
\
\
\
# Merges the training and the test sets to create one data set.\
x.train <- read.table('./UCI HAR Dataset/train/X_train.txt')\
x.test <- read.table('./UCI HAR Dataset/test/X_test.txt')\
x <- rbind(x.train, x.test)\
\
subj.train <- read.table('./UCI HAR Dataset/train/subject_train.txt')\
subj.test <- read.table('./UCI HAR Dataset/test/subject_test.txt')\
subj <- rbind(subj.train, subj.test)\
\
y.train <- read.table('./UCI HAR Dataset/train/y_train.txt')\
y.test <- read.table('./UCI HAR Dataset/test/y_test.txt')\
y <- rbind(y.train, y.test)\
\
# Extracts only the measurements on the mean and standard deviation for each measurement. \
features <- read.table('./UCI HAR Dataset/features.txt')\
mean.sd <- grep("-mean\\\\(\\\\)|-std\\\\(\\\\)", features[, 2])\
x.mean.sd <- x[, mean.sd]\
\
# Uses descriptive activity names to name the activities in the data set\
names(x.mean.sd) <- features[mean.sd, 2]\
names(x.mean.sd) <- tolower(names(x.mean.sd)) \
names(x.mean.sd) <- gsub("\\\\(|\\\\)", "", names(x.mean.sd))\
\
activities <- read.table('./UCI HAR Dataset/activity_labels.txt')\
activities[, 2] <- tolower(as.character(activities[, 2]))\
activities[, 2] <- gsub("_", "", activities[, 2])\
\
y[, 1] = activities[y[, 1], 2]\
colnames(y) <- 'activity'\
colnames(subj) <- 'subject'\
\
# Appropriately labels the data set with descriptive activity names.\
data <- cbind(subj, x.mean.sd, y)\
str(data)\
\
write.table(data, file = "merged.txt",row.names=TRUE, na="",col.names=TRUE, sep=",")\
\
# Creates a second, independent tidy data set with the average of each variable for each activity and each subject. \
average <- aggregate(x=data, by=list(activities=data$activity, subj=data$subject), FUN=mean)\
str(average)\
write.table(average, file = "average.txt",row.names=TRUE, na="",col.names=TRUE, sep=",")\
\
}