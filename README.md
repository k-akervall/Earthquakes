# Earthquake Program
This program processes a JSON file of earthquake data from the USGS website amd returns three outputs
1. a table showing the average magnitude and significance for each alert category of earthquake, and number of quakes in that category
2. a scatterplot of the magnitude and significance of each earthquake in the dataset, colored by alert category
3. a grouped bar chart of the average number of felt reports submitted by alert type and tsunami possibility
    
Significant event earthquakes have a significance score greater than 600, however, all earthquakes are included in the dataset and the significance score is a field of data used in analysis.
    
Alert categories are from the alert level from the PAGER earthquake impact scale and can include values: green, yellow, orange and red. 
The PAGER earthquake impact scale provides fatality and economic loss impact estimates following significant earthquakes worldwide.

A felt report is submitted to the DYFI (Did You Feel It?) system and is data collected from people who felt an earthquake.
The Tsunami flag is not the same as a tsunami warning. Tsunami warnings are performed by NOAA. The flag simply notes events in oceanic regions where there is a possibility of tsunami.
    
The Mongo server must be running.
The results are written to a csv file or jpg.
The program uses pandas dataframes to represent the analysis tables.

**Created by Kristin Akervall, May 2022**

### Data
The dataset used for this program is found on the USGS website and is collected from the ANSS Comprehensive Earthquake Catalog (ComCat). The dataset is updated on the USGS website every minute. In the class and async activities students used the significant earthquakes from the last month dataset, however, this analysis uses the all earthquakes from the last month dataset. 

This dataset contains measurements and descriptions of earthquakes. Specific fields used for the analysis are displayed below. The number of records used in the analysis will vary depending on recent earthquake activity. At the time the program was created 8,874 earthquake records were included in the analysis. No preprocessing was done on the datafile prior to reading it into python. Notes further explaining the meaning of each field and an example value is included:

| Field | Description | Example |
|-------|-------------|---------|
| Place | A description of the geographic region (city name or other) nearest the earthquake. | 34 km SE of Denali National Park, Alaska |
| Time | The time the event started (origin time). The earthquake may continue many seconds beyond this initial time. Times are reported in the original dataset in milliseconds since the epoch. | 1653337298322
(translated to “2022-05-23 13:21:38”) |
| Mag (Magnitude) | The magnitude considered the official estimate of the earthquake’s size by the U.S. Geological Survey. | 1.60 |
| Alert | Alert categories* are from the alert level from the PAGER earthquake impact scale and can include values: green, yellow, orange and red. The PAGER earthquake impact scale provides fatality and economic loss impact estimates following significant earthquakes worldwide. | None |
| Tsunami | The Tsunami flag is not the same as a tsunami warning. Tsunami warnings are performed by NOAA. This flag simply notes events in oceanic regions where there is a possibility of tsunami. A value of 1 indicates possibility of tsunami, 0 indicates there is no possibility of tsunami. | 0 |
| Felt | A felt report is submitted to the DYFI (Did You Feel It?) system and is data collected from people who felt an earthquake. | |
| Sig (Significance) | Significant event earthquakes have a significance score greater than 600, however, all earthquakes are included in the analysis dataset regardless of significance score value and this field is used in the analysis. | 39 |

*Alert Level Categories
| Category | Estimated Fatalities | Estimated Losses (USD) |
|----------|----------------------|------------------------|
| Red | 1,000+ | $1 billion + |
| Orange | 100-999 | $100 million - $1 billion |
| Yellow | 1-99 | $1 million - $100 million |
| Green	| 0	| <$1 million |

### Research Questions
##### Question 1: What is the average magnitude and significance score by alert category, and how many earthquakes are recorded in each alert category?
This question helps summarize the data in the dataset using the Mag, Sig and Alert fields. A groupby is applied to the Alert type and the mean for both Mag and Sig is calculated along with the count of Place (to tally the number of earthquakes in each category). Column names are clarified, and the table is sorted on Count of Earthquakes. The output csv file is named Avg_Mag_By_Alert.csv.

##### Question 2: What is the relationship between magnitude and significance for earthquakes? 
This question compares the magnitude value with the significance score. The fields utilized in the analysis are Mag, Sig and the plots are colored by Alert category. In order to do the color coding, I first first needed to set a dictionary with the colors utilized in the chart. Then, when creating the plot, I referred to the Alert field which corresponded with the key in the dictionary created. I did anticipate that colors may need to be used in future charts for orange or red alert levels, that did not appear on the current chart, and coded it appropriately. The output jpg file is named Mag_And_Sig.jpg.

##### Question 3: What is the average felt reports for each alert category by tsunami flag? 
This question compares the average felt report number for each alert category and splits the data into two groups: earthquakes with tsunami potential and earthquakes without tsunami potential. This analysis uses the Tsunami, Alert, and Felt fields. Data is first put into a pivot table to split the felt averages into the alert categories and by tsunami flag. In the bar chart output, I changed the color of the bars to correspond with the alert category names. Again, I anticipated that at some point the chart may include data for alert categories not currently utilized (orange and red) and added code for these categories so they would also be correctly labeled when or if that time comes. The output jpg file is named Avg_Felt_By_Tsunami_And_Alert.jpg.

### Program Description (and data cleaning steps)
This program processes a JSON file of earthquake data from the USGS website and returns three outputs. The mongo database server must be running for this program. The program first reads in the data from the website into a json string and parses it into a python dictionary. The user is notified how many records have been read into the program.

The program uses pandas dataframes to represent the analysis tables. Columns were renamed for clarity in data tables. 

The datetime library was used to convert the time field into a more readable format.  

In reviewing the data, it became apparent some records did not have a value in the Alert field. In this case the missing value was replaced with ‘None recorded’. This category label was used in data tables and analysis. 

The matplotlib library was used to create the charts. 

Functions preformed on the dataframe result in a summary table which is written to a csv file, and two chart outputs are created as jpg files, all of which are described below.

### Output Files and Conclusion
Three files are created by this program. The first data table represents the average magnitude and significance score of earthquakes, and the count of earthquakes, by the alert category. This file is named Avg_Mag_By_Alert.csv.

| Alert	| Average Magnitude	| Significance Score | Count of Earthquakes |
|-------|-------------------|--------------------|----------------------|
| None recorded	| 1.507368959	| 59.87848877	| 8814 |
| green	| 5.523559322	| 490.5423729	| 59 |
| yellow | 5.4 | 761 | 1 |

This table lists the averages and total count of earthquake alert categories, sorted by the count of earthquakes. At the time of analysis, we can see from the table that the great majority of earthquakes are not recorded to have an alert level and have an average magnitude of slightly more than 1.5. Normally, earthquakes below a 3.0 are rarely felt, so it makes sense that these earthquakes have not been assigned an alert category and also have a low significance score.

The green and yellow level earthquakes represent a higher average magnitude and significance score. The green alert level earthquakes are expected to have less than $1 million USD of estimated losses, and yellow level earthquakes have $1-100 million USD of estimated losses or 1-99 fatalities. At the time of analysis, both of these levels of earthquakes had an average magnitude of more than 5.0, so it makes sense that they would create the associated destruction with their alert level. It is curious that the yellow level earthquake had an average magnitude that was just under the average for the green level earthquakes. However, a closer look at the chart shows this is based on just one yellow level earthquake, and it would be possible that the alert level was raised to yellow if it was in a more urban area that was more likely to result in property loss and injury.


The second file is a scatterplot of the magnitude and significance of each earthquake in the dataset, colored by alert category. This file is named Mag_And_Sig.jpg.

![image](https://user-images.githubusercontent.com/117952432/206112407-d833c7d9-c7d7-4dd1-80e8-8022c47a4752.png)

This chart graphs the relationship between the magnitude for an earthquake and the significance score. There definitely appears to be a relationship between these variables. In fact, from the data descriptions found on the USGS website, we can learn that the magnitude is used to calculate the significance score. As the magnitude raises, the significance score also increases. This relationship is expected.

This chart graphs the relationship between the magnitude for an earthquake and the significance score. There definitely appears to be a relationship between these variables. In fact, from the data descriptions found on the USGS website, we can learn that the magnitude is used to calculate the significance score. As the magnitude raises, the significance score also increases. This relationship is expected.

With the color coding by alert level, we are able to see the range of earthquakes belonging in each alert category. It appears that the majority of the green level earthquakes have a magnitude between 5 and 6.5, and a significance score between 400 and 800. However, there is one green plot with a significance score beyond 1000 and one with a score below 400. It is possible the high data point is an outlier or was assigned a higher significance score because it was in a more vulnerable area or had more felt reports associated with it. Conversely, the low datapoint may have been in a more rural area. 

The one yellow plot strays slightly below the curve with a lower magnitude for the significance score assigned. This makes me wonder why was this earthquake assigned a yellow alert level and the 1000+ significance score earthquake was not. Further analysis might be able to tease out the difference between these two data points or a larger dataset may also be helpful in bringing clarity to the categorization.

The third file is a grouped bar chart of the average number of felt reports submitted by alert type and tsunami possibility. This file is named Avg_Felt_By_Tsunami_And_Alert.jpg.

![image](https://user-images.githubusercontent.com/117952432/206112522-9e46b713-fcb0-40ed-a0ca-32b64c4d785f.png)

I was initially curious about this view of the data, because I assumed that felt reports may be more common in areas with larger populations—simply because having more people present in an area could lead to more reports being sent in. I then reflected that larger populations tend to live near coastlines. Since the presence of a coastline would also trigger a tsunami possibility (remember that the tsunami flag does not mean a tsunami warning was issued, just that it could be a possibility), I wondered if earthquakes with a tsunami flag would also have a higher number of felt reports.

This analysis, however, showed that the earthquakes without a tsunami flag had a higher average number of felt reports. This is contrary to what I would have assumed. However, a few learnings from this chart include:
1. The high number of felt reports for the one yellow alert level quake, do make me believe that this quake occurred in a populated area—thus possibly leading to a higher ranking.
2. There are a surprising number of felt reports for the quakes that were not assigned an alert level. These earthquakes are represented with the gray bar in the chart. The Avg_Mag_By_Alert data table showed that these earthquakes had an average magnitude of just 1.5. However, many earthquakes fall in this category (the first data table revealed 8,814 earthquakes were in this category.) So, this data may represent only a small percent of the overall number of earthquakes, but I am curious about the circumstances of these felt reports. 

Ultimately, the analysis preformed by this program both confirmed some of my assumptions and surprised me in other ways. Who are the very sensitive people that felt the non-alert-categorized earthquakes? Or were these just for the non-alert-categorized earthquakes that had a higher magnitude (above the average value)? Why were more felt reports not recorded for the earthquakes with the tsunami flag? Do these populations experience so many earthquakes that they don’t report them because they have been desensitized to some extent? Why was the one green plot with the 1000+ significance score not categorized as a yellow alert?

One thing that I would have liked to include in my analysis is the level of urbanization for each earthquake. While this wasn’t part of the original dataset, it would be interesting to see if there is a separate data table containing an urbanization category for longitude and longitude that could be appended on to the data for further analysis. This may have helped answer some of the questions above.

The exciting thing about this data, and looking at the outputs, is that it is constantly changing. There is a chance that the graph representations will look different the next time the program is run. For this reason, new questions may arise over time or insights learned.


