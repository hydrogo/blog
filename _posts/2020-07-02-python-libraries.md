---
toc: true
layout: post
title: Python libraries I use in my research
description: Which you may (or may not) consider suitable for your research too
categories: [tips&tricks]
---

I do have a pretty skimpy experience in programming. As many pupils in Russia, I had been learning Pascal for a couple of years in school. Then it was Fortran in university. During my PhD in Russia, I had written a couple of scripts also in Fortran. After that, I have stick to R for a couple of projects and finished my small journey with Python, which I use as a primary programming language since 2015.

Python does have a pretty powerful standard library, but Python's real power is in the utilization of additional libraries that can provide you an endless functionality.  

Below I categorize Python libraries I use in my research. 

## General 
* [numpy](https://numpy.org/) -- the fundamental library for scientific computing.
* [pandas](https://pandas.pydata.org/) -- the fundamental library for data analysis.
* [scipy](https://www.scipy.org/) -- provides efficient numerical routines for, e.g., interpolation and optimization.


## Geographical analysis
* [geopandas]() -- `pandas` for geographical data. If you have `.shp` or `.geojson` files to analyze, you definitely should give `geopandas` a try!
* [xarray](https://geopandas.org/) -- the best library for manipulation of meteorological datasets provided in `netcdf` or `grib` formats. 
* [cdo](https://code.mpimet.mpg.de/projects/cdo/) -- I use it exclusively for remapping meteorological data from one grid to another.


## Radar-related (nowcasting)
* [wradlib](https://docs.wradlib.org/en/stable/) -- the core library for weather radar data processing.
* [opencv](https://opencv.org/) -- the fundamental library for computer vision problems (e.g., computation of optical flow).
* [scikit-image](https://scikit-image.org/) -- image processing without a pain.
* [trackpy](soft-matter.github.io/trackpy/) -- tracking particles made easy.


## Machine learning
* [scikit-learn](https://scikit-learn.org/stable/) -- the core package for machine learning applications.
* [tensorflow](https://www.tensorflow.org/) -- deep neural networks development in a few of lines of code with built-in [keras](https://keras.io/) API.
* [fbprophet](https://facebook.github.io/prophet/) -- time-series forecasting library developed by Facebook. I use it for water levels forecasting in [OpenLevels](https://openlevels.github.io/).


## Plotting
* [matplotlib](https://matplotlib.org/) -- the core library for plotting.
* [seaborn](https://seaborn.pydata.org/) -- nicer plots, `pandas` support.
* [bokeh](https://docs.bokeh.org/) -- interactive plots. For example, [runoff forecasts](https://openforecast.github.io/10240.html) produced by [OpenForecast](https://openforecast.github.io/).
* [folium](https://python-visualization.github.io/folium/) -- interactive maps, e.g. like in [1](https://openforecast.github.io/), [2](https://openlevels.github.io/), [3](https://hydrogo.github.io/beats-of-the-era/), [4](https://hydrogo.github.io/4Y11M2D/).
* [branca](https://github.com/python-visualization/branca) -- customization of `html` popups in `folium`.


## Code development
* [jupyter](https://jupyter.org/) -- interactive IDE running in your web-browser.
* [pylint](https://www.pylint.org/) -- library developed for code analysis. 


## Misc
* [h5py](https://www.h5py.org/) -- make use of HDF files.
* [numba](http://numba.pydata.org/) -- accelerates your code for fast compute.
* [requests](https://requests.readthedocs.io/en/master/) -- API requests for humans.


## Dependencies (non-direct usage)
* pytables -- HDF files processing with `pandas`.
* xlrd -- reading xls with `pandas`.
* dask -- recommended `xarray` dependency.
* netCDF4 -- recommended `xarray` dependency.
* bottleneck -- recommended `xarray` dependency.
* cartopy -- recommended `xarray` dependency.
* pynio -- recommended `xarray` dependency.
* pseudonetcdf -- recommended `xarray` dependency.
* nc-time-axis -- recommended `xarray` dependency.
* cfgrib -- reading `grib` files.
* eccodes -- `cfgrib` dependency.
* descartes -- plotting polygons with `geopandas`.
* pims -- recommended `trackpy` dependency.

## The environment

You can use [conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-from-an-environment-yml-file) to install all the libraries mentioned above in one isolated programming environment. Everything you need is just to create a file `environment.yml` copy-pasting the text below and then run `conda env create -f environment.yml` in your terminal. Have fun!

```yaml
name: megaenv
channels:
  - conda-forge
dependencies:
  - numpy
  - pandas
  - scipy
  - geopandas
  - xarray
  - cdo
  - wradlib
  - opencv
  - scikit-image
  - trackpy
  - scikit-learn
  - tensorflow
  - fbprophet
  - matplotlib
  - seaborn
  - bokeh
  - folium
  - branca
  - jupyter
  - pylint
  - h5py
  - numba
  - requests
  - pytables 
  - xlrd 
  - dask 
  - netCDF4 
  - bottleneck 
  - cartopy 
  - pynio 
  - pseudonetcdf 
  - nc-time-axis 
  - cfgrib 
  - eccodes 
  - descartes 
  - pims
```

After the installation (can take really long), you need to activate the environment you created by running `conda activate megaenv` in your terminal. Have fun!