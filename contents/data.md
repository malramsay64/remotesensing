Getting some data
============

In some cases there is no substitute for having the satellite data available on your computer to play around with in the tools you are familiar with.
There is a lot of satellite imagery available thanks to NASA and ESA,
and this is available to browse and download on a number of different platforms.
The most useful of these platforms when first getting started with satellite data is [Land Viewer][landviewer],
a web interface which allows you to interactively select an area on the map and see the imagery available from
a number of satellites in that area.
![An example of the Land Viewer interface, it is very simple for newcomers to satellite data to understand][landviewer interface]

There are also links to download the resulting image bands directly from this interface.

A more advanced interface is through Google's [BigQuery](bigquery), 
with the tables for both Landsat and Sentinel available under bigquery-public-data > cloud_storage_geo_index.
This is more advanced in that querying the data is done using SQL,
however it also allows you a far more powerful search language which can also be the starting point for further analysis on Google Cloud.

One example of the power of BigQuery is using it to find the earliest cloud-free images from Sentinel for a region.
For this example I have picked the Moree area in NSW,
which has the Military Grid Reference System (MGRS) tile designation of `55JGH`. 
The SQL query to generate the code is as follows;
```SQL
SELECT
  sensing_time, cloud_cover, base_url
FROM
  [bigquery-public-data:cloud_storage_geo_index.sentinel_2_index]
WHERE
  mgrs_tile = "55JGH"
  AND FLOAT(cloud_cover) < 10
ORDER BY
  sensing_time
LIMIT
  10
```

The table this generates is shown below 
and can be output in a range of formats as the starting point for further processing.
While I have used an MGRS tile to define an area it would be relatively straightforward to
query based on the latitude and longitude of a point or area of interest.
Alternatively the query can be run directly using the [Google Cloud SDK][gcloud sdk],
the output feeding directly into another script.

![A table of the 10 earliest cloud-free images from the Moree area in NSW.][bigquery table example]

Each of the base_url fields is a folder containing all the data for that tile.
These folders can be easily downloaded after installing the [Google Cloud SDK][gcloud sdk]
using the `gsutil` tool.
The command is simply
```bash
gsutil cp -r <base_url> .
```
which will copy the folder of the base url to the current directory. 
There is some reasonably extensive documentation on starting with `gsutil` with the [Cloud SDK Documentation][cloud sdk docs].


[earthengine]: earthengine.md
[landviewer]: https://lv.eosda.com/
[landviewer interface]: /remotesensing/contents/images/landviewer.png
[bigquery]: https://bigquery.cloud.google.com/table/bigquery-public-data:cloud_storage_geo_index.landsat_index
[bigquery table example]: /remotesensing/contents/images/bigquery-example1.png
[gcloud sdk]: https://cloud.google.com/sdk/
[gcloud sdk docs]: https://cloud.google.com/storage/docs/quickstart-gsutil
