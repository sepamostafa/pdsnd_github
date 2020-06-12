
### Explore Bike Share Data

For this project, your goal is to ask and answer three questions about the available bikeshare data from Washington, Chicago, and New York.  This notebook can be submitted directly through the workspace when you are confident in your results.

You will be graded against the project [Rubric](https://review.udacity.com/#!/rubrics/2508/view) by a mentor after you have submitted.  To get you started, you can use the template below, but feel free to be creative in your solutions!


```R
## loading the necessary libraries
library(tidyr)
library(ggplot2)
library(readr)
library(dplyr)

```

    



```R
## importing the datasets
ny = read.csv('new_york_city.csv')
wash = read.csv('washington.csv')
chi = read.csv('chicago.csv')
```

### Question 1

## Investigating The rush day and rush our we have in our system 

## So the question is

## What is the popular time of travel over the 3 cities ?

## I will investigate the most common day and hour of travel over the 3 cities together ..

# To do that I shoud bind the data from the 3 cities 



```R
## To bind the 3 datasets they have two be in same number of columns so
##I will Adjust the columns for NY and Chi to have same No of columns as Wash using the function subset. 

adjusted_ny  <- subset(ny,select =  c(X,Start.Time,End.Time,Trip.Duration,Start.Station,End.Station,User.Type))
adjusted_chi  <-  subset(chi,select =  c(X,Start.Time,End.Time,Trip.Duration,Start.Station,End.Station,User.Type))

## using rbind to bind the 3 datasets into one 

binded_cities  <-  rbind(adjusted_ny,adjusted_chi,wash)

```


```R
##I want to take a look into the datatypes of my variables
str(binded_cities)
```

    'data.frame':	152451 obs. of  7 variables:
     $ X            : int  5688089 4096714 2173887 3945638 6208972 1285652 1675753 1692245 2271331 1558339 ...
     $ Start.Time   : Factor w/ 143945 levels "2017-01-01 00:17:01",..: 45448 32799 17316 31589 49688 10220 13390 13509 18111 12449 ...
     $ End.Time     : Factor w/ 143948 levels "201","2017-01-01 00:30:56",..: 45432 32783 17295 31567 49668 10204 13364 13505 18092 12422 ...
     $ Trip.Duration: num  795 692 1325 703 329 ...
     $ Start.Station: Factor w/ 1585 levels "","1 Ave & E 16 St",..: 522 406 10 93 5 521 325 309 151 245 ...
     $ End.Station  : Factor w/ 1586 levels "","1 Ave & E 16 St",..: 613 8 362 558 269 107 389 110 151 243 ...
     $ User.Type    : Factor w/ 3 levels "","Customer",..: 3 3 3 3 3 3 3 3 2 3 ...



```R
##To explore the most popular day and hour I need to split the Start.Time column into two more column :
#(one for the day and one for the hour)
# first I want to deal with data with no nas so I will remove nas

binded_cities  <- na.omit(binded_cities)

```


```R
##I tried this code first to seperate the date into multiple columns for month, days,.... but the problem was 
##the results are characters and can't be converted into num to apply stat functions

#Date_seperated <- separate(binded_cities, Start.Time, into = c("year" ,"month","day", "hour","minutes","secs"),convert = FALSE)

#head(Date_seperated)


#day <-  as.numeric(Date_seperated$day)

#hour <- as.numeric(Date_seperated$hour)

#str(Date_seperated)
```


```R
##So I tried This code to separate the day and month from the data 

Date_separated<-as.Date(binded_cities$Start.Time)

month <- format(Date_separated,"%m")

month <- as.numeric(month)


day<- format(Date_separated,"%d")


day <- as.numeric(day)

## run str to make sure the data type is adjusted
str(month)

str(day)


```

     num [1:152449] 6 5 3 5 6 2 3 3 4 3 ...
     num [1:152449] 11 11 29 8 21 22 6 7 2 1 ...



```R
##this code to bind theextracted month and day to our dataset

binded_cities <- cbind(binded_cities , month ,day)

head(binded_cities)



```


<table>
<thead><tr><th scope=col>X</th><th scope=col>Start.Time</th><th scope=col>End.Time</th><th scope=col>Trip.Duration</th><th scope=col>Start.Station</th><th scope=col>End.Station</th><th scope=col>User.Type</th><th scope=col>month</th><th scope=col>day</th></tr></thead>
<tbody>
	<tr><td>5688089                    </td><td>2017-06-11 14:55:05        </td><td>2017-06-11 15:08:21        </td><td> 795                       </td><td>Suffolk St &amp; Stanton St</td><td>W Broadway &amp; Spring St </td><td>Subscriber                 </td><td>6                          </td><td>11                         </td></tr>
	<tr><td>4096714                                                           </td><td>2017-05-11 15:30:11                                               </td><td>2017-05-11 15:41:43                                               </td><td> 692                                                              </td><td>Lexington Ave &amp; E 63 St                                       </td><td><span style=white-space:pre-wrap>1 Ave &amp; E 78 St       </span></td><td>Subscriber                                                        </td><td>5                                                                 </td><td>11                                                                </td></tr>
	<tr><td>2173887                                                            </td><td>2017-03-29 13:26:26                                                </td><td>2017-03-29 13:48:31                                                </td><td>1325                                                               </td><td><span style=white-space:pre-wrap>1 Pl &amp; Clinton St      </span></td><td><span style=white-space:pre-wrap>Henry St &amp; Degraw St  </span> </td><td>Subscriber                                                         </td><td>3                                                                  </td><td>29                                                                 </td></tr>
	<tr><td>3945638                                                            </td><td>2017-05-08 19:47:18                                                </td><td>2017-05-08 19:59:01                                                </td><td> 703                                                               </td><td><span style=white-space:pre-wrap>Barrow St &amp; Hudson St  </span></td><td><span style=white-space:pre-wrap>W 20 St &amp; 8 Ave       </span> </td><td>Subscriber                                                         </td><td>5                                                                  </td><td> 8                                                                 </td></tr>
	<tr><td>6208972                                                            </td><td>2017-06-21 07:49:16                                                </td><td>2017-06-21 07:54:46                                                </td><td> 329                                                               </td><td><span style=white-space:pre-wrap>1 Ave &amp; E 44 St        </span></td><td><span style=white-space:pre-wrap>E 53 St &amp; 3 Ave       </span> </td><td>Subscriber                                                         </td><td>6                                                                  </td><td>21                                                                 </td></tr>
	<tr><td>1285652                                                            </td><td>2017-02-22 18:55:24                                                </td><td>2017-02-22 19:12:03                                                </td><td> 998                                                               </td><td><span style=white-space:pre-wrap>State St &amp; Smith St    </span></td><td><span style=white-space:pre-wrap>Bond St &amp; Fulton St   </span> </td><td>Subscriber                                                         </td><td>2                                                                  </td><td>22                                                                 </td></tr>
</tbody>
</table>




```R


binded_cities <- binded_cities %>% mutate( time = sub(".* (.*)","\\1",Start.Time))


head(binded_cities)

str(binded_cities)


```


<table>
<thead><tr><th scope=col>X</th><th scope=col>Start.Time</th><th scope=col>End.Time</th><th scope=col>Trip.Duration</th><th scope=col>Start.Station</th><th scope=col>End.Station</th><th scope=col>User.Type</th><th scope=col>month</th><th scope=col>day</th><th scope=col>time</th></tr></thead>
<tbody>
	<tr><td>5688089                    </td><td>2017-06-11 14:55:05        </td><td>2017-06-11 15:08:21        </td><td> 795                       </td><td>Suffolk St &amp; Stanton St</td><td>W Broadway &amp; Spring St </td><td>Subscriber                 </td><td>6                          </td><td>11                         </td><td>14:55:05                   </td></tr>
	<tr><td>4096714                                                           </td><td>2017-05-11 15:30:11                                               </td><td>2017-05-11 15:41:43                                               </td><td> 692                                                              </td><td>Lexington Ave &amp; E 63 St                                       </td><td><span style=white-space:pre-wrap>1 Ave &amp; E 78 St       </span></td><td>Subscriber                                                        </td><td>5                                                                 </td><td>11                                                                </td><td>15:30:11                                                          </td></tr>
	<tr><td>2173887                                                            </td><td>2017-03-29 13:26:26                                                </td><td>2017-03-29 13:48:31                                                </td><td>1325                                                               </td><td><span style=white-space:pre-wrap>1 Pl &amp; Clinton St      </span></td><td><span style=white-space:pre-wrap>Henry St &amp; Degraw St  </span> </td><td>Subscriber                                                         </td><td>3                                                                  </td><td>29                                                                 </td><td>13:26:26                                                           </td></tr>
	<tr><td>3945638                                                            </td><td>2017-05-08 19:47:18                                                </td><td>2017-05-08 19:59:01                                                </td><td> 703                                                               </td><td><span style=white-space:pre-wrap>Barrow St &amp; Hudson St  </span></td><td><span style=white-space:pre-wrap>W 20 St &amp; 8 Ave       </span> </td><td>Subscriber                                                         </td><td>5                                                                  </td><td> 8                                                                 </td><td>19:47:18                                                           </td></tr>
	<tr><td>6208972                                                            </td><td>2017-06-21 07:49:16                                                </td><td>2017-06-21 07:54:46                                                </td><td> 329                                                               </td><td><span style=white-space:pre-wrap>1 Ave &amp; E 44 St        </span></td><td><span style=white-space:pre-wrap>E 53 St &amp; 3 Ave       </span> </td><td>Subscriber                                                         </td><td>6                                                                  </td><td>21                                                                 </td><td>07:49:16                                                           </td></tr>
	<tr><td>1285652                                                            </td><td>2017-02-22 18:55:24                                                </td><td>2017-02-22 19:12:03                                                </td><td> 998                                                               </td><td><span style=white-space:pre-wrap>State St &amp; Smith St    </span></td><td><span style=white-space:pre-wrap>Bond St &amp; Fulton St   </span> </td><td>Subscriber                                                         </td><td>2                                                                  </td><td>22                                                                 </td><td>18:55:24                                                           </td></tr>
</tbody>
</table>



    'data.frame':	152449 obs. of  10 variables:
     $ X            : int  5688089 4096714 2173887 3945638 6208972 1285652 1675753 1692245 2271331 1558339 ...
     $ Start.Time   : Factor w/ 143945 levels "2017-01-01 00:17:01",..: 45448 32799 17316 31589 49688 10220 13390 13509 18111 12449 ...
     $ End.Time     : Factor w/ 143948 levels "201","2017-01-01 00:30:56",..: 45432 32783 17295 31567 49668 10204 13364 13505 18092 12422 ...
     $ Trip.Duration: num  795 692 1325 703 329 ...
     $ Start.Station: Factor w/ 1585 levels "","1 Ave & E 16 St",..: 522 406 10 93 5 521 325 309 151 245 ...
     $ End.Station  : Factor w/ 1586 levels "","1 Ave & E 16 St",..: 613 8 362 558 269 107 389 110 151 243 ...
     $ User.Type    : Factor w/ 3 levels "","Customer",..: 3 3 3 3 3 3 3 3 2 3 ...
     $ month        : num  6 5 3 5 6 2 3 3 4 3 ...
     $ day          : num  11 11 29 8 21 22 6 7 2 1 ...
     $ time         : chr  "14:55:05" "15:30:11" "13:26:26" "19:47:18" ...



```R

 ## as we see time is separated but in character format so I have to separate the hour and change it to a number

hour  <- as.numeric(substr(binded_cities$time, 1, 2))


binded_cities  <-  cbind(binded_cities, hour)

head(binded_cities)

str(binded_cities)


```


<table>
<thead><tr><th scope=col>X</th><th scope=col>Start.Time</th><th scope=col>End.Time</th><th scope=col>Trip.Duration</th><th scope=col>Start.Station</th><th scope=col>End.Station</th><th scope=col>User.Type</th><th scope=col>month</th><th scope=col>day</th><th scope=col>time</th><th scope=col>hour</th></tr></thead>
<tbody>
	<tr><td>5688089                    </td><td>2017-06-11 14:55:05        </td><td>2017-06-11 15:08:21        </td><td> 795                       </td><td>Suffolk St &amp; Stanton St</td><td>W Broadway &amp; Spring St </td><td>Subscriber                 </td><td>6                          </td><td>11                         </td><td>14:55:05                   </td><td>14                         </td></tr>
	<tr><td>4096714                                                           </td><td>2017-05-11 15:30:11                                               </td><td>2017-05-11 15:41:43                                               </td><td> 692                                                              </td><td>Lexington Ave &amp; E 63 St                                       </td><td><span style=white-space:pre-wrap>1 Ave &amp; E 78 St       </span></td><td>Subscriber                                                        </td><td>5                                                                 </td><td>11                                                                </td><td>15:30:11                                                          </td><td>15                                                                </td></tr>
	<tr><td>2173887                                                            </td><td>2017-03-29 13:26:26                                                </td><td>2017-03-29 13:48:31                                                </td><td>1325                                                               </td><td><span style=white-space:pre-wrap>1 Pl &amp; Clinton St      </span></td><td><span style=white-space:pre-wrap>Henry St &amp; Degraw St  </span> </td><td>Subscriber                                                         </td><td>3                                                                  </td><td>29                                                                 </td><td>13:26:26                                                           </td><td>13                                                                 </td></tr>
	<tr><td>3945638                                                            </td><td>2017-05-08 19:47:18                                                </td><td>2017-05-08 19:59:01                                                </td><td> 703                                                               </td><td><span style=white-space:pre-wrap>Barrow St &amp; Hudson St  </span></td><td><span style=white-space:pre-wrap>W 20 St &amp; 8 Ave       </span> </td><td>Subscriber                                                         </td><td>5                                                                  </td><td> 8                                                                 </td><td>19:47:18                                                           </td><td>19                                                                 </td></tr>
	<tr><td>6208972                                                            </td><td>2017-06-21 07:49:16                                                </td><td>2017-06-21 07:54:46                                                </td><td> 329                                                               </td><td><span style=white-space:pre-wrap>1 Ave &amp; E 44 St        </span></td><td><span style=white-space:pre-wrap>E 53 St &amp; 3 Ave       </span> </td><td>Subscriber                                                         </td><td>6                                                                  </td><td>21                                                                 </td><td>07:49:16                                                           </td><td> 7                                                                 </td></tr>
	<tr><td>1285652                                                            </td><td>2017-02-22 18:55:24                                                </td><td>2017-02-22 19:12:03                                                </td><td> 998                                                               </td><td><span style=white-space:pre-wrap>State St &amp; Smith St    </span></td><td><span style=white-space:pre-wrap>Bond St &amp; Fulton St   </span> </td><td>Subscriber                                                         </td><td>2                                                                  </td><td>22                                                                 </td><td>18:55:24                                                           </td><td>18                                                                 </td></tr>
</tbody>
</table>



    'data.frame':	152449 obs. of  11 variables:
     $ X            : int  5688089 4096714 2173887 3945638 6208972 1285652 1675753 1692245 2271331 1558339 ...
     $ Start.Time   : Factor w/ 143945 levels "2017-01-01 00:17:01",..: 45448 32799 17316 31589 49688 10220 13390 13509 18111 12449 ...
     $ End.Time     : Factor w/ 143948 levels "201","2017-01-01 00:30:56",..: 45432 32783 17295 31567 49668 10204 13364 13505 18092 12422 ...
     $ Trip.Duration: num  795 692 1325 703 329 ...
     $ Start.Station: Factor w/ 1585 levels "","1 Ave & E 16 St",..: 522 406 10 93 5 521 325 309 151 245 ...
     $ End.Station  : Factor w/ 1586 levels "","1 Ave & E 16 St",..: 613 8 362 558 269 107 389 110 151 243 ...
     $ User.Type    : Factor w/ 3 levels "","Customer",..: 3 3 3 3 3 3 3 3 2 3 ...
     $ month        : num  6 5 3 5 6 2 3 3 4 3 ...
     $ day          : num  11 11 29 8 21 22 6 7 2 1 ...
     $ time         : chr  "14:55:05" "15:30:11" "13:26:26" "19:47:18" ...
     $ hour         : num  14 15 13 19 7 18 16 7 8 23 ...



```R
plot <-  function (column, DF,scale_x, scale_y,title,xlab, ylab){
ggplot(aes(x= column) , data = DF)+
 geom_histogram(color= 'white' , fill = '#A02330',binwidth =1)+
  scale_x_continuous(breaks = scale_x )+
  scale_y_continuous(breaks = scale_y)+
ggtitle(title)+
xlab(xlab)+
ylab(ylab)
}

plot(hour, binded_cities,0:23,seq(0,15000,1000),
     "A Histogram of the count of rentals in the different day hours","Hour","Number of bike rentals")



## As we see from the histogram of the hours on 24 hour format 
#1- the most popular hour or our rush hour is 8 am with no of rentals more than 14500
#2- the second most rented hour is 7 am with approximately 11500 rentals  


```


![png](output_12_0.png)



```R


plot(day, binded_cities,1:31,seq(0,6000,500),
     "A Histogram of the count of rentals in the different days","Day","Number of bike rentals")



## As we see from the histogram of the hours on 31 day breaks 
#1- the most popular days or our rush days are 27 then 21 
#2- we can see some seasonality in this histogram and it is maybe accounted by the weekends as the data is increasing every 5 days
#days for two days then decrease slightly and go back increase
#3- as we see strange is day 31 is the least but it makes sense as not all months have this day 

summary(binded_cities)

## We can see from the summary statistics a glimpse on the top start stations ,, en stations ,, and take a lookm

```


           X                         Start.Time                    End.Time     
     Min.   :      7   2017-02-19 12:19:00:     6   2017-03-09 17:54:00:     7  
     1st Qu.: 589315   2017-02-20 11:35:00:     6   2017-03-28 18:11:00:     7  
     Median :1184899   2017-02-24 17:46:00:     6   2017-01-13 17:48:00:     6  
     Mean   :1781608   2017-03-01 08:20:00:     6   2017-01-31 08:49:00:     6  
     3rd Qu.:2085919   2017-03-02 08:39:00:     6   2017-02-13 18:09:00:     6  
     Max.   :6816152   2017-03-09 17:31:00:     6   2017-02-20 11:38:00:     6  
                       (Other)            :152413   (Other)            :152411  
     Trip.Duration                                  Start.Station   
     Min.   :     60   Columbus Circle / Union Station     :  1700  
     1st Qu.:    392   Lincoln Memorial                    :  1546  
     Median :    667   Jefferson Dr & 14th St SW           :  1488  
     Mean   :   1098   Massachusetts Ave & Dupont Circle NW:  1219  
     3rd Qu.:   1159   Jefferson Memorial                  :  1068  
     Max.   :1088634   15th & P St NW                      :  1040  
                       (Other)                             :144388  
                                                    End.Station    
     Columbus Circle / Union Station                      :  1767  
     Jefferson Dr & 14th St SW                            :  1603  
     Lincoln Memorial                                     :  1514  
     Massachusetts Ave & Dupont Circle NW                 :  1344  
     Smithsonian-National Mall / Jefferson Dr & 12th St SW:  1103  
     15th & P St NW                                       :  1077  
     (Other)                                              :144041  
          User.Type          month            day            time          
               :   119   Min.   :1.000   Min.   : 1.00   Length:152449     
     Customer  : 30754   1st Qu.:3.000   1st Qu.: 9.00   Class :character  
     Subscriber:121576   Median :4.000   Median :16.00   Mode  :character  
                         Mean   :4.016   Mean   :16.02                     
                         3rd Qu.:5.000   3rd Qu.:24.00                     
                         Max.   :6.000   Max.   :31.00                     
                                                                           
          hour      
     Min.   : 0.00  
     1st Qu.: 7.00  
     Median :11.00  
     Mean   :11.17  
     3rd Qu.:16.00  
     Max.   :23.00  
                    



![png](output_13_1.png)


## I investigated the rush day and hour ,, these were harder than investigating the months as extraction of days and hours is a bit challenging .. 

## It is also a good value to know our rush hour and rush days to help the marketing process 

## Our final Results that Rush hours are 7, 8 am (maybe because people are going to their jobs and schools) and also the 17 and 18 (5pm , 6 pm) are significant maybe because people are using the bikes to go back from jobs

## The days have no significant increase in one day but it has kind of seasonality every 5 days maybe accounted by the weekends. 

### Question 2

## Investigating the trip duration for users in different cities


```R
## Addding a summary for trip duration for each city
print("Total Trip duration for chicago city ")
sum(chi$Trip.Duration)


print("Summary Trip duration for Chicago city: ")

summary(chi$Trip.Duration)


print("Average Trip duration for Chicago city: ")

mean(chi$Trip.Duration)

##I tried to sum the trip duration for both ny and DC but it seems the data has nas so it returns na so ia m gonna remove it

ny <- na.omit(ny)
print("Total Trip duration for new york city ")
sum(ny$Trip.Duration)

print("Summary Trip duration for NY city: ")

summary(ny$Trip.Duration)

print("Average Trip duration for New York city: ")

mean(ny$Trip.Duration)

#--------------------------------------

wash <- na.omit(wash)


print("Total Trip duration for Washington city ")
sum(wash$Trip.Duration)



print("Summary Trip duration for Washington city: ")

summary(wash$Trip.Duration)


print("Average Trip duration for Washington city: ")

mean(wash$Trip.Duration)

```

    [1] "Total Trip duration for chicago city "



8087801


    [1] "Summary Trip duration for Chicago city: "



       Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
       60.0   394.2   670.0   937.2  1119.0 85408.0 


    [1] "Average Trip duration for Chicago city: "



937.172769409038


    [1] "Total Trip duration for new york city "



39410864


    [1] "Summary Trip duration for NY city: "



         Min.   1st Qu.    Median      Mean   3rd Qu.      Max. 
         61.0     353.0     573.0     795.3     955.0 1088634.0 


    [1] "Average Trip duration for New York city: "



795.343558282209


    [1] "Total Trip duration for Washington city "



109883544.668


    [1] "Summary Trip duration for Washington city: "



        Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
        60.3    410.9    707.0   1234.0   1233.2 904591.4 


    [1] "Average Trip duration for Washington city: "



1233.95333709152



```R
    
chi_user_info  <- filter(chi, User.Type == "Customer" || User.Type == "Subscriber")



ggplot(aes(x= Trip.Duration) , data = chi_user_info )+
 geom_histogram(color= 'white' , fill = '#A02330' ,binwidth =100)+
scale_x_continuous(limits = c(0, 5000),breaks= seq(0,5000,500))+
  scale_y_continuous(breaks= seq(0,6500,500) )+
facet_wrap(~User.Type)+
ggtitle("A Histogram of the count of Trip Duration per User Type in Chicago")+
xlab("Trip Duration")+
ylab("Number of bike rentals for this duration")



plot(Trip.Duration, binded_cities,1:31,seq(0,6000,500),
     "A Histogram of the count of rentals in the different days","Day","Number of bike rentals")

 
summary(chi_user_info$User.Type)
```

    Warning message:
    “Removed 93 rows containing non-finite values (stat_bin).”Warning message:
    “Removed 6 rows containing missing values (geom_bar).”


<dl class=dl-horizontal>
	<dt>1</dt>
		<dd>1</dd>
	<dt>Customer</dt>
		<dd>1746</dd>
	<dt>Subscriber</dt>
		<dd>6883</dd>
</dl>




![png](output_17_2.png)



```R
    
wash_user_info  <- filter(wash, User.Type == "Customer" || User.Type == "Subscriber")

ggplot(aes(x= Trip.Duration) , data = wash_user_info )+
 geom_histogram(color= 'white' , fill = '#E09930' ,binwidth =100)+
scale_x_continuous(limits = c(0, 7000),breaks= seq(0,7000,500))+
  scale_y_continuous(breaks= seq(0,6500,500) )+
facet_wrap(~User.Type) +
ggtitle("A Histogram of the count of Trip Duration per User Type in Washington")+
xlab("Trip Duration")+
ylab("Number of bike rentals for this duration")

summary(wash_user_info$User.Type)




```

    Warning message:
    “Removed 1431 rows containing non-finite values (stat_bin).”Warning message:
    “Removed 4 rows containing missing values (geom_bar).”


<dl class=dl-horizontal>
	<dt>1</dt>
		<dd>0</dd>
	<dt>Customer</dt>
		<dd>23450</dd>
	<dt>Subscriber</dt>
		<dd>65600</dd>
</dl>




![png](output_18_2.png)



```R
ny_user_info  <- filter(ny, User.Type == "Customer" || User.Type == "Subscriber")


ggplot(aes(x= Trip.Duration) , data = ny_user_info )+
 geom_histogram(color= 'white' , fill = '#C02399' ,binwidth =100)+
scale_x_continuous(limits = c(0, 4000),breaks= seq(0,4000,500))+
  scale_y_continuous(breaks= seq(0,6500,500) )+
facet_wrap(~User.Type)+
ggtitle("A Histogram of the count of Trip Duration per User Type in NYC")+
xlab("Trip Duration")+
ylab("Number of bike rentals for this duration")

summary(ny_user_info$User.Type)

```

    Warning message:
    “Removed 142 rows containing non-finite values (stat_bin).”Warning message:
    “Removed 6 rows containing missing values (geom_bar).”


<dl class=dl-horizontal>
	<dt>1</dt>
		<dd>118</dd>
	<dt>Customer</dt>
		<dd>825</dd>
	<dt>Subscriber</dt>
		<dd>48609</dd>
</dl>




![png](output_19_2.png)


## Summary of question two:

### Total Trip duration in Wahington was the highest reached 109883544.668 seconds 
### or total of 30523.2 hours,, the second highest was new york of 39410864 or 10947.46 hours. The last was chicago. 

### All the trip duration was rightly skewed which means that few users made very high trip duration and that accounts for higher ##mean than median. 

### In general in the three cities the subscribers made up the bike rentals as the count of their trips is a way more than the ##customers.
### In New York The Trips made by the customers was only 825 compared with 48609 made by the subscribers which means they have  ##no potential effect on the data. 
   




## Question 3: Analysis of the gender in  NYC and Chicago

### What are the earliest, most recent, most common year of birth (only available for NYC and Chicago)?


```R
print("number of males and females in nyc")
table(ny$Gender)
#______________________
print("number of males and females in Chicago")
table(chi$Gender)


```

    [1] "number of males and females in nyc"



    
           Female   Male 
      5410  12159  37201 


    [1] "number of males and females in Chicago"



    
           Female   Male 
      1748   1723   5159 



       Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
       1899    1975    1984    1981    1989    2002    1747 



```R

ny_gender  <- na.omit(ny)
summary(ny_gender$Birth.Year)



ggplot(aes(x= Birth.Year) , data = ny_gender )+
 geom_bar(color= 'white' , fill = '#A02330' )+
ggtitle("A Histogram of the count of users in each age group in NYC")+
xlab("Age")+
ylab("Number of users")

##it seems like the bulk of the data is between 1930 and 2005

```


       Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
       1885    1970    1981    1978    1988    2001 



![png](output_23_1.png)



```R


ggplot(aes(x= Birth.Year) , data = ny_gender )+
 geom_bar(color= 'white' , fill = '#A02330' )+
scale_x_continuous(limits = c(1930, 2005),breaks= seq(1930,2005,5))+
ggtitle("A Histogram of the count of users in each age group in NYC")+
xlab("Age")+
ylab("Number of users")


```

    Warning message:
    “Removed 23 rows containing non-finite values (stat_count).”Warning message:
    “Removed 1 rows containing missing values (geom_bar).”


![png](output_24_1.png)



```R

chi_gender  <- na.omit(chi)
summary(chi_gender$Birth.Year)


ggplot(aes(x= Birth.Year) , data = chi_gender )+
 geom_bar(color= 'white' , fill = '#F66412' )+
ggtitle("A Histogram of the count of users in each age group in Chicago")+
xlab("Age")+
ylab("Number of users")


##it seems like the bulk of the data is between 1930 and 2005

```


       Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
       1899    1975    1984    1981    1989    2002 



![png](output_25_1.png)



```R


ggplot(aes(x= Birth.Year) , data = chi_gender )+
 geom_bar(color= 'white' , fill = '#F66412' )+
scale_x_continuous(limits = c(1935, 2005),breaks= seq(1935,2005,5))+
ggtitle("A Histogram of the count of users in each age group in Chicago")+
xlab("Age")+
ylab("Number of users")


```

    Warning message:
    “Removed 7 rows containing non-finite values (stat_count).”


![png](output_26_1.png)



```R
getmode <- function(v) {
   uniqv <- unique(v)
   uniqv[which.max(tabulate(match(v, uniqv)))]
}
getmode(chi_gender$Birth.Year)

getmode(ny_gender$Birth.Year)
```


1989



1989


## Summarizing Question 3 for gender analysis 

### As we see number of males in the two cities is around 3 times no of females

### The earliest year in NYC was 1885 which is outlier and can be ignored as it means that 135 years old person is renting a bike ,, the most recent was 2001 (19 years old) 

### The earliest year in chicago was 1889 which is outlier and can be ignored as it means that 131 years old person is renting a bike ,, the most recent was 2002 (18 years old) 

### The most common year in the two cities found by the mode was 1989 (31 years) 

### The data is negatively skewed which means many young users are renting the bikes more than older ones and that accounts why the mean is less than the median in both cities


## Finishing Up

> Congratulations!  You have reached the end of the Explore Bikeshare Data Project. You should be very proud of all you have accomplished!

> **Tip**: Once you are satisfied with your work here, check over your report to make sure that it is satisfies all the areas of the [rubric](https://review.udacity.com/#!/rubrics/2508/view). 


## Directions to Submit

> Before you submit your project, you need to create a .html or .pdf version of this notebook in the workspace here. To do that, run the code cell below. If it worked correctly, you should get a return code of 0, and you should see the generated .html file in the workspace directory (click on the orange Jupyter icon in the upper left).

> Alternatively, you can download this report as .html via the **File** > **Download as** submenu, and then manually upload it into the workspace directory by clicking on the orange Jupyter icon in the upper left, then using the Upload button.

> Once you've done this, you can submit your project by clicking on the "Submit Project" button in the lower right here. This will create and submit a zip file with this .ipynb doc and the .html or .pdf version you created. Congratulations!


```R
system('python -m nbconvert Explore_bikeshare_data.ipynb')
```
