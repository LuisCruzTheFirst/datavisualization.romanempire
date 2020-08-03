# data visualization on Roman Empire's Roads and Cities
Data Visualization Project for History 111 Class.

This is the road network identified in the Barrington Atlas. Harvard Dtataverse.
The file is on a shape file, which is a geospatial file, so I need to install and download a specific R package that will allow me to read and manipulate it. 

> install.packages("rgdal")
> library(rgdal) 

Now I read and call "my_ spdf" the shape file I downloaded in my working directory. 
This will allow me to create a map of the road network of the Roman Empire. 

> my_spdf <- readOGR( 
  dsn= paste0(getwd(),"/R stuff/Roman Road Network (version 2008)/roads/") , 
  layer="roman_roads_v2008",
  verbose=FALSE
)

Basic plot of this shape file. This is a plot of the road network of the Roman Empire.

> par(mar=c(0,0,0,0))
> plot(my_spdf, col="#f2f2f2", bg="black", lwd=0.25, border=0 )

Now I need to install and load the following packages in order to plot on a map of Europe the 
coordinates of the roman cities I found in the Hanson dataset. 

for the data visualization I use ggplot2
>install.packages("ggplot2")
>library(ggplot2)  

Dplyr is for some simple data manipulation.
>install.packages("dyplr")
>library(dplyr)  
Let's load rgdal again in order to read and manipulate geospatial data
>library(rgdal)  
>library(raster)  
>install.packages("ggsn")
>library(ggsn) 

"Rworldmap" is needed in order to pull out the borders of the countries which were
part of the roman empire. 

>install.packages("rworldmap")
>library(rworldmap)

Let's read the comma separated vector file into the R session:
cities <-  read.csv("Hanson2016_Cities_OxREP.csv")

Let's have a glimpse of how the data set is structured and let's see if we can get some useful information for our data visualization.

> summary(cities)

Let's plot latitude and longitude of the cities dataset, and let's color them by current Country,
we will put them on an actual map later on. 

> ggplot(cities, aes(x=Longitude..X., y=Latitude..Y., color=Country))+ geom_point()

# Let's get a map of the world, to add in the back ground of the above visualization.
world <- getMap(resolution="coarse")

# Now, since the world map is too broad to have a detailed representation of the roman empire cities,
# I will create a vector with all the current countries' names that once were part of the Roman Empire. 
# I will pull out a map of all these countries in order to display the first visualization of all the cities. 
roman_countries <- c("Albania", "Algeria","Austria","Belgium","Bosnia and Herzegovina", "Bulgaria","Croatia","Cyprus","Egypt","France","Germany","Greece","Hungary","Israel","Italy","Jordan","Kosovo","Lebanon","Libya","Macedonia","Malta","Montenegro","Morocco","Netherlands","Northern Cyprus","Portugal","Romania","Serbia","Slovenia","Spain","Switzerland","Syria","Tunisia","Turkey","United Kingdom")        
# I now restrict the data in "world" using roman_countries, and I call the new variable "world_rom"
world_rom <- world[world@data$ADMIN %in% roman_countries, ]

## Now I can include in my ggplot visualization the new world_rom map. On the x axis I inrest the longitude and
# On y axis I insert the latitude. This time I will color the cities based on the modern country. 
(roman_empire <- ggplot() +
    geom_polygon(data = world_rom, 
                 aes(x = long, y = lat, group = group),
                 fill = NA, colour = "black") +   ## Visualization of the modern countries present in the Roman Empire
    geom_point(data = cities,  # Add and plot on the cities
               aes(x = Longitude..X., y = Latitude..Y., 
                   colour = Country)) +
    coord_quickmap() + 
    xlim(-15.145, 60.508) +  # Set x axis limits, xlim(min, max)
    ylim(10.08, 70.98) +  # Set y axis limits
    theme_classic() +  # Remove ugly grey background
    xlab("Longitude") +
    ylab("Latitude") + 
    guides(colour=guide_legend(title="Provinces")))

## now I color the cities based on the ancient roman Province. 

(roman_empire <- ggplot() +
    geom_polygon(data = world_rom, 
                 aes(x = long, y = lat, group = group),
                 fill = NA, colour = "black") +     ## Visualization of the modern countries present in the Roman Empire
    geom_point(data = cities,  # Add and plot on the cities
               aes(x = Longitude..X., y = Latitude..Y., 
                   colour = Province)) +
    coord_quickmap() + 
    xlim(-15.145, 60.508) +  # Set x axis limits, xlim(min, max)
    ylim(10.08, 70.98) +  # Set y axis limits
    theme_classic() +  # Remove ugly grey background
    xlab("Longitude") +
    ylab("Latitude") + 
    guides(colour=guide_legend(title="Provinces")))
