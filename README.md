---
title: "README.md"
author: "vaniferro"
date: "July 22, 2014"
output: html_document
---

This repository contains the run_analysis.R file for the Course Project associated with the Coursera Getting and Cleaning Data course run by Johns Hopkins University.

By calling the function runanalysis() contained within run_analysis.R, the function automatically combines the training and test datasets, cleans the data and outputs the following:
- a combined, tidy data set with headers
- a table containing the mean and standard deviation for each measurement taken
- a table containing the mean for each measurement taken for each subject and activity

*To use the output tables in subsequent analysis, read them into R using the read.table() function.*

***Note***: to use this script, the working directory needs to be set to the folder that contains the readme file for the data.

Below is the R source code for the run_analysis.R file:

```
runanalysis <- function() {
        library(plyr) #load plyr
        
        #import training set
        subtrain <- read.table("./train/subject_train.txt")
        activitytrain <- read.table("./train/y_train.txt")
        xtrain <- read.table("./train/X_train.txt")
        xtrainlab <- read.table("./features.txt")
        
        #combine columns for training set
        trainset <- cbind(subtrain, activitytrain, xtrain)
        colnames(trainset) <- c("Subject_ID","Activity", as.character(xtrainlab[,2]))
        
        #import test set
        subtest <- read.table("./test/subject_test.txt")
        activitytest <- read.table("./test/y_test.txt")
        xtest <- read.table("./test/X_test.txt")
        xtestlab <- read.table("./features.txt")
        
        #combine columns for test set
        testset <- cbind(subtest, activitytest, xtest)
        colnames(testset) <- c("Subject_ID","Activity", as.character(xtestlab[,2]))
        
        #combine test and training sets; add labels; export cleaned dataset
        data <- rbind(trainset, testset)
        data$Activity <- factor(data$Activity, levels = 1:6, 
                                labels = c("Walking", "Walking Upstairs",
                                           "Walking Downstairs", "Sitting",
                                           "Standing", "Laying"))
        data[,1] <- factor(data[,1])
        write.table(data, file = "./tidydata.txt")
        
        #calculate means and std devs; export summary table for each measurement
        means <- sapply(data, function(x) mean(as.numeric(x)))
        stdevs <- sapply(data, function(x) sd(as.numeric(x)))
        summarytable <- rbind(means,stdevs)
        summarytable <- summarytable[,3:ncol(summarytable)]
        write.table(summarytable, file = "./measuresummary.txt")
        
        #calculate means for every measurement by subject and activity; export data
        xt <- ddply(data, .(Subject_ID, Activity), numcolwise(mean))
        write.table(xt, file = "./averagebysubject&activity.txt")       
}
```

