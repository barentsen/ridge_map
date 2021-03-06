ridge_map
=========

*Ridge plots of ridges*
-----------------------

A library for making ridge plots of... ridges. Choose a location, get an elevation map, and tinker with it to make something beautiful. Heavily inspired from [Zach Cole's beautiful art](https://twitter.com/ZachACole/status/1121554541101477889), [Jake Vanderplas' examples](https://github.com/jakevdp/altair-examples/blob/master/notebooks/PulsarPlot.ipynb), and Joy Division's [1979 album "Unknown Pleasures"](https://gist.github.com/ColCarroll/68e29c92b766418b0a4497b4eb2ecba4).

Uses [matplotlib](https://matplotlib.org/), [SRTM.py](https://github.com/tkrajina/srtm.py), [numpy](https://www.numpy.org/), and [scikit-image](https://scikit-image.org/) (for lake detection).

Installation
------------

Install from github with

```bash
pip install git+git://github.com/colcarroll/ridge_map.git
```

Want to help?
-------------

- I feel like I am missing something easy or obvious with lake/road/river/ocean detection, but what I've got gets me most of the way there. If you hack on the `RidgeMap.preprocessor` method and find something nice, I would love to hear about it!
- As far as I can tell, there is no way to change the color of a line in matplotlib dynamically. I would love to color the lines by elevation.
- Did you make a cool map? Open an issue with the code and I will add it to the examples.

Examples
--------

The API allows you to download the data once, then edit the plot yourself,
or allow the default processor to help you.

### New Hampshire by default

Plotting with all th defaults should give you a map of my favorite mountains.

```python
from ridge_map.ridge_map import RidgeMap

RidgeMap().plot_map()
```

![png](examples/white_mountains.png)

### Download once and tweak settings

First you download the elevation data to get an array with shape
`(num_lines, elevation_pts)`, then you can use the preprocessor
to automatically detect lakes, rivers, and oceans, and scale the elevations.
Finally, there are options to style the plot

```python
rm = RidgeMap((11.098251,47.264786,11.695633,47.453630))
values = rm.get_elevation_data(num_lines=150)
values=rm.preprocess(
    values=values,
    lake_flatness=2,
    water_ntile=10,
    vertical_ratio=240)
rm.plot_map(values=values,
            label='Karwendelgebirge',
            label_y=0.1,
            label_x=0.55,
            label_size=40,
            linewidth=1)
```

![png](examples/karwendelgebirge.png)

### Plot with colors!

If you are plotting a town that is super into burnt orange for whatever
reason, you can respect that choice.

```python
rm = RidgeMap((-97.794285,30.232226,-97.710171,30.334509))
values = rm.get_elevation_data(num_lines=80)
rm.plot_map(values=rm.preprocess(values=values, water_ntile=12, vertical_ratio=40),
            label='Austin\nTexas',
            label_x=0.75,
            linewidth=6,
            line_color='orange')
```

![png](examples/austin.png)

### Plot with even more colors!

The line color accepts a [matplotlib colormap](https://matplotlib.org/gallery/color/colormap_reference.html#sphx-glr-gallery-color-colormap-reference-py), so really feel free to go to town.

```python
rm = RidgeMap((-123.107300,36.820279,-121.519775,38.210130))
values = rm.get_elevation_data(num_lines=150)
rm.plot_map(values=rm.preprocess(values=values, lake_flatness=3, water_ntile=50, vertical_ratio=30),
            label='The Bay\nArea',
            label_x=0.1,
            line_color = plt.get_cmap('spring'))
```

![png](examples/san_francisco.png)

### How do I find a bounding box?

I have been using [this website](http://bboxfinder.com). I find an area I like, draw a rectangle, then copy and paste the coordinates into the `RidgeMap` constructor.

```python
rm = RidgeMap((-73.509693,41.678682,-73.342838,41.761581))
values = rm.get_elevation_data()
rm.plot_map(values=rm.preprocess(values=values, lake_flatness=2, water_ntile=2, vertical_ratio=60),
            label='Kent\nConnecticut',
            label_y=0.7,
            label_x=0.65,
            label_size=40)
```

![png](examples/kent.png)

### What about really flat areas?

You might really have to tune the `water_ntile` and `lake_flatness` to get the water right. You can set them to 0 if you do not want any water marked.

```python
rm = RidgeMap((-71.167374,42.324286,-70.952454, 42.402672))
values = rm.get_elevation_data(num_lines=50)
rm.plot_map(values=rm.preprocess(values=values, lake_flatness=4, water_ntile=30, vertical_ratio=20),
            label='Cambridge\nand Boston',
            label_x=0.75,
            label_size=40,
            linewidth=1)
```

![png](examples/boston.png)

### What about Walden Pond?

It is that pleasant kettle pond in the bottom right of this map, looking entirely comfortable with its place in Western writing and thought.

```python
rm = RidgeMap((-71.418858,42.427511,-71.310024,42.481719))
values = rm.get_elevation_data(num_lines=100)
rm.plot_map(values=rm.preprocess(values=values, water_ntile=15, vertical_ratio=30),
            label='Concord\nMassachusetts',
            label_x=0.1,
            label_size=30)
```

![png](examples/concord.png)

### Do you play nicely with other matplotlib figures?

Of course! If you really want to put a stylized elevation map in a scientific plot you are making, I am not going to stop you, and will actually make it easier for you. Just pass an argument for `ax` to `RidgeMap.plot_map`.

```python
import numpy as np
fig, axes = plt.subplots(ncols=2, figsize=(20, 5))
x = np.linspace(-2, 2)
y = x * x

axes[0].plot(x, y, 'o')

rm = RidgeMap()
rm.plot_map(label_size=24, background_color=(1, 1, 1), ax=axes[1])
```

![png](examples/multiaxis.png)
