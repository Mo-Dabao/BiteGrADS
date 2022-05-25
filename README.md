# BiteGrADS

[![Documentation Status](https://readthedocs.org/projects/bitegrads/badge/?version=latest)](https://bitegrads.readthedocs.io/en/latest/?badge=latest)

For reading GrADS format data. Read the docs [here](https://bitegrads.readthedocs.io/).

## Install

```shell
pip install bite_grads
```

Requires `xarray; dask; pyproj; numpy; pandas`.

## Supported ctl entries

[Components of a GrADS Data Descriptor File](http://cola.gmu.edu/grads/gadoc/descriptorfile.html)

- DTYPE
  - [x] gridded binary
  - [ ] station
  - [ ] ...
- OPTIONS
  - [x] byte order
  - [ ] template
  - [ ] ...
- [x] UNDEF
- PDEF
  - [x] lcc
  - [ ] ...
- [x] XDEF
- YDEF
  - [x] LINEAR
  - [x] LEVELS
  - [ ] GAUS*
- [x] ZDEF
- [x] TDEF
- VARS
  - [x] varname
  - [x] levs
  - [x] units
  - [ ] additional_codes
  - [x] description
- ATTRIBUTE METADATA
  - [x] varname
  - [ ] attribute_type
  - [x] attribute_name
  - [x] attribute_value
- [x] COMMENTS
- [ ] ...

## Features

- Lazy loading
- Manual correction of `TDEF`
- Manual correction of `PDEF`
- Projection information following CF-Conventions

## Else

Use [xgrads](https://github.com/miniufo/xgrads) for more features.

Use nc or other modern formats to store you data.
