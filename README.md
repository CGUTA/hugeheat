# HUGEHEAT
### A pipeline to make a visualization heatmap of large matrices with float values stored as csv
forked from: https://github.com/CGUTA/hugeheat

---

### PROCEDURE

the pipeline reduces the size of the matrix by tiling it with bins of `--size width,height` (box sampling) cells covered by each bin get summarized into an average cell treating negative and positive values separately


the positive and negative values in the reduced matrix cells are separately scaled to fit a 0-255 range for displaying color intensities. The `--threshold` percentile value becomes 255 and higher values are truncated to 255. This facilitates contrast to be appreciated in the heatmap.

A shade of blue or red is assigned to each cell coresponding to the magnitude of the negative or positive values and a final color is created for the heatmap cell by linear interpolation of these two colors
Finally, the resulting colors are tinted with black inversely proportional to the absolute sum of the encoded negative and positive values


The heatmap is plotted using `--background_color_u8` as the color for cells with 0 positive and 0 negative values should they exist. each cell is plotted over n,n `--pixels`, sometimes is desirable to exagerate one dimension of the cell for visualization purposes.

---

### PARAMETERS

```
REQUIRED:
	--input_file \ #[path or glob] csv of numeric matrix

OPTIONAL:
	--outdir \ #[path] where you want the heatmap to be printed (default .)
	--size \ #[Int,Int] # width and height of the binning tile (default 1,1 no binning)
	--pixel \ #[Int, Int] # width and height in pixels that cell will be rendered into (default 1,1 no distortion)
	--background_color_u8 \ #[0-255] from black to white (default 155)
	--threshold \ #[0-1] truncating threshold (default 0.99)
	--intensity \ #[true/false] smaller values get less saturated colors; set false to cancel this adjustment (default true)

COLORS:
	--bicolor \ #[str] where str is one or two comma separated hex colors (e.g. #FFFFFF #000000,#CAFFEE) these colors are then used instead of default for positive and negative numbers.

GAPS:
Gap file: A TSV with at least one column header 'position' where gaps should be drawn (data.table header)
	--column_gaps \ #[path] to Gap file, must not be same name as --row_gaps (see grid)
	--row_gaps \ #[path] to Gap file, must not be same name as --column_gaps (see grid)
	--grid \ #[path] to Gap file, will be applied both row and column wise

	--default_gap_size \ #[Int] pixel size to be applied to any gap where no specific sizes were provided (default 5)
	--column_gap_size \ #[Int] pixel size to be applied to row gaps (overrides default)
	--row_gap_size \ #[Int] pixel size to be applied to column gaps (override default)

NEXTFLOW:
	-profile conda # to run with conda (default: standard)
```


---
### EXAMPLE USAGE

run with docker:
```
docker build -t hugeheat https://raw.githubusercontent.com/loipf/hugeheat/master/docker/Dockerfile

nextflow run loipf/hugeheat --input_file my_big_matrix_with_header.csv --outdir . --size 2,1 -with-docker hugeheat
```

more detailed:
```
nextflow run loipf/hugeheat \
	--input_file my_big_matrix_with_header.csv \
	--outdir . \
	--size 2,1 \ # bin the rows in sets of two leave cols alone 
	--pixel 1,5 \ # plot every cell into a rectangle 5 pixels wide and one pixel of height
	--threshold 1 \ # values are scaled to 0-255 but no truncation ocurrs (truncated to max value)
	--grid gap_file.csv \ # with positions where the grid will be drawn
	--column_gap_size 2 \ # row gaps are drawn with the default gap size column gaps with 2 pixel
```



---
### EXAMPLE FILES

my_big_matrix_with_header.csv:
```
""	gene_1	gene_2	gene_3
sample_1	0.27	1.63	0.84
sample_2	-0.83	0.15	0.37
sample_3	0.56	0.21	0.19
```

column_gaps.tsv:
```
position
237
359
480
550
```



---
### ILLUSTRATIVE EXAMPLE OF BINNING PROCEDURE


INPUT FILE:
```
	a	b
a	0.5	1
b	0.5	-1
c	1	0
```

binning 2,1 would result in this reduced matrix (averages are calculated for incomplete bins as well)
```
	a	b
a+b	0.5,0	0.5,-0.5  <- average is calculated separately for positives and negatives
c	1,0	0,0
```
binning 1,2 would result in this reduced matrix
```
	a+b
a	0.75,0
b	0.25,-0.5 
c	0.5,0
```
