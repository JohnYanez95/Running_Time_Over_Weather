# Introduction

As detailed within the description within the host repository, this is an ongoing project to determine the optimal weather conditions for me to lower the time of a fixed course run of 4.2 miles within Austin, TX, 78747.

# Data Used

The data used for the project is a mix of unique data and weather data.

The unique data, known as the Running Times Data Set, is created by me from the runs I conduct within the residential neighborhood I reside in within the year of 2021. The unique data consists of: 
* date_time - the date_time recorded at the beginning my run,
* duration - the duration of time taken to complete my 4.2 miles fixed course run.

This data is then loaded into python by panda's read_csv function.

The weather data is scraped from an online source known as 'https://www.wunderground.com', where the weather data provided comes originally from the Austin-Bergstrom International Airport. 
The website is pinged for its html_file, by iterating over the following url:
  
    'https://www.wunderground.com/history/daily/us/tx/austin/KAUS/date/'+str(dt.year)+'-'+str(dt.month)+'-'+str(dt.day)'
where dt.year, dt.month, and dt.day are varied by the iteration over the 'Running Times' Data Set's column: date_time. Once the appropriate row is stripped from the website it is then written into a local csv file using python's open function.

The weather data consists of:
* date_time - the date_time from the 'Running Times' Data set. This is used for inner join purposes later detailed,
* poll_time - the time in which the weather data was recorded,
* temperature - the degree or intensity of heat present in a substance or object, especially as expressed according to a comparative scale and shown by a thermometer or perceived by touch,
* dew_point - the reported dew_point from the poll_time. The atmospheric temperature (varying according to pressure and humidity) below which water droplets begin to condense and dew can form,
* humidity - a quantity representing the amount of water vapor in the atmosphere or in a gas,
* wind - the perceptible natural movement of the air, especially in the form of a current of air blowing from a particular direction,
* wind_speed - or wind flow speed, is a fundamental atmospheric quantity caused by air moving from high to low pressure, usually due to changes in temperature,
* wind_gust - A gust or wind gust is a brief increase in the speed of the wind, usually less than 20 seconds,
* pressure - is the force per unit area exerted by the weight of the atmosphere,
* precip - is any product of the condensation of atmospheric water vapor that falls under gravitational pull from clouds,
* condition - the state of the environment determined by the wind velocity and direction, the air temperature and humidity, atmospheric pressure and the stability class. 

It should be noted that the approximated distance of Austin-Bergstrom International Airport to my running course is 7-8 miles WSW from Bergstrom; this approximated distance was determined by the website: 

    'https://www.freemaptools.com/measure-distance.htm'

# Scraping 

Scraping is conducted using selenium for initial access of the website's html_file, 
this was done by the recommendation of the following post: 
    
    'https://stackoverflow.com/questions/55306320/scraping-wunderground-without-api-using-python'
Once I retrieved the html_file of the current iteration of the 'Running Times' Data Set, I filter the html_file using BeautifulSoup.
Here a single row of data is taken from the website to the closest polling time listed by Austin-Bergstrom International Airport and the 'midpoint date time', 
where the 'midpoint data time' is calculated by adding half of the 'duration time' to 'date time' from the 'Running Times' Data Set. 
This is of course done for each iteration of the 'Running Times' Data Set.

# Data Base Management

Once the appropriate rows are selected from 'https://www.wunderground.com' to form our weather_data.csv, we use panda’s functionality '.to_sql(...)' to send our data to a local PostgreSQL data base.
This also includes the 'Running Times' Data Set. If a similar table is found within the database it will simply be replaced by the updated iteration

$\underline{\text{Note}}:$ When sending data sets from python to PostgreSQL make sure that the tables and columns are labeled with no spaces and within all lower-case letters. Doing otherwise will lead to easily avoidable headaches and confusion when attempting to load the data using SQL.

This functionality would be impossible to implement if it were not for sqlachemy's create_engine function.

$\underline{\text{Note}}:$ The connection to my PostgreSQL data base is not established until it my engine is pinged for use by pandas. This is the crux of my method, since connection to a data base is not known until script is used. Though, this has no issues in regards to my project.


# Inner Join of Running Times and Weather Data

To once again re-iterate, the purpose of this project is to determine the optimal weather conditions for me to lower the time of a fixed course run of 4.2 miles within Austin, TX, 78747. 
Meaning, I will need to see how the weather conditions affect my running times. 
Since, my data is stored within a PostgreSQL data base and I may manipulate them using SQL now. 
One great functionality of pandas and sqlalchemy is that I may run SQL queries within my juptyr notebook for your viewing pleasure as well as my sanity.

As a call back to the **Data Used** Section, Running Times and Weather Data share the column date_time. This is useful to pair the weather data and duration together into a larger data set.

# Regressions

This is where the project concludes, for now! 
A simple multiple linear regression was formed on the data set using the weather data as predictors to the variable duration, running time; no significant results were found yet. 
The most apparent reasons for the lack of results could be:
* the lack of data
* and the lack of variation within my data.

I wish to come back to this project once more runs are established, perhaps within 30 or 60 data points.

# Thoughts

I feel the project is well designed, but lacks a better pipeline for data entry. This became apparent when I was updating the data base with new data. 
Since, I require to ping the website 'https://www.wunderground.com' using the current 'Running Times' Data Set where I would regather information, I had already retrieved
from the previous update/initialization of table within my PostgreSQL data base.

The problem faced is to ignore the data points of the 'Running Times' Data Set where I had already retrieved corresponding 'Weather Data' to ease the load I put
on the website. A workaround is to start my iterations one after the last index of my previous tables located in my PostgreSQL data base, 
which should be smaller than my local text file's table. This is one solution, but it leads to another problem. 
I can no longer store the newly retrieved weather data into our old tables spot for it lacks the previous information, e.g., the if_exists='replace' variable from panda's functionality '.to_sql(...)'. 
I think the use of panda's append or sql's append may lead to a reasonable solution. 
For now, I will leave this as something I can come back to after some more thought and skill is obtained.

