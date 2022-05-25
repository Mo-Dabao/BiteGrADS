# Gridded binary without `pdef`

## Test data

This test data is a dead product of CMA which the `DSET` of `ctl` file even doesn't match the actual data file name.

The content of `CHN_PRCP_HOUR_MERG_DISPLAY_0.1deg.lnx.ctl` is

```text
DSET ^SEVP_CLI_CHN_MERGE_FY2_PRE_HOUR_GRID_0.10-%y4%m2%d2%h2.grd
*
UNDEF -999.0
*
OPTIONS   little_endian  template
*
TITLE  China Hourly Merged Precipitation Analysis
*
xdef  700 linear  70.05  0.10
*
ydef  440 linear  15.05  0.10 
*
ZDEF     1 LEVELS 1  
*
TDEF 9999 LINEAR 00Z01Aug2010 1hr 
*
VARS 2                           
crain      1 00  CH01   combined analysis (mm/Hour)
gsamp      1 00  CH02   gauge numbers
ENDVARS
```

while the data file name is like `SURF_CLI_CHN_MERGE_CMP_PRE_HOUR_GRID_0.10-2018081707.grd`.

## Read

```python
from datetime import datetime

import pandas as pd
from bite_grads import GrADS

ctl_path = './test_data/CHN_PRCP_HOUR_MERG_DISPLAY_0.1deg.lnx.ctl'
dat_path = './test_data/SURF_CLI_CHN_MERGE_CMP_PRE_HOUR_GRID_0.10-2018081707.grd'
grads = GrADS(ctl_path)
time = pd.date_range(datetime.strptime(dat_path[-14:-4], '%Y%m%d%H'), periods=1)
grads.set_time(time)
ds = grads.open_dataset(dat_path)
```

The `ds` here will be

```
>>> ds
<xarray.Dataset>
Dimensions:  (t: 1, y: 440, x: 700, z: 1)
Coordinates:
  * t        (t) datetime64[ns] 2018-08-17T07:00:00
  * z        (z) float32 1.0
  * y        (y) float32 15.05 15.15 15.25 15.35 ... 58.65 58.75 58.85 58.95
  * x        (x) float32 70.05 70.15 70.25 70.35 ... 139.6 139.8 139.9 140.0
Data variables:
    crain    (t, y, x) float32 dask.array<chunksize=(1, 440, 700), meta=MaskedArray>
    gsamp    (t, y, x) float32 dask.array<chunksize=(1, 440, 700), meta=MaskedArray>
```

Through `grads.set_time(time)`, the `ds` carries the correct `t` coordinate.

## `need_var_attrs`

By default, `grads.open_dataset` doesn't provide the attributes of each DataArray.

```
>>> ds['crain']
<xarray.DataArray 'crain' (t: 1, y: 440, x: 700)>
dask.array<masked_values, shape=(1, 440, 700), dtype=float32, chunksize=(1, 440, 700), chunktype=numpy.MaskedArray>
Coordinates:
  * t        (t) datetime64[ns] 2018-08-17T07:00:00
  * y        (y) float32 15.05 15.15 15.25 15.35 ... 58.65 58.75 58.85 58.95
  * x        (x) float32 70.05 70.15 70.25 70.35 ... 139.6 139.8 139.9 140.0
```

If you need them, pass `need_var_attrs=True` to `grads.open_dataset`:

```
>>> ds = grads.open_dataset(dat_path, need_var_attrs=True)
>>> ds['crain']
<xarray.DataArray 'crain' (t: 1, y: 440, x: 700)>
dask.array<masked_values, shape=(1, 440, 700), dtype=float32, chunksize=(1, 440, 700), chunktype=numpy.MaskedArray>
Coordinates:
  * t        (t) datetime64[ns] 2018-08-17T07:00:00
  * y        (y) float32 15.05 15.15 15.25 15.35 ... 58.65 58.75 58.85 58.95
  * x        (x) float32 70.05 70.15 70.25 70.35 ... 139.6 139.8 139.9 140.0
Attributes:
    description:  CH01   combined analysis (mm/Hour)
```

## `need_crs`

You can get a `ds` that carries coordinate reference system information following CF-Conventions
by passing `need_crs=True` to `grads.open_dataset`:

```
>>> ds = grads.open_dataset(dat_path, need_crs=True, need_var_attrs=True)
>>> ds
<xarray.Dataset>
Dimensions:  (t: 1, y: 440, x: 700, z: 1)
Coordinates:
  * t        (t) datetime64[ns] 2018-08-17T07:00:00
  * z        (z) float32 1.0
  * y        (y) float32 15.05 15.15 15.25 15.35 ... 58.65 58.75 58.85 58.95
  * x        (x) float32 70.05 70.15 70.25 70.35 ... 139.6 139.8 139.9 140.0
    crs      float64 nan
Data variables:
    crain    (t, y, x) float32 dask.array<chunksize=(1, 440, 700), meta=MaskedArray>
    gsamp    (t, y, x) float32 dask.array<chunksize=(1, 440, 700), meta=MaskedArray>

>>> ds['crs']
<xarray.DataArray 'crs' ()>
array(nan)
Coordinates:
    crs      float64 nan
Attributes:
    crs_wkt:                      GEOGCRS["unknown",DATUM["unknown",ELLIPSOID...
    semi_major_axis:              6371200.0
    semi_minor_axis:              6371200.0
    inverse_flattening:           0.0
    reference_ellipsoid_name:     unknown
    longitude_of_prime_meridian:  0.0
    prime_meridian_name:          Greenwich
    geographic_crs_name:          unknown
    grid_mapping_name:            latitude_longitude
    earth_radius:                 6371200.0

>>> ds['x']
<xarray.DataArray 'x' (x: 700)>
array([ 70.05   ,  70.15   ,  70.25   , ..., 139.75   , 139.85   , 139.95001],
      dtype=float32)
Coordinates:
  * x        (x) float32 70.05 70.15 70.25 70.35 ... 139.6 139.8 139.9 140.0
    crs      float64 nan
Attributes:
    units:          degrees_east
    long_name:      longitude coordinate
    standard_name:  longitude


>>> ds['crain']
<xarray.DataArray 'crain' (t: 1, y: 440, x: 700)>
dask.array<masked_values, shape=(1, 440, 700), dtype=float32, chunksize=(1, 440, 700), chunktype=numpy.MaskedArray>
Coordinates:
  * t        (t) datetime64[ns] 2018-08-17T07:00:00
  * y        (y) float32 15.05 15.15 15.25 15.35 ... 58.65 58.75 58.85 58.95
  * x        (x) float32 70.05 70.15 70.25 70.35 ... 139.6 139.8 139.9 140.0
    crs      float64 nan
Attributes:
    description:   CH01   combined analysis (mm/Hour)
    grid_mapping:  crs
```

Because this data has no `pdef`, the `y x` coordinates are actually `lat lon`.
