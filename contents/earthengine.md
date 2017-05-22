Google Earth Engine
===================

Google's [Earth Engine](https://earthengine.google.com/) platform is an amazing starting point for testing out applications for satellite data and performing light processing.
It is an excellent tool for hypothesis testing,
and it is one of the first tools we used a FluroSat to get feedback from farmers on what we were working on.
Gaining access is done by signing up on the Earth Engine [website](https://earthengine.google.com/).
This sign up process involves describing how you are going to be using the platform and will likely take a few days.

The power of Earth Engine is that you have access to a huge library of satellite data,
all Sentinel and Landsat images which can be queried, processed and visualised in a handful of lines of code.
An example of this is shown below:
![Sentinel 2 RGB image of Narrabri][rgb narrabri]

The code to generate this is only 4 lines,
```javascript
var Sentinel2 = ee.ImageCollection('COPERNICUS/S2');
var Sentinel2016 = Sentinel2.filterDate('2016-01-01', '2016-12-31');
var visParams = {bands: ['B4', 'B3', 'B2'], max:2000, min:200};
Map.addLayer(Sentinel2016, visParams, 'Sentinel Collection');
```
we choose a collection of images,
perform some filtering,
describe how to display that information,
then display it.

To some processing of the data we only need some small changes,
```javascript
var Sentinel2 = ee.ImageCollection('COPERNICUS/S2');
var Sentinel2016 = Sentinel2.filterDate('2016-01-01', '2016-12-31');
var ndvi = Sentinel2016.reduce('median')
.normalizedDifference(['B8_median', 'B4_median']);
var visParams = {max:0.8, min:0, palette: ['ffffe5', '004529']};
Map.addLayer(ndvi, visParams, 'NDVI');
```
The reduce function reduces the `ImageCollection` to an `Image` object,
to which we can then perform a normalised difference, giving this result.
![Sentinel 2 NDVI image][ndvi narrabri]

Above we have calculated the Normalised Difference Vegetation Index ([NDVI][NDVI Info])
which is currently a fairly standard metric that is computed in an attempt to determine vegetative health.
In the image above,
regions with a high vegetation density are a dark green,
while those with a low density are a light yellow.

The NDVI has the following mathematical formula
```
NDVI = (NIR - Red) / (NIR + Red)
```
where NIR is the intensity of the Near Infrared band and Red is the intensity of the red band.
This is one of the simplest bits of analysis to perform,
there is far more that is possible with Earth Engine,
with many examples of functionality provided to get started with experimentation.

Note: when generating the images above I was using Safari because the _Run_ button wasn't working on Chrome.
If you are unable to run the script, try a different browser.

[NDVI info]: https://earthobservatory.nasa.gov/Features/MeasuringVegetation/measuring_vegetation_1.php
[rgb narrabri]: /remotesensing/contents/images/rgb_narrabri.png
[ndvi narrabri]: /remotesensing/contents/images/ndvi_narrabri.png


