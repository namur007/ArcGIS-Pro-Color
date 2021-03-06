# ArcGIS Pro Greedy Colorization

Toolbox applies the greedy color algorithm to polyline or polygon features. Goal is to symbolize the features with the fewest required colors.

This is best used in scenarios where there are a high cardinality column where individual colors do not matter. 

## Installation Requirements

Requirements

* ArcGIS Pro (Locally installed)
* networkx [https://networkx.org/](https://networkx.org/)

To run, the networkx library must be installed in a cloned arcgis pro environment. 

Clone the environment in your Python settings in ArcGIS Pro, or via the commandline and set this as the active python environment.

once cloned, install the networkx library.

    pip install networkx


## Usage

When run in ArcGIS Pro, the tool will use the open map and active view. Based on the selected layer and field, the dataset will be colored with the fewest colors required to avoid color collisions.

Feature symbology is updated in place. Data is unchanged.

Default search strategy is 'largest first', but a different one can be specified. Full options available [here](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.coloring.greedy_color.html#networkx.algorithms.coloring.greedy_color).

## Method

To calculate the minimum number of features, the toolbox must generate a graph representation of the dataset. As the graph represents a connected network, with nodes and links, the dataset is converted to this representation based on its spatial location.

For polyline feature, the expectation is that each end represents a connected location. For example, two lines that end on each other, are considered connected and will be linked together. This represented is with the lines as node element in a graph, linked with an arbitrary line. 

To calculate the graph, the polyline feature is reduced to a point feature class with a point representing each end of the line. Each line will contain two points, sharing the input field value, which will then create the link in the networkx graph.

A spatial join is completed to find the connected locations. The polygon features skips the previous step, and begins here. Similar assumption, in this case, if it touches, it is connected.

Finally, the greedy color algorithm is applied to the graph and the layer is updated via the CIM in ArcGIS Pro.

## Examples

### Polygon Layer

![Polygon](images/polygon_example.png)


### Polyline Layer

![Polyline](images/polyline_example.png)

## Pypi

Library is also installable with pip.

    pip install arcgiscolor

Then called with the following command.

    from ArcGISColor import ColorPolyline, ColorPolygon

If using this as a standalone, the same parameters of needing to supply an ArcGIS Project and layer apply.
    
    project = arcpy.mp.ArcGISProject("C:\\path\\to\\project.aprx")
    layer = project.listMaps('map_name')[0].listLayers('polygon_layer_name')[0]

    cpoly = ColorPolygon()
    cpoly.apply_colors(layer, 'field_name', 'largest_first')

    project.save()

Additional options are available this way, such as saving a graph, re-using a graph, etc.