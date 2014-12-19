# Machine Learning Project
Jeff Tomlinson  
Thursday, December 18, 2014  

## Background

 Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible
 to collect a large amount of data about personal activity relatively
 inexpensively. These type of devices are part of the quantified self movement
 – a group of enthusiasts who take measurements about themselves regularly to
 improve their health, to find patterns in their behavior, or because they are
 tech geeks. One thing that people regularly do is quantify how much of a
 particular activity they do, but they rarely quantify how well they do it. In
 this project, your goal will be to use data from accelerometers on the belt,
 forearm, arm, and dumbell of 6 participants. They were asked to perform
 barbell lifts correctly and incorrectly in 5 different ways. More information
 is available from the website here: http://groupware.les.inf.puc-rio.br/har
 (see the section on the Weight Lifting Exercise Dataset).
 
## Data

The training data for this project are available from
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv

The test data are available from 
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv

The data for this project come from this source: http://groupware.les.inf.puc-rio.br/har.

### Required libraries

```
## Loading required package: lattice
## Loading required package: ggplot2
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
```

Read in the training and test data. Assign all of the NA, blank and divide by zero 
fields to NA.


## Data Cleaning

Remove any columns that have over half of their data as NA.  
The first column X contains a order variable.  The data is sorted by classe,
so all that would be required would be the X column, which would be 
different in the test set.  Similarly the window variable also increments.
The name and the exact date and time would also not be useful in anything
other than the training dataset.


There are now 19622 records with 53 columns.

At this point the dataset only contains values that we can use.

## Paritioning

Subset into training and testing setsm with 80% for training and 20% for cross
validation.


The training dataset has 15699 records and the cross validation dataset 
has 3923 records.

## Model Selection

We will check 2 models.  A quick *recursive partion* model and a more computationally intensive
*random forest* model.  My earlier tests used the *random forest* model via caret, 
which required a lot more processing than the simple *random forest* model used here.

## Recursive Partition

```r
mod1 <- rpart(classe ~ ., method="class", data=subTr)
pred1 <- predict(mod1, subTr, type="class")
cmMod1 <-confusionMatrix(pred1, subTr$classe)
cmMod1
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 4085  455   42  147   53
##          B  142 1746  131  184  195
##          C  111  287 2215  388  355
##          D   51  217  179 1640  158
##          E   75  333  171  214 2125
## 
## Overall Statistics
##                                           
##                Accuracy : 0.7523          
##                  95% CI : (0.7455, 0.7591)
##     No Information Rate : 0.2843          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.6861          
##  Mcnemar's Test P-Value : < 2.2e-16       
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9151   0.5747   0.8090   0.6374   0.7363
## Specificity            0.9380   0.9485   0.9120   0.9539   0.9381
## Pos Pred Value         0.8542   0.7281   0.6600   0.7305   0.7282
## Neg Pred Value         0.9653   0.9029   0.9576   0.9307   0.9405
## Prevalence             0.2843   0.1935   0.1744   0.1639   0.1838
## Detection Rate         0.2602   0.1112   0.1411   0.1045   0.1354
## Detection Prevalence   0.3046   0.1527   0.2138   0.1430   0.1859
## Balanced Accuracy      0.9265   0.7616   0.8605   0.7956   0.8372
```

### Random Forest

```r
mod2 <- randomForest(classe ~. , data=subTr, method="class")
pred2 <- predict(mod2, subTr)
cmMod2 <-confusionMatrix(pred2, subTr$classe)
cmMod2
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 4464    0    0    0    0
##          B    0 3038    0    0    0
##          C    0    0 2738    0    0
##          D    0    0    0 2573    0
##          E    0    0    0    0 2886
## 
## Overall Statistics
##                                      
##                Accuracy : 1          
##                  95% CI : (0.9998, 1)
##     No Information Rate : 0.2843     
##     P-Value [Acc > NIR] : < 2.2e-16  
##                                      
##                   Kappa : 1          
##  Mcnemar's Test P-Value : NA         
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            1.0000   1.0000   1.0000   1.0000   1.0000
## Specificity            1.0000   1.0000   1.0000   1.0000   1.0000
## Pos Pred Value         1.0000   1.0000   1.0000   1.0000   1.0000
## Neg Pred Value         1.0000   1.0000   1.0000   1.0000   1.0000
## Prevalence             0.2843   0.1935   0.1744   0.1639   0.1838
## Detection Rate         0.2843   0.1935   0.1744   0.1639   0.1838
## Detection Prevalence   0.2843   0.1935   0.1744   0.1639   0.1838
## Balanced Accuracy      1.0000   1.0000   1.0000   1.0000   1.0000
```

#### Compare Methods


The accuracy of the *recursive partition* model is 0.752340913434 and that of the *random forest* model 
is 1 (which is unreasonably good). If we then compare the out of sample accuracy, 
it is 0.749426459342 for the *random partion* model and 0.996686209534 for the *random forest* 
model.  As expected these are both less than the accuracy for their respective
training models.  The random forest model still appears to very accurate, with regards
to the cross validation data, so we will use this model for the test data.

## Prediction
We will predict classe for the test data using our random forest model.


```r
testPred <- predict(mod2, tsRaw, type="class")
testPred
```

```
##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
##  B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
## Levels: A B C D E
```

### Output Test Results
Write the output files (of the form "problem_id_x.txt", where x is 1 to 20)
each file will just contain the single letter of classe for the associated 
test record.


```r
pml_write_files = function(x){
  n = length(x)
  for(i in 1:n){
    filename = paste0("problem_id_",i,".txt")
    write.table(x[i],file=filename,quote=FALSE,row.names=FALSE,col.names=FALSE)
  }
}
pml_write_files(testPred)
```








