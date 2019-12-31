# KNN, Logistic Regression, SVM Model comparisons

#### Kexin Wang

#### 10/24/2019

## R Cleanup

    
    
    data <- read.csv("./tele.csv")
    
    #convert y to
    data$duration <- NULL
    data$y = ifelse(data$y == 'yes', 1, 0)
    
    #create dummy variables for each categorical variable
    data = as.data.frame(model.matrix(~.-1, data = data))
    
    #normalizes data and binds it with colum y
    data = cbind(as.data.frame(lapply(data[1:53], scale)), data$y)
    
    #reassigns last colum nae to y
    colnames(data)[colnames(data) == "data$y"] <- "y"
    str(data)
    
    
    ## 'data.frame':    41188 obs. of  54 variables:
    ##  $ X                           : num  -1.73 -1.73 -1.73 -1.73 -1.73 ...
    ##  $ age                         : num  1.53302 1.62897 -0.29018 -0.00231 1.53302 ...
    ##  $ jobadmin.                   : num  -0.582 -0.582 -0.582 1.718 -0.582 ...
    ##  $ jobblue.collar              : num  -0.538 -0.538 -0.538 -0.538 -0.538 ...
    ##  $ jobentrepreneur             : num  -0.191 -0.191 -0.191 -0.191 -0.191 ...
    ##  $ jobhousemaid                : num  6.153 -0.163 -0.163 -0.163 -0.163 ...
    ##  $ jobmanagement               : num  -0.276 -0.276 -0.276 -0.276 -0.276 ...
    ##  $ jobretired                  : num  -0.209 -0.209 -0.209 -0.209 -0.209 ...
    ##  $ jobself.employed            : num  -0.189 -0.189 -0.189 -0.189 -0.189 ...
    ##  $ jobservices                 : num  -0.327 3.062 3.062 -0.327 3.062 ...
    ##  $ jobstudent                  : num  -0.147 -0.147 -0.147 -0.147 -0.147 ...
    ##  $ jobtechnician               : num  -0.442 -0.442 -0.442 -0.442 -0.442 ...
    ##  $ jobunemployed               : num  -0.159 -0.159 -0.159 -0.159 -0.159 ...
    ##  $ jobunknown                  : num  -0.0899 -0.0899 -0.0899 -0.0899 -0.0899 ...
    ##  $ maritalmarried              : num  0.808 0.808 0.808 0.808 0.808 ...
    ##  $ maritalsingle               : num  -0.625 -0.625 -0.625 -0.625 -0.625 ...
    ##  $ maritalunknown              : num  -0.0441 -0.0441 -0.0441 -0.0441 -0.0441 ...
    ##  $ educationbasic.6y           : num  -0.243 -0.243 -0.243 4.119 -0.243 ...
    ##  $ educationbasic.9y           : num  -0.415 -0.415 -0.415 -0.415 -0.415 ...
    ##  $ educationhigh.school        : num  -0.548 1.824 1.824 -0.548 1.824 ...
    ##  $ educationilliterate         : num  -0.0209 -0.0209 -0.0209 -0.0209 -0.0209 ...
    ##  $ educationprofessional.course: num  -0.382 -0.382 -0.382 -0.382 -0.382 ...
    ##  $ educationuniversity.degree  : num  -0.648 -0.648 -0.648 -0.648 -0.648 ...
    ##  $ educationunknown            : num  -0.209 -0.209 -0.209 -0.209 -0.209 ...
    ##  $ defaultunknown              : num  -0.514 1.947 -0.514 -0.514 -0.514 ...
    ##  $ defaultyes                  : num  -0.00853 -0.00853 -0.00853 -0.00853 -0.00853 ...
    ##  $ housingunknown              : num  -0.157 -0.157 -0.157 -0.157 -0.157 ...
    ##  $ housingyes                  : num  -1.049 -1.049 0.953 -1.049 -1.049 ...
    ##  $ loanunknown                 : num  -0.157 -0.157 -0.157 -0.157 -0.157 ...
    ##  $ loanyes                     : num  -0.423 -0.423 -0.423 -0.423 2.365 ...
    ##  $ contacttelephone            : num  1.32 1.32 1.32 1.32 1.32 ...
    ##  $ monthaug                    : num  -0.42 -0.42 -0.42 -0.42 -0.42 ...
    ##  $ monthdec                    : num  -0.0666 -0.0666 -0.0666 -0.0666 -0.0666 ...
    ##  $ monthjul                    : num  -0.459 -0.459 -0.459 -0.459 -0.459 ...
    ##  $ monthjun                    : num  -0.385 -0.385 -0.385 -0.385 -0.385 ...
    ##  $ monthmar                    : num  -0.116 -0.116 -0.116 -0.116 -0.116 ...
    ##  $ monthmay                    : num  1.41 1.41 1.41 1.41 1.41 ...
    ##  $ monthnov                    : num  -0.333 -0.333 -0.333 -0.333 -0.333 ...
    ##  $ monthoct                    : num  -0.133 -0.133 -0.133 -0.133 -0.133 ...
    ##  $ monthsep                    : num  -0.118 -0.118 -0.118 -0.118 -0.118 ...
    ##  $ day_of_weekmon              : num  1.96 1.96 1.96 1.96 1.96 ...
    ##  $ day_of_weekthu              : num  -0.515 -0.515 -0.515 -0.515 -0.515 ...
    ##  $ day_of_weektue              : num  -0.494 -0.494 -0.494 -0.494 -0.494 ...
    ##  $ day_of_weekwed              : num  -0.496 -0.496 -0.496 -0.496 -0.496 ...
    ##  $ campaign                    : num  -0.566 -0.566 -0.566 -0.566 -0.566 ...
    ##  $ pdays                       : num  0.195 0.195 0.195 0.195 0.195 ...
    ##  $ previous                    : num  -0.349 -0.349 -0.349 -0.349 -0.349 ...
    ##  $ poutcomenonexistent         : num  0.398 0.398 0.398 0.398 0.398 ...
    ##  $ poutcomesuccess             : num  -0.186 -0.186 -0.186 -0.186 -0.186 ...
    ##  $ emp.var.rate                : num  0.648 0.648 0.648 0.648 0.648 ...
    ##  $ cons.price.idx              : num  0.723 0.723 0.723 0.723 0.723 ...
    ##  $ cons.conf.idx               : num  0.886 0.886 0.886 0.886 0.886 ...
    ##  $ euribor3m                   : num  0.712 0.712 0.712 0.712 0.712 ...
    ##  $ y                           : num  0 0 0 0 0 0 0 0 0 0 ...

## Logistic Regression

    
    
    #randomize data, split 80% of it into training data and 20% of it into testing data 
    data_rand = data[sample(nrow(data)), ]
    train <- data_rand[1:ceiling(0.8*nrow(data)), ]
    test <-  data_rand[(ceiling(0.8*nrow(data))+1) :nrow(data), ]
    dim(train)
    
    
    ## [1] 32951    54
    
    
    dim(test)
    
    
    ## [1] 8237   54
    
    
    logistic <- glm(y ~. ,data = train, family = "binomial")
    # use step(logistic) to find the optimized logistic model
    logistic_optimized = glm(y ~ X + jobretired + jobstudent + maritalsingle + 
        maritalunknown + educationbasic.6y + educationhigh.school + 
        educationilliterate + educationprofessional.course + educationuniversity.degree + 
        defaultunknown + contacttelephone + monthjul + monthjun + 
        monthmar + monthmay + monthnov + monthoct + monthsep + day_of_weekmon + 
        day_of_weekwed + campaign + pdays + poutcomenonexistent + 
        poutcomesuccess + emp.var.rate + cons.price.idx + cons.conf.idx + 
        euribor3m, data = train, family = "binomial")
    summary(logistic_optimized)
    
    
    ## 
    ## Call:
    ## glm(formula = y ~ X + jobretired + jobstudent + maritalsingle + 
    ##     maritalunknown + educationbasic.6y + educationhigh.school + 
    ##     educationilliterate + educationprofessional.course + educationuniversity.degree + 
    ##     defaultunknown + contacttelephone + monthjul + monthjun + 
    ##     monthmar + monthmay + monthnov + monthoct + monthsep + day_of_weekmon + 
    ##     day_of_weekwed + campaign + pdays + poutcomenonexistent + 
    ##     poutcomesuccess + emp.var.rate + cons.price.idx + cons.conf.idx + 
    ##     euribor3m, family = "binomial", data = train)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -2.1376  -0.3928  -0.3177  -0.2530   2.9345  
    ## 
    ## Coefficients:
    ##                               Estimate Std. Error  z value Pr(>|z|)    
    ## (Intercept)                  -2.511466   0.024204 -103.761  < 2e-16 ***
    ## X                             0.545377   0.074647    7.306 2.75e-13 ***
    ## jobretired                    0.076668   0.016033    4.782 1.74e-06 ***
    ## jobstudent                    0.034106   0.014787    2.306 0.021086 *  
    ## maritalsingle                 0.021911   0.020424    1.073 0.283362    
    ## maritalunknown                0.008532   0.018730    0.456 0.648731    
    ## educationbasic.6y             0.022274   0.023018    0.968 0.333187    
    ## educationhigh.school          0.017216   0.024405    0.705 0.480534    
    ## educationilliterate           0.024498   0.015820    1.549 0.121485    
    ## educationprofessional.course  0.023287   0.022805    1.021 0.307191    
    ## educationuniversity.degree    0.053931   0.024838    2.171 0.029911 *  
    ## defaultunknown               -0.114170   0.026220   -4.354 1.34e-05 ***
    ## contacttelephone             -0.266232   0.035053   -7.595 3.08e-14 ***
    ## monthjul                     -0.085411   0.030106   -2.837 0.004554 ** 
    ## monthjun                     -0.296568   0.035783   -8.288  < 2e-16 ***
    ## monthmar                      0.142886   0.012782   11.178  < 2e-16 ***
    ## monthmay                     -0.253308   0.029524   -8.580  < 2e-16 ***
    ## monthnov                     -0.350463   0.032848  -10.669  < 2e-16 ***
    ## monthoct                     -0.073979   0.015721   -4.706 2.53e-06 ***
    ## monthsep                     -0.040477   0.013468   -3.005 0.002653 ** 
    ## day_of_weekmon               -0.091826   0.020965   -4.380 1.19e-05 ***
    ## day_of_weekwed                0.049795   0.020200    2.465 0.013695 *  
    ## campaign                     -0.112616   0.028119   -4.005 6.20e-05 ***
    ## pdays                        -0.184174   0.039541   -4.658 3.20e-06 ***
    ## poutcomenonexistent           0.168890   0.021697    7.784 7.03e-15 ***
    ## poutcomesuccess               0.138506   0.038194    3.626 0.000287 ***
    ## emp.var.rate                 -2.393563   0.177365  -13.495  < 2e-16 ***
    ## cons.price.idx                1.000785   0.061274   16.333  < 2e-16 ***
    ## cons.conf.idx                 0.089738   0.021010    4.271 1.94e-05 ***
    ## euribor3m                     1.461711   0.184601    7.918 2.41e-15 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 23265  on 32950  degrees of freedom
    ## Residual deviance: 18158  on 32921  degrees of freedom
    ## AIC: 18218
    ## 
    ## Number of Fisher Scoring iterations: 6
    
    
    #summary(logistic)$coeff[-1] < 0.05
    #names(summary(logistic))[summary(logistic)$coeff[-1] < 0.05]
    library(lattice)
    library(ggplot2)
    
    
    ## Registered S3 methods overwritten by 'ggplot2':
    ##   method         from 
    ##   [.quosures     rlang
    ##   c.quosures     rlang
    ##   print.quosures rlang
    
    
    toselect.x <- summary(logistic)$coeff[-1,4] < 0.05
    relevant.x <- names(toselect.x)[toselect.x == TRUE] 
    sig.formula <- as.formula(paste("y ~",paste(relevant.x, collapse= "+")))
    sig.model <- glm(formula=sig.formula,data=train)
    predictions <- predict(sig.model, newdata = test, type = "response")
    library(caret)
    #data$y = as.factor(data$y)
    #levels(data$y) <- as.factor(predictions)
    test_pred_logistic <- ifelse(predictions<0.5,0,1)
    confusionMatrix(as.factor(test$y), as.factor(test_pred_logistic))
    
    
    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction    0    1
    ##          0 7230   95
    ##          1  704  208
    ##                                           
    ##                Accuracy : 0.903           
    ##                  95% CI : (0.8964, 0.9093)
    ##     No Information Rate : 0.9632          
    ##     P-Value [Acc > NIR] : 1               
    ##                                           
    ##                   Kappa : 0.3039          
    ##                                           
    ##  Mcnemar's Test P-Value : <2e-16          
    ##                                           
    ##             Sensitivity : 0.9113          
    ##             Specificity : 0.6865          
    ##          Pos Pred Value : 0.9870          
    ##          Neg Pred Value : 0.2281          
    ##              Prevalence : 0.9632          
    ##          Detection Rate : 0.8777          
    ##    Detection Prevalence : 0.8893          
    ##       Balanced Accuracy : 0.7989          
    ##                                           
    ##        'Positive' Class : 0               
    ## 
    
    
    (7229+218)/(7229+101+689+218)
    
    
    ## [1] 0.9040913
    
    
    #Accuracy rate of logistic model:(7229+218)/(7229+101+689+218)=0.9041

# kNN

    
    
    #randomize data, split 80% of it into training data and 20% of it into testing data 
    #data_rand <- data_rand[complete.cases(data_rand),]
    knn_train = as.data.frame(data_rand[1:ceiling(0.8*nrow(data_rand)),-54], drop = FALSE )
    knn_test = as.data.frame(data_rand[ceiling(0.8*nrow(data_rand)+1):nrow(data_rand),-54], drop = FALSE )
    
    knn_train_labels = (data_rand[1:ceiling(0.8*nrow(data_rand)),54])
    knn_test_labels = (data_rand[ceiling(0.8*nrow(data_rand)+1):nrow(data_rand),54])
    library(gmodels)
    
    
    ## Warning: package 'gmodels' was built under R version 3.6.1
    
    
    library(class)
    
    
    ## Warning: package 'class' was built under R version 3.6.1
    
    
    test_pred_knn = knn(train = knn_train, test = knn_test, cl = knn_train_labels, k = 3)
    CrossTable(x = knn_test_labels, y= test_pred_knn, prop.chisq = FALSE)
    
    
    ## 
    ##  
    ##    Cell Contents
    ## |-------------------------|
    ## |                       N |
    ## |           N / Row Total |
    ## |           N / Col Total |
    ## |         N / Table Total |
    ## |-------------------------|
    ## 
    ##  
    ## Total Observations in Table:  8237 
    ## 
    ##  
    ##                 | test_pred_knn 
    ## knn_test_labels |         0 |         1 | Row Total | 
    ## ----------------|-----------|-----------|-----------|
    ##               0 |      6999 |       326 |      7325 | 
    ##                 |     0.955 |     0.045 |     0.889 | 
    ##                 |     0.916 |     0.543 |           | 
    ##                 |     0.850 |     0.040 |           | 
    ## ----------------|-----------|-----------|-----------|
    ##               1 |       638 |       274 |       912 | 
    ##                 |     0.700 |     0.300 |     0.111 | 
    ##                 |     0.084 |     0.457 |           | 
    ##                 |     0.077 |     0.033 |           | 
    ## ----------------|-----------|-----------|-----------|
    ##    Column Total |      7637 |       600 |      8237 | 
    ##                 |     0.927 |     0.073 |           | 
    ## ----------------|-----------|-----------|-----------|
    ## 
    ## 
    
    
    (7025+283)/8237
    
    
    ## [1] 0.8872162
    
    
    #Accuracy rate of kNN model: (7025+283)/8237=0.8872

# SVM

    
    
    data <- read.csv("./tele.csv")
    
    #convert y to
    data$duration <- NULL
    data$y = ifelse(data$y == 'yes', 1, 0)
    
    #create dummy variables for each categorical variable
    data = as.data.frame(model.matrix(~.-1, data = data))
    
    #normalizes data and binds it with colum y
    data = cbind(as.data.frame(lapply(data[1:53], scale)), data$y)
    
    #reassigns last colum nae to y
    colnames(data)[colnames(data) == "data$y"] <- "y"
    str(data)
    
    
    ## 'data.frame':    41188 obs. of  54 variables:
    ##  $ X                           : num  -1.73 -1.73 -1.73 -1.73 -1.73 ...
    ##  $ age                         : num  1.53302 1.62897 -0.29018 -0.00231 1.53302 ...
    ##  $ jobadmin.                   : num  -0.582 -0.582 -0.582 1.718 -0.582 ...
    ##  $ jobblue.collar              : num  -0.538 -0.538 -0.538 -0.538 -0.538 ...
    ##  $ jobentrepreneur             : num  -0.191 -0.191 -0.191 -0.191 -0.191 ...
    ##  $ jobhousemaid                : num  6.153 -0.163 -0.163 -0.163 -0.163 ...
    ##  $ jobmanagement               : num  -0.276 -0.276 -0.276 -0.276 -0.276 ...
    ##  $ jobretired                  : num  -0.209 -0.209 -0.209 -0.209 -0.209 ...
    ##  $ jobself.employed            : num  -0.189 -0.189 -0.189 -0.189 -0.189 ...
    ##  $ jobservices                 : num  -0.327 3.062 3.062 -0.327 3.062 ...
    ##  $ jobstudent                  : num  -0.147 -0.147 -0.147 -0.147 -0.147 ...
    ##  $ jobtechnician               : num  -0.442 -0.442 -0.442 -0.442 -0.442 ...
    ##  $ jobunemployed               : num  -0.159 -0.159 -0.159 -0.159 -0.159 ...
    ##  $ jobunknown                  : num  -0.0899 -0.0899 -0.0899 -0.0899 -0.0899 ...
    ##  $ maritalmarried              : num  0.808 0.808 0.808 0.808 0.808 ...
    ##  $ maritalsingle               : num  -0.625 -0.625 -0.625 -0.625 -0.625 ...
    ##  $ maritalunknown              : num  -0.0441 -0.0441 -0.0441 -0.0441 -0.0441 ...
    ##  $ educationbasic.6y           : num  -0.243 -0.243 -0.243 4.119 -0.243 ...
    ##  $ educationbasic.9y           : num  -0.415 -0.415 -0.415 -0.415 -0.415 ...
    ##  $ educationhigh.school        : num  -0.548 1.824 1.824 -0.548 1.824 ...
    ##  $ educationilliterate         : num  -0.0209 -0.0209 -0.0209 -0.0209 -0.0209 ...
    ##  $ educationprofessional.course: num  -0.382 -0.382 -0.382 -0.382 -0.382 ...
    ##  $ educationuniversity.degree  : num  -0.648 -0.648 -0.648 -0.648 -0.648 ...
    ##  $ educationunknown            : num  -0.209 -0.209 -0.209 -0.209 -0.209 ...
    ##  $ defaultunknown              : num  -0.514 1.947 -0.514 -0.514 -0.514 ...
    ##  $ defaultyes                  : num  -0.00853 -0.00853 -0.00853 -0.00853 -0.00853 ...
    ##  $ housingunknown              : num  -0.157 -0.157 -0.157 -0.157 -0.157 ...
    ##  $ housingyes                  : num  -1.049 -1.049 0.953 -1.049 -1.049 ...
    ##  $ loanunknown                 : num  -0.157 -0.157 -0.157 -0.157 -0.157 ...
    ##  $ loanyes                     : num  -0.423 -0.423 -0.423 -0.423 2.365 ...
    ##  $ contacttelephone            : num  1.32 1.32 1.32 1.32 1.32 ...
    ##  $ monthaug                    : num  -0.42 -0.42 -0.42 -0.42 -0.42 ...
    ##  $ monthdec                    : num  -0.0666 -0.0666 -0.0666 -0.0666 -0.0666 ...
    ##  $ monthjul                    : num  -0.459 -0.459 -0.459 -0.459 -0.459 ...
    ##  $ monthjun                    : num  -0.385 -0.385 -0.385 -0.385 -0.385 ...
    ##  $ monthmar                    : num  -0.116 -0.116 -0.116 -0.116 -0.116 ...
    ##  $ monthmay                    : num  1.41 1.41 1.41 1.41 1.41 ...
    ##  $ monthnov                    : num  -0.333 -0.333 -0.333 -0.333 -0.333 ...
    ##  $ monthoct                    : num  -0.133 -0.133 -0.133 -0.133 -0.133 ...
    ##  $ monthsep                    : num  -0.118 -0.118 -0.118 -0.118 -0.118 ...
    ##  $ day_of_weekmon              : num  1.96 1.96 1.96 1.96 1.96 ...
    ##  $ day_of_weekthu              : num  -0.515 -0.515 -0.515 -0.515 -0.515 ...
    ##  $ day_of_weektue              : num  -0.494 -0.494 -0.494 -0.494 -0.494 ...
    ##  $ day_of_weekwed              : num  -0.496 -0.496 -0.496 -0.496 -0.496 ...
    ##  $ campaign                    : num  -0.566 -0.566 -0.566 -0.566 -0.566 ...
    ##  $ pdays                       : num  0.195 0.195 0.195 0.195 0.195 ...
    ##  $ previous                    : num  -0.349 -0.349 -0.349 -0.349 -0.349 ...
    ##  $ poutcomenonexistent         : num  0.398 0.398 0.398 0.398 0.398 ...
    ##  $ poutcomesuccess             : num  -0.186 -0.186 -0.186 -0.186 -0.186 ...
    ##  $ emp.var.rate                : num  0.648 0.648 0.648 0.648 0.648 ...
    ##  $ cons.price.idx              : num  0.723 0.723 0.723 0.723 0.723 ...
    ##  $ cons.conf.idx               : num  0.886 0.886 0.886 0.886 0.886 ...
    ##  $ euribor3m                   : num  0.712 0.712 0.712 0.712 0.712 ...
    ##  $ y                           : num  0 0 0 0 0 0 0 0 0 0 ...
    
    
    knn_train = as.data.frame(data_rand[1:ceiling(0.8*nrow(data_rand)),-54], drop = FALSE )
    knn_test = as.data.frame(data_rand[ceiling(0.8*nrow(data_rand)+1):nrow(data_rand),-54], drop = FALSE )
    
    knn_train_labels = (data_rand[1:ceiling(0.8*nrow(data_rand)),54])
    knn_test_labels = (data_rand[ceiling(0.8*nrow(data_rand)+1):nrow(data_rand),54])
    library(gmodels)
    library(class)
    library(e1071)
    
    
    ## Warning: package 'e1071' was built under R version 3.6.1
    
    
    library(kernlab)
    
    
    ## 
    ## Attaching package: 'kernlab'
    
    
    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     alpha
    
    
    # svm_model = ksvm(y ~ X + jobretired + jobstudent + maritalsingle + 
    #     maritalunknown + educationbasic.6y + educationhigh.school + 
    #     educationilliterate + educationprofessional.course + educationuniversity.degree + 
    #     defaultunknown + contacttelephone + monthjul + monthjun + 
    #     monthmar + monthmay + monthnov + monthoct + monthsep + day_of_weekmon + 
    #     day_of_weekwed + campaign + pdays + poutcomenonexistent + 
    #     poutcomesuccess + emp.var.rate + cons.price.idx + cons.conf.idx + 
    #     euribor3m, data = train, kernel = "rbfdot")
    train$y = as.factor(train$y)
    svm_model2 = ksvm(y ~ X + jobretired + jobstudent + maritalsingle + 
        maritalunknown + educationbasic.6y + educationhigh.school + 
        educationilliterate + educationprofessional.course + educationuniversity.degree + 
        defaultunknown + contacttelephone + monthjul + monthjun + 
        monthmar + monthmay + monthnov + monthoct + monthsep + day_of_weekmon + 
        day_of_weekwed + campaign + pdays + poutcomenonexistent + 
        poutcomesuccess + emp.var.rate + cons.price.idx + cons.conf.idx + 
        euribor3m, data = train, kernel = "rbfdot")
    # summary(svm_model)
    test_pred_svm <- predict(svm_model2, test)
    #predictions <- ifelse(predictions >=0.5, 1, 0)
    predictions = test_pred_svm
    head(predictions)
    
    
    ## [1] 0 0 0 0 0 0
    ## Levels: 0 1
    
    
    table(predictions, test$y)
    
    
    ##            
    ## predictions    0    1
    ##           0 7224  689
    ##           1  101  223
    
    
    result <- predictions == test$y
    table(result)
    
    
    ## result
    ## FALSE  TRUE 
    ##   790  7447
    
    
    prop.table(table(result))
    
    
    ## result
    ##     FALSE      TRUE 
    ## 0.0959087 0.9040913
    
    
    (7218+235)/(7218+672+112+235)
    
    
    ## [1] 0.9048197

# Combine all three models together

    
    
    combined_result = as.numeric(test_pred_knn)-1+as.numeric(test_pred_logistic)+as.numeric(test_pred_svm) -1
    final_pred = ifelse(combined_result>=2, 1, 0 )
    pred_table = prop.table(table(final_pred, test$y))
    pred_table
    
    
    ##           
    ## final_pred          0          1
    ##          0 0.87762535 0.08498240
    ##          1 0.01165473 0.02573753
    
    
    prop.table(table(test_pred_logistic, test$y))
    
    
    ##                   
    ## test_pred_logistic          0          1
    ##                  0 0.87774675 0.08546801
    ##                  1 0.01153333 0.02525191
    
    
    cat("accuracy=", pred_table[1,1]+pred_table[2,2])
    
    
    ## accuracy= 0.9033629

# Conclusion

Based on our previous results,

  * the SVM model has an accuracy of 90.48%,
  * the logistic regression model has an accuracy of 90.41%,
  * the KNN model has an accuracy of 88.72%
  * the synthetic model has an accuracy of 90.40%

From the result, although synthetic model is not more accurate than the
logistic regression model, we can still conclude that the synthetic model is
better because the negative true chance is smaller, which indicates that a
higher chance of the phone call to be success. In other words, using the
synthetic model will help making a better decision in terms of efforts because
of its least negative true chance.

