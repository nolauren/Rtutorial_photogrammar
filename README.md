# Rtutorial_photogrammar
An introduction to R using data from Photogrammar

## R Tutorial with Photogrammar

Digital Humanities has recently become a popular topic in the humanities. Fields such as literature, history, and American studies have dedicated sections, journal issues, and entire conferences focused on the intersection of data, computing, and the humanities. The goal of this workshop is to begin to move from thinking about digital humanities in the abstract towards actually using data and computing to solve (and ask!) humanist questions. 
To this end, we are going use some statistical software to explore a dataset we have been working on that was collected online via the Library of Congress. The focus will be on the kinds of problems and solutions that data and computing can answer, rather than giving a thorough background on the underlying programming techniques. If there is interest, we would be happy to follow up with a more technical workshop. 

### 1 Installing R on your machine 
Today we are going to work with the R programming language, an open source tool for graphics, statistics, and scientific computing. It is often available on university computer clusters. You can download R for Linux, Mac, or Windows at http://cran.r-project.org/. Commands should be platform independent, though the look of R differs slightly depending on your operating system. 
Another free tool to consider using is R Studio, which provides a very nice front end to the under-stated standard R program; it can be downloaded at http://rstudio.org. There are also thousands of free packages for R, one of which we will explore at the end of this workshop. 

###2 Reading in the Dataset 
The dataset we are going to look at today consists of metadata records from the FSA-OWI photographic archive located at the Library of Congress. We are dealing with a sizeable amount of data, but only a subset of all of the variables that are available. The dataset is available in the GitHub repository. 
The file extension stands for comma separated values (csv), and can be read by Google docs, Excel/Open Office, or any standard text editor. We recommend saving it to your desktop. 
Now, open the R program and set the working directory to the location where the dataset is stored. Typing in the 'dir()' command should show all of the contents of the working directory, including the workshop data set: 
```{r}
> dir () 
[1] "workshopData.csv" 
```
The following command reads the dataset into R, it should not take more than a few seconds:

```{r} 
> z = read.csv("workshopData.csv", as.is=TRUE) 
```
The dataset is now saved in the object named 'z'. To see the number of rows and columns type: 
```{r} 
> dim( z ) 
[1] 82954 	 13 
```
So there are about eighty-two thousand records (here, these correspond to photographs) each with thirteen associated variables. We do not want to look at all of these at once, but viewing the first few rows can give a better sense of what the data looks like. This can be done with the following command: 

```{r} 
> head(z)
  month year           city      state longitude latitude smalltowns greenbelt
1     1 1935         Nipomo California -120.4760 35.04275          0         0
2     1 1935         Nipomo California -120.4760 35.04275          0         0
3     2 1935 San Bernardino California -117.2898 34.10834          0         0
4     2 1935 San Bernardino California -117.2898 34.10834          0         0
5     2 1935   San Fernando California -118.4390 34.28195          0         0
6     2 1935           <NA> California -121.5608 39.29433          0         0
  migrants farms rural steel                                    url
1        1     0     0     0 http://hdl.loc.gov/loc.pnp/fsa.8b27537
2        1     0     0     0 http://hdl.loc.gov/loc.pnp/fsa.8b38259
3        0     0     0     0 http://hdl.loc.gov/loc.pnp/fsa.8b14336
4        0     0     0     0 http://hdl.loc.gov/loc.pnp/fsa.8e07168
5        0     1     1     0 http://hdl.loc.gov/loc.pnp/fsa.8b29524
6        1     0     0     0 http://hdl.loc.gov/loc.pnp/fsa.8b14346
```

Notice that we have a few basic variables such as year, month, and city, a few tags (0 means that the tag does not apply and 1 means that it does) and the url where the data can actually be found on Library of Congress website. 

###3 Basic Tables 
Now we are ready to do something somewhat useful to our dataset. Let's make a table of the years in the dataset. This can be achieved by the following command (note: to access a particular column, we use the notation DATASET$VARIABLE): 

```{r}
> table(z$year) 
 1935  1936  1937  1938  1939  1940  1941  1942  1943  1944  1945 
 1229  3056  3755  4364 11707 11538 12630 20930 13286   324   135 
```


What helpful information does this give us? What patterns do we see in the data? Do these patterns make sense? 

Now, we will make a similar table of cities. If you use the same syntax as above for years it will work ne, but be hard to read (try it!). A better approach is the following, which sorts and truncates the results to the top 30 cities. 

```{r}
> sort( table(z$city), decreasing =TRUE)[1:30] 

Washington (DC)        New York         Chicago       Baltimore         Detroit 
           5021            2235            2140            1263            1182 
  San Augustine      Pittsburgh        Pie Town       Greenbelt      San Angelo 
            722             641             619             597             436 
      Inglewood     Belle Glade     Minneapolis       Bridgeton  Corpus Christi 
            427             417             394             359             345 
  Oklahoma City      Gloucester         Buffalo       Nashville     San Antonio 
            341             340             327             324             323 
        Penasco           Akron          Lititz    Fort Belvoir            Ames 
            312             306             294             288             284 
  Daytona Beach     Southington        Caldwell     Los Angeles           Omaha 
            282             277             271             257             256 
```
FSA collection is often portrayed as a collection of southern and dustbowl poverty. How do you feel about this claim now? How could we further study this question; what other tables or variants of tables might be useful?

Often, it is useful to create a two-way table to show the interaction of two variables. Here is a table of the number of photographs that involve steel broken out by year:

```{r}
> table(z$year,z$steel)
      
           0     1
  1935  1223     6
  1936  3046    10
  1937  3746     9
  1938  4222   142
  1939 11686    21
  1940 11484    54
  1941 12353   277
  1942 20541   389
  1943 13157   129
  1944   324     0
  1945   135     0
```

Do you see any interesting patterns here? Any ideas what causes may be at play in faciliting this particular pattern?


Statistics and computing often emphasises looking at data in aggregate. However it is often useful to use computational methods as simply a way to access records; one can then revert to standard close reading techniques. For instance, only 6 photographs of steel were taken in 1935, so it might be interesting to see these photos. We can pull out these six records with the following commands:

```{r} 
> z[z$steel == 1 & z$year == 1935,] 

     month year       city        state longitude latitude smalltowns greenbelt
1045    11 1935 Hightstown   New Jersey -74.52321 40.26955          0         0
1080    11 1935  Bethlehem Pennsylvania -75.37046 40.62593          0         0
1083    11 1935  Bethlehem Pennsylvania -75.37046 40.62593          0         0
1140    12 1935 Cincinnati         Ohio -84.45689 39.16200          0         0
1171    12 1935       <NA>         Ohio -84.56606 39.39506          0         0
1175    12 1935       <NA>         Ohio -84.56606 39.39506          0         0
     migrants farms rural steel                                    url
1045        0     0     0     1 http://hdl.loc.gov/loc.pnp/fsa.8b38183
1080        0     0     0     1 http://hdl.loc.gov/loc.pnp/fsa.8c52905
1083        0     0     0     1 http://hdl.loc.gov/loc.pnp/ppmsc.00231
1140        0     0     0     1 http://hdl.loc.gov/loc.pnp/fsa.8a00723
1171        0     0     0     1 http://hdl.loc.gov/loc.pnp/fsa.8a00679
1175        0     0     0     1 http://hdl.loc.gov/loc.pnp/fsa.8a00703

```
[mstrsplit](http://hdl.loc.gov/loc.pnp/fsa.8c52905)
How can the approach we have taken to getting to this photograph help us appreciate the subject matter? How would we have found this photograph otherwise? 

###4 More Numbers 
The two way table above was helpful, but can be a bit difficult to use given that the number of photographs in a given year is not constant. Instead we can have R calculate the percentage of photographs in each year with a given characteristic. Here is the proportion of farm photographs: 
```{r}
> round( tapply (z$farms, z$year, mean) * 100) 

1935 1936 1937 1938 1939 1940 1941 1942 1943 1944 1945 
  12   28   23   22   24   23   18   12    4   10    0 
```


Now that we can interpret these as percentages, what patterns emerge? Does this make us question previous understandings of thethe FSA-OWI collection as rural poverty photographs? What temporal changes appear to have occurred in the collection?
We can make similar tables from transformed variables. Here we see the proportion of photographs taken (roughly) east of the Mississippi River: 
```{r}
>  round( tapply (z$longitude > -89.25 , z$year , mean) * 100)

1935 1936 1937 1938 1939 1940 1941 1942 1943 1944 1945 
  74   45   51   48   26   43   63   58   73  100  100 
```

And a similar table giving the percentage of photographs in Texas and Oklahoma (the center of the dust bowl region): 
```(r)
> round(tapply(z$state %in% c("Texas", "Oklahoma "), z$year, mean) * 100)

1935 1936 1937 1938 1939 1940 1941 1942 1943 1944 1945 
   0    3    2    1   24    5    0    6    8    0    0 
```

How do these tables help us think about the geographic and temporal distribution of the collection? What might be a better way to represent this data? 

###5 Basic Plots 
Tables are great for simple data analysis, but plots and graphs often give a better overall picture; this is particularly true of spatial data. Here is a dot plot of all of the photographs in the collection: 

```{r}
> plot(z$longitude , z$latitude , pch =19, cex =0.3) 
```

![](https://github.com/nolauren/Rtutorial_photogrammar/blob/master/images/BasicPlots1.png)

It isn't particularly pretty yet, but it gives us a general sense of the distribution of photographs. How does the plot alter thoughts we had after looking at the tables? 
Another type of plot is a line chart, which is useful for data spread over time. Here we create a new date variable (it is the date in decimal form, so April of 1935 becomes 1935.25) and plot a table of it: 
```{r}
> date = z$year + (z$month - 1) / 12 
> plot(table(date), type ="l") 
```

Does this change any of our conclusions from before? Do you find this easier or harder to look at or think about than the tables we had before? 
We can easily start creating complex plots. Here is the code and output to overlay photographs depicting migrants over our previous map: 

```{r}

> par (mar=c (0 ,0 ,0 ,0)) 
> plot(z$longitude, z$latitude, pch =19, cex =0.3) 
> index = which(z$migrants == 1) 
> points(z$longitude[index], z$latitude[index], pch=19, 
+ cex=0.8, col="salmon") 
```


What do you find surprising or unsurprising about this photograph? What are some locations with clusters of immigrant photographs? Are there any photographs you would like to try to look at now? 

###6 Packages & Maps 
There are literally thousands of free "packages", add on functions, which can be installed to your R program. This means that once you grasp the basic syntax of R, you will rarely need to use another program. Since the data we have been looking at today has a strong geospatial component. We will quickly look at the maps package for plotting political boundaries on plots. The following commands will install (only need to do this once) and load (need do do this every time you restart R) the *maps* package: 
```{r}
> install.packages ("maps")
> library (maps) 
```
That's it! Now we can produce a plot of Connecticut and overlay a dot plot of photographs on top of it: 
```{r}
> par(mar=c (0 ,0 ,0 ,0)) 
> map('county','connecticut') 
> points(z$longitude , z$latitude , pch =19, cex =0.3) 
```
This is a fairly simple example, but how could this functionality help us analyse other plots we have previously looked at? How about this revised plot of the migrant photographs: 

How would you describe this plot compared to the other? Does it have additional information, or is it just the same information in a better format? Do you think of the difference as just aesthetic? How might this change depending on the audience which you were producing this plot for? 


