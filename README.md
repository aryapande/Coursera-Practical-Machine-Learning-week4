---
title: "Peer graded PML 4"
author: "Aryaman"
date: "10/17/2021"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
setwd("C:/Users/arya/Desktop/coursera/R_pro/Rstudio/practical machine learning/week4")
```

## Import necessary Libraries
```{r , warning=FALSE}
library(caret)
library(randomForest)
library(rpart)
library(rpart.plot)
library(Rcpp)
library(ggplot2)
```
## Loading Dataset 
```{r}
orig_train_data <- read.csv("pml-training.csv", na.strings=c("","NA","#DIV/0!"))
orig_test_data <- read.csv("pml-testing.csv" , na.strings=c("","NA","#DIV/0!"))
orig_train_data<-orig_train_data[,colSums(is.na(orig_train_data)) == 0]
orig_test_data<-orig_test_data[,colSums(is.na(orig_test_data)) == 0]
```
### dimentions of training 
```{r}
dim(orig_train_data)
```
### dimentions of testing
```{r}
dim(orig_test_data)
```

### Removing non-numeric columns. i.e, first 7 columns
```{r}
orig_train <- orig_train_data[,-c(1:7)]
orig_test <- orig_test_data[,-c(1:7)]
colnames(orig_train)
```

## Cross validation(75% training and 25% testing)
```{r}
t <- createDataPartition(orig_train$classe, p=0.75, list=FALSE)
training <- orig_train[t,]
testing <- orig_train[-t,]
dim(training)
dim(testing)
```

## Models
### Random Forest

```{r}
RF <- train(classe ~ ., data = training, method = "rf", ntree = 10)
RF_pred <- predict(RF, testing)
RF_pred_conf <- confusionMatrix(RF_pred, as.factor(testing$classe))
RF_pred_conf
```
The accuracy of Random Forest Model is >98% which is great!

### Decesion Tree
```{r}
DT <- train(classe ~ ., data = training, method="rpart")
DT_pred <- predict(DT, testing)
confusionMatrix(DT_pred, as.factor(testing$classe))
rpart.plot(DT$finalModel, roundint=FALSE)
```
The accuracy of Decesion models is too low at 48%, Random forest is a much better choice

## Conclusion

### Results

According to the confusion matrices, the the Random Forest algorithm is performing better than the decision trees. The accuracy for the Random Forest model was >0.98 compared to 0.44 for Decision Tree model. The random Forest model is taken.

### Expected out-of-sample error

The expected out-of-sample error is estimated at 0.005, or 0.5%. The expected out-of-sample error is calculated as 1 - accuracy for predictions made against the cross-validation set. Our Test data set comprises 20 cases. With an accuracy above 99% on our cross-validation data, we can expect that very few, or none, of the test samples will be missclassified.

## Submission
In this section the files for the project submission are generated using the random forest algorithm on the testing data.

```{r submission, echo=TRUE}
RF_prediction <- predict(RF, orig_test )
RF_prediction
```
