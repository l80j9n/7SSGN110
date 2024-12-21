java c
7SSGN110 Environmental Data Analysis | Practical 4 | Correlation  Regression
1.   About this practicalThe aim of this practical is to explore correlation and regression techniques that are common to environmental research. The practical is composed of four sections that consider (1) Pearson’s r correlation coefficient, (2) non-parametric correlations coefficients, (3) simple linear regression and (4) multiple linear regression. The practical will use R but offer suggestions for how to do some things in Excel.
There are three data files used in this practical, all available on KEATS: ‘PlantAbundance.csv’, ‘GQA.csv’ and ‘UKJanClimate.csv’. These data sets have been obtained from Moggridge (2006) [Plant Abundance and GQA] and MetOffice (2013) [Climate Data].
1.1.   Getting started in RBefore you start the data analysis In R, ensure that you have:·   Created a folder on your computer for this week’s practical ·   Set the working directory for the practical work in R Studio·   Loaded the required packages. We will be using the packages car, ggExtra, GGally, Hmisc.·   Read the ‘PlantAbundance.csv’, ‘GQA.csv’ and ‘UKJanClimate.csv’ files into R.2.   Pearson’s r correlation coefficientWe are interested in whether the abundance of Juncus effusus (J. effusus) in a riverine wetland is related to the particle size and the organic content of the soil. To investigate this, 103 quadrats were placed in random locations. In each quadrat, the percentage cover of J. effusus   was recorded and a soil sample was taken. The soil samples were taken back to the laboratory, where they were analysed for particle size and organic content.In this data set, the particle size is represented as % sand and the organic content is expressed as a percentage of the total volume of the soil sample. The abundance of J. effusus   is also recorded as a percentage. All data follow a normal distribution, so parametric tests will be used (you can test this yourself in R using the standard normality tests).2.1.   Data visualisation2.1.1.   Scatterplot typesThe 1st   thing we are going to do is visualize the possible relationship between J. effusus   abundance, % sand and % organic content. The simplest way to do this is by creating a simple scatterplot.
We can use the base R function plot() to do this:PA.data <-   read.csv(“PlantAbundance.csv”)
library(ggplot2)plot(PA.data$organic, PA.data$juncus)   
We can also use ggplot2 to create a scatterplot:plot1<-ggplot(PA.data, aes(organic, juncus)) +
                        geom_point() +   xlab(“Organic content (%)”) +   ylab(“J. effusus % cover”)
plot1   
Don’t forget you can customize your ggplot graphs to suit – a good guide is here!Q1. Repeat the script. above to create scatterplots that compare J. effusus abundance to % sand. Do the graphs suggest any relationship between the variables? If so, what?
We can go one step further and produce a scatterplot that also shows boxplots of the variables in the margin on the graph. This is helpful when you want to indicate the distribution of the variables. We need to use the package ‘car’ to do this, so make sure it is installed and loaded.scatterplot(juncus ~   organic, data =   PA.data, boxplots =   “xy”, smooth =   F, regLine =   F)#boxplots = ‘xy’ is to add a marginal boxplot for both the X and Y variables
#smooth = F removes the smoothed line of best fit from the scatterplot – we are not interested in this
#regLine = F removes the regression line – we are only interested in the scatterplot for the moment   We can also use the package ‘ggExtra’ (make sure it is installed  loaded!) to produce marginal histograms for each variable:ggMarginal(plot1, type=’histogram’)   # Note the code above re-uses the ggplot scatter plot we created above.2.1.2.   Matrix scatterplotA simple scatterplot is useful for showing how J. effusus   varies with each variable individually, but it is also often useful to plot more than two variables to look for multiple relationships. Matrix scatterplots allow you to visualize the relationship between all combinations of the different pairs of variables in one plot.
To create a matrix scatterplot in R use the pairs() command:pairs(~juncus +   organic +   sand, data =   PA.data)   
We can also use the package GGally (make sure it is installed  loaded!)   to create a matrix scatterplot:ggpairs(PA.data)   
Both ‘Ggally’ and ‘ggExtra’ are extension to ggplot2, meaning they have the same underlying functionality, and you can use the same arguments as you would in ggplot2 to modify your graphs.
3.   Pearson’s r correlation co-efficientThe scatterplots above suggested that there was a possible relationship between J. effusus abundance and organic content and sand. To explore this further and test for statistically significant relationships, we will perform. a Pearson’s r correlation. In R you can do this using the cor.test() command with method = “pearson”:cor.test(~juncus +   sand, data =   PA.data, method =   “pearson”)## 
##    Pearson’s product-moment correlation
## 
## data:    juncus and sand
## t = -4.938, df = 101, p-value = 3.128e-06
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##    -0.5846244 -0.2705591
## sample estimates:
##                      cor 
## -0.4409934The cor value at the bottom of the output is the r value for the correlation.Q. Based on this r value, make a statement about the strength and direction of the correlation between Juncus abundance and % sand
The output also gives you the t-test statistics and the p-value for the correlation.Q. What would be the null and alternative hypotheses for the correlation t-test? Based on an alpha value of 0.05, which hypothesis would you accept?Q. Repeat the analysis above to test the correlation of Juncus abundance with organic content. Use the results of this to make a statement about the strength, direction, and significance of the correlation.Q. Based on these correlations, make a statement about how sand and organic content influence Juncus abundance.3.1.   Correlation matrixWith only two independent variables it doesn’t take too long to run all possible combinations of variables. However, if you have many variables the approach above could take a while. To run many correlations simultaneously there are packages available with functions to create a correlation matrix. For example, we can use the rcorr function in the Hmisc package (ensure this package is installed and loaded!):rcorr(as.matrix(PA.data), type =   “pearson”)##                         organic juncus    sand
## organic          1.00       0.40 -0.71
## juncus             0.40       1.00 -0.44
## sand                -0.71    -0.44    1.00
## 
## n= 103 
## 
## 
## P
##                         organic juncus sand
## organic                            0                0    
## juncus       0                                        0    
## sand             0                   0Ensure you understand the output. The matrix at the top shows the Pearson correlation coefficient for each pair of variables, and the matrix below shows the p-value for each of these coefficients.
You can also use the ggcorr function from the GGally package to do this.
4.   Non-parametic correlation co-efficentsThe Environment Agency (EA) monitors the quality of UK rivers. In the past, the EA used a measurement scheme known as the General Quality Assessment (GQA). Although the EA no longer uses the GQA (it uses the EU Water Framework Directive), the GQA is a good example to use to learn about non-parametric correlation as it is a categorical index. In the GQA a site is assigned four scores based on chemistry, biology, nutrients and aesthetics. An EA worker is often interested in whether there is a relationship between a river’s chemical status and its biological status.
Along the River Thames, the Agency worker monitors 25 sites. Each site is given a chemical score and a biological score. Under the GQA, there are 6 chemical categories, based on dissolved oxygen levels, biological oxygen demand and ammonia levels. Rivers can score from A (the cleanest category, 1) to F (the most polluted, 6). There are also 6 biological categories: very good (1), good (2), fairly good (3), fair (4), poor (5) and bad (6). This is based on the invertebrates present. As our data is categorical rather than interval, we will use a non-parametric correlation test to determine if a relationship exists.
If you haven’t done so already, import the ‘GQA.csv’ file into R.Q. Produce a scatterplot of the data using one of the visualization techniques above. What do you notice about the visualisation of the data and units of measurement?
You should observe that some points are not visible – they lie directly on top of one another because of the ordinal nature of the data. To help in this situation we can use the jitter function to add some random variation to the value in each variable:plot(jitter(Biological) ~   jitter(Chemical), data =   GQA.data)   
You can also add this function to ggplot2:GQAplot <-   ggplot(GQA.data, aes(Biological, Chemical))
GQAplot +   geom_jitter(width =   0.2) +   xlab(“Jittered Biological Score”) +
ylab(“Jittered Chemical Score”)   
NB: Jittering is useful for plotting (ensuring we inform. the reader) but we should go back to the original data for our actual analyses.
To run a non-parametric correlation in R use the cor.test() command again but with method = “spearman”:cor.test(GQA.data$Biological, GQA.data$Chemical, method =   “spearman”)## Warning in cor.test.default(GQA.data$Biological, GQA.data$Chemical, method =
## “spearman”): Cannot compute exact p-value with ties##代 写7SSGN110 Environmental Data Analysis | Practical 4 | Correlation & Regression
代做程序编程语言 
##    Spearman’s rank correlation rho
## 
## data:    GQA.data$Biological and GQA.data$Chemical
## S = 1418, p-value = 0.02243
## alternative hypothesis: true rho is not equal to 0
## sample estimates:
##                   rho 
## 0.4546021The correlation output is now in the form. ‘rho’ rather than ‘r’.Q. Make a statement about the strength, direction, and significance of this correlation.To calculate Kendall’s Tau non-parametric correlation coefficient in R use the cor.test() command again but with method = “kendall”:cor.test(GQA.data$Biological, GQA.data$Chemical, method =   “kendall”)## Warning in cor.test.default(GQA.data$Biological, GQA.data$Chemical, method =
## “kendall”): Cannot compute exact p-value with ties## 
##    Kendall’s rank correlation tau
## 
## data:    GQA.data$Biological and GQA.data$Chemical
## z = 2.1794, p-value = 0.0293
## alternative hypothesis: true tau is not equal to 0
## sample estimates:
##                   tau 
## 0.3539614The correlation output is now in the form. ‘tau’.5.   Simple linear regressionIn the next part of the practical, we will be using simple linear regression to evaluate the controls on UK climate. We will be using the dataset ‘UKJanClimate.csv’ to do this.
Read the UkJanClimate.csv file into R and open the dataframe.
You will see that the dataset contains 6 variables:1.   minTemp: average of daily minima (°C)2.   maxTemp: average of daily maxima (°C)3.   rainfall: average monthly sum (mm)4.   elevation: height of climate station above mean sea level (m)5.   latitude: latitude of climate station (decimal degrees)6.   longitude: longitude of climate station (decimal degrees)In this practical, 1), 2) and 3) will always be dependent (response) variables and 4), 5) and 6) will be independent (predictor) variables. In this part of the practical, we will explore simple linear regression with minTemp as the dependent variable considering each of the three independent variables in turn.
Before we produce our linear regression models, we should first visualize the data.Q. Using one of the approaches described above, produce graphs that visualise the relationship between minTemp and elevation, latitude, and longitude.
Hint: You can use the scatterplot() function from the package ‘car’ to plot a simple line of best fit through our data – we just need to change the ‘regLine’ argument to true (regLine = T).
Hint 2: You can also plot a line of best fit in ggplot using the ‘geom_smooth()’ argument. To do this we have to specify the type of type of model we want to use (in this case we want to use a linear model so we use the argument ‘lm’) and whether we want the uncertainty represented (using the argument ‘se’ to plot the standard error).
Example code would be:ggplot(Jan.data, aes(minTemp, Elev)) + geom_point() + geom_smooth(method = lm, se = FALSE)
Before we run our linear regression models, we want to test the correlation between MinTemp and the three independent variables.Q. Using the code above, determine the correlation coefficient for MinTemp and the three independent variables.Q. make a statement about the strength, direction, and significance of the relationship between minTemp and the three independent variables.Q. Which independent variable do you think is going to be the most useful predictor for minTemp?5.1.   Producing a simple linear regression modelNow we will actually fit a regression model. When fitting a regression in R we create a model ‘object’. Once the model object is created we can run functions and commands with the model object to assess model fit, view diagnostics, etc.
To fit a simple linear regression model in R, you can use the lm() function and summary() with the model object created to access the results. For example, to fit a model that regresses Latitude against minimum January temperature:model1 <-   lm(minTemp ~   Lat, data =   Jan.data)
summary(model1)## 
## Call:
## lm(formula = minTemp ~ Lat, data = Jan.data)
## 
## Residuals:
##             Min                1Q    Median                3Q             Max 
## -1.2435 -0.4695 -0.1034    0.6577    1.9768 
## 
## Coefficients:
##                                     Estimate Std. Error t value Pr(>|t|)       
## (Intercept)    23.9423             6.1881       3.869    0.00112 **
## Lat                            -0.4191             0.1175    -3.567    0.00220 **
## ---
## Signif. Codes:    0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ‘ 1
## 
## Residual standard error: 0.8219 on 18 degrees of freedom
## Multiple R-squared:    0.4141, Adjusted R-squared:    0.3816 
## F-statistic: 12.72 on 1 and 18 DF,    p-value: 0.002204The code above fits a regression and puts the result in an ‘object’ (of class lm) named model1. The summary() function allows us to display the results of the model.
This summary gives us most of the information about the fit and significance of the model.
The first thing we will look at is the model co-coefficients. This table gives us the values of the intercept and slope for the model. The value of the intercept is given in the row (Intercept) – in this model it is 23.9423. The value of the slope is given in the row (Lat)- in this model it is -0.4191.
Beneath the co-efficient table, you can find the RMSE of the model, the result of the F-test for the significance of the model, and the adjusted R2   value.
Q. State the null and alternative hypothesis for the F test.
Q.What is the F-test statistic for the model? Can we accept or reject the null hypothesis in this case?
Q. How much variation in minimum temperature is explained by variation in latitude? Hint: the adjusted R2 value can be used to determine this – refer back to your lecture notes
To access the residuals from a lm() model object, we can use the residuals() function (you can also can use object$residuals, where ‘object’ is the name of the lm model object). For example, to plot a histogram of the model residuals:hist(model1$residuals)   Q. Why would plotting a histogram of model residuals might be useful given assumptions of linear regression?
To produce diagnostic plots of the model we can simply use the plot() function with such a model as an argument (you need to hit Enter to move from one plot to the next in R-Studio):plot(model1)   
Q. Review the diagnostic plots for this model. Based on these plots, do you think the model is a good fit?
Hint: Look back through your Week 4 lecture notes to help interpret these plots
Next, fit simple linear regression models to predict minTemp for each of the rest of the independent variables (i.e.   three separate models).
Recreate the table below in Word or Excel, replacing x.xxx with values from the results for your simple linear regression models (you may wish to refer back to your lecture notes).   ElevationLatitudeLongitudeModel   r2x.xxxx.xxxx.xxxModel   F   Statisticx.xxxx.xxxx.xxxModel   p-valuex.xxxx.xxxx.xxxIntercept   βx.xxxx.xxxx.xxxIntercept   p-valuex.xxxx.xxxx.xxxCoefficient (gradient)   βx.xxxx.xxxx.xxxCoefficient (gradient)   p-valuex.xxxx.xxxx.xxx
Now answer the following questions:
Q: What is the null hypothesis associated with each of the p-values listed above?
Q: Looking at your diagnostic plots, do you think the assumptions of simple linear regression have been met?
Q: What do you notice about the relationship between the overall Model p-value and β1 p-value in each model?
Q: Which independent variable provides the best estimation of average daily minima in January? Why did you make this choice?
Q: Which independent variable provides the worst estimation of average daily minima in January? Why did you make this choice?
Q: For the independent variable you think provides the best estimation, state the equation for estimating minTemp in the form. y = β1x + β0 where y and x are variables, β1 is the gradient, and β0 is the y-intercept (replace each of y, β1, x and β0 with either a number or a word)
Q: Using your answer to the previous question, predict minTemp using the simple linear regression model for your preferred variable for a fictitious climate station with elevation = 75 m, latitude = 52.000 and longitude = 0.000
6.   Correlation  regression in Excel
6.1.      Parametric correlation in Excel
You should be able to repeat sections on simple scatter and Pearson’s R in Excel. The simple scatter is similar to the ones created in Practical 1 and you can use the PEARSON() formula to calculate r. Note that without any add-ins or macros, Excel cannot produce matrix scatterplots.
6.2.   Non-parametric correlation in Excel
Excel does not come with predefined formulas for Spearman or Kendall’s correlation coefficients (highlighting the fact Excel is rather basic from a statistical standpoint and the need to use other software). However, if you’re feeling adventurous and if you know the equation for these coefficients you could try fitting them yourself in Excel using combinations of existing Excel formulas.
6.3.   Simple linear regression in Excel
The easiest way to perform. simple linear regression in Excel is to plot the data on a scatterplot and add a trendline (right-click on the data in the scatterplot and select Add Trendline). However, the only information we get about our regression using this approach is the regression line equation and r2   .
To get more information about a regression you will need to use the Regression tool in the Data Analysis ToolPak. You can find out how to do this either using the help function and your own knowledge or, if you need a detailed step by step instructions, go to Smith (2010).
   

         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
