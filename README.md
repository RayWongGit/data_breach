#README
## Citation
- Raw dataset downloaded from [PRC Data breaches](https://www.privacyrights.org/data-breaches) on 2019/04/24.   
- All types of breaches selected.   
- All types of institutions selected. 
- Data breaches from year 2006 to 2018 selected. Year 2005 is excluded in consideration that the first year the data collection just started may be not reliable enough. Year 2019 is also excluded since we downloaded dataset in April, only a few months' data won't provide us many insights of the year.


## Data Cleaning
- Excluded data of year 2018 in consideration of reliability caused by time taken to update the data on the website etc.
- Filled in missing values of State. Some of these values could be inferred from the City name, some the company name, while some others could be inferred from the [latitude and longitude in Google map](https://www.google.com/maps/place/42%C2%B013'49.9%22N+83%C2%B044'47.9%22W/@41.9773424,-88.1267188,5z/data=!4m5!3m4!1s0x0:0x0!8m2!3d42.230537!4d-83.74664)
- Dropped 13 duplicated records
- Changed the data type of column 'Total Records' from float to int
- Drop the 'City' column 
- There are some missing values in 'Description of incident', 'Information Source' and 'Source URL'. We are not likely to get reliable insights from these columns. However, we will keep them since we may refer to some of the values at some point.
- excluded Puerto Rico from US states



## Feature Engineering

- created a breach type group called 'breach_type\_gA' to re-categorize the type of breaches by 'Hacker', 'Others', 'Unknown', 'Hacker' correspond to the type of breach 'HACK’. 'Unknown' correspond to the type of breach'Unknown'. And 'Others' include all the rest types 
- created breach type group called 'breach\_type_gB' to re-categorize the type of breaches by 'Outsider', 'Insider', 'Mixed', and 'Unknown'.
'Outsider' includes the original type of breach 'HACK' and 'CARD'.
'Insider' includes the original type of breach 'INSD' and 'DISC'.
'Unknown' right as the original type of breach 'Unknown' .
'Mixed' includes all the rest types of breach for which we could not say for sure whether it is outsider or insider since it depends on each specific event
- created a breach type group called 'breach_type\_gC'
re-categorize the types of breaches by 'Online', 'Offline', 'Others', 'Unknown'. 'Online' includes the type of breach 'CARD’ and'HACK'. 'Offline' includes the type of breach 'PHYS' and 'PORT'. 
'Unknown' correspond to the type of breach'UNKN'. And 'Others' include all the rest types which cover both online and offline
- breach type group D
	- values 'Network', 'Local',  'Others'
	- 'Network' includes the type of breach 'CARD’ and'HACK'
		- **CARD:** Fraud Involving Debit and Credit Cards Not Via Hacking (skimming devices at point-of-service terminals, etc.)
		- **HACK:** Hacked by an Outside Party or Infected by Malware
		- **DISC:** Unintended Disclosure Not Involving Hacking, Intentional Breach or Physical Loss (sensitive information posted publicly, mishandled or sent to the wrong party via publishing online, sending in an email, sending in a mailing or sending via fax) 

	- 'Local' includes the type of breach 'PHYS', 'PORT' and 'STAT'. 
		- **PHYS:** Physical (paper documents that are lost, discarded or stolen)
		- **PORT:** Portable Device (lost, discarded or stolen laptop, PDA, smartphone, memory stick, CDs, hard drive, data tape, etc.)
		- **STAT:** Stationary Computer Loss (lost, inappropriately accessed, discarded or stolen computer or server not designed for mobility)
	- And 'Others' include 'INSD' and 'UNKN'
		- **INSD:** Insider (employee, contractor or customer)
		- **Unknown:** correspond to the type of breach'UNKN'.
- created a new variable 'region\_A' from ‘State’, including ‘Northeast’, ‘Midwest’,‘South’, ‘West’ regions in US and 'Non_US' for locations in other countries rather than US.
- created a new variable 'region\_B' from 'State',with the values‘Northeast’, ‘Midwest’, ‘Southeast’, 'Southwest', ‘West’ for US and 'Non_US' for locations in other countries rather than US. 
- created a new variable called 'Age of Law' to describe how many years a state have had a law regarding the data breach 
- created a new variable 'Org\_group', assign value 'F_regulated' to 'Org\_group' where the type of organization is 'EDU'/'MED'/'BSF', for other types of organization assign value 'non\_regulated'
- created a new variable 'records_group' to identify whether the record is an extraordinary incident or not.
- created a new variable named records_index to measure the severity of data breach incidents. This is calculated by dividing the total records by the respective average of total records for extraordinary incidents and  not extraordinary ones. 
- severity index1: record scale + breach type scale
	- record scale between 0 - 10, computed from applying sigmoid function to total records
	- type scale, 1- 10, measuring the cost of breach by type, refer to IBM report page 37.
- severity index2: record scale + orgnization type scale
	- record scale between 0 - 10, computed from applying sigmoid function to total records
	- organization type scale, 1- 10, measuring the cost of breach by organization (industry), refer to IBM report page 25.
- severity index3: record scale + breach type scale + orgnization type scale
- severity index4: average of (record scale + breach type scale + orgnization type scale)
- severity index5: variance of (record scale + breach type scale + orgnization type scale)


## Exploratory data analysis
- got a subset with 'subset' function in r, only include the records in US. Exclude the 3 extraordinary events
- checked the dimention of the dataset with 'dim' function in r
- checked the columns names with 'names' function in r
- checked the dataset structure with the 'str' function in r
- checked the description of the dataset with 'summary' function in r 
- made a bar chart for type of breach. Most of the data breach events are of type 'PHYS', 'DISC' and 'HACK'.
- made a bar chart for type of organization. 'Med' organization has much more data breach events than others. 
- made a histogram to check the distribution of total records with ggplot2 library in r. We found that the 'Total Records' is heavily right skewed. We applied a log transformation and then check distribution of total records in US and CA. They both look like normal distribution. 
- made a frequency polygon and a density plot to explore the trend of data breach events by year with ggplot2 library in r. We found that although there are two drops in year 2006 and 2014, the overall trend is increasing. 
- created bar plot for breach type group A. Most of the events are in the 'Others' category (more than 70%). 
- created bar plot for breach type group B. 'Mixed' has more events than the other categories (about 43%). 
- create bar chart for region group B. Southwest, which includes 'Texas', 'Oklahoma', 'New Mexico', 'Arizona', has relatively less data breach events than other regions. 
- created bar chart for org_group. Almost 80% of the events are in federal regulated category. 
- create histogram for records_index. The distribution is heavily right skewed. After a log transformation, it looks like normal distribution with a mean at 0.001. Most incidents have an index less than 1.


## Further exploration
- we checked the mean records_index grouped by state. Only California, Michigan, Virginia, New Jersey,Georgia, Indiana, Texas have an average index bigger than 1.
- we created a boxplot to explore the distribution of records_index among different breach types. 'HACK' has some outliers with really large indexes. After log tranformation, we found that 'HACK' type has a relatively larger index while 'INSD' and 'CARD' types have a smaller index.
- we created a boxplot to explore the distribution of records_index among different breach type A groups. 'Hacker' has some outliers with really large indexes. After log transformation, we found that 'HACK' type has a relatively larger index.
- we created a boxplot to explore the distribution of records_index among different breach type B groups. 'Outsider' has some outliers with really large indexes. After log transformation, we found that 'Outsider' type has a relatively larger index.
- we created a boxplot to explore the distribution of records\_index among different organization types. 'BSO' has some outliers with really large indexes. After log tranformation, we found that 'BSO' type has a relatively larger index while 'BSR' and 'UNKN' types have a smaller index.
- we created a boxplot to explore the distribution of records\_index among different organization groups. Both the federal regulated group and the non-regulated group have some outliers with really large indexes. After log tranformation, we found that also the medians look close, the IQR (interquantile range) of 'non_regulated' is much larger than 'F\_regulated'.
- we created a scatter plot to explore the relationship between age\_of\_Law and records_index. The scatter plot shows that for wwhere data breach incidents happened, if there is a state law, the records index tends to be larger. We also conducted a correlation test. however, the correlation test shows no correlation. The correlation coefficient is 0.04 with a p-value of 0.003.

## Research objective
Identify the informative patterns of data breach 
## Research questions
- breach type over time
- organization over time
- is a certain organization type more vulnerable to a certain type of breach
- is any region more prone to data breach
- does state law on data breach have an impact

## Findings

- **type over time**: 
	- All types have some flutuations. 
	- Breach of 'HACK' and 'DISC' type are increasing over the years and have more incidents. 
	- The other types are decreasing and have less incidents.  
	- Organizations('BSO', 'BSR', 'EDU') more vulnerable to 'HACK' and 'DISC' need to pay more attention.
- **type group B over time:** Breaches caused by outsiders have been increasing during these years, while breaches caused by insiders have not been increasing too much since year 2013.
- **type group C over time:** Breaches from offline have been dramatically decresing since year 2010. Breaches from online have been increasing.
- **type group D over time:** Breaches from network have been increasing while breaches from local have been decreasing.

- **organization over time**: 
	- Breach in medical is dramatically increasing especially after year 2009. 
	- Breach in 'BSO' reached its peak in year 2011, it has been decreasing through year 2015 but starts to rebound after that. 
	- Medical and 'BSO' need to pay relatively more attention to data breach. 
- **organization group over time:** Although there are some fluctuations. For the overall trend, breaches in federal regulated organizations are increasing, breaches in non federal regulated organizations are decreasing.
- **region**:
	- Southwest, which includes 'Texas', 'Oklahoma', 'New Mexico', 'Arizona', has relatively less data breach events than other regions. 

- **state law**:
	- there is no correlation between ages of a state law and total records, correltion coefficient 0.02, p-value 0.087
- **severity index 1:** This distribution of severity index 1 is a little left skewed. The peak appears at 17.
- **severity index 2:** This distribution of severity index 2 is a heavily left skewed. The peak appears at 20.
- **org vs. type**: 
	- 'BSF' is more prone to 'HACK' and 'PORT'. 
	- 'BSO' and 'BSR' are more prone to 'HACK'. 
	- 'EDU' is more prone to 'DISC' and 'HACK'. 
	- 'GOV' is more prone to 'PORT'. 
	- 'MED' is more prone to 'PHYS'. 
	- 'NGO' is more prone to 'PORT' and 'HACK'.
- **organization vs. type vs. severity_index1:** 
	- BSF need to pay more attention to PORT and HACK
	- BSO/BSR/EDU need to pay more attention to HACK
	- GOV need to pay more attention to PORT and DISC
	- MED need to pay more attention to PHYS and HACK
	- NGO need to pay more attention to PORT and HACK 
- **organization vs. type vs. severity_index2:** 
	- BSF need to pay more attention to PORT and HACK
	- BSO/BSR need to pay more attention to HACK
	- EDU need to pay more attention to HACK and DISC (diff)
	- GOV need to pay more attention to PORT and DISC
	- MED need to pay more attention to PHYS (diff)
	- NGO need to pay more attention to PORT and HACK


## Reference
- [PRC Data breaches](https://www.privacyrights.org/data-breaches)
- [A longitudinal analysis of data breaches](https://www.emerald.com/insight/search?q=A+longitudinal+analysis+of+data+breaches)
- Lessons from data breach reporting in the U.S: A Longitudinal, Categorical and Geographical Analysis (Sun's master thesis)
- [Verizon 2020 data breach investigations report](https://enterprise.verizon.com/resources/reports/dbir/)
- [Hurricane Severity Index](https://www.stormgeo.com/weather/articles/how-to-estimate-hurricanes-damage-potential/)
- [The Severity of an Earthquake](https://pubs.usgs.gov/gip/earthq4/severitygip.html)
- [stock market crash](https://www.investopedia.com/a-history-of-bear-markets-4582652)
- [data breach severity assessment](https://www.idx.us/knowledge-center/assessing-data-breach-severity-third-party-incident-expose-credit-card-data)
- [107 Must-Know Data Breach Statistics for 2020](https://www.varonis.com/blog/data-breach-statistics/)
- [IBM Security - Cost of a Data Breach Report](https://www.ibm.com/security/digital-assets/cost-data-breach-report/#/)