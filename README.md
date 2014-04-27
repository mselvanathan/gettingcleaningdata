#All files were unzipped to the E drive since downloading through the URL for
#websites starting with https:// was not feasible on Windows

#Set working directory
setwd("e:/GetCleanData/")

#Merging the test and training dataset to create one dataset
features = read.delim("./data/uci/features.txt", header=FALSE, sep="")
names <- features$V2

subjtest = read.delim("./data/uci/test/subject_test.txt", header=FALSE, sep="", col.names="Subject_ID")
ytest = read.delim("./data/uci/test/y_test.txt", header=FALSE, sep="", col.names="Activity_ID")
Xtest = read.delim("./data/uci/test/X_test.txt", header=FALSE, sep="", col.names=names)


Xtrain = read.delim("./data/uci/train/X_train.txt", header=FALSE, sep="", col.names=names)
ytrain = read.delim("./data/uci/train/y_train.txt", header=FALSE, sep="", col.names="Activity_ID")
subjtrain = read.delim("./data/uci/train/subject_train.txt", header=FALSE, sep="", col.names="Subject_ID")

test_dataframe <-cbind(subjtest, ytest, Xtest)
train_dataframe <-cbind(subjtrain, ytrain, Xtrain)

test_train_df <- rbind(test_dataframe, train_dataframe)


#Extracts measurements on the mean and standard deviation for each measurement
ReqCols <- c("mean\\(\\)[-]", "std\\(\\)[-]")
colnames <- unique (grep(paste(ReqCols,collapse="|"), features$V2, value=FALSE))
#Subset dataset with column names containing mean & std deviation
test_train_dfss <- subset(test_train_df[, c(1,2,3,4,5,6,7,8,43,44,45,46,47,48,83,84,85,86,87,88,123,124,125,126,127,128,163,164,165,166,167,168,268,269,270,271,272,273,347,348,349,350,351,352,426,427, 428,429,430,431)])
names(test_train_dfss)

#Creating a new colummn for naming activity
test_train_df$Activity = ifelse(test_train_df$Activity_ID == 1, "walking",
                         ifelse(test_train_df$Activity_ID ==2, "walking upstairs",
                         ifelse(test_train_df$Activity_ID ==3, "walking downstairs",
                         ifelse(test_train_df$Activity_ID ==4, "sitting",
                         ifelse(test_train_df$Activity_ID ==5, "standing","laying")))))

#Create tidy dataset
ttmelt <- melt(test_train_df, id=c(1:563), measure.vars=c("Activity_ID","Subject_ID"))
head(ttmelt, n=3)
ttdata <- dcast(ttmelt, Activity_ID ~ variable, mean)
ttdata