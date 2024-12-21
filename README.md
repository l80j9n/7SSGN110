java c
7SSGN110 Environmental Data Analysis | Practical 6 | Time Series Analysis – Time Domain
1.   Introduction   and data processing
The aim of this practical is to introduce time series decomposition and autocorrelation.
This case study data are monthly average atmospheric CO2 concentrations measured at the Mauna Loa Observatory, Hawaii, USA, a brief overview of which is provided by Keeling (2008).   
Data from Mauna Loa Observatory are available from the National Oceanic and Atmospheric Administration (NOAA) Earth System Research Laboratory Global Monitoring Division website: http://www.esrl.noaa.gov/gmd/obop/mlo/. We have downloaded the relevant data for you and posted this as an Excel file on KEATS. Download and open this file.
As always, the first thing we should do when working with a new data set is to quickly plot the data to familiarise ourselves with it and check for any inconsistencies or potential problems:
·   Create a Single Line Plot that displays the value column. To show years on the x-axis, right-click on your graph →   Select Data →   Edit the horizontal axis →   Column B (year)
·   Change the scale of the vertical axis so that you can clearly see the full variation of the time series.
You should be able to immediately notice both an increasing trend and a seasonal pattern.·   What do you notice about the pattern early in the time series?Hopefully, you notice that the early values in the time series do not fit the seasonal pattern as well as the remainder of the time series. Look at the data to see why this is.The data we downloaded has values for 1969 and 1970 before skipping to 1976 (check you can see this in your raw data). To make our data easier to handle we will work here with data that are for the complete years 1977-2020:·   Remove the rows of data for 1969, 1970 and 1976 (select rows, right-click, delete).You should now have data for all months for 44 years.·   How could you quickly check you really do have data for all months in these years? [Hint, for 44 years how many data values would you expect? How can you count values quickly to check?]
2.   Time series decomposition
2.1.   Time series decomposition in Excel
Time series can be understood as being composed as shown by Eq. 1:
   
   
Eq. 1
In the following we will see how we can decompose a time series into these components in Excel to help us describe and better understand the data.
a) Trend: Running Mean
One way of estimating a trend in a time series is to use a running mean. This approach calculates the average value of a variable across a sliding window of a specific width (duration). We usually aim for this window to be of equal size above and below the original data point, which is straight-forward for an odd-numbered size window but requires some additional thought when the window is even in size.
For example, for a window of size five the running mean m at time t calculated from values x would be given by Eq. 2:
   
   
Eq. 2
Thus, the running mean is calculated from the current value, the two data points before, and the two values after.    To ensure an even-sized window is symmetrical we use a slightly different approach. For example, for a window of size six, the running mean would be given by Eq. 3:
   
   
Eq. 2
Thus, the running mean is calculated as before except we account for only half the value of the data points at the edges of the window. To create a running mean for the Mauna Loa data we will use a window size of 12. To do this:
I.   In column E add a sensible column title for a running mean with window size 12
II.   Assuming data values are in column D enter the following formula in cell E8: =SUM(D2/2, D3:D13, D14/2)/12
III.   Copy this formula down to cell E523
Make sure you understand what the formula you’ve implemented in Excel is doing.·   Why have we used a window of size 12? How would you change the formula to use window size 13? Why have we stopped at row 523?
To check what your running means looks like, add the data you have created in column E to your existing quick plot of the data (re-create this if you deleted it!):i.   Right-click on the chartii.   Click Select Dataiii.   In Legend Entries (series) click the Add buttoniv.   Provide series name Running Mean (12)v.   For series values, select the entirety of column E
Your plot should look something like Figure 1.·   Note how the running mean line does not extend to the length of the empirical data. Why is this?
   
Figure 1.   Example plot of CO2 data with running mean.
Check your running mean is centred on the data, with equal number of missing values at start and end of the time series.
b) Periodicity
We now calculate the periodic signal in the data, using the trend data. First, for each observation we calculate the difference between the observed value and the trend (running mean) value. Calculate this difference in column F for all the running mean values (and provide a relevant column header label, e.g. “diff”).
After you have done this, we determine the average difference for each of the 12 months using a pivot table:I.   Select the entire data block (all columns and rows, including the headers)II.   Go to menu: Insert – Pivot Table – From Table/Range, on a New WorksheetIII.   On this new PivotTable worksheet on the right-hand side:·   drag the ‘month’ field from the top into the ‘Rows’ panel below·   drag the ‘diff’ field from the top into the ‘Values’ panel at the bottom-right·   change the Value Field Setting to calculate an average instead of a sumIV.   On the left you should now see the average difference for each of the 12 months
Look at the seasonal effect values you have just calculated.·   You should notice some are positive and some are negative. Why is this?
c) Residuals 
Finally, we will calculate the residuals at each time t once the trend and seasonal effect have been removed.
First, populate column G with repeat copies of the seasonal cycle we compiled in the previous step. You may have to “hard copy” the values from the pivot table first.
The residuals are then the remainder after subtracting the trend and the periodic signal from the original value (column D minus column E and minus Column G). A plot of these residuals should look something like this.
   
Figure 2.   Residuals from time series decomposition of Mauna Loa data.
These residuals are now de-trended and should be ‘stationary’ such that the probabilistic character of the series does not change over time (i.e. any section of the time series is “typical” for every other section; but note there are different ‘degrees’ of stationarity). Such stationary data can then be subjected to auto-regressive and/or moving average modelling which can be used for forecasting (e.g. see Von Storch and Zwiers 2001). These techniques are beyond the level of this practical but you may be interested in pursuing them further elsew代 写7SSGN110 Environmental Data Analysis | Practical 6 | Time Series Analysis – Time Domain R
代做程序编程语言here.
2.2.   Time series decomposition in R
We will now briefly introduce you to a powerful R function (‘DECOMPOSE’) which takes a time-series and splits it into the components like we did above.Before you start the data analysis in R, ensure that you have:•   Created a folder on your computer for this week’s practical•   Set the working directory for the practical work in R Studio•   Loaded the required packages. We will be using the package timeSeries•   Read the csv file with the CO2   data into RHint: Refer to Practical 2, section 4.1 for guidance on how to do these things.
We start by cleaning the data just as we did in Excel. First plot the data to visualise and notice the oddities prior to 1976. Remove the odd data from the dataframe.plot(mlo$value, type = "l")mlo <-   mlo[mlo$year > 1976,]
Now we can create a timeseries object using the data we have loaded.   mlo.ts <- ts(mlo$value, frequency=12, start=c(1977,1))
A timeseries object has some build in functionalities that we can use. For example, we can use plot for a timeseries object and it will automatically select the type “line” and add labels to the x-axis.plot(mlo.ts)
Now we can decompose the time-series into a decomposition dataframe. and plot all of the decomposed time-series at the same time:DEC <- decompose(mlo.ts, ”additive”)plot(DEC)
If you want to practice with R, you could try and do the decomposition yourself, like we did in Excel. The code is provided below, see if you understand what each line is doing.yrs <- max(mlo$year) - min(mlo$year) + 1weights <- c(.5, rep(1,11),.5)/12   trend <- filter(mlo.ts, filter=weights, sides=2)plot(mlo.ts)lines(trend, col="red")trend.adj <- mlo.ts - trendmonth <- factor(rep(1:12, yrs))seasonality <- tapply(trend.adj, month, mean, na.rm=TRUE)plot(seasonality, type="h", xlab="Month")title("Seasonal Effects for Mauna Loa Data")abline(h=0, col="grey")residual <- mlo.ts - trend - rep(seasonality, yrs)plot(residual, main="Estimated Residual Variability")3.   Autocorrelation
Another aspect of time series we will consider here is the characteristic of auto-correlation, or the correlation of the data with itself at different ‘lags’. A ‘lag’ in this sense is a difference between points in time (a time delay), so for example the difference between a value at time t and at time t+3 would be a lag of +3.
3.1.   Autocorrelation in ExcelTo calculate autocorrelation in Excel we can use the CORREL function. First, we duplicate our data with vertical off-sets:I.   Create a new blank worksheet in your Excel workbook and copy the original core data to this sheetII.   Insert a few empty rows above the dataset to give space for doing statistics laterIII.   Select the values in column D and ctrl+c (copy) this blockIV.   In a next column, move to a cell one below the top and paste the data (ctrl+v)V.   Repeat this process so that you have a ‘cascading’ sequence of data columns, where the data in each column is off-set by one row from the previous one:We can now calculate the auto-correlation between the time-series and its own copy with a lag of +1, by doing the following calculation in column F above the dataset. In the space above the column with your lag +1 off-set, calculate the autocorrelation as:=CORREL($D6:$D1000, F6:F1000)By using a data range in the formula extending to row 1000 we don’t have to worry about the bottom end of the dataset, and by using the $ sign in front of the column letter we keep the first array in the formula always to be the original time-series in column D. We can now copy this formula to the other lagged columns to the right.Extend this system to lag +24.After this you should have a horizontal row with 24 auto-correlations, from lag +1 to lag +24. Select these 24 values and plot them as a simple bar-chart:   
·   What do you notice about the correlogram? For what lags is autocorrelation high? For what lags is autocorrelation low?In Part 2 above it was quite obvious that the seasonal effect would be for a 12-month cycle and so we used a moving window of size 12 to estimate our running mean.
·   The correlogram you have just produced justifies the decision to use a moving window of 12 – why is this?Note that the way we have calculated to autocorrelation in Excel, by copying and pasting columns of data and shifting them, is not only time-consuming but also not the most mathematically rigorous way of calculating autocorrelation. If we were to do this in R (see below) it would not only be quicker, but also yield more accurate results (for the reasons see: http://stats.stackexchange.com/questions/10947/formula-for-autocorrelation-in-r-vs-excel).   3.2.   Autocorrelation in RThe same analysis that you just did in Excel, can also be done in R. Continuing from the script. that we write in section 2.2, we can now add the autocorrelation calculation. The following R code performs the same calculations that we just did in Excel. See if you understand what each line is doing. (Remember: typing ?functionname in the console will open the page with a description of the function.)
We can create plots of the lags in the timeseries:lag.plot(mlo.ts, lags = 1, set.lags = c(3), do.lines=F, pch=20, main="Lag Plot, k=3")lag.plot(mlo.ts, lags = 9, set.lags = seq(0,24,3), do.lines=F, pch=20, main="Lag Plot")
We can use the Auto- and Cross- Covariance and -Correlation Function Estimation to calculate the autocorrelation:acf(mlo.ts, lag.max = 24, plot=T)acf(mlo.ts, lag.max = 24, plot=F)
We can use the same function to calculate the autocorrelation for the separate timeseries of the trend, seasonality and residuals:trend.notna <- na.omit(trend)acf(trend.notna)acf(seasonality)residual.notna <- na.omit(residual)acf(residual.notna)4.   More advanced time series analysis in RThe analysis above can be done much quicker in R than in Excel. Furthermore, many of the advanced methods highlighted in the lecture are either impossible or very difficult to implement in Excel. Good examples of time series analysis in R, including the advanced methods discussed in lecture, are provided in Dettling (2014). The timeSeries package has useful time series functionality.
References
Dettling, M. (2014) Applied Time Series Analysis. Zurich: Swiss Federal Institute of Technology. Available from: https://ethz.ch/content/dam/ethz/special-interest/math/statistics/sfs/Education/Advanced%20Studies%20in%20Applied%20Statistics/course-material-1921/Zeitreihen/ATSA_Script_v200504.pdf   [Accessed 24 October 2024]
Keeling, R.F. (2008). Recording Earth's Vital Signs. Science, 319(5871), 1771-1772.
NOAA ESRL GMD (2016) Mauna Loa Carbon Dioxide Flask Monthly Average Data   [Online] Available from: ftp://aftp.cmdl.noaa.gov/data/trace_gases/co2/flask/surface/co2_mlo_surface-flask_1_ccgg_month.txt    [Accessed 13 November 2016]
Von Storch, H.,  Zwiers, F.W. (2001). Statistical analysis in climate research. Cambridge: Cambridge University Press.

         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
