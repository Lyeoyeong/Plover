# ComplexHeatmap Simple Guide

### About

- Install the up-to-date version by:
```
library(devtools)
install_github("jokergoo/ComplexHeatmap")
```

- For the full documentation, see: https://jokergoo.github.io/ComplexHeatmap-reference/book/index.html

### General Design

A single heatmap is composed by the heatmap body and the heatmap components.

### ComplexHeatmap Classes 

The ComplexHeatmap package is structured using several key classes:

| **Class Type**             | **Class Name**                    | **Description**                                           |
|----------------------------|-----------------------------------|-----------------------------------------------------------|
| **Main Class**             | `Heatmap`                         | Represents a single heatmap with data, titles, names, dendrograms, and annotations. |
| **Main Class**             | `HeatmapAnnotation`               | Defines row and column annotations, either as part of a heatmap or standalone. |
| **Main Class**             | `HeatmapList`                     | A collection of multiple heatmaps and their annotations.   |
| **Supporting Class**       | `SingleAnnotation`                | Represents a single row or column annotation within a heatmap. |
| **Supporting Class**       | `ColorMapping`                    | Manages the mapping of data values to colors for the heatmap and annotations. |
| **Supporting Class**       | `AnnotationFunction`              | Allows for creating custom, user-defined annotations.      |

