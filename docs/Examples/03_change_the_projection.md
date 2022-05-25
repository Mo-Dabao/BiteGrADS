# Change the projection

After instantiating a `GrADS` by passing the path of ctl file, you can get the `proj_dict`.

```pycon
>>> from bite_grads import GrADS

>>> ctl_path = './test_data/CHN_PRCP_HOUR_MERG_DISPLAY_0.1deg.lnx.ctl'
>>> dat_path = './test_data/SURF_CLI_CHN_MERGE_CMP_PRE_HOUR_GRID_0.10-2018081707.grd'
>>> grads = GrADS(ctl_path)
>>> grads.ctl.title
'OUTPUT FROM WRF V4.1.2 MODEL'
>>> grads.proj_dict
{'proj': 'lcc', 'lat_1': 60.0, 'lat_2': 30.0, 'lat_0': 32.4, 'lon_0': 117.3, 'R': 6370000, 'x_0': 439528.6984313187, 'y_0': 439524.70233380556}
```

In this case, case the `title` in ctl is start with `'OUTPUT FROM WRF'`, `GrADS` will get the correct lcc projection parameters partly from the global attributes (like `MOAD_CEN_LAT`).

If you have already known your data's projection, and the `GrADS` didn't get the correct projection parameters, you can totally manually change the `proj_dict` like:

```pycon
>>> grads.set_proj_dict(
    {'proj': 'lcc', 'lat_1': 60.0, 'lat_2': 30.0, 'lat_0': 32.4, 'lon_0': 117.3, 'ellps': 'WGS84'}
)
```

No need to add the false easting or false northing (`x_0` or `y_0`), because `.set_proj_dict()` will calculate them automatically:

```pycon
>>> grads.proj_dict
{'proj': 'lcc', 'lat_1': 60.0, 'lat_2': 30.0, 'lat_0': 32.4, 'lon_0': 117.3, 'ellps': 'WGS84', 'x_0': 439549.26480943576, 'y_0': 439501.9685321826}
```
