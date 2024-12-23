java c
7SSGN110 Environmental Data Analysis | Practical 7 | Time Series Analysis – Frequency Domain
1.   Introduction   and data processing
The aim of this practical is to explore time-series analysis in the frequency domain (spectral analysis). The instructions in this exercise are perhaps less-detailed than previous practical sessions. At this stage you hopefully have sufficient skills to navigate and solve individual little details and steps along the way.
We will use data from the London Air Quality Network; this is an excellent resource for data on lots of air quality variables, including CO2, NOx, Ozone, PM10, and so-on from many locations around London. You can easily find their general website homepage, but we’re interested in the data download section here:
https://www.londonair.org.uk/london/asp/datadownload.asp   
Have a look and try to download/access data from one of the many monitoring stations. When you select a certain dataset it will show you a graph and generate a link to download the data in a .csv file format. Note that if the dataset gets too large (roughly >65,000 values, which is the old Excel limit, in fact) the download fails.
For the next exercise we want to use hourly data over a very long span of time (10 years). Because these data need to be downloaded in chunks (and because we don’t want to overload the LAQN data servers with 40+ simultaneous downloads) the relevant data is provided to you as a .csv file on KEATS. The data consist of hourly readings of NOx at the Marylebone monitoring station, from 1/1/2011 00:00 through 31/12/2020 23:00. NOx is the sum of the concentrations of NO and NO2 in the air, gas pollutants emitted from combustion engines. Concentrations of NOx in the air are also affected by temperature and solar radiation.
Loading the data
1)   Explore the Marylebone .csv file:
2)   Open the file in Excel first, so that you have a good understanding of the variables. Scroll down through several 100s of rows of data. You will see there are gaps in the NOx data; this is a common feature of real datasets.
3)   Open RStudio and read the .csv into an R dataframe. called data
4)   Make sure you understand the variables in the dataframe.
5)   Plot the data as a time-series to explore it visually
2.   Autocorrelation
Before doing spectral analysis, we’ll explore the autocorrelation in the time-series to see if there is a specific dominant periodicity. You have learned how to calculate the autocorrelation using acf() in last week’s practical.
Try running the autocorrelation function on your data, e.g. acf(data$value). You will notice that this returns an error. This is because of the missing values. The error can be resolved by specifying:    acf(data$value, na.action=na.exclude). Have a look at the autocorrelation function plot.
·   Q1: what do you think the autocorrelation peaks are associated with?
The lag range in the default ACF here is limited to just 50. What if we want to explore autocorrelations over much longer lags? Look up the usage of R’s acf function online (Google search or by typing ?acf in the console).
·   Q2: if we want to explore up to a lag of 2 months, how many sampling points does this represent?
Run the acf function to a maximum lag of 2 months, and review the plot.
·   Q3: can you see evidence of a second periodic cycle in the time-series?
We have seen that you can use the autocorrelation function to explore periodicities, but it gets harder to detect beyond just one dominant periodic signal, and also harder for much longer cycles. Spectral analysis is a much more powerful technique for detecting periodicitie代 写7SSGN110 Environmental Data Analysis | Practical 7 | Time Series Analysis – Frequency DomainJava
代做程序编程语言s in time-series over a very wide range of time-scales.
3.   Spectral analysis
Before embarking on data analysis it is always good to try and imagine what sort of results you might expect. Given what you know about the variable here (NOx in the air besides a roadway) and based on what you can see in the time-series:
·   Q4: what kind of periodicities would you expect to encounter in the time-series?
The basic R command for spectral analysis is the spectrum() function. Try running the spectrum function on your data, e.g. spectrum(data$value). You will again notice that this returns an error. This is because of the missing values. The error can be resolved by specifying:    spectrum(data$value, na.action=na.exclude). Have a look at the periodogram. There seem to be various peaks…
·   Q5: can you associate specific peaks to your expectations?The raw periodogram is very ‘noisy’ and this can be reduced by applying a smoothing filter over some scale defined as a span in the R function: spectrum(data$value, na.action=na.exclude, span=30).
Play around with different lengths for the smoothing filter.
Determining the time-scales of the peaks from the periodogram is difficult because of the default x-axis settings in the function: it scales the frequencies so that 1 = one cycle over the length of the time-series (3652.958 days…). We will now store the results from the spectrum() function into a dataframe. so that we can manipulate the outputs:
data.spect <- spectrum(data$value, na.action=na.exclude, span=30)
Study the contents of the dataframe. so that you understand every variable.
First, we convert the scaled frequencies to our actual time unit (our time-unit is a day), by dividing by the sampling interval, 1 hour = 1/24 day:
data.spect$freq <- data.spect$freq/(1/24)
Most of the frequency range here is far below 1, so let’s look at periods instead. Create a new variable to store the sequence of frequencies converted to periods:
data.spect$period <- 1/   data.spect$freq
Now let’s plot the spectrum as a function of period:
plot(data.spect$spec~   data.spect$period, type=’l’)       [NB: the type is l for “linear”, not for the number 1]
This looks completely different from the previous periodogram! Compare and determine the key differences:·   the original periodogram plots the power (y-axis) on a logarithmic scale·   in the original periodogram the highest frequencies (and thus the lowest periods) are on the right-hand side of the x-axis
We can achieve this formatting as follows:
plot( log10(data.spect$spec)~ data.spect$period, type='l', xlim=c(3600,min(data.spect$period)) )
This still looks awful… because most of the graph covers periods of many 100s of days (and nothing much happens in that period range). Better to consider the time-scale on a logarithmic basis as well:
plot(log10(data.spect$spec)~log10(data.spect$period), type='l', xlim=c(log10(3600),log10(min(data.spect$period))) )
This looks much better! For interpreting this periodogram we can still optimise more, because there seem to be no obvious peaks in the periods ranging longer than 1000 days, so let’s limit the x-axis:
plot(log10(data.spect$spec)~log10(data.spect$period), type='l', xlim=c(3,log10(min(data.spect$period))) )
Add gridlines to aid reading:
grid(nx=NULL, ny=NULL, lty=2, col="gray", lwd=2)
Now we can interpret the periodogram. The strongest peak is found at log(T)=0.·   Q6: What’s the time period that this corresponds to?·   Q7: Can you interpret the other visible peaks?
Hint: convert from the log(period) scale to actual time units.
   

         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
