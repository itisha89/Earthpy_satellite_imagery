# Understanding Satellite Imagery and Raster Data

#### **Introduction to Satellite Imagery**

Satellite data, or satellite imagery, is information gathered from space by man-made satellites orbiting Earth and other planets. It provides essential insights into Earth's surface and weather changes, with a significant application in environmental monitoring and resource management. Satellite data is collected using **remote sensing technologies**, which allow for the observation of an area from a distance, typically through the use of satellites or aircraft.

#### **What is Remote Sensing?**
Remote sensing refers to the process of collecting information about an object or area without making direct contact. In the context of satellite imagery, this means detecting and monitoring physical characteristics of Earth's surface by measuring emitted radiation from the area (e.g., temperature, vegetation, or land cover) using sensors aboard satellites.

The following diagram illustrates the concept:

![Remote Sensing](https://s3-whjr-v2-prod-bucket.whjr.online/whjr-v2-prod-bucket/a586ee36-d312-4e3d-bfce-47553f712154.svg)

*Image Credit: https://openclipart.org/detail/244734/satellite-imaging-remote-sensing*

#### **What is a Raster?**
A raster is an image composed of a matrix of cells or pixels organized in rows and columns. Each cell contains a value that represents a specific measurement, such as temperature, vegetation index, or elevation. Common raster formats include `.BMP`, `.JPEG`, `.PNG`, and `.TIFF`. Most satellite imagery is stored in raster format as **TIFF** files, which offer high-quality data for further processing.

![Raster Example](https://s3-whjr-v2-prod-bucket.whjr.online/whjr-v2-prod-bucket/a99d0d0e-7904-4dbd-82e4-3d2db4b025a0.png)

*Image Source: https://desktop.arcgis.com/en/arcmap/10.3/manage-data/raster-and-images/what-is-raster-data.htm*

#### **TIFF and GeoTIFF Images**
- **TIFF** (Tagged Image File Format) is a widely used image format for storing high-quality raster data.
- **GeoTIFFs** are an extension of TIFF that embed geographic metadata within the image file, such as the location (latitude and longitude), map projection, and other spatial references. This metadata enables the proper alignment of the image on the Earth's surface.

#### **Exploring Satellite Datasets using EarthPy**
In this section, we will explore satellite data using the **EarthPy** Python package, which provides several example datasets for hands-on exploration. You can list available datasets in EarthPy as follows:

```python
import earthpy as et
et.data.get_data()
```

Some example datasets available in EarthPy include:
- `california-rim-fire`
- `co-flood-extras`
- `cold-springs-fire`
- `colorado-flood`
- `vignette-landsat`

For example, to download the **colorado-flood** dataset:
```python
import earthpy as et
et.data.get_data("colorado-flood", path="./")
```

Once downloaded, you can access the dataset files in the file explorer of your notebook environment.

#### **Landsat Data and Spectral Bands**
The **Landsat mission** is a joint program between NASA and the USGS, providing Earth observation data through satellites for over 40 years. The Landsat 8 satellite, for instance, captures multispectral data across 7 spectral bands:

- **Band 1 (Coastal/Aerosol)**
- **Band 2 (Blue)**
- **Band 3 (Green)**
- **Band 4 (Red)**
- **Band 5 (Near-Infrared)**
- **Band 6 (Shortwave Infrared)**
- **Band 7 (Shortwave Infrared)**

Each band corresponds to a different part of the electromagnetic spectrum, capturing distinct information about the Earth's surface, such as vegetation, water bodies, or urban areas.

![image](https://github.com/user-attachments/assets/60fd89d3-70ed-49d6-b72c-416b739bd813)


#### **Working with Raster Data**
Once the dataset is downloaded, you will find the 7 spectral bands stored as separate `.tif` files. You can use the **Glob module** in Python to filter out files related to spectral bands:

```python
import glob
files = glob.glob("vignette-landsat/*band*.tif")
print(files)
```

This will give you a list of all TIFF files related to spectral bands that you can use for further analysis.

#### **Creating RGB Composite Image**
Satellite imagery can be combined to create a color image by stacking specific bands. RGB images can be created by combining **red**, **green**, and **blue** spectral bands. EarthPy provides a `stack()` function to stack the bands together into a single composite image:

```python
import earthpy.spatial as es
stacked_bands, meta = es.stack(files)
```

The metadata will contain information about the stacked image, such as the width, height, and the number of bands (7 in the case of Landsat 8).

#### **Visualizing the Bands**
After stacking the bands, you can visualize them using the `plot_bands()` function:

```python
import earthpy.plot as ep
import matplotlib.pyplot as plt

ep.plot_bands(stacked_bands, titles=["Coastal/Aerosol", "Blue", "Green", "Red", "Near-Infrared", "Shortwave Infrared 1", "Shortwave Infrared 2"])
plt.show()
```

This function plots the stacked raster image, and you can customize the titles for each band to reflect their respective names.


---

## NDVI Calculation and Usage

The **Normalized Difference Vegetation Index (NDVI)** is a key metric for assessing vegetation health using satellite imagery. NDVI is calculated using the red and near-infrared bands of the image.

### NDVI Formula:
\[
\text{NDVI} = \frac{\text{NIR} - \text{RED}}{\text{NIR} + \text{RED}}
\]
- **NIR**: Near Infrared Band (usually Band 5 for Landsat 8)
- **RED**: Red Band (usually Band 4 for Landsat 8)

### Steps to Calculate NDVI:
1. **Obtain the NIR and RED bands** from the satellite image.
2. **Use the NDVI formula** to calculate the index.

### Example Code:
```python
import numpy as np
import rasterio
from rasterio.plot import show
import matplotlib.pyplot as plt

# Load the RED and NIR bands (Band 4 and Band 5 for Landsat 8)
with rasterio.open('path_to_band_5.tif') as nir:
    nir_band = nir.read(1)

with rasterio.open('path_to_band_4.tif') as red:
    red_band = red.read(1)

# Calculate NDVI
ndvi = (nir_band - red_band) / (nir_band + red_band)

# Visualize NDVI result
plt.imshow(ndvi, cmap='RdYlGn')
plt.colorbar()
plt.title('NDVI')
plt.show()
```

### Why NDVI Matters:
- **High NDVI values (closer to +1)** indicate healthy, dense vegetation.
- **Low or negative NDVI values** suggest sparse vegetation, barren land, or water bodies.
- Commonly used in agriculture, environmental monitoring, and climate studies.

### Applications:
- Crop health monitoring
- Drought detection
- Vegetation analysis



#### **Conclusion**
In this lesson, we introduced the concept of **satellite imagery**, explained **remote sensing**, and covered the technical details of **TIFF** and **GeoTIFF** files. We explored how to download and process satellite data using the **EarthPy** Python package, focusing on **Landsat 8** data. By stacking and visualizing different spectral bands, we gain a deeper understanding of Earth's surface and can extract meaningful insights from the imagery.

This knowledge forms the basis for more advanced remote sensing applications, such as vegetation analysis, land use classification, and environmental monitoring.

## 🌍 Explore More Projects  
For more exciting machine learning and AI projects, visit **[The iVision](https://theivision.wordpress.com/)** and stay updated with the latest innovations and research! 🚀  

---
