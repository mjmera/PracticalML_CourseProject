---
title: "PML_Project"
author: "MJ Mera"
date: "Sunday, August 24, 2014"
output: html_document
---


GOAL
----------
Using the data obtained from 6 participants we will fit a model that predicts if a barbell lift was performed correctly or incorrectly in 5 different ways. The "classe" variable indicates which observations were A, B,c, D, E.


MODEL BUILD
--------------
I chose to implement the Random Forest method for classification due to its high accuracy.

1. Load the data
2. Clean the data. Convert blanks and na to NA. Use complete cases to eliminate the columns that are incomplete. Eliminate columns 1 and 2. Convert cvtd_timestamp to a numeric value. 
3. Fit the model using the randomForest funtion from library(randomForest)


```{r}
library(randomForest)

training <- read.csv("pml-training.csv", na.strings=c("", "NA", "na"))

training$cvtd_timestamp <- as.numeric(as.POSIXct(training$cvtd_timestamp, 
                                                 format="%d/%m/%Y  %H:%M"))
training$new_window <- as.numeric(training$new_window)
ttraining <- as.data.frame(t(training))
complete <- complete.cases(ttraining)

complete[1]<-F; complete[2]<-F #; complete[5]<-F;complete[6]<-F

training<- training[,complete]
fit <- randomForest(training$classe ~ ., data=training, importance=T)
```

CROSS VALIDATION
--------------------
The Random Forest Method does not require Cross Validation to get an unbiased estimate of the test set error. The OOB error is calculated internally.

OUT OF SAMPLE ERROR
-------------------------
```{r}
print(fit)
```


PREDICTIONS 
-------------------------------------
```{r}
testing <- read.csv("pml-testing.csv", na.strings=c("", "NA", "na"))
testing$new_window <- as.numeric(testing$new_window)
testing$cvtd_timestamp <- as.numeric(as.POSIXct(testing$cvtd_timestamp, 
                                                 format="%d/%m/%Y  %H:%M"))
testing<- testing[,complete]
predictions <- predict(fit, testing, type="response") 
print(predictions)

```

