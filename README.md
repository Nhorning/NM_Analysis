# Capstone Project 1 - Milestone

## Client
The Nepal Monitor Project (NMP) comprehensively maps human rights and violence data across Nepal using the vast majority of national news sources. The embedded Peace Monitoring Project sends local sources in from 28 districts to have all incidents of violence systematically mapped, with the goal of improving understanding of violence in Nepal to better respond to it and promote peace.  Analysis from the project is distributed to its subscribers, including local human rights defenders, national monitoring bodies, and the international community on a [regular basis](https://www.nepalmonitor.org/blog/).  


## Problem
Analysis till date has focused on describing what is happening and various trends in violence, rather than combining with other data to find correlations, that could perhaps be used to make predictions or policy recommendations. While the project is tracking and coding for hundreds of different variables in types of violence and contestation, predictive analysis based on the data has proven elusive. 

In order to be an effective "Early Warning System" Nepal Monitor should answer two main questions.
1. What violence is likely to happen where, and when?

2. What variables predict that.

NMP has committed to using parts of this analysis in one or more of their publications (it has already utilized one of the resulting graphics in their [2017 annual review](https://www.nepalmonitor.org/blog/2018/07/27/the-nepal-peace-monitor-annual-review-2017/)), and to distributing some of the broader implications as a post on their analysis blog. The project has no shortage of GIS/Mapping capacity, so map based visualizations will not be utilised.


## Datasets
<details>

### Nepal Monitor export
The Nepal Monitor Data set is open data, and freely available on the [reports page](https://www.nepalmonitor.org/reports/) of its website [NM_2016_17export.csv](/Data/NM_2016_17export.csv).  It is geocoded by gps and local level boundaries.  The source of each incident is tracked, and violent incidents are further categorised by heigherachical category codes according to [criteria](https://www.nepalmonitor.org/blog/wp-content/uploads/2017/04/NEPAL-CODING-GUIDELINES_FINAL_MAR2017.pdf) developed together with The Asia Foundation. The broad categories for these variables are Location, Event Forms, Weapons, Causes, Cross Cutting Issues, Actors, and Impacts. 

### No Duplicate File
While the full dataset dates back to 2012, the Peace Monitoring Project only started applying this methodology in mid 2016. NMP produced a dataset for their 2017 annual review in which duplicate incidents (multiple reports referring to the same incident in the field) had been meticulously removed through a manual process. [2017 PMP Data Final No Duplicates.xlsx](/Data/2017%20PMP%20Data%20Final%20No%20Duplicates.xlsx) is similar to the export, but in excel format with the duplicates removed. 

### Ward level population
The “ward” is the lowest level administrative boundary in Nepal of which there were 36020 until the implementation of federalism. [Nepal_population_household_ward-2011.csv](/Data/Nepal_population_household_ward-2011.csv) is a merge of the [place codes](https://www.humanitarianresponse.info/sites/www.humanitarianresponse.info/files/documents/files/FieldGuideForTheUseOfGeo-Codes.pdf) of these boundaries with 2011 census data from the Nepal Central Bureau of statistics, and altitude data in order to prioritize winterization material distribution as part of the Nepal 2015 earthquake response  hosted on [HDX](https://data.humdata.org/).  The place codes are unique ids for a  hierarchical list of boundaries used  to eliminate errors generated by duplicate and misspelled names in joins and merges between datasets.  Boundary hierarchy:

 Level | Boundary type | Number of units |
|-----|---------|---------|
| 1 | Development Region | 5 |
| 2 | Zone | 14 |
| 3 | District | 75 |
| 4 | Village Development Committee (VDC) | ~7094 |
| 5 | Ward | 36020 |


### Ward Boundary Shapefile
‘[npl_polbanda_adm5_wad_25k_50k_sdn_wgs84.csv](/Data/npl_polbanda_adm5_wad_25k_50k_sdn_wgs84.csv)‘ is ward level boundary data exported from an official UN boundary [shapefile](https://en.wikipedia.org/wiki/Shapefile) hosted on HDX  using QGIS to calculate the area of the shapes. 

### Federal Relational File
In 2017 Nepal created new federal structures, agreed upon in it’s peace process. A relational file [Federal_VDC_Relation_web_clean.xlsx](/Data/Federal_VDC_Relation_web_clean.xlsx) has been provided by the UNRCO correlating the new structures with the old ones. VDC level boundaries were divided, but ward level were not significantly. Agreements on boundaries are controversial and have been subject to change over the last 2 years. Official data is still scarce. 
Boundary hierarchy:

| Level | Boundary type | Number of units |
|-----|---------|---------|
| 1 | Province | 7 |
| 2 | District | 77 |
| 3 | Gaupalika | ~767 |
| 4 | Ward | ? |



### Potential Data Sets

Datasets that could add additional variables are available on [Humanitarian Data Exchange](https://data.humdata.org/search?q=nepal), the [code4Nepal github repo](https://data.humdata.org/search?q=nepal), and [Open Nepal](http://opennepal.net/).
Notable examples include:
* [Complete sensus data](https://github.com/Code4Nepal/census-data) including drinking water, school attendance, disability, education level, literacy status, and toilet type, down to VDC level.
* [Nepal Health Sites](https://data.humdata.org/dataset/nepal-healthsites)
* [Nepal District Headquarters](https://data.humdata.org/dataset/nepal-district-headquarters)
* [Nepal Population Statistics](https://data.humdata.org/dataset/nepal-population-statistics-census-2011)
* [Nepal Earthquake Severity Index](https://data.humdata.org/dataset/nepal-earthquake-severity-index)
* [Literate population](https://github.com/Code4Nepal/data/blob/master/datasets/unesco/literacy-rates.csv)
* [Local Boundaries (unofficial)](http://localboundries.oknp.org/)

</details>

## Data cleaning
<details>
  
After an attempt to engineer an automatic process to match manual duplicate removal so it could be applied to the partial 2016 data,  I opted to simply use incidents from the existing 2017 “No Duplicates”  dataset . Because this was an excel file there were some formatting errors importing Nepali script, so I used the index of the no duplicates file to filter the csv export from Nepalmonitor.org.

### Additional steps: 
* Manually removed an errant comma from corrupt csv export   
* Normalized timestamps so that only dates remained
* Removed unnecessary columns 
* Converted yes and no values to 1’s and zeros
* Investigated implications of flattening unique values into dummy columns, but decided against implementation during cleaning steps
* For federal unit relational file, used a function to break out untidy lists of which old wards applied to which new units into separate column.
* Saved resulting files as csv.

see [Datacleaning_steps.txt](/Datacleaning_steps.txt) and [Data Cleaning](/Data/Data%20Cleaning.ipynb) for more details
</details>

## Data Wrangling
<details>

[SpringBoard 7.2 - Apply Data Storytelling.ipynb](/SpringBoard%207.2%20-%20Apply%20Data%20Storytelling.ipynb)  
* Separated out impacts of 2017 incident data for analysis
* Created dataframe of impacts grouped by date
* Created dataframe of impacts grouped by district joined with province number and district population data.

[EDA.ipynb](/EDA.ipynb)
* After analysis of above, created a dataframe of incidents grouped by VDC and joined with VDC level census data
* Joined the above with ‘AREA’ Data exported from the shapefile export and divided population by area to derive a density column
* Created dummy columns for nearly variables tracked by the NMP project.  This made the dataset 375 columns wide. 
* Created a separate dataframe of source type dummy columns for 35 different types of media sources. 

</details>

## Exploritory Data Analysis
Initial analysis focused on identifying obvious correlations and trends with more simple questions and progressively moved on toward the more advanced questions in the business problem.


### Question 1: Are the impacts of violence in the data correlated with specific time periods?
<details>
  
In 2017, Nepal experienced 3 rounds of elections for new local bodies (created by combining different wards together), and 1 combined round of elections for the House of Representatives and officials at the provincial level. The decision making process for the creation of all these units was highly contested, and resulted in multiple rounds of strikes and protests, beginning with the registration process. These time periods can be highlighted while plotting impacts in a time series to find if there are any notable correlations
![2017 Impacts of Violence by Week](/images/time_series.png)

It is immediately apparent that, while there is no obvious correlation between election rounds and the total number of people killed or raped per week, there is a striking relationship between the total number of injured and the timing of the elections. Interestingly, the spikes in the total number of injured are not reflected in the number of 'Female Injured.' This can either be attributed to an exclusively male participation in election related contestation, or an inability to differentiate gender among injured in media reports. Reports of physical damage to buildings and vehicles similarly match the timing of election rounds
</details>

### Question 2: how are impacts of violence correlated with population and each other?
<details>
  
![linear regression](/images/replot_all.png)

Simply dividing the sum of the Total Killed by the sum of the population per district (divided by 100,000) yields a homicide rate of about 1.68 per 100,000 people.  A linear regression, produces a slope of 1.59 Total Killed per 100,000 people + 0.33

![joint plot - Killed](/images/joint_plot_killed.png)
![joint plot - Injured](/images/joint_plot_injured.png)

From the joint plots above, there is an obvious relationship between district population and various impact numbers. For the Total Killed by population chart, for instance, a "pearsonr = 0.86" shows there is a essentially an 86% linear correlation between Population and 'Total Killed.' In the same graph, "p=1.1e-22" means there is a vanishingly small (1.1 * 10 to the negative 22nd) chance that the null hypothesis (that the relationship is a result of random chance) is correct.

![correlation of impacts](/images/correlation_impacts.png)

A correlation heat map of all impacts shows a slight correlation between nearly all impacts, save for severe building damage. This could be a function of all these impacts being correlated with population. The highest overall correlation with population appears to be "Total Killed."

![correlation of impacts normalized by population](/images/correlation_impacts_normalized.png)


Controlling for population by using impact numbers re-mapped per 100,000 population reveals a few interesting hot-spots. Some correlations between impacts are obvious, as they reflect overlapping events. For example, "Female Killed" is a subset of "Total Killed" so both should be correlated with each other. The same applies for "Total injured" v. "Female Injured," "Total no. Rape or Attempted Rape" v. female and youth rape, etc. Total killed numbers still show slight correlations with most other impacts, even when controlling for population, including injuries, rapes, abductions, and building damage. This may be a result of multiple impacts resulting from the same major events, but perhaps also shows there are some commonalities beyond population numbers which might predict where violence takes place. Incorporating other demographic information could be informative.

Other interesting correlations in the population normalized heat map:
* Total/Female Injured and Total Buildings damaged (pearsonr = 0.51).
* Total Buildings and Total Vehicles (pearsonr = 0.47)
* Population and abductions (pearsonr = 0.17 - 0.28 despite normalization)
* Negative correlation between population and injuries per capita (pearsonr = -0.22)
* Higher correlation between Total Injured and Youth - below 25 - Killed (0.66) than Female Killed and Total Killed (0.45)

![Histogram - Killed](/images/hist_killed.png)
![Histogram - Injured](/images/hist_injured.png)

The histograms in the left show the raw distribution of 'Total Killed' and 'Total Injured, with their similar skew to the right, indicating more districts have lower impact numbers and fewer districts have higher impact numbers.

The histograms on the right show the same impacts per 100,000 population. While dividing the 'Total Killed' by population creates a more normal (bell shaped) distribution, doing the same to 'Total Injured,' barely changes the shape of the distribution at all. A theoretical normal distribution has been overlaid on the Total Killed per 100,000 histogram to show the similarity. Additionally, a Shapiro-Wilk test with a p-value of 0.19 means the distribution is probably normal or - more accurately - is not rejected as a normal distribution. 
</details>

### Question 3: How does the distribution of violence differ between provinces?
<details>
  
![Pair Plot](/images/pairplot_province.png)
Shading a pair plot of Total Killed v. Population per district according to province, produces a histogram of population per district and Total Killed by district, while combining both together in scatter plots. It is immediately apparent that both the histograms look very much like each other. The most common number of people per district is around 250,000 while the most common Total Killed is somewhere between 2.5 and 5, with the distribution rapidly falling off in both graphs as numbers rise.

Kathmandu district can be seen standing apart in all graphs with a population of slightly under 1,750,000 and approximately 26 Total Killed, colored to match province 3. Province 2 also stands out, in that it doesn't have any districts with a population below 500,000, and is thus responsible for a "bulge" in the middle of both histograms.

Of course, the most obvious way in which Province 2 stands out is with its regression line. All other provinces more or less display a regression line correlating Total Killed with Population, while Province 2, displays a prominently different trend. Why is this?

![Regression - Nepal v. Province 2](/images/regplot_prov_2.png)

By plotting Total Killed v. Population regression line for Nepal and separating out province 2, We can see the reason for Province 2 bucking the trend is two fold: Sarlahi has far fewer violent deaths per-capita than the linear regression line for all provinces(1.59 +0.33 deaths per 100,000 people), while Parsa, Saptari, Siraha, and Rautahat have considerably more. These are different enough from each other to result in a regression line of (-2.12 +28.3 deaths per 100,000 people) for province 2.

![Distribution of violent deaths](/images/dist_violent_deaths.png)

The the box plot above visualizes the distribution of violent deaths per 100,000 people over Nepal's federal provinces. Each dot in the overlaid swarm plot represents a district, with it's vertical position indicating the 'Total Killed' per 100,000 pop. in that district. Each province is plotted separately, by the province number, with the leading and trailing districts for each labeled.

The outlier status of Sarlahi can be seen in province 2, with ~1.1 killed per 100,000 people, while the rest of the Province is bunched up between 2 and 3 killed per 100,000, with a median of ~2.1 per 100,000. Note that despite the wide distribution between 0 and 4 Killed per 100,000, the boxes are mostly centered along the 1.59 deaths per 100,000 line from the previous regression plot, with the medians between 1 and 2.1 deaths per 100,000 people.
</details>

### Question 4: Is bias in media coverage measurable?
<details>

![Top and bottom 10 correlation with INSEC](/images/INSEC_corr.png)

The figure above displays the top and 10 correlations with INSEC on a per-incident basis. The most extreme negative correlation between sources is between INSEC and National/Online Media.  This might suggest an underreporting of the types of incidents reported by INSEC (Notably Sexual assault and one sided violence) in the national media. 
</details>

### Question 5: How do types of violence correlate with census demographics?
<details>

![Top and bottom 10 correlation with POPULATION](/images/pop_corr.png)
![Top and bottom 10 correlation with DENSITY](/images/density_coor.png)

Several of the variables least and most correlated with population are also least and most correlated with population density.  These (highlighted above) include household numbers, National/Online Media as a source, Violence targeted at criminals, and a negative correlation with high elevation.  Other variables change their ranking significantly between the two.  Dalit (untouchable) related issues, sexual assaults and suicides are in the 10 most negatively correlated with population density, where this is not the case with Population, likely indicating that such issues may be more likely to occur in rural areas. 
</details>

### Question 6: How are types of violence correlated between thematic categories?
<details>

![Pearson Correlation - every 10,000th paired variable](/images/short_corr.png)

Plotting every 10,000th pair with less than a perfect 1 to 1 pearson correlation (which should only occur when variables are paired against themselves) shows that out of the 132,076 pairs with such a relationship, Roughly 50% of those have a some positive correlation, with the other half having either no correlation or a very slight negative correlation.

![Paired Correlation Histogram](/images/hist_corr.png)

A histogram of this correlation highlights that the most common r value for paired variables is 0, and that slightly more than 1% represented a Pearson correlation greater than 0.9. However, this 1% still represents 134 pairs of variables, which is still an overwhelming amount for meaningful analysis.

</details>

## Time Series analysis
To predict what will happen one week in the future, the model is fed what has happened several weeks prior (X) to a given set of weeks (y) and trains itself to find any relationship. It is then asked to guess what will  happen in the next week (y_pred).

This is done in sequence for every week using multiple variables. 
### Baseline

![MLPRegressor Baseline](/images/MLP_baseline.png.png)

![MLPRegressor Baseline Scores](/images/MLP_baseline_scores.png)

Baseline scores are calculated by assuming that each variable will have the same value as the previous week, such as in the example variables above. Note the gridlines show the forecast lagging behind the ground truth by a week.  r² and explained variance measure the degree actual values are predicted by the forecast, with 1 being the highest possible score.   

### Improvement
![MLPRegressor Improvement](/images/MLP_improvement.png)

![MLPRegressor Scores](/images/MLP_scores.png)

In the run above, MLPRegressor (a neural net), significantly improves the accuracy in forecasts over the baseline in multiple tracked variables. Note how the forecast lags a week behind the first two peaks, but the model seems to have “learned” enough to predict the 3rd one. 


## Findings Summary:

### 1. Certain impacts of violence are correlated with elections. 
Specifically, injuries and physical damage appear correlated with electoral rounds, while other impacts have no obvious correlation with these periods.
### 2: Impacts of violence are correlated with population
'Total Killed' is the most highly correlated with population, but all other impacts are at least somewhat correlated with population, and therefore with each other to varying degrees. 'Total Killed' appears to be normally distributed by population, however other impacts of violence do not appear as uniformly distributed according to population.
### 3. There are significant differences in the distribution of violence between provinces, particularly regarding province 2.
Province 2 has the highest Total Killed among all the provinces and the highest population.  It also has the tightest distribution of Total Killed per 100,000 people.  And a negative regression line of violence per-capita.  These differences might warrant further exploration when additional demographics are added to the dataset. 
### 4. National/Online Media has the most negative correlation with reports of Human Rights violations (from INSEC).  
This suggests it is more likely for local media to report a given human rights incident than National/Media.  Further investigations could explore statistical significance, as well as which variables predict whether a given report will be published by what type of source. If more finely grained altitude data can be added to the dataset, correlation of the types of reporting with altitude could explore possible bias in hill v. flatland coverage.
### 5. Clear differences in correlations appear between types of violence and total population compared to correlations with population density. 
This indicates that there may be an urban rural divide between certain types of violence or contestation, and that other relationships may be identified when further demographic information is combined with the dataset.  
### 6. Over 100 pairs of variables correlate nearly perfectly with each other
A high correlation with population increases the likelihood that a given variable pair will also have a high correlation but is certainly not the only factor. The overwhelming numbers increase the difficulty of identifying which are the most important relationships without additional assistance, and may highlight the utiltiy of machine learning in further analysis




