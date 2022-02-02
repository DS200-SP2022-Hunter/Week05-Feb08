# Lab Assignment 05, Due on [Canvas](https://psu.instructure.com/courses/2174978), Feb. 16 at 11:59pm
## Creating interactive maps using folium

The main objective of today's lab is to reproduce the output and maps of [Section 8.5](https://inferentialthinking.com/chapters/08/5/Bike_Sharing_in_the_Bay_Area.html) in the textbook, with some modifications.  The original code in that section does not work (at least it does not work in the Google Colab environment), so you'll need to modify the code to make it work.  Along the way, you'll learn how to join tables, an extremely important data-handling skill.

Most of the code you should need is in [Section 8.5 of the textbook](https://inferentialthinking.com/chapters/08/5/Bike_Sharing_in_the_Bay_Area.html).  When you turn in your work, it should not contain any irrelevant text and code boxes; therefore, if you use the textbook as the basis for your work, please delete text and code boxes unrelated to the assignment before you turn in your lab.

This assignment uses the 'join' method extensively, which is the topic of [Section 8.4](https://inferentialthinking.com/chapters/08/4/Joining_Tables_by_Columns.html).  I recommend that you work through Section 8.4 and make sure you understand its logic before moving on to the tasks below.

**Objective**:  Produce a map of the Bay Area in which the number of bike rentals starting at each station is represented by a colored circle whose area is proportional to the number of rentals and whose color represents the city where the station is located.  The map will look almost like the final map in Section 8.5 except with colors chosen as in the five-color map earlier in the section.

Your assignment is as follows:

1. Reconstruct the 'trips' and 'stations' objects from Section 8.5 by downloading the .csv files at these two URLs (one way to do this is to replace the `path_data` object by `'http://personal.psu.edu/drh20/200DS/assets/data/'`: 
```
http://personal.psu.edu/drh20/200DS/assets/data/trip.csv
http://personal.psu.edu/drh20/200DS/assets/data/station.csv
```
2. Make sure you can reproduce the first two maps in Section 8.5.  This will require changing the code.  Below are three code boxes that could help.  The first imports the 'folium' module.  The next two produce the maps.  If you'd like to learn more about folium, check out https://python-visualization.github.io/folium/modules.html.
```
import folium # This line is needed to use the folium library capabilities below
```
```
# OLD CODE (using map_table in datascience module; no longer works correctly)
#Marker.map_table(stations.select('lat', 'long', 'name'))

# NEW CODE using folium module directly:
BayAreaMap = folium.Map(location=[37.34, -121.9], tiles="OpenStreetMap", zoom_start=8, width='50%', height='50%')
for i in np.arange(stations.num_rows):
  latitude = stations.column('lat')[i]
  longitude = stations.column('long')[i]
  LatLonLocation = [latitude, longitude]
  label = stations.column('name')[i]
  folium.Marker(LatLonLocation, popup=label).add_to(BayAreaMap)
BayAreaMap
```
```
sf = stations.where('landmark', are.equal_to('San Francisco'))

# OLD CODE (using map_table in datascience module; no longer works correctly)
#sf_map_data = sf.select('lat', 'long', 'name')
#Circle.map_table(sf_map_data, color='green', radius=200)

# NEW CODE using folium module directly:
SFMap = folium.Map(location=[37.8, -122.4], tiles="OpenStreetMap", zoom_start=13, width='50%', height='50%')
for i in np.arange(sf.num_rows):
  folium.Circle([sf.column('lat')[i], sf.column('long')[i]], popup=sf.column('name')[i], color='green', radius=200).add_to(SFMap)
SFMap
```
3. Create a table called 'starts_map_data' that is just like the one at the end of Section 8.5 except that the colors should match the station colors from the 'joined' table created earlier in that section.  That is, Mountain View stations should be blue, Palo Alto stations red, and so on. 
**Hint**:  One way to do this is to join the starts and colored tables from Section 8.5:  
```
starts_map_data = starts.join("Start Station", colored, "name")
```
Then you just need to create an 'area' column by multiplying the 'counts' column by some number and then using the values in 'area' to create your map. You might have to experiment a bit with the value of 'multiplier' to make the radius of the circles look good on the map.
```
multiplier = 1000 # Too big!  (See what happens.)
rad = pow(starts_map_data.column('count'), 0.5) # Take the square root of counts
starts_map_data = starts_map_data.with_column('radius', rad * multiplier) 
```
**Note**: The code above uses the square root (which is the 0.5 power) of the trip counts as the radius. This will make the area proportional to the trip counts, since the area is proportional to the square of the radius.

4. In your output, turn in the code that creates your 'starts_map_data' object as well as printing out the entire object.  (Remember, you can use the 'show' method with no argument to print the whole object.)

5. Finally, reproduce the final map in Section 8.5 using the different colors for each city's stations.  Feel free to play around with the look of the circles; you don't have to use 1000 as the multiplier for the area, for instance.
Hint: The code below uses the same format and commands as the SFMap code presented earlier in step 2.  See if you can play around a bit with some of the code to change the look of your map.
```
MyMap = folium.Map(location=[37.8, -122.4], tiles="OpenStreetMap", zoom_start=9, width='50%', height='50%')
for i in np.arange(starts_map_data.num_rows):
  folium.Circle([starts_map_data.column('lat')[i], 
                 starts_map_data.column('long')[i]], 
                popup=starts_map_data.column('Start Station')[i], 
                color=starts_map_data.column('color')[i], 
                radius=starts_map_data.column('radius')[i],
                fill=True).add_to(MyMap)
MyMap
```
When you've done this, if you're feeling ambitious try playing around with some of the additional parameters for the path_options at https://python-visualization.github.io/folium/modules.html.

6. To summarize:  What you turn in should include (only) the full 'stats_map_data' object and the modified final map, along with the code boxes that produce them.  Remember that when you produce your pdf file to turn in, the map will no longer be interactive; therefore, you should position and zoom the map in the most appealing and informative way you can before you print to pdf.

When you've completed this, you should select "Print" from the File menu, then somehow save to pdf using this option.  The pdf file that you create in this way is the file that you should upload to Canvas for grading.  Recall that sometimes the right side of the notebooks are chopped off by this print procedure, but we have found that if you can select the "A3" paper size from the advanced options, this seems to solve the problem.
