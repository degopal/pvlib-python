pythonic-PVLIB
==============

pythonic-PVLIB is a fork of the [PVLIB_Python](https://github.com/Sandia-Labs/PVLIB_Python) project.

It provides a set of sometimes-well-documented and usually correct functions for simulating the performance of photovoltaic energy systems. The toolbox was originally developed at Sandia National Laboratories and it implements many of the models and methods developed at the Labs. 

We make some contributions to the PVLIB\_Python develop branch, and we pull some commits to PVLIB\_Python back into our fork. We hope that the Sandia PVLIB_Python becomes the community standard, but we had some different ideas about how things should be done and we needed to make progress faster than the collaborative cycle would allow. See below for a partial list of differences. 

We use this library to generate solar power forecasts for TEP, APS, and SVERI utilities, and to perform grid integration and variability studies for SVERI utilities. For more information, see [https://forecasting.uaren.org](https://forecasting.uaren.org) and [https://sveri.uaren.org](https://sveri.uaren.org).

The primary drawback to using this library over the official library is that, well, it's not the official Sandia library. If you use this library you need to attribute both UA and Sandia. Another drawback is that the structure of this library will look a lot different to people coming from the MATLAB world, which is either a good thing or a bad thing depending on your perspective. 


Key differences
===============
Here are some of the major differences between our fork and PVLIB\_Python. Note that some of these differences have been resolved in the PVLIB\_Python [develop branch](https://github.com/Sandia-Labs/PVLIB_Python/tree/develop).

* Remove pvl_ from module names.
* Locations are now referred to as objects, not structs.
* Return one DataFrame instead of a tuple of DataFrames.
* Specify time zones using a string from the standard IANA Time Zone Database naming conventions or using a pytz.timezone instead of an integer GMT offset. 
* Add PyEphem option to solar position calculations. 
* Consolidation of similar modules. For example, functions from ``pvl_clearsky_ineichen.py`` and ``pvl_clearsky_haurwitz.py``` have been consolidated into ``clearsky.py``. Similar consolidations have occured for airmass, solar position, and diffuse irradiance modules.
* Created ``planeofarray.py`` module with AOI, projection, and irradiance sum and calculation functions
* Removing Vars=Locals(); Expect...; var=pvl\_tools.Parse(Vars,Expect); pattern. Very few tests of input validitity remain. Garbage in, garbage out.
* Removing unnecssary and sometimes undesired behavior such as setting maximum zenith=90.
* ```__init__.py``` imports have been removed.
* Adding logging calls, removing print calls.
* Code in reviewed modules is mostly PEP8 compliant.
* Code in reviewed modules is mostly python 3 compatible.
* Changing function and module names so that they do not conflict.
* Not bothering with boilerplate unit test code such as ```unittest.main()```. 
* No wildcard imports.
* Improved documentation here and there.
* TMY data is not forced to 1987.

Pythonic code is easier to use, easier to maintain, and faster to develop. Adhering to PEP8 guidelines allows other python developers read code more quickly and accurately. 

Quick Start
============
Hopefully you're using [virtualenv](http://virtualenv.readthedocs.org/en/latest/) and [virtualenvwrapper](http://virtualenvwrapper.readthedocs.org). To install, clone this library and run

```
pip install .
```

Add ``-e`` to install in [develop mode](http://pip.readthedocs.org/en/latest/reference/pip_install.html#editable-installs).

```
# built-in imports
import sys
import datetime

# add-on imports
import pandas as pd

# pvlib imports
from pvlib.location import Location
import pvlib.solarposition
import pvlib.clearsky

# make a location
tus = Location(32.2, -111, 700, 'MST')

# make a pandas DatetimeIndex for some day
times = pd.date_range(start=datetime.datetime(2014,6,24), end=datetime.datetime(2014,6,25), freq='1Min')

# calculate the solar position
solpos = pvlib.solarposition.get_solarposition(times, tus, method='pyephem')
solpos.plot()

# calculate clear sky data
tus_cs = pvlib.clearsky.ineichen(times, tus, airmass_model='young1994')
tus_cs.plot()
```

Until the code is tested more thoroughly, you might find it useful to add:
```
import logging
logging.getLogger('pvlib').setLevel(logging.DEBUG)
```


Testing
============
Testing can be accomplished by running nosetests on the pvlib directory (or pvlib/tests):
```
nosetests -v pvlib
```
Unit test code should be placed in the ```pvlib/test``` directory. Each module should have its own test module. 

