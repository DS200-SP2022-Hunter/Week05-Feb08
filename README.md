# Lab Assignment 05, Due on [Canvas](https://psu.instructure.com/courses/2174978), Feb. 16 at 11:59pm
## Creating interactive maps using folium

The main objective of today's lab is to reproduce the output and maps of [Section 8.5](https://inferentialthinking.com/chapters/08/5/Bike_Sharing_in_the_Bay_Area.html) in the textbook, with some modifications.  The original code in that section does not work (at least it does not work in the Google Colab environment), so you'll need to modify the code to make it work.  Along the way, you'll learn how to join tables, an extremely important data-handling skill.

Most of the code you should need is in [Section 8.5 of the textbook](https://inferentialthinking.com/chapters/08/5/Bike_Sharing_in_the_Bay_Area.html).  When you turn in your work, it should not contain any irrelevant text and code boxes; therefore, if you use the textbook as the basis for your work, please delete text and code boxes unrelated to the assignment before you turn in your lab.

This assignment uses the 'join' method extensively, which is the topic of [Section 8.4](https://inferentialthinking.com/chapters/08/4/Joining_Tables_by_Columns.html).  I recommend that you work through Section 8.4 and make sure you understand its logic before moving on to the tasks below.

**Objective**:  Produce a map of the Bay Area in which the number of bike rentals starting at each station is represented by a colored circle whose area is proportional to the number of rentals and whose color represents the city where the station is located.  The map will look almost like the final map in Section 8.5 except with colors chosen as in the five-color map earlier in the section.

Your assignment is as follows:

1. Reconstruct the 'trips' and 'stations' objects from Section 8.5 by downloading the .csv files at these two URLs (one way to do this is to replace the `path_data` object by `'http://personal.psu.edu/drh20/200DS/assets/data/'`): 
```
http://personal.psu.edu/drh20/200DS/assets/data/trip.csv
http://personal.psu.edu/drh20/200DS/assets/data/station.csv
```
2. Make sure you can reproduce, more or less, the four maps in Section 8.5.  This will require changing the code.  Below are four code boxes that can replace existing code.  The first imports the 'folium' module, which is needed.  After getting the code to run after you copy-paste what's below, I encourage you to experiment by changing some of the code to see what happens.  If you'd like to learn more about folium, check out https://python-visualization.github.io/folium/modules.html.
```
import folium # This line is needed to use the folium library capabilities below

# OLD CODE (uses map_table in datascience module, no longer works correctly)
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

# OLD CODE (uses map_table in datascience library, no longer works correctly)
#sf_map_data = sf.select('lat', 'long', 'name')
#Circle.map_table(sf_map_data, color='green', radius=200)

# NEW CODE using folium module directly:
SFMap = folium.Map(location=[37.8, -122.4], tiles="OpenStreetMap", zoom_start=13, width='50%', height='50%')
for i in np.arange(sf.num_rows):
  folium.Circle([sf.column('lat')[i], sf.column('long')[i]], popup=sf.column('name')[i], color='green', radius=200).add_to(SFMap)
SFMap
```
```
joined = stations.join('landmark', colors, 'city')
colored = joined.select('lat', 'long', 'name', 'color').relabel('name', 'labels')

# OLD CODE (uses map_table in datascience module, no longer works correctly)
#Marker.map_table(colored)

# NEW CODE using folium module directly:
BayAreaMap2 = folium.Map(location=[37.4, -121.9], tiles="OpenStreetMap", zoom_start=9, width='80%', height='80%')
for i in np.arange(colored.num_rows):
  latitude = colored.column('lat')[i]
  longitude = colored.column('long')[i]
  LatLonLocation = [latitude, longitude]
  label = colored.column('labels')[i]
  color = colored.column('color')[i]
  folium.Marker(LatLonLocation, popup=label, icon=folium.Icon(color=color)).add_to(BayAreaMap2)
BayAreaMap2
```
```
starts_map_data = station_starts.select('lat', 'long', 'name').with_columns(
    'colors', 'blue',
# OLD CODE:    'areas', station_starts.column('count') * 0.3 
    'radius', np.sqrt(station_starts.column('count') * 2000) # Need radius instead of area
)
starts_map_data.show(3)

# OLD CODE (uses map_table in datascience module, no longer works correctly)
#Circle.map_table(starts_map_data.relabel('name', 'labels'))

# NEW CODE using folium module directly:
SFMap2 = folium.Map(location=[37.4, -121.9], tiles="OpenStreetMap", zoom_start=9, width='80%', height='80%')
for i in np.arange(starts_map_data.num_rows):
  folium.Circle([starts_map_data.column('lat')[i], starts_map_data.column('long')[i]], 
                popup=starts_map_data.column('name')[i], color='blue', opacity=0.4,
                radius=starts_map_data.column('radius')[i]).add_to(SFMap2)
SFMap2
```

3. Create a new version of the table called 'starts_map_data' that is just like the one in Section 8.5 except that the colors should match the station colors from the 'colored' table created earlier in that section.  That is, Mountain View stations should be blue, Palo Alto stations red, and so on. 
**Hint**:  A starting point is to use the `join` method on the `starts` and `colored` tables already created, as in
```
starts.join("Start Station", colored, "labels")
```

4. In your output, turn in the code that creates your new version of the 'starts_map_data' table as well as printing out the entire object.  (Remember, you can use the 'show' method with no argument to print the whole object.)

5. Finally, reproduce the final map in Section 8.5 using the different colors for each city's stations.  Feel free to play around with the look of the circles and map.

6. To summarize:  What you turn in should include (only) the full Table object from step 3 and the modified final map from step 5, along with the code boxes that produce them.  Remember that when you produce your pdf file to turn in, the map will no longer be interactive; therefore, you should position and zoom the map in the most appealing and informative way you can before you print to pdf.

When you've completed this, you should select "Print" from the File menu, then somehow save to pdf using this option.  The pdf file that you create in this way is the file that you should upload to Canvas for grading.  Recall that sometimes the right side of the notebooks are chopped off by this print procedure, but we have found that if you can select the "A3" paper size from the advanced options, this seems to solve the problem.
