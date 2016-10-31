# Introduction to Pandas and Geopandas

### Topics

1. What are Pandas or Geopandas -modules
2. Working with spatial data using Geopandas

### Sources

These materials are partly based on [Pandas introductory tutorial](http://pandas.pydata.org/pandas-docs/version/0.15.2/10min.html#min) 
and Geopandas documentation.

## What are Pandas and Geopandas -modules?

[**Pandas**](http://pandas.pydata.org) is a modern, powerful and feature rich library that is designed for doing data analysis in Python. 
It is a mature data analytics framework that is widely used among different fields 
of science, thus there exists a lot of good examples and documentation that can help you get going with your data analysis tasks. In Pandas the data is typically stored into a **DataFrame** 
that looks like a typical table with rows and columns (+ indices and column names), where columns can contain data of different data types. 
Thus, it reminds a little bit of how data is stored e.g. in Excel or in R that also uses a concept of dataframe.
 
Pandas takes advantage of **numpy** -module which runs under the hood, thus it is fast and powerful and can handle efficiently even large datasets. Pandas, however, is much 
feature-rich module and it also makes some of the same functionalities that numpy has much easier and more intuitive to use, such as creating new empty columns and doing 
data selections. Thus, it was useful to learn a little bit of how numpy works since many features included in pandas uses the same 
syntax as numpy. However, all numpy functions are not included in pandas, such as `np.linspace()` or `np.arange()`, hence it is really common to see that pandas and numpy -modules are both imported and used in a same Python script.
 
Compared to numpy, pandas is also a more flexible and feature rich module (or framework) as it combines functionalities from other scientific Python -modules as well, such as [**scipy**]() and [**matplotlib**]() 
for visualization purposes. Thus, you can use many of the features included in those packages even without importing them at all. 

[**Geopandas**](http://geopandas.org/#description) is a Python module that is built on top of [Pandas](http://pandas.pydata.org/) extending its functionalities.  
data analysis library. Geopandas makes it possible to work with spatial data stored e.g. in Shapefiles or PostGIS database. As Geopandas is built on top of Pandas, 
it means that all functionalities of pandas works also in geopandas. Geopandas has many nice built-in spatial processing / analysis features such as overlay analysis, 
geocoding, spatial aggregation methods and attribute / spatial joins that are all fairly useful and commonly used GIS-functionalities.  
It is also possible to do some simple processing with rasters using geopandas with [rasterio](https://github.com/mapbox/rasterio) module 
(see [example](http://gis.stackexchange.com/questions/151339/rasterize-a-shapefile-with-geopandas-or-fiona-python)), however there are more feature-rich Python modules for 
doing raster analysis (will be covered during the [Lesson 7]()). 

## Downloading and extracting data

For this lesson we are using data that you can download from [**here**](https://github.com/Automating-GIS-processes/Lesson-2-Geo-DataFrames/raw/master/data/Data.zip). 
Once you have downloaded the Data.zip file into your home directory, you can unzip the file using the `unzip` command in the Terminal window.
 
```bash
$ cd $HOME
$ unzip Data.zip
$ ls Data
DAMSELFISH_distributions.dbf   DAMSELFISH_distributions.prj   DAMSELFISH_distributions.sbn   DAMSELFISH_distributions.sbx 
DAMSELFISH_distributions.shp   DAMSELFISH_distributions.shp.xml   DAMSELFISH_distributions.shx
```
The Data folder includes a Shapefile called **DAMSELFISH_distribution.shp** (and files related to it).

## Working with spatial data using Geopandas

Let's start working with spatial data using geopandas. 

### Data I/O (in / out) 

#### Reading a Shapefile

Spatial data can be read easily with geopandas using `gpd.from_file()` -function:

```python
# Import necessary modules
import geopandas as gpd
import matplotlib.pyplot as plt

# Set filepath
fp = r"/home/geo/Data/DAMSELFISH_distributions.shp"

# Read file using gpd.read_file()
data = gpd.read_file(fp)

# Let's see what datatype is our 'data' variable
>>> type(data)
geopandas.geodataframe.GeoDataFrame
```

Okey so from the above we can see that our `data` -variable is a **GeoDataFrame**. GeoDataFrame extends the functionalities of **pandas.DataFrame** in a 
way that it is possible to use and handle spatial data within pandas (hence the name geopandas). GeoDataFrame have some special features and functions that 
are useful in GIS. 

- Let's take a look at our data and print the first 5 rows using the `head()` -function prints the first 5 rows by default

```python
>>> data.head()
              binomial category  \
0   Stegastes leucorus       VU   
1   Stegastes leucorus       VU   
2   Stegastes leucorus       VU   
3  Chromis intercrusma       LC   
4  Chromis intercrusma       LC   

                                            citation      class_name compiler  \
0  International Union for Conservation of Nature...  ACTINOPTERYGII     IUCN   
1  International Union for Conservation of Nature...  ACTINOPTERYGII     IUCN   
2  International Union for Conservation of Nature...  ACTINOPTERYGII     IUCN   
3  International Union for Conservation of Nature...  ACTINOPTERYGII     IUCN   
4  International Union for Conservation of Nature...  ACTINOPTERYGII     IUCN   

  dist_comm     family_nam genus_name  \
0      None  POMACENTRIDAE  Stegastes   
1      None  POMACENTRIDAE  Stegastes   
2      None  POMACENTRIDAE  Stegastes   
3      None  POMACENTRIDAE    Chromis   
4      None  POMACENTRIDAE    Chromis   

                                            geometry     id_no  ...  origin  \
0  POLYGON ((-115.6437454219999 29.71392059300007...  183963.0  ...       1   
1  POLYGON ((-105.589950704 21.89339825500002, -1...  183963.0  ...       1   
2  POLYGON ((-111.159618439 19.01535626700007, -1...  183963.0  ...       1   
3  POLYGON ((-80.86500229899997 -0.77894492099994...  183793.0  ...       1   
4  POLYGON ((-67.33922225599997 -55.6761029239999...  183793.0  ...       1   

  phylum_nam rl_update seasonal  source   species_na  subpop  subspecies  \
0   CHORDATA    2012.1        1    None     leucorus    None        None   
1   CHORDATA    2012.1        1    None     leucorus    None        None   
2   CHORDATA    2012.1        1    None     leucorus    None        None   
3   CHORDATA    2012.1        1    None  intercrusma    None        None   
4   CHORDATA    2012.1        1    None  intercrusma    None        None   

  tax_comm  year  
0     None  2010  
1     None  2010  
2     None  2010  
3     None  2010  
4     None  2010  

[5 rows x 24 columns]
```

 - Let's also take a look how our data looks like on a map. If you just want to explore your data on a map, you can use `.plot()` -function in 
 geopandas that creates a simple map out of the data (uses matplotlib as a backend):

```python
>>> data.plot()
```
![simple map](../img/damselfish-simple-map.PNG)

#### Coordinate reference system (CRS)

GeoDataFrame that is read from a Shapefile contains _always_ (well not always but should) information about the coordinate system in which the data is 
projected. 

 - We can see the current coordinate reference system from `.crs`  attribute:

```python
>>> print(data.crs)
{'init': 'epsg:4326'}
```

Okey, so from this we can see that the data is something called **epsg:4326**. The EPSG number (_"European Petroleum Survey Group"_) 
is a code that tells about the coordinate system of the dataset. "[EPSG Geodetic Parameter Dataset](http://www.epsg.org/) is a collection 
of definitions of coordinate reference systems and coordinate transformations which may be global, regional, national or local in application". 
EPSG-number 4326 that we have here belongs to the WGS84 coordinate system (i.e. coordinates are in decimal degrees (lat, lon)). You can check easily 
different epsg-codes from [this website](http://spatialreference.org/ref/epsg/).

#### Writing a Shapefile

Writing a new Shapefile is also something that is needed frequently. 

- Let's select 50 first rows of the input data and write those into a new Shapefile by first selecting the data using index slicing and then write the selection into a Shapefile with `gpd.to_file()` -function:

```python
# Create a output path for the data
out = r"/home/geo/Data/DAMSELFISH_distributions_SELECTION.shp"

# Select first 50 rows
selection = data[0:50]

# Write those rows into a new Shapefile (the default output file format is Shapefile)
selection.to_file(out)
```

**Task:** Open the Shapefile now in QGIS that has been installed into our computer instance, and see how the data looks like.

### Geometries in Geopandas

Geopandas takes advantage of Shapely's geometric objects. Geometries are stored in a column called *geometry* that is a default column name for storing 
geometric information in geopandas.

 - Let's print the first 5 rows of the column 'geometry':

```python
>>> # It is possible to use only specific columns by specifying the column name within square brackets []
... data['geometry'].head()
0    POLYGON ((-115.6437454219999 29.71392059300007...
1    POLYGON ((-105.589950704 21.89339825500002, -1...
2    POLYGON ((-111.159618439 19.01535626700007, -1...
3    POLYGON ((-80.86500229899997 -0.77894492099994...
4    POLYGON ((-67.33922225599997 -55.6761029239999...
Name: geometry, dtype: object
```

Since spatial data is stored as Shapely objects, **it is possible to use all of the functionalities of Shapely module** that we practiced earlier.

 - Let's print the areas of the first 5 polygons:

```python
# Make a selection that contains only the first five rows
selection = data[0:5]
```

 - We can iterate over the selected rows using a specific `.iterrows()` -function in (geo)pandas:

```python
>>> for index, row in selection.iterrows():
...     # Calculate the area of the polygon
...     poly_area = row['geometry'].area
...     # Print information for the user
...     print("Polygon area at index {0} is: {1:.3f}".format(index, poly_area))
Polygon area at index 0 is: 19.396
Polygon area at index 1 is: 6.146
Polygon area at index 2 is: 2.697
Polygon area at index 3 is: 87.461
Polygon area at index 4 is: 0.001
```

 - Let's create a new column into our GeoDataFrame where we calculate and store the areas individual polygons:

```python
# Empty column for area
data['area'] = None
```

-  Let's iterate over the rows and calculate the areas

```
for index, row in data.iterrows():
    # Update the value in 'area' column with area information at index
    data.loc[index, 'area'] = row['geometry'].area
```
 
 - Let's see the first 2 rows of our 'area' column

```
>>> data['area'].head(2)
0    19.3963
1     6.1459
Name: area, dtype: object
```

 - Let's check what is the mean and the max of those areas using familiar functions from our previous numpy lessions

```python
>>> max_area = data['area'].max()
>>> min_area = data['area'].mean()
>>> print("Max area: %s\nMean area: %s" % (round(max_area, 2), round(min_area, 2)))
Max area: 1493.2
Mean area: 19.96
```

### Creating geometries into GeoDataFrame

Since geopandas takes advantage of Shapely geometric objects it is possible to create a Shapefile from a scratch by passing Shapely's geometric objects into the GeoDataFrame. This is useful as it makes it easy to convert e.g. a text file that contains coordinates into a Shapefile. 

 - Let's create an empty `GeoDataFrame`.

```python
# Import necessary modules first
import pandas as pd
import geopandas as gpd
from shapely.geometry import Point, Polygon
import fiona

# Create an empty geopandas GeoDataFrame
newdata = gpd.GeoDataFrame()

# Let's see what's inside
>>> print(newdata)
Empty GeoDataFrame
Columns: []
Index: []
```

The GeoDataFrame is empty since we haven't placed any data inside. 

 - Let's create a new column called `geometry` that will contain our Shapely objects:

```python
# Create a new column called 'geometry' to the GeoDataFrame
newdata['geometry'] = None

# Let's see what's inside
>>> print(newdata)
Empty GeoDataFrame
Columns: [geometry]
Index: []
```

Now we have a geometry column in our GeoDataFrame but we don't have any data yet. 

 - Let's create a Shapely Polygon repsenting the Helsinki Senate square that we can insert to our GeoDataFrame:

```python
# Coordinates of the Helsinki Senate square in Decimal Degrees
coordinates = [(24.950899, 60.169158), (24.953492, 60.169158), (24.953510, 60.170104), (24.950958, 60.169990)]

# Create a Shapely polygon from the coordinate-tuple list
poly = Polygon(coordinates)

>>> print(poly)
POLYGON ((24.950899 60.169158, 24.953492 60.169158, 24.95351 60.170104, 24.950958 60.16999, 24.950899 60.169158))
```

Okey, so now we have appropriate Polygon -object. 

 - Let's insert the polygon into our 'geometry' column in our GeoDataFrame:

```python
# Insert the polygon into 'geometry' -column at index 0
newdata.loc[0, 'geometry'] = poly

# Let's see what we have now
>>> print(newdata)
                                            geometry
0  POLYGON ((24.950899 60.169158, 24.953492 60.16...
```

Now we have a GeoDataFrame with Polygon that we can export to a Shapefile. 

 - Let's add another column to our GeoDataFrame called `Location` with text _Senaatintori_.

```python
# Add a new column and insert data
newdata.loc[0, 'Location'] = 'Senaatintori'

# Let's check the data
>>> print(newdata)
                                            geometry      Location
0  POLYGON ((24.950899 60.169158, 24.953492 60.16...  Senaatintori
```

Okey, now we have additional information that is useful to be able to recognice what the feature represents. 

Before exporting the data it is useful to **determine the spatial reference system for the GeoDataFrame.**

As was shown earlier, GeoDataFrame has a property called *.crs* that shows the coordinate system of the data which is empty (None) 
in our case since we are creating the data from the scratch:

```python
>>> print(newdata.crs)
None
```

 - Let's add a crs for our GeoDataFrame. A Python module called **fiona** has a nice function called `from_epsg()` for passing coordinate system for the GeoDataFrame. Next we will use that and determine the projection to WGS84 (epsg code: 4326):

```python
# Import specific function 'from_epsg' from fiona module
from fiona.crs import from_epsg

# Set the GeoDataFrame's coordinate system to WGS84
newdata.crs = from_epsg(4326)

# Let's see how the crs definition looks like
>>> print(newdata.crs)
{'init': 'epsg:4326', 'no_defs': True}
```

 - Finally, we can export the data using GeoDataFrames `.to_file()` -function. The function works similarly as numpy or pandas, but here we only need to provide the output path for the Shapefile. Easy isn't it!:

```python
# Determine the output path for the Shapefile
outfp = r"/home/geo/Data/Senaatintori.shp"

# Write the data into that Shapefile
newdata.to_file(out)
```

Now we have successfully created a Shapefile from the scratch using only Python programming. Similar approach can be used to for example to read 
coordinates from a text file (e.g. points) and create Shapefiles from those automatically. 

**Task:** check the output Shapefile in QGIS and make sure that the attribute table seems correct.

## Pro -tips (optional - recommended!)

### Grouping data

One really useful function that can be used in Pandas/Geopandas is *__<a href="http://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.groupby.html" target="_blank">.groupby()</a>__*. 
This function groups data based on values on selected column(s). 

- Let's group individual fishes in `DAMSELFISH_distribution.shp` and export the species to individual Shapefiles.
  - *Note: If your `data` -variable doesn't contain the Damselfish data anymore, read the Shapefile again into memory using `gpd.read_file()` -function* 

```python
# Group the data by column 'binomial'
>>> grouped = data.groupby('binomial')

# Let's see what we got
>>> grouped
<pandas.core.groupby.DataFrameGroupBy object at 0x0000000003FB6710>
```
 
 - `groupby` -function gives us an object called `DataFrameGroupBy` which is similar to list of keys and values (in a dictionary) that we can iterate over.
  
```python
# Iterate over the group object 

for key, values in grouped:
    individual_fish = values
    
# Let's see what is the LAST item that we iterated
>>> print(individual_fish)
TODO: DATA HERE
```

 - Let's check the datatype of the grouped object and what does the `key` variable contain
 
```python
>>> type(individual_fish)
geopandas.geodataframe.GeoDataFrame

>>> print(key)
'Teixeirichthys jordani'
```

As can be seen from the example above, each set of data are now grouped into separate GeoDataFrames that we can export into Shapefiles using the variable 'key' 
for creating the output filepath names. Let's now export those species into individual Shapefiles.

```python
# Determine outputpath
outFolder = r"/home/geo/Data"

# Create a new folder called 'Results' (if does not exist) to that folder using os.makedirs() function
resultFolder = os.path.join(outFolder, 'Results')
if not os.path.exists(resultFolder):
    os.makedirs(resultFolder)

# Iterate over the 
for key, values in grouped:
    # Format the filename (replace spaces with underscores)
    outName = "%s.shp" % key.replace(" ", "_")
    
    # Print some information for the user
    print("Processing: %s" % key)
    
    # Create an output path
    outpath = os.path.join(resultFolder, outName)
    
    # Export the data
    values.to_file(outpath)
```

Now we have saved those individual fishes into separate Shapefiles and named the file according to the species name. These kind of grouping operations can be really 
handy when dealing with Shapefiles. Doing similar process manually would be really laborious and error-prone.  