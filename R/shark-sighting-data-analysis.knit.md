## Shark Sighting Data Analysis

```r
library(ggpubr)
```

```
## Loading required package: ggplot2
```

```r
sharks <- read.csv("sharks.csv")
```


```r
species = sharks$Species.Name
date = sharks$Date
area = sharks$Area
location = sharks$Location
latitude = sharks$Latitude
longitude = sharks$Longitude
length = sharks$Length..m.
temperature = sharks$Water.Temp..C.
month = sharks$Month
day = sharks$Day.of.Week
```

## Background

   The analyzed data is retrieved from Queensland Government website (Agriculture and Fisheries 2016) and is obtained through the shark control program (SCP). Sharks are captured using nets and drumlines, which produces ethical complications regarding animal protection and welfare, and are danger to not only the targeted shark species but also other aquatic life and humans (Clif & F.J.Dudley 2011, pp.700-709). This data collection method also means data limitation resulted from selection bias, since the nets and drumlines are only placed at certain fixed locations. 
   
  It recorded shark captures within Queensland during the year 2016 along with relevant variables. The species of the shark is a nominal categorical variable, as species belong to distinct categories with no inherent order. Location is another nominal categorical variable, as there is no intrinsic way to order qualitative locations. Temperature and shark length are continuous numerical variables, as they both exist on continuous quantitative spectra with directions.The potential stakeholders include the government, researchers and tourists, who can all benefit from the data. The government could use it to monitor the progress of SCP, researchers gain insights into shark behavior and tourists avoid shark attacks. Since the data is published by the government, it is unlikely to be influenced by stakeholders.
  
   This analysis report aims to reduce the risk of shark attacks without shark culling by investigating the most frequent regions and seasons of shark appearances. 

## Research Questions
### 1. What are the most frequently captured shark species

```r
species_count = table(species)
summary(cbind(species_count))
```

```
##  species_count   
##  Min.   :  1.00  
##  1st Qu.:  1.75  
##  Median :  5.50  
##  Mean   : 22.17  
##  3rd Qu.: 22.00  
##  Max.   :207.00
```

```r
boxplot(cbind(species_count))
```

<img src="https://github.com/yhuuc/data_analysis/blob/main/R/1.png" width="672" />

```r
species_top = species_count[which(species_count>10)]
pie(species_top,main="Frequently Captured Shark Species",col=rainbow(length(species_top)),radius = 1)
```

<img src="https://github.com/yhuuc/data_analysis/blob/main/R/2.png" width="672" />
    
   The number of different species of sharks captured by SCP within Queensland during 2016 is analyzed using five number summary and visualized into a boxplot and a pie chart named "Frequently Captured Shark Species". Note not all shark species are displayed in the pie chart, only species with counts greater than 10, in order to keep the diagram clean. There are 23 species in total with two cases of unknown species. The general capture number is relatively low, with an interquartile range from 1.75 to 22, and a mean of 22.17. Tiger shark is the most frequently captured species with 207 counts, and bull whaler follows behind. These two species appear far more than the others by noticeable difference, and appear as two outliers in the boxplot. This data is useful for research on shark attacks. Their commonness partly explains why tiger shark and bull shark are considered two of the three species that are most likely to attack human (Bull Shark n.d.). This could be contributed to their frequent encounter with human compared to other shark species rather than mere aggressiveness.
  
#### What is the behaviour pattern of top two species (tiger shark & bull whaler) in relation to area and month

```r
x1=which(species=="TIGER SHARK")
x2=which(species=="BULL WHALER")
tiger=sharks[x1,]
bull=sharks[x2,]
top2=rbind(tiger,bull)
```


```r
top2area=table(top2$Species.Name,top2$Area)
barplot(top2area,axis.lty = 1,beside=T,legend = rownames(top2area),xlim=c(0,30),las=2,col=c("brown","darkblue"))
```

<img src="https://github.com/yhuuc/data_analysis/blob/main/R/3.png" width="672" />

```r
colnames(top2area)
```

```
##  [1] "Bundaberg"                           
##  [2] "Cairns"                              
##  [3] "Capricorn Coast"                     
##  [4] "Gladstone"                           
##  [5] "Gold Coast"                          
##  [6] "Mackay"                              
##  [7] "Nth Stradbroke Is."                  
##  [8] "Rainbow Beach"                       
##  [9] "Sunshine Coast North"                
## [10] "Sunshine Coast South & Bribie Island"
## [11] "Townsville"
```

```r
top2month=table(top2$Species.Name,top2$Month)
barplot(top2month,axis.lty = 1,beside=T,legend = rownames(top2month),las=2,col=c("brown","darkblue"),main="Northeast Coast Shark Sighting in 2016")
```

<img src="https://github.com/yhuuc/data_analysis/blob/main/R/4.png" width="672" />

  To further investigate the behavior of tiger shark and bull whaler to avoid shark attack, their appearance in different areas and months are drawn and compared. From the two side-by-side bar charts, it can be observed these two species do prefer certain locations and months. Tiger sharks visit Mackay and Townsville most often while bull whalers stay at Capricorn Coast. Tiger sharks also appear more frequently in May while bull whalers appear most in February. These statistic are extremely helpful for tourists and swimmers to avoid potential shark attacks. They are also valuable research material of shark behavior along Australia's northeast shore in 2016.

### 2. Is there a correlation between longitudes and latitudes of capture coordinates


```r
lo=substr(longitude,1,3)
longitude_num=as.numeric(lo)
la=substr(latitude,1,3)
latitude_num=as.numeric(la)
coor=data.frame(cbind(longitude_num,latitude_num))
ggscatter(coor,x="longitude_num",y="latitude_num",main="Correlatin between longitude and latitude of capture locations",ylab="Latitude",xlab="Longtitude",add = "reg.line",cor.coef = TRUE)
```

```
## `geom_smooth()` using formula 'y ~ x'
```

<img src="https://github.com/yhuuc/data_analysis/blob/main/R/5.png" width="672" />

  The correlation between longitudes and latitudes of capture locations is plotted in a scatter plot. The correlation coefficient and p-value are labeled. Note for simplicity only coordinate degrees are used, minutes and seconds are disregarded. The analysis found a statistically significant strong negative correlation between longitude and latitude. In other words, as the longitude increases the latitude decreases. Since Australia is in the southern hemisphere, this means as the capture location goes further north, it also goes further west. If the capture coordinates are connected on a map, they would form a slanting line, which is suspected to correspond to the Queensland coastline. This strong correlation is possibly caused by the strategic placement of fishing nets and drumlines along the coastline. It is useful in terms of locating the nets and drumlines precisely. Those interested such as researchers can use this information to observe the SCP in person, while swimmers, surfers and divers can avoid surrounding areas.   

### 3. Do tiger shark and bull whaler differ in size

   The two most common species in this dataset,tiger shark and bull whaler, are compared for their lengths that were measured when captured. The null hypothesis is their lengths have the same population mean. The alternative hypothesis is their populations have different length means. A f-test is firstly performed to determine which type of t-test will be employed. The results reveal there is a significant difference between variances. A two-tailed two-sample t-test with unequal variance is therefore conducted on the data. Results show a t-value of and p-value of. The null hypothesis is rejected. Hence, it is concluded that the tiger shark and bull whaler in Queensland coastline water have different length. 
   
   Moreover, their length are compared to the mean length of all captured species. The barplot named "Length means comparison" indicated tiger sharks are larger in size than average sharks while bull whalers are smaller. Shark size plays an important role in spotting sharks and preventing shark attacks (How, where & when sharks attack n.d.). Swimmers, surfers and divers in Queensland can presume they have encountered shark when they spot marine animals between 1.5 and 2.5 meters. Sharks are critical to marine and Great Barrier Reef ecosystems and some of them face extinction (Dun 2020). The analysis result could also be an indicator that SCP will not impose detrimental harm to shark population and ecosystem around northeast coastline, as sharks that are over three meters are the reproductively mature ones (Meeuwig 2014).
    

```r
mean(sharks$Length..m.)
```

```
## [1] 1.919981
```

```r
f_test=var.test(tiger$Length..m.,bull$Length..m.,alternative="two.sided")
f_test
```

```
## 
## 	F test to compare two variances
## 
## data:  tiger$Length..m. and bull$Length..m.
## F = 2.1565, num df = 206, denom df = 90, p-value = 5.614e-05
## alternative hypothesis: true ratio of variances is not equal to 1
## 95 percent confidence interval:
##  1.498132 3.029009
## sample estimates:
## ratio of variances 
##            2.15654
```

```r
t_test=t.test(tiger$Length..m.,bull$Length..m.,var.equal = F)
t_test
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  tiger$Length..m. and bull$Length..m.
## t = 12.181, df = 245.24, p-value < 2.2e-16
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  0.8264279 1.1452566
## sample estimates:
## mean of x mean of y 
##  2.459469  1.473626
```

```r
x=mean(sharks$Length..m.)
y=mean(tiger$Length..m.)
z=mean(bull$Length..m.)
barplot(cbind(x,y,z), names.arg = c("All shark species", "Tiger shark","Bull whaler" ),ylim=c(0,3.0),main="Length means comparison",col="lightblue1")
```

<img src="https://github.com/yhuuc/data_analysis/blob/main/R/6.png" width="672" />

## Conclusion

  This report identifies tiger shark and bull sharks as the two species that most frequently visit Australian northeast shore in 2016. Their behaviors and characteristics including preferred locations and months and lengths are analyzed and studied, in the hope to help ocean lovers avoid shark attacks and researchers gain an insight into Australia's shark ecosystem.
  
## References

Agriculture and Fisheries 2016, *Shark Control Program Shark Catch Statistics by year*, Queensland Government Open Data Portal, viewed 17   November 2022, <https://www.data.qld.gov.au/dataset/shark-control-program-shark-catch-statistics>.

*Bull Shark* n.d., Natinal Geographic, viewed 18 November 2022, <https://www.nationalgeographic.com/animals/fish/facts/bull-shark#:~:text=Human%20Encounters,most%20likely%20to%20attack%20humans.>.

Clif, G & F.J.Dudley, S 2011, *Reducing the environmental impact of shark-control programs: a case study from KwaZulu-Natal,South Africa*, Marine and Freshwater Research, vol.62, pp. 700–709.

Dun, T 2020, *Drum lines don't make us safer*, The Bullsheet, viewed 18 November 2022, <https://thebullsheet.com.au/2020/09/25/drum-lines-dont-make-us-safer/>.

*How, where & when sharks attack* n.d., Florida Museum, viewed 18 November 2022, <https://www.floridamuseum.ufl.edu/shark-attacks/odds/how-where-when/>.

Meeuwig, J 2014, *Explainer: sharks — why size and species matter*, The Conversation, viewed 18 November 2022, <https://theconversation.com/explainer-sharks-why-size-and-species-matter-22535#:~:text=Size%20really%20does%20matter,are%20likely%20reproductively%20mature%20animals.>.



