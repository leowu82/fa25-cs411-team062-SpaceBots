# MISR Vision Project Description

## Summary

The [Multi-Angle Imaging SpectroRadiometer (MISR)](https://misr.jpl.nasa.gov/) is an instrument that flies aboard the NASA Terra satellite. It is an imager that measures radiance (intensity of light) in the blue, green, red, and near-infrared portions of the electromagnetic spectrum. While most other earth observing satellite instruments look straight down, the MISR instrument consists of nine cameras pointed at different viewing angles spanning from 70.5 degrees from zenith looking forward to 70.5 degrees looking backwards. This unique multi-angle prospective provides the opportunity to deploy a new class of remote sensing retrieval algorithms that specifically exploit the multi-angle information.

Our project will be creating a site that displays visualizations of data from the MISR instrument. Our inspiration for this project is [NASA Worldview](https://worldview.earthdata.nasa.gov/), a tool for visualizing satellite imagery, but specifically to display MISR images. The site will be capable of displaying the many years of already existing MSIR data, as well as incorporating data that will be taken in the future. The user will be able to select a location and see the data from all 9 of MISR’s cameras.

## Description

The purpose of our project is to create a site that displays visualizations of the imager data from the MISR instrument. On the back-end we want to automate the visualization of MISR images, ideally using already existing Python code, then store the images in either a file system, where it can easily be retrieved by the front-end and then displayed to the user, and/or generated when they are needed and used by the front-end, depending on the trade offs for storage space and how long the image generation takes. The default view will be of the world, displaying the needed number of MISR paths. The user will ideally be able to zoom in on the images and view them in higher resolution as well as the “MISR movie”. There will be a time slider on the bottom of the screen where the user will be able to change the day of the data displayed from the available dates based on the data. The user will be able to upload their own MISR data files which will be processed and be able to be displayed. This allows users to visualize current and future data without needing an extremely large database to store the files. Currently a tool like NASA Worldview does not support MISR movies. Our site would be used by people who currently utilize MISR data and lower the barriers for potential future users of MISR data.

## Creative Component (Technically Challenging Features)

A creative component that improves the functionality of our application is the interactive data visualization. We plan to allow users to zoom in on 1-2 blocks to view the images in higher resolution, as well as view the MISR movie for the blocks separately. In the zoomed out world view we would also like to include a feature where the user can hover the mouse over the block and view additional information about that block, like date/time and other information. If we have time there are a few other quality of life features we would like to add. We would like to include optimization for generating images and queries to improve the user experience, and also possibly implement caching for the same reason. We also would like to implement API calls to allow the user to directly retrieve MISR data instead of uploading it themselves.

## Usefulness

The development of NASA Worldview has been revolutionary in the field of earth science and beyond as the application has broken down major barriers to the most basic of tasks — to visualize the data. For MISR data, it is not only useful to easily browse through its imagery, but also be able to look at “MISR movies” where all nine view angles are panned through sequentially. Such MISR movies give insight into the depth of objects within the image. For example, if multi-angle images are registered to the surface of the earth, then clouds that are higher in altitude will have a larger [parallax](https://en.wikipedia.org/wiki/Parallax) between images in the MISR movie. Our application will prove to be useful as it will allow for a user to easily browse through MISR imagery, much like NASA Worldview, with the additional feature of generating MISR movies.

## Realness

With each new orbit, the Terra satellite travels over one of 233 unique ground paths. MISR collects a swath of imagery over the sunlit hemisphere of the earth that is about 400 km wide (cross-track dimension) and approximately 20,000 km long (along-track dimension) for a singular orbit. This swath of imagery is projected onto the Space Oblique Mercator (SOM) grid, which is a special map projection used for narrow swath satellite imagery. The SOM grid can be thought of as a cartesian coordinate system with its x and y coordinates representing length in meters. The SOM grid is defined relative to the path so that each orbit uses the same SOM grid. In other words, the SOM grid is independent of the path, so it is the same for every orbit. There is a one-to-one mapping between the SOM grid and latitude and longitude coordinates for each path. Imagery along the swath is divided into 180 blocks that are 140.8 km in the along-track dimension and 563.2 km in the across-track dimension. Each block has a fixed position on the SOM grid defined by the upper left corner (ulc) and lower right corner (lrc) of each block. Since the SOM grid is independent of the path, the blocks relative to the SOM grid are also independent of the path.

In the MISR Ancillary Geographic Product (AGP) the mapping between SOM coordinates and latitude and longitude coordinates is provided at 1.1 km grid resolution for each of the 233 paths. At 1.1 km resolution, each block is 128 grid points long and 512 grid points wide. We can store this data in a relation with a degree of 6 (path, block, i, j, latitude, longitude) where i and j represent the row and column of the 128 x 512 grid for each block. Given that there are 233 paths, 180 blocks, 128 rows, and 512 columns, the cardinality of this relation would be 233 x 180 x 128 x 512 = 2,748,579,840.

Also reported in the MISR AGP are the SOM x and y coordinates of the ulc and lrc of each block, which are independent of the path. We might store this information in a relation with a degree of 5 (block, ulc_SOM_x, ulc_SOM_y, lrc_SOM_x, lrc_SOM_y). This relation would have a cardinality of 180.

In the MISR Georectified Radiance Product (GRP) blue, green, red, and near infrared (NIR) radiance is recorded for each MISR camera at either 1.1 km resolution or 275 m resolution depending on view angle and spectral band. The red band is reported at 275 m resolution for all view angles while the blue, green, and NIR bands are reported at 1.1 km for all off-nadir views and at 275 m at nadir. Again, radiance is formatted in the path-block format where a block of 1.1 km resolution data is 128 x 512 pixels and for 275 m resolution a block is 512 x 2048 pixels. Additionally, this data has an orbit identifier number. This data can be stored in two relations; one for each resolution. The 1.1 km relation would have a degree of 9 (orbit, path, camera, block, i, j, blue radiance, green radiance, NIR radiance), and for a single orbit, 8 cameras, 180 blocks, 128 rows, 512 columns, on 3 channels, this relation would have a cardinality of 8 x 180 x 128 x 512 x 3 = 283,115,520. The 275 m relation would have a degree of 10 (orbit, path, camera, block, i, j, blue radiance, green radiance, red radiance, NIR radiance). To calculate the cardinality for a single orbit, we need to consider that the red radiance has 9 view angles and the blue, green and NIR only have a single view angle, so the cardinality would be (9 x 180 x 512 x 2048) + (180 x 512 x 2048 x 3) = 2,264,924,160.

All MISR data is freely and publicly available via [NASA Earthdata](https://search.earthdata.nasa.gov/). For the GRP, just search “MISR Level 1B2 Ellipsoid Data V003”. For the AGP, search “MISR Ancillary Geographic Product V001”. Data files are in hdf-4 format.

## Functionality

A user will have the ability to upload MISR data files and insert that data into the database if it doesn't already exist. The time slider bar will allow users to select the datetime of MISR imagery being displayed, which will query the database for a particular time range. In the whole world view, a user will be able to hover over a block to get details such as the datetime, sun geometry, and ocean flag. A user will be able to select 1-2 blocks to look at in another window at full resolution and have the ability to pan through the multi-angle images and create a MISR movie.

## Project Work Contribution

Below, is the final work distribution:
- Design database (ER model and UML): Everyone
- Implement database: Everyone
- Develop code to populate the database given MISR AGP and GRP files: John, Tessa
- Develop backend code to generate a MISR world-view image (show global coverage using multiple orbits). Image resolution will likely be degraded: Leo
- Develop backend code to generate a close up view & MISR movie: Leo
- Develop frontend: Leo, John
- Implement stored procedure: Tessa, John
- Implement transaction: Leo
- Implement trigger: John
- Infrastructure (Full-Stack Architecture, GCP Setup/Connection, Dockers, etc.): Leo
