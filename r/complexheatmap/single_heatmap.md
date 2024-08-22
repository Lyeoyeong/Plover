# Single Heatmap

### ComplexHeatmap Colors 

| **Feature**                    | **Parameter**                     |
|--------------------------------|-----------------------------------|
| **Custom Color Mapping**       | `col` with `colorRamp2()`         |
| **Control Outlier Effect**     | `col` with `colorRamp2()`         |
| **Same Color Mapping**         | `col` with `colorRamp2()`         |
| **Color Mapping for Discrete Values** | `col` with named vector    |
| **Set NA Color**               | `na_col`                          |
| **Set Heatmap Border**         | `border_gp`                       |
| **Set Cell Borders**           | `rect_gp` with `gpar()`           |

### ComplexHeatmap Titles 

| **Feature**                | **Parameter**                     |
|----------------------------|-----------------------------------|
| **Set Titles**             | `column_title`, `row_title`       |
| **Title Position**         | `column_title_side`               |
| **Customize Appearance**   | `column_title_gp`, `row_title_gp` |
| **Rotate Titles**          | `row_title_rot`, `column_title_rot` |
| **Template Titles**        | `%s` in `row_title`, `column_title` |
| **Set Background and Border** | `fill`, `border` in `gpar()`    |
| **Padding**                | `ht_opt$TITLE_PADDING`            |
| **Mathematical Titles**    | `expression()`                    |
| **Rich Text Titles**       | `gt_render()` with `gridtext`     |