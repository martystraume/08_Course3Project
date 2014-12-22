## README.md for "martystraume" Course 3 Project repo

###The function run_analysis <- function(){

-------------------------------------------------------------------------------

 The purpose of this project is to demonstrate your ability to collect, work
 with, and clean a data set. The goal is to prepare tidy data that can be used
 for later analysis. You will be graded by your peers on a series of yes/no
 questions related to the project. You will be required to submit:

 1) a tidy data set as described below

 2) a link to a Github repository with your script for performing the analysis

 3) a code book that describes the variables, the data, and any transformations
    or work that you performed to clean up the data called CodeBook.md

 4) a README.md in the repo with your scripts (this repo explains how all of
    the scripts work and how they are connected)

-------------------------------------------------------------------------------

 One of the most exciting areas in all of data science right now is wearable
 computing - see for example this article

 http://www.insideactivitytracking.com/data-science-activity-tracking-and-the-battle-for-the-worlds-top-sports-brand/

 Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most
 advanced algorithms to attract new users. The data linked to from the course
 website represent data collected from the accelerometers from the Samsung
 Galaxy S smartphone. A full description is available at the site where the
 data was obtained: 
        
 http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 

 Here are the data for the project: 
        
 https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip 

-------------------------------------------------------------------------------

 You should create one R script called run_analysis.R that does the following: 

 1.Merges the training and the test sets to create one data set.

 2.Extracts only the measurements on the mean and standard deviation for each
   measurement. 

 3.Uses descriptive activity names to name the activities in the data set

 4.Appropriately labels the data set with descriptive variable names. 

 5.From the data set in step 4, creates a second, independent tidy data set
   with the average of each variable for each activity and each subject.

-------------------------------------------------------------------------------

 run_analysis.R

 1) Merges the training and the test sets to create one data set.

 2) Extracts only the measurements on the mean and standard deviation for each
    measurement. 

 3) Uses descriptive activity names to name the activities in the data set

 4) Appropriately labels the data set with descriptive variable names. 

 5) From the data set in step 4, creates a second, independent tidy data set
    with the average of each variable for each activity and each subject.

-------------------------------------------------------------------------------

 Create [data] subdirectory, if not already there, and set as working directory

	if(!file.exists("data")) {
        	dir.create("data")
        	setwd("./data")
	}

-------------------------------------------------------------------------------

 Download data file from designated source and place locally

	fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
	ZipFile <- "./data.zip"
	download.file(fileURL, destfile = ZipFile)
	dateDownloaded <- date()

-------------------------------------------------------------------------------

 Assuming you have a zip file (data.zip) that consists of a csv file
 (data.csv). This is how you read the csv file:

	 data <- read.csv(unz("data.zip", "data.csv"))

 Here’s another example. In this example, data.csv is inside a folder (folder1)

	 data <- read.csv(unz("data.zip", "folder1/data.csv"))

-------------------------------------------------------------------------------

 Read data into data frames

	activity_labels_TXT <- "UCI HAR Dataset/activity_labels.txt"
	activity_labels <- read.table(unz(ZipFile, activity_labels_TXT))

	features_TXT <- "UCI HAR Dataset/features.txt"
	features <- read.table(unz(ZipFile, features_TXT))

	subject_test_TXT <- "UCI HAR Dataset/test/subject_test.txt"
	subject_test <- read.table(unz(ZipFile, subject_test_TXT))

	X_test_TXT <- "UCI HAR Dataset/test/X_test.txt"
	X_test <- read.table(unz(ZipFile, X_test_TXT))

	y_test_TXT <- "UCI HAR Dataset/test/y_test.txt"
	y_test <- read.table(unz(ZipFile, y_test_TXT))

	subject_train_TXT <- "UCI HAR Dataset/train/subject_train.txt"
	subject_train <- read.table(unz(ZipFile, subject_train_TXT))

	X_train_TXT <- "UCI HAR Dataset/train/X_train.txt"
	X_train <- read.table(unz(ZipFile, X_train_TXT))

	y_train_TXT <- "UCI HAR Dataset/train/y_train.txt"
	y_train <- read.table(unz(ZipFile, y_train_TXT))

-------------------------------------------------------------------------------

 1) Merges the training and the test sets to create one data set

	Test <- cbind(subject_test,y_test,X_test)
	Train <- cbind(subject_train,y_train,X_train)
	Total <- rbind(Train,Test)

-------------------------------------------------------------------------------

 4) Appropriately labels the data set with descriptive variable names

	rowmax <- dim(features)[1]
	columnNames <- c("SubjectID","ActivityID",as.character(features[1:rowmax,2]))
	colnames(Total) <- columnNames

-------------------------------------------------------------------------------

 2) Extracts only the measurements on the mean and standard deviation for each
    measurement

	columnIndices <- c(1,2)
	columnIndices <- append(columnIndices,grep("-mean()",columnNames,value=F,fixed=T))
	columnIndices <- append(columnIndices,grep("-std()",columnNames,value=F,fixed=T))
	columnIndices <- sort(columnIndices)
	MeanAndStd <- Total[,columnIndices]

-------------------------------------------------------------------------------

 3) Uses descriptive activity names to name the activities in the data set

	ActivityNames <- character()
	rowmax <- dim(MeanAndStd)[1]
	for(i in 1:rowmax){
	  ActivityNames[i] <- as.character(activity_labels[MeanAndStd$ActivityID[i],2])
	}
	colmax <- dim(MeanAndStd)[2]
	MeanAndStdNamed <- cbind(MeanAndStd$SubjectID,ActivityNames,MeanAndStd[,3:colmax])
	columnNames <- colnames(MeanAndStdNamed)
	columnNames <- c("SubjectID","ActivityName",columnNames[3:colmax])
	colnames(MeanAndStdNamed) <- columnNames

-------------------------------------------------------------------------------

 5) From the data set in step 4, creates a second, independent tidy data set
    with the average of each variable for each activity and each subject.

 NOTE: I will be using data frame [MeanAndStdNamed] for the subsequent analysis

	z <- MeanAndStdNamed[order(MeanAndStdNamed$SubjectID,MeanAndStdNamed$ActivityName),]
	sIndex_max <- range(z$SubjectID)[2]
	aIndex_max <- 6L
	vIndex_max <- dim(z)[2]
	zz <- data.frame()
	TidyDataOfAverages <- data.frame()
	for(sIndex in 1:sIndex_max){
	  for(aIndex in 1:aIndex_max){
	    zz <- z[(z$SubjectID == sIndex & z$ActivityName == activity_labels[aIndex,2]),]
	    TidyDataOfAverages[(((sIndex-1)*aIndex_max)+aIndex),1] <- sIndex
	    TidyDataOfAverages[(((sIndex-1)*aIndex_max)+aIndex),2] <- activity_labels[aIndex,2]
	    for(vIndex in 3:vIndex_max){
	      TidyDataOfAverages[(((sIndex-1)*aIndex_max)+aIndex),vIndex] <- mean(zz[,vIndex])
	    }
	  }
	}
	colnames(TidyDataOfAverages) <- columnNames

-------------------------------------------------------------------------------

 Write output to file [TidyDataOfAverages.txt]

	write.table(TidyDataOfAverages,file="TidyDataOfAverages.txt",row.names=F)
	write.table(columnNames,file="TidyDataOfAverages_ColumnNames.txt",row.names=F)

-------------------------------------------------------------------------------

	return(TidyDataOfAverages)
	}

-------------------------------------------------------------------------------

