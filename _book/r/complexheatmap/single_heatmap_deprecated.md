# Single Heatmap

#### 0. Style Guide

- `Function_name()`
- {% em %}Argument Name{% endem %}


#### 1. Creating a basic heatmap
- Use `Heatmap()` fundtion to generate basic heatmap.

```
library(ComplexHeatmap)

# Generate a random matrix
mat <- matrix(rnorm(100), nrow=10, ncol=10)
rownames(mat) <- paste0("Gene", 1:10)
colnames(mat) <- paste0("Sample", 1:10)

# Create a basic heatmap
Heatmap(mat)

# Draw in a loop
for(...) {
    ht = Heatmap(mat)
    draw(ht)
}
```

#### 2. Title and names

- Name: The legend title comes from the heatmap's {% em %}name{% endem %} , which also serves as its identifier.
- Column and Row Titles: use {% em %}column_title{% endem %} and {% em %}row_title{% endem %} to set titles for columns and rows, respectively.

```
Heatmap(mat, name = "mat", column_title = "I am a column title", row_title = "I am a row title")
```
- Title Position: Control the position of the column title with {% em %}column_title_side{% endem %}. It can be set to "top" (default) or "bottom".

```
Heatmap(mat, name = "mat", ..., column_title_side = "bottom")
```
- Title Appearance: Use {% em %}column_title_gp{% endem %} and {% em %}row_title_gp{% endem %} to customize the appearance of the titles. These parameters require a `gpar()` object.

```
Heatmap(mat, name = "mat", ..., column_title_gp = gpar(fontsize = 20, fontface = "bold"))
```
- Title Rotation: Rotate titles using {% em %}row_title_rot{% endem %} and {% em %}column_title_rot{% endem %}.

```
Heatmap(mat, name = "mat", ..., row_title_rot = 0)
```
- Dynamic Titles: When rows or columns are split, use templates in row_title or column_title to dynamically update titles. The `%s` placeholder is replaced by the cluster number or category.

```
Heatmap(mat, name = "mat", row_km = 2, row_title = "cluster_%s")
```
- Background Color and Border: Set the background color and border for titles using the {% em %}fill{% endem %} and {% em %}border{% endem %} parameters within column_title_gp or row_title_gp.

```
Heatmap(mat, name = "mat", ..., column_title_gp = gpar(fill = "red", col = "white", border = "blue"))
```


#### 3. Colors

- Color Mapping: Map specific data values to colors using the `colorRamp2()` function using {% em %}col{% endem %} parameter. It takes two arguments: a vector of *break values* and a vector of *corresponding colors*. 

```
library(circlize)

# Define a custom color mapping
col_fun = colorRamp2(c(-2, 0, 2), c("green", "white", "red"))

# Apply the color mapping to the heatmap
Heatmap(mat, name = "mat", col = col_fun)
```

- Discrete Values: Assign specific colors.

```
char_colors <- structure(1:4, names = letters[1:4])

# Heatmap for a discrete character matrix
Heatmap(discrete_char_mat, name = "mat", col = char_colors, ...)
```

- NA Values: Use {% em %}na_col{% endem %} argument to coltrol the color.

```
# Introduce NA values into the matrix
mat_with_na[sample(length(mat), 10)] <- NA

# Heatmap with NA values shown in black
Heatmap(mat_with_na, ..., na_col = "black", ...)
```

- Borders and Grid: Use {% em %}border_gp{% endem %} to customize the heatmap's border color and style with a gpar object. For cell borders, use {% em %}rect_gp{% endem %} with `grid::gpar()` to set color ({% em %}col{% endem %}), line width ({% em %}lwd{% endem %}), and line type ({% em %}lty{% endem %}).

```
 # Set borders for the entire heatmap
Heatmap(mat, name = "mat", border_gp = gpar(col = "black", lty = 2), ...)

# Set borders for individual cells
Heatmap(mat, name = "mat", rect_gp = gpar(col = "white", lwd = 2), ...)
```