#initiate the dplyr and tidyr packages
library(dplyr)
library(tidyr)
#read in files for variables (X) and activity labels (Y)
Xtrain<-read.table('UCI HAR Dataset/train/X_train.txt')
Ytrain<-read.table('UCI HAR Dataset/train/y_train.txt')
Xtest<-read.table('UCI HAR Dataset/test/X_test.txt')
Ytest<-read.table('UCI HAR Dataset/test/y_test.txt')
#read in file for feature descriptions
features<-read.table('UCI HAR Dataset/features.txt')
#read in file for subject identifiers
testsub<-read.table('UCI HAR Dataset/test/subject_test.txt')
trainsub<-read.table('UCI HAR Dataset/train/subject_train.txt')
#merge the training and test sets to create one data set
traintest<-bind_rows(Xtest,Xtrain)
#name the variables for merged dataset using the features data table
names(traintest)<-features[,2]
#remove duplicated names so as not to cause and error with select function
traintest<-traintest[,!duplicated(colnames(traintest))]
#select means and standard deviations
means<-select(traintest,contains("mean"))
means<-select(means,-contains("meanfreq"))
means<-select(means,-contains("angle"))
stds<-select(traintest<-contains("std"))
#merge the means and standard deviation data tables
#creates a new data set with all means and standard deviations, with 10299 cases and 86 variables
data<-bind_cols(means,stds)
#label activities for train and test sets
names(Ytrain)<-"activity"
Ytrain[Ytrain$"activity"==1,]<-"Walking"
Ytrain[Ytrain$"activity"==2,]<-"Walking_Upstairs"
Ytrain[Ytrain$"activity"==3,]<-"Walking_Downstairs"
Ytrain[Ytrain$"activity"==4,]<-"Sitting"
Ytrain[Ytrain$"activity"==5,]<-"Standing"
Ytrain[Ytrain$"activity"==6,]<-"Laying"
names(Ytest)<-"activity"
Ytest[Ytest$"activity"==1,]<-"Walking"
Ytest[Ytest$"activity"==2,]<-"Walking_Upstairs"
Ytest[Ytest$"activity"==3,]<-"Walking_Downstairs"
Ytest[Ytest$"activity"==4,]<-"Sitting"
Ytest[Ytest$"activity"==5,]<-"Standing"
Ytest[Ytest$"activity"==6,]<-"Laying"
#combine the activities for test and train sets
activities<-bind_rows(Ytest,Ytrain)
#combine the subjects for test and train sets
subjects<-bind_rows(testsub,trainsub)
names(subjects)<-"subject"
#combine the activities and subjects to mean and std data to make final data set
data2<-bind_cols(data,activities)
finaldata<-bind_cols(data2,subjects)
#group the data by subject and activity
grouped<-group_by(finaldata,subject,activity)
#take the mean across each variable of the grouped data
tidydata<-summarise_each(grouped,funs(mean))
#print to a text file
dir.create("./GCDProject2/Project.txt")
write.table(tidydata,"GCDProject2/Project.txt",row.name=FALSE,quote=FALSE)