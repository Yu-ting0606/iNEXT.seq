
<!-- README.md is generated from README.Rmd. Please edit that file -->

`iNEXT.seq` (iNterpolation and EXTrapolation for UniFrac distance) is an
R package. In this document, we provide a quick introduction
demonstrating how to run `iNEXT.seq`. Detailed information about
`iNEXT.seq` functions is provided in the iNEXT.seq manual, also
available in [CRAN](https://cran.r-project.org/package=iNEXT.seq). An
online version of
[iNEXT.seq-online](http://chao.stat.nthu.edu.tw/wordpress/software_download/iNEXT.seq-online/)
is also available for users without an R background.

`iNEXT.seq` provides a new way to measure UniFrac distance, by applying
dissimilarity measures based on Chiu’s et al.’s multiple assemblages
decomposition (Chiu, Jost, and Chao (2014)). And we mainly consider
three measures of Hill numbers of order q: species richness (`q = 0`),
Shannon diversity (`q = 1`, the exponential of Shannon entropy) and
Simpson diversity (`q = 2`, the inverse of Simpson concentration).

`iNEXT.seq` uses the observed sample of OTU count data (called the
“reference sample”) to calculate UniFrac distance estimates and the
associated confidence intervals for coverage-based rarefaction and
extrapolation (R/E) curve.

`iNEXT.seq` computes dissimilarity as UniFrac distance estimates by
doing monotonic transformation on phylogenetic beta diversity for
rarefied and extrapolated samples, based on standardized levels of
sample completeness (measured by sample coverage). This coverage-based
sampling curve plots the diversity estimates with respect to sample
coverage.

## HOW TO CITE iNEXT.seq

If you publish your work based on the results from the `iNEXT.seq`
package, you should make references to the following methodology paper:

- 

## SOFTWARE NEEDED TO RUN iNEXT.seq IN R

- Required: [R](https://cran.r-project.org/)
- Suggested: [RStudio
  IDE](https://www.rstudio.com/products/RStudio/#Desktop)

## HOW TO RUN iNEXT.seq:

The `iNEXT.seq` package is available from
[CRAN](https://cran.r-project.org/package=iNEXT.seq) and can be
downloaded with a standard R installation procedure or can be downloaded
from Anne Chao’s
[iNEXT.seq_github](https://github.com/yayunteng/iNEXT.seq) using the
following commands. For a first-time installation, an additional
visualization extension package (`ggplot2`) must be installed and
loaded.

``` r
## install iNEXT.seq package from CRAN
install.packages("iNEXT.seq")
## install the latest version from github
install.packages('devtools')
library(devtools)
install_github('yayunteng/iNEXT.seq')
## import packages
library(iNEXT.seq)
```

Here are two main functions we provide in this package :

- **iNEXTseq** : Computing dissimilarity estimates as UniFrac distances
  at specified sample coverage.

- **ggiNEXTseq** : Visualizing the output from the function `iNEXTseq`

## MAIN FUNCTION: iNEXTseq()

We first describe the main function `iNEXTseq()` with default arguments:

``` r
iNEXTseq(data, q = c(0, 1, 2), level = NULL, nboot = 10, conf = 0.95,
             PDtree = NULL, PDreftime = NULL)
```

The arguments of this function are briefly described below, and will be
explained in more details by illustrative examples in later text. This
main function computes UniFrac distance estimates of order q at
specified sample coverage. By default of <code>level = NULL</code>, then
this function computes estimates up to one (for q = 1, 2) or up to the
coverage of double the reference sample size (for q = 0).

<table style="width:100%;">
<colgroup>
<col width="20%">
<col width="80%">
</colgroup>
<thead>
<tr class="header">
<th align="center">
Argument
</th>
<th align="left">
Description
</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="center">
<code>data</code>
</td>
<td align="left">
OTU count data can be input as a <code>matrix/data.frame</code> (species
by assemblages), or a list of <code>matrices/data.frames</code>, each
matrix represents species-by-assemblages abundance matrix.
</td>
</tr>
<tr class="even">
<td align="center">
<code>q</code>
</td>
<td align="left">
a numerical vector specifying the diversity orders. Default is
<code>c(0, 1, 2)</code>.
</td>
</tr>
<tr class="odd">
<td align="center">
<code>level</code>
</td>
<td align="left">
A numerical vector specifying the particular value of sample coverage
(between 0 and 1 when <code>base = “coverage”</code>). <code>level =
1</code> (<code>base = “coverage”</code>) means complete coverage (the
corresponding diversity represents asymptotic diversity). If <code>level
= NULL</code>, then this function computes UniFrac distance estimates up
to one (for <code>q = 1, 2</code>) or up to the coverage of double the
reference sample size (for <code>q = 0</code>).
</td>
</tr>
<tr class="even">
<td align="center">
<code>nboot</code>
</td>
<td align="left">
a positive integer specifying the number of bootstrap replications when
assessing sampling uncertainty and constructing confidence intervals.
Bootstrap replications are generally time consuming. Enter
<code>0</code> to skip the bootstrap procedures. Default is
<code>10</code>. Note that large bootstrap replication needs more run
time.
</td>
</tr>
<tr class="odd">
<td align="center">
<code>conf</code>
</td>
<td align="left">
a positive number \< 1 specifying the level of confidence interval.
Default is <code>0.95</code>.
</td>
</tr>
<tr class="even">
<td align="center">
<code>PDtree</code>
</td>
<td align="left">
a <code>phylo</code>, a phylogenetic tree in Newick format for all
observed species in the pooled assemblage.
</td>
</tr>
<tr class="odd">
<td align="center">
<code>PDreftime</code>
</td>
<td align="left">
a numerical value specifying reference time for PD. Default is
<code>NULL</code> (i.e., the age of the root of PDtree).
</td>
</tbody>
</table>

This function returns an `"iNEXTseq"` object which can be further used
to make plots using the function `ggiNEXTseq()` to be described below.

## DATA FORMAT/INFORMATION

Data should be provided as Individual-based abundance data.

Input data for each region with several assemblages/sites include
samples species abundances in an empirical sample of n individuals
(“reference sample”). When there are N assemblages in a region, input
data consist of a list with an S by N abundance matrix; For M regions
consisting N assemblages, input data should be M lists of S by N
abundance matrix.

A data set (a microbiome sample take from tongue dorsum and buccal
mucosa) is included in `iNEXT.seq` package for illustration. The data
consist a species-by-assemblages matrix with two assemblages (tongue and
cheek).For the data, the following commands display how to compute
UniFrac distance estimate at specified sample coverage.

Run the following code to view `tongue_cheek` OTU count data: (Here we
only show the first ten rows for the matrix)

``` r
data("tongue_cheek")
tongue_cheek
```

    #> $tongue_cheek
    #>              Cheek Tongue
    #> OTU_97.10006     2      0
    #> OTU_97.10025     1      0
    #> OTU_97.1008      0      2
    #> OTU_97.10090     2      0
    #> OTU_97.101       1      1
    #> OTU_97.10216     1      0
    #> OTU_97.10217     3      0
    #> OTU_97.10220     1      0
    #> OTU_97.10238     0      1
    #> OTU_97.1026      1      0
    #> OTU_97.10264     1      0
    #> OTU_97.10278     1      0
    #> OTU_97.10280     1      0
    #> OTU_97.10325     0      1
    #> OTU_97.10349     0      1
    #> OTU_97.10355     0      1
    #> OTU_97.10384     1      0
    #> OTU_97.10415     0      1
    #> OTU_97.1043      1      0
    #> OTU_97.10439    15      0
    #> OTU_97.10463     2      1
    #> OTU_97.10494     2      2
    #> OTU_97.10497     1      1
    #> OTU_97.10499     1      0
    #> OTU_97.105       1      0
    #> OTU_97.1059      3      0
    #> OTU_97.10611     1      0
    #> OTU_97.10728     1      0
    #> OTU_97.10730     0      2
    #> OTU_97.10783     0      1
    #> OTU_97.10787     0      1
    #> OTU_97.10798     1      0
    #> OTU_97.10819     0      1
    #> OTU_97.10834     1      0
    #> OTU_97.10864     1      0
    #> OTU_97.10872     0      1
    #> OTU_97.1089      0      4
    #> OTU_97.10910     0      1
    #> OTU_97.10928     0      1
    #> OTU_97.10946     3      0
    #> OTU_97.10977     0      1
    #> OTU_97.11022     0      2
    #> OTU_97.11037     0      1
    #> OTU_97.11078     1      0
    #> OTU_97.11091     2      0
    #> OTU_97.11121     0      6
    #> OTU_97.1114      0      1
    #> OTU_97.11140     0      1
    #> OTU_97.11156     2      1
    #> OTU_97.11165     0      1
    #> OTU_97.11180     1      0
    #> OTU_97.11249     2      0
    #> OTU_97.11251     1      0
    #> OTU_97.11270     1      0
    #> OTU_97.1133      0      1
    #> OTU_97.11365     1      8
    #> OTU_97.1140      0      1
    #> OTU_97.11404     1      2
    #> OTU_97.1141      1      0
    #> OTU_97.11425     0      4
    #> OTU_97.11451     2      0
    #> OTU_97.11468     0      3
    #> OTU_97.11480     0      1
    #> OTU_97.11486     1      1
    #> OTU_97.115       1      1
    #> OTU_97.11501     1      0
    #> OTU_97.11510     3      6
    #> OTU_97.11551     0      1
    #> OTU_97.1157      0      3
    #> OTU_97.116      25      0
    #> OTU_97.1162      0      1
    #> OTU_97.11660     0      1
    #> OTU_97.1169      1      0
    #> OTU_97.11691     0      3
    #> OTU_97.11715     0      1
    #> OTU_97.1176      0      1
    #> OTU_97.11765     1      5
    #> OTU_97.11770     0      4
    #> OTU_97.11810     0      2
    #> OTU_97.11822     1      0
    #> OTU_97.11840     2      0
    #> OTU_97.11847     0      1
    #> OTU_97.11855     5      3
    #> OTU_97.11885     2      0
    #> OTU_97.11904     1      0
    #> OTU_97.11905     0      2
    #> OTU_97.11911     1      0
    #> OTU_97.11929     1      0
    #> OTU_97.11943     1      0
    #> OTU_97.11945     1      0
    #> OTU_97.11961     1      0
    #> OTU_97.11965     0      6
    #> OTU_97.11985     1      0
    #> OTU_97.11988     0      5
    #> OTU_97.120      11      0
    #> OTU_97.12003     1      0
    #> OTU_97.1205      1      0
    #> OTU_97.1209      1      1
    #> OTU_97.1210      1      0
    #> OTU_97.1211      1      0
    #> OTU_97.12131     1      0
    #> OTU_97.12179     0      1
    #> OTU_97.12221     1      0
    #> OTU_97.12247     0      1
    #> OTU_97.1225      0      8
    #> OTU_97.12259     1      0
    #> OTU_97.12292     4      0
    #> OTU_97.12297     0     76
    #> OTU_97.12299     0      2
    #> OTU_97.12306     0      1
    #> OTU_97.12308     1      0
    #> OTU_97.12315     1      0
    #> OTU_97.12371     0      3
    #> OTU_97.1239      1      0
    #> OTU_97.12408     1      0
    #> OTU_97.1248      5      0
    #> OTU_97.12480     0      3
    #> OTU_97.12488     0      1
    #> OTU_97.125       1      0
    #> OTU_97.12522     1      0
    #> OTU_97.12530     0      2
    #> OTU_97.12560     0      1
    #> OTU_97.12572     1      0
    #> OTU_97.12613     2      0
    #> OTU_97.12621     2      0
    #> OTU_97.12753     1      0
    #> OTU_97.12787     0      1
    #> OTU_97.1279      0      1
    #> OTU_97.12802     0      2
    #> OTU_97.12814     1      0
    #> OTU_97.12818     0      1
    #> OTU_97.12836     1      0
    #> OTU_97.12852     1      0
    #> OTU_97.12863     0     10
    #> OTU_97.12869     0      2
    #> OTU_97.12879     0      1
    #> OTU_97.12895     1      0
    #> OTU_97.12902     1      0
    #> OTU_97.12934     1      0
    #> OTU_97.12988     1      0
    #> OTU_97.13        1      0
    #> OTU_97.13038     0      1
    #> OTU_97.13039     0      1
    #> OTU_97.1307      1      0
    #> OTU_97.13076     2      0
    #> OTU_97.13091     0      1
    #> OTU_97.13115     1      0
    #> OTU_97.13124     0      1
    #> OTU_97.13173     0      1
    #> OTU_97.13179     0      1
    #> OTU_97.1318     19      0
    #> OTU_97.132       0      1
    #> OTU_97.13200     0      1
    #> OTU_97.1321      0      3
    #> OTU_97.1324      1      0
    #> OTU_97.13253     0      1
    #> OTU_97.13268     1      0
    #> OTU_97.13272    18      0
    #> OTU_97.13296     1      0
    #> OTU_97.13300     1      0
    #> OTU_97.13324     0      2
    #> OTU_97.13384     1      0
    #> OTU_97.13391     0      2
    #> OTU_97.13416     1      0
    #> OTU_97.1343      0      1
    #> OTU_97.1348      0      1
    #> OTU_97.13480     1      0
    #> OTU_97.13493     0      2
    #> OTU_97.13563     0      2
    #> OTU_97.13577     0      1
    #> OTU_97.13582     0      1
    #> OTU_97.13584     0      1
    #> OTU_97.13604     0      1
    #> OTU_97.13605     1      0
    #> OTU_97.13626     0      1
    #> OTU_97.13642     1      0
    #> OTU_97.1368      1      0
    #> OTU_97.13685     0      2
    #> OTU_97.13714     0     10
    #> OTU_97.13719     2      0
    #> OTU_97.13793     1      0
    #> OTU_97.13837     0      2
    #> OTU_97.13839     0      1
    #> OTU_97.13846     0      1
    #> OTU_97.13867     2      0
    #> OTU_97.13887     1      0
    #> OTU_97.13895     0      1
    #> OTU_97.13925     1      0
    #> OTU_97.13938     1      0
    #> OTU_97.13972     0      1
    #> OTU_97.13996     0     16
    #> OTU_97.14        0      1
    #> OTU_97.14002     1      0
    #> OTU_97.14058     1      0
    #> OTU_97.14071     3      0
    #> OTU_97.14099     0      3
    #> OTU_97.14127     0      1
    #> OTU_97.14128     0      1
    #> OTU_97.14186     0      1
    #> OTU_97.142      13      0
    #> OTU_97.14217     1      0
    #> OTU_97.14250     0      1
    #> OTU_97.14267     0      1
    #> OTU_97.14286     2      0
    #> OTU_97.143       4      0
    #> OTU_97.14310     0      1
    #> OTU_97.14340     1      0
    #> OTU_97.14358     1      0
    #> OTU_97.14368     1      0
    #> OTU_97.14441     0      2
    #> OTU_97.14481     0      1
    #> OTU_97.14495     0      1
    #> OTU_97.14523     0      1
    #> OTU_97.14540     1      0
    #> OTU_97.1455      1      0
    #> OTU_97.1456      2      0
    #> OTU_97.14599     1      0
    #> OTU_97.14606     1      0
    #> OTU_97.1462      1      0
    #> OTU_97.14653     0      2
    #> OTU_97.14664     0      1
    #> OTU_97.14680     0      2
    #> OTU_97.1469      0      1
    #> OTU_97.14698     0      1
    #> OTU_97.14773     2      0
    #> OTU_97.14789     0      1
    #> OTU_97.14804     0      1
    #> OTU_97.14809     1      0
    #> OTU_97.14811     1      0
    #> OTU_97.1482      0      1
    #> OTU_97.14837     1      5
    #> OTU_97.1485      0      1
    #> OTU_97.14868     0      1
    #> OTU_97.14891     1      0
    #> OTU_97.14893     2      1
    #> OTU_97.14901     0      1
    #> OTU_97.14911     0      3
    #> OTU_97.14915     0      1
    #> OTU_97.14950     0      2
    #> OTU_97.14957     0      1
    #> OTU_97.14967     0      1
    #> OTU_97.14982     0      1
    #> OTU_97.15        1      0
    #> OTU_97.1500      0      1
    #> OTU_97.15000     0      1
    #> OTU_97.15017     6      0
    #> OTU_97.15064     0      1
    #> OTU_97.15074     0      1
    #> OTU_97.15086     0      1
    #> OTU_97.15130     1      0
    #> OTU_97.15132     4      0
    #> OTU_97.15162     0      1
    #> OTU_97.15163     1      0
    #> OTU_97.15221     0      1
    #> OTU_97.15236     0      2
    #> OTU_97.15247     1      0
    #> OTU_97.153       0      1
    #> OTU_97.1530      0      2
    #> OTU_97.15320     1      0
    #> OTU_97.15331     0      4
    #> OTU_97.1539      0      1
    #> OTU_97.1540      0      1
    #> OTU_97.15416     1      0
    #> OTU_97.15433     1      0
    #> OTU_97.15437     1      1
    #> OTU_97.15438     1      0
    #> OTU_97.15446     0      4
    #> OTU_97.15451     1      0
    #> OTU_97.15508     1      0
    #> OTU_97.15539     0      1
    #> OTU_97.15634     3      2
    #> OTU_97.15665     2      0
    #> OTU_97.15666     0      1
    #> OTU_97.15690     3      0
    #> OTU_97.157      31      0
    #> OTU_97.15723     1      0
    #> OTU_97.15724     0      1
    #> OTU_97.15752     0      1
    #> OTU_97.15796     1      0
    #> OTU_97.15806     0      1
    #> OTU_97.15809     0      2
    #> OTU_97.15856     1      0
    #> OTU_97.15857     0      5
    #> OTU_97.15871     1      0
    #> OTU_97.1589      2      0
    #> OTU_97.15908     0      1
    #> OTU_97.15913     2      0
    #> OTU_97.15928     0      1
    #> OTU_97.15950     1      0
    #> OTU_97.15960     1      0
    #> OTU_97.15980     0      1
    #> OTU_97.16        0      2
    #> OTU_97.1601      1      0
    #> OTU_97.16043     1      0
    #> OTU_97.16065     2      0
    #> OTU_97.16095     1      0
    #> OTU_97.1615      0      2
    #> OTU_97.16159     2      0
    #> OTU_97.16163     0      1
    #> OTU_97.1617      0      1
    #> OTU_97.16180     1      0
    #> OTU_97.16182     1      0
    #> OTU_97.16190     1      0
    #> OTU_97.16224     0      2
    #> OTU_97.16235     0      1
    #> OTU_97.16238     0      1
    #> OTU_97.16243     0      2
    #> OTU_97.16264     1      0
    #> OTU_97.16292     0      2
    #> OTU_97.16301     0      1
    #> OTU_97.16321     0      1
    #> OTU_97.16326     0      1
    #> OTU_97.16328     1      0
    #> OTU_97.16338     0      4
    #> OTU_97.16340     0      1
    #> OTU_97.16372     0      1
    #> OTU_97.16428     0      1
    #> OTU_97.1644      1      0
    #> OTU_97.16608     1      0
    #> OTU_97.1662      2      0
    #> OTU_97.16624     1      0
    #> OTU_97.16670     2      0
    #> OTU_97.16683     0     15
    #> OTU_97.16699     0      1
    #> OTU_97.1671      1      0
    #> OTU_97.16725     1      0
    #> OTU_97.16741     0      1
    #> OTU_97.16751     1      0
    #> OTU_97.16780     1      0
    #> OTU_97.16819     1      0
    #> OTU_97.16860     0      1
    #> OTU_97.16864     1      0
    #> OTU_97.16894     1      2
    #> OTU_97.1698      0      1
    #> OTU_97.16980     0      1
    #> OTU_97.16989     5      1
    #> OTU_97.170       0      2
    #> OTU_97.17002     1      0
    #> OTU_97.17011     0      1
    #> OTU_97.1704      0     29
    #> OTU_97.1705      0      1
    #> OTU_97.17067     1      0
    #> OTU_97.17070     0      1
    #> OTU_97.17082     0      1
    #> OTU_97.1713      0      1
    #> OTU_97.17139     1      2
    #> OTU_97.17145     1      0
    #> OTU_97.1718      1      0
    #> OTU_97.17183     1      0
    #> OTU_97.17220     1      0
    #> OTU_97.17247     2      0
    #> OTU_97.17251     0      1
    #> OTU_97.17295     0      1
    #> OTU_97.17317     0     15
    #> OTU_97.17319     1      0
    #> OTU_97.17322     1      0
    #> OTU_97.17343     1      0
    #> OTU_97.1736      0     41
    #> OTU_97.17367     4      0
    #> OTU_97.17439     0      1
    #> OTU_97.17441     0      1
    #> OTU_97.17465     1      0
    #> OTU_97.1747      1      0
    #> OTU_97.17507     0      3
    #> OTU_97.17605     1      0
    #> OTU_97.17606     0      5
    #> OTU_97.17645     3      0
    #> OTU_97.17669     1      0
    #> OTU_97.1770      1      0
    #> OTU_97.17702     0      1
    #> OTU_97.17722     1      0
    #> OTU_97.17726     0      1
    #> OTU_97.17727     0      1
    #> OTU_97.17747     0      6
    #> OTU_97.17779     1      0
    #> OTU_97.17839     1      0
    #> OTU_97.17886     0      2
    #> OTU_97.17888     0      5
    #> OTU_97.17891     0      1
    #> OTU_97.17911     0      1
    #> OTU_97.17920     0      1
    #> OTU_97.1801      0      6
    #> OTU_97.18039     0      1
    #> OTU_97.18060     2      0
    #> OTU_97.18127     0      1
    #> OTU_97.1815      0      4
    #> OTU_97.18245     0      2
    #> OTU_97.18298     0      1
    #> OTU_97.1832      0      1
    #> OTU_97.18344     0      1
    #> OTU_97.18379     1      7
    #> OTU_97.18388     0      6
    #> OTU_97.18395     1      1
    #> OTU_97.184       1      0
    #> OTU_97.18412     0      1
    #> OTU_97.18413     1      0
    #> OTU_97.18431     1      0
    #> OTU_97.1848      1      0
    #> OTU_97.18492     0      1
    #> OTU_97.18496     0      1
    #> OTU_97.18500     1      0
    #> OTU_97.18545     0      2
    #> OTU_97.18555     0      7
    #> OTU_97.18577     0      3
    #> OTU_97.18605     0      1
    #> OTU_97.18619     1      0
    #> OTU_97.18645     0      1
    #> OTU_97.18646     0      5
    #> OTU_97.18658     1      0
    #> OTU_97.18675     0      1
    #> OTU_97.18691     1      0
    #> OTU_97.18692     0      1
    #> OTU_97.1870      1      0
    #> OTU_97.18715     2      1
    #> OTU_97.1876      0      1
    #> OTU_97.18769     1      1
    #> OTU_97.18778     1      1
    #> OTU_97.188       0      1
    #> OTU_97.18820     1      0
    #> OTU_97.18830     1      1
    #> OTU_97.1884      0      2
    #> OTU_97.18842     0      1
    #> OTU_97.18877     2      0
    #> OTU_97.18880     0      1
    #> OTU_97.18898     2      0
    #> OTU_97.18920     1      0
    #> OTU_97.18938     0      1
    #> OTU_97.1896      1      0
    #> OTU_97.18979     1      0
    #> OTU_97.18982     0      2
    #> OTU_97.19010     0      1
    #> OTU_97.1906      0      1
    #> OTU_97.19101     0      4
    #> OTU_97.19150     0      1
    #> OTU_97.19157     0      1
    #> OTU_97.19162     1      0
    #> OTU_97.19205     1      1
    #> OTU_97.19241     2      1
    #> OTU_97.19244     1      0
    #> OTU_97.19265     0      1
    #> OTU_97.1927      0      1
    #> OTU_97.19307     0      1
    #> OTU_97.19369     1      0
    #> OTU_97.19439     2      0
    #> OTU_97.19448     0      2
    #> OTU_97.19551     3      0
    #> OTU_97.19562     0      1
    #> OTU_97.1957      0      2
    #> OTU_97.19636     0      2
    #> OTU_97.19649     1      0
    #> OTU_97.19680     0      1
    #> OTU_97.19743     0      1
    #> OTU_97.19758     0      3
    #> OTU_97.19778     8      4
    #> OTU_97.19793     0      3
    #> OTU_97.19801     1      0
    #> OTU_97.19805     0      1
    #> OTU_97.19813     0      2
    #> OTU_97.1982      4      3
    #> OTU_97.19822     1      0
    #> OTU_97.19850     1      0
    #> OTU_97.19907     0      1
    #> OTU_97.19918     0      5
    #> OTU_97.1992      1      0
    #> OTU_97.19949     0      1
    #> OTU_97.20        5    107
    #> OTU_97.200       1      0
    #> OTU_97.20019     1      0
    #> OTU_97.20028     0      1
    #> OTU_97.2007      0      1
    #> OTU_97.20146     0      1
    #> OTU_97.202       1      0
    #> OTU_97.20200     0      2
    #> OTU_97.20207     0      2
    #> OTU_97.20253     0      4
    #> OTU_97.20264     0      1
    #> OTU_97.20271     0      2
    #> OTU_97.20293     0      6
    #> OTU_97.20339     0      1
    #> OTU_97.20358     1      0
    #> OTU_97.20361     0      2
    #> OTU_97.20373     4      0
    #> OTU_97.204       1      0
    #> OTU_97.20403     1      0
    #> OTU_97.20425     0      2
    #> OTU_97.20499     2      0
    #> OTU_97.20527     1      0
    #> OTU_97.20536     8      0
    #> OTU_97.20539     2      0
    #> OTU_97.20612     0      1
    #> OTU_97.20623     0      3
    #> OTU_97.20647     0      3
    #> OTU_97.20668     0      2
    #> OTU_97.2068      1      0
    #> OTU_97.20684     5      0
    #> OTU_97.20717     0      1
    #> OTU_97.20722     0      1
    #> OTU_97.20766     1      0
    #> OTU_97.208       1      0
    #> OTU_97.20802     1      0
    #> OTU_97.20838     0      2
    #> OTU_97.20896     1      0
    #> OTU_97.20908     4      0
    #> OTU_97.20911     0      2
    #> OTU_97.2093      1      0
    #> OTU_97.20930     1      0
    #> OTU_97.20931     3      0
    #> OTU_97.20946     0      1
    #> OTU_97.20973     0      1
    #> OTU_97.210       1      0
    #> OTU_97.21048     2      0
    #> OTU_97.21070     1      0
    #> OTU_97.21073     2      0
    #> OTU_97.21093     0      1
    #> OTU_97.211       2      0
    #> OTU_97.21104     0      1
    #> OTU_97.2113      0      1
    #> OTU_97.21138     3      1
    #> OTU_97.21139     0      1
    #> OTU_97.21154     3      2
    #> OTU_97.21206     0      1
    #> OTU_97.21223     1      0
    #> OTU_97.21232     0      3
    #> OTU_97.21261     2      0
    #> OTU_97.21263     0      1
    #> OTU_97.21277     0      1
    #> OTU_97.21280     0      1
    #> OTU_97.213       0      4
    #> OTU_97.21322     0      1
    #> OTU_97.21338     0      1
    #> OTU_97.21342     1      0
    #> OTU_97.21376     1      0
    #> OTU_97.21420     2      0
    #> OTU_97.2143      1      0
    #> OTU_97.21456     1      0
    #> OTU_97.21466     1      0
    #> OTU_97.21471     1      0
    #> OTU_97.21513     0      1
    #> OTU_97.21554     1      0
    #> OTU_97.21560     1      0
    #> OTU_97.21579     2      0
    #> OTU_97.21630     1      0
    #> OTU_97.21634     0      1
    #> OTU_97.21635     1      0
    #> OTU_97.21658     0      1
    #> OTU_97.21744     1      1
    #> OTU_97.21778     1      0
    #> OTU_97.21789     1      0
    #> OTU_97.21794     1      0
    #> OTU_97.21876     1      0
    #> OTU_97.21994     1      0
    #> OTU_97.21997     0      1
    #> OTU_97.22038     2      0
    #> OTU_97.22054     2      0
    #> OTU_97.2209      0      3
    #> OTU_97.22090     2      0
    #> OTU_97.22119     0     27
    #> OTU_97.2213      0      9
    #> OTU_97.22130     1      0
    #> OTU_97.22140     1      0
    #> OTU_97.22144     0      1
    #> OTU_97.22200     0      2
    #> OTU_97.22232     1      0
    #> OTU_97.22299     1      0
    #> OTU_97.22300     1      2
    #> OTU_97.2232      1      0
    #> OTU_97.22325     1      0
    #> OTU_97.22374     0      1
    #> OTU_97.22378     0      4
    #> OTU_97.22389    24      0
    #> OTU_97.224       1      3
    #> OTU_97.22447     5      0
    #> OTU_97.22466     0      1
    #> OTU_97.22482     0      5
    #> OTU_97.22491     3      0
    #> OTU_97.2252      1      0
    #> OTU_97.22539     2      0
    #> OTU_97.22553     1      0
    #> OTU_97.22560     0      1
    #> OTU_97.22562     0      1
    #> OTU_97.22563     0     12
    #> OTU_97.22616     0      1
    #> OTU_97.22625     1      0
    #> OTU_97.22626     1      0
    #> OTU_97.2269      0      4
    #> OTU_97.2272      1      1
    #> OTU_97.22776     1      0
    #> OTU_97.22777     1      0
    #> OTU_97.22786     0      1
    #> OTU_97.22804     1      0
    #> OTU_97.2282      0      1
    #> OTU_97.22827     1      0
    #> OTU_97.22873     1      0
    #> OTU_97.22888     1      0
    #> OTU_97.22894     2      0
    #> OTU_97.22903     0      5
    #> OTU_97.22978     1      1
    #> OTU_97.23        0      3
    #> OTU_97.23002     0      1
    #> OTU_97.23003     1      0
    #> OTU_97.23009     0      1
    #> OTU_97.23011     0      1
    #> OTU_97.23043     1      0
    #> OTU_97.23057     7      0
    #> OTU_97.23066     0      1
    #> OTU_97.2307      1      1
    #> OTU_97.23154     0      2
    #> OTU_97.23158     1      0
    #> OTU_97.23161     1      0
    #> OTU_97.23168    20      0
    #> OTU_97.23178     1      0
    #> OTU_97.2318      1      0
    #> OTU_97.23203     5      1
    #> OTU_97.23237     0      4
    #> OTU_97.23244     0      1
    #> OTU_97.23269     0      1
    #> OTU_97.23317     0      5
    #> OTU_97.23318     1      1
    #> OTU_97.23330     0      1
    #> OTU_97.23366     2      0
    #> OTU_97.23378     0      2
    #> OTU_97.2338      0      1
    #> OTU_97.23386     0      1
    #> OTU_97.23395     1      0
    #> OTU_97.23413     0      2
    #> OTU_97.23423     2      0
    #> OTU_97.23438     1      0
    #> OTU_97.23454     2      1
    #> OTU_97.23478     0      1
    #> OTU_97.23494     1      0
    #> OTU_97.235       2      0
    #> OTU_97.23528     1      0
    #> OTU_97.23536     1      0
    #> OTU_97.2357      1      0
    #> OTU_97.23576     3      0
    #> OTU_97.23596     1      0
    #> OTU_97.236       0      1
    #> OTU_97.23608     0      1
    #> OTU_97.2362      0      3
    #> OTU_97.23667    11     21
    #> OTU_97.2367      1      0
    #> OTU_97.2368      0      1
    #> OTU_97.2369      0      1
    #> OTU_97.237       0      1
    #> OTU_97.23791     0      1
    #> OTU_97.23841     1      0
    #> OTU_97.23879     0      3
    #> OTU_97.23897     0      2
    #> OTU_97.2391      1      0
    #> OTU_97.23929     1      0
    #> OTU_97.23939     0      1
    #> OTU_97.23953     1      0
    #> OTU_97.24        3      0
    #> OTU_97.24002     1      0
    #> OTU_97.24025     3      0
    #> OTU_97.24026     1      0
    #> OTU_97.24028     1      0
    #> OTU_97.24055     6      0
    #> OTU_97.24077     0      1
    #> OTU_97.24116     0      1
    #> OTU_97.2412      0      1
    #> OTU_97.24122     0      1
    #> OTU_97.24133     1      0
    #> OTU_97.24135     0      2
    #> OTU_97.24168     0      1
    #> OTU_97.24169     0      1
    #> OTU_97.24203     0      1
    #> OTU_97.24225     0      1
    #> OTU_97.2423      1      0
    #> OTU_97.24239     0      1
    #> OTU_97.24251     0      1
    #> OTU_97.24289     0      1
    #> OTU_97.24309     1      0
    #> OTU_97.24323     0      1
    #> OTU_97.24326     0      1
    #> OTU_97.24342     0      1
    #> OTU_97.24351     1      0
    #> OTU_97.24365     1      1
    #> OTU_97.24375     0      1
    #> OTU_97.24378     0      1
    #> OTU_97.24396     0      1
    #> OTU_97.24400     1      0
    #> OTU_97.24496     0      1
    #> OTU_97.24553     0      2
    #> OTU_97.2456      0      1
    #> OTU_97.24640     1      0
    #> OTU_97.24656     0      2
    #> OTU_97.24694     1      0
    #> OTU_97.24705     1      0
    #> OTU_97.2473      1      0
    #> OTU_97.24733     1      0
    #> OTU_97.24753     1      1
    #> OTU_97.2478      0      1
    #> OTU_97.24792     1      1
    #> OTU_97.24812     1      0
    #> OTU_97.24813     0      1
    #> OTU_97.24822     0      1
    #> OTU_97.24836     0     12
    #> OTU_97.24853     0      2
    #> OTU_97.24905     1      0
    #> OTU_97.24911     0      1
    #> OTU_97.24923     1      0
    #> OTU_97.24956     0      1
    #> OTU_97.2498      0      1
    #> OTU_97.24998     0      1
    #> OTU_97.25        1      0
    #> OTU_97.25001     1      0
    #> OTU_97.25022     6      0
    #> OTU_97.25041     2      0
    #> OTU_97.25045     3      0
    #> OTU_97.25047     1      0
    #> OTU_97.2505      0      2
    #> OTU_97.2512      0      1
    #> OTU_97.25141     0      2
    #> OTU_97.25176     1      0
    #> OTU_97.25225     2      0
    #> OTU_97.25254     0      3
    #> OTU_97.25256     0      7
    #> OTU_97.25261     1      0
    #> OTU_97.25264     2      0
    #> OTU_97.25271     0      1
    #> OTU_97.25287     0      2
    #> OTU_97.25297     0      2
    #> OTU_97.25327     2      0
    #> OTU_97.25338     2      0
    #> OTU_97.25370     0      2
    #> OTU_97.25390     0      1
    #> OTU_97.254       2      0
    #> OTU_97.25409     1      0
    #> OTU_97.25412     0      1
    #> OTU_97.25413     1      0
    #> OTU_97.25432     1      0
    #> OTU_97.25442     0      2
    #> OTU_97.25460     0      3
    #> OTU_97.25486     1      8
    #> OTU_97.25526     1      0
    #> OTU_97.25542     1      0
    #> OTU_97.25728     2      0
    #> OTU_97.25760     1      0
    #> OTU_97.25775     0      3
    #> OTU_97.25796     0      1
    #> OTU_97.25816     0      7
    #> OTU_97.25880     2      0
    #> OTU_97.25913     1      1
    #> OTU_97.25956     0      5
    #> OTU_97.25999     0      1
    #> OTU_97.26022     1      0
    #> OTU_97.26034     1      0
    #> OTU_97.26041     1      0
    #> OTU_97.26051     0      1
    #> OTU_97.26089     0      1
    #> OTU_97.261       1      0
    #> OTU_97.26105     0      1
    #> OTU_97.26118     2      2
    #> OTU_97.26130     0      1
    #> OTU_97.26135     2      0
    #> OTU_97.26167     0      1
    #> OTU_97.2619      0      1
    #> OTU_97.26198     0      1
    #> OTU_97.26223     0      1
    #> OTU_97.2623      0      1
    #> OTU_97.26244     0      1
    #> OTU_97.26245     0      1
    #> OTU_97.26267     0      1
    #> OTU_97.26330     0      1
    #> OTU_97.2637      1      3
    #> OTU_97.26373     0      1
    #> OTU_97.26379     0      2
    #> OTU_97.26394     1      0
    #> OTU_97.26425     1      0
    #> OTU_97.26473     1      0
    #> OTU_97.2648      0      1
    #> OTU_97.26482     0      2
    #> OTU_97.26500     0      1
    #> OTU_97.26526     1      0
    #> OTU_97.26541     0      1
    #> OTU_97.26542     1      0
    #> OTU_97.2656      1      0
    #> OTU_97.26595     0      2
    #> OTU_97.266       4      0
    #> OTU_97.26602     3      1
    #> OTU_97.26613     0      1
    #> OTU_97.26615     0      1
    #> OTU_97.26625     1      0
    #> OTU_97.26639     1      0
    #> OTU_97.26643     0      1
    #> OTU_97.26651     2      0
    #> OTU_97.26664     1      9
    #> OTU_97.26684     0      1
    #> OTU_97.26696     0      1
    #> OTU_97.26714     0      1
    #> OTU_97.26724     0      1
    #> OTU_97.26751     0      1
    #> OTU_97.26795     1      0
    #> OTU_97.26813     0     11
    #> OTU_97.26826     1      0
    #> OTU_97.26829     1      0
    #> OTU_97.2684      1      0
    #> OTU_97.26846     1      0
    #> OTU_97.26862     2      1
    #> OTU_97.26878     1      0
    #> OTU_97.26880     1      0
    #> OTU_97.269       1      5
    #> OTU_97.2691      1      0
    #> OTU_97.26921     0      1
    #> OTU_97.26923     0      3
    #> OTU_97.26928     1      0
    #> OTU_97.26931     0      1
    #> OTU_97.26932     0      2
    #> OTU_97.2695      1      0
    #> OTU_97.2698      1      0
    #> OTU_97.27005     3      1
    #> OTU_97.27021     0      1
    #> OTU_97.27077     1      0
    #> OTU_97.27087     0      1
    #> OTU_97.27125     2      0
    #> OTU_97.27137     1      0
    #> OTU_97.27144     1      0
    #> OTU_97.27176     1      0
    #> OTU_97.27199     0      1
    #> OTU_97.27215     0      1
    #> OTU_97.27232     1      0
    #> OTU_97.27256     0      2
    #> OTU_97.27270     0      2
    #> OTU_97.27284     2      0
    #> OTU_97.273       0     12
    #> OTU_97.2732      2      0
    #> OTU_97.27328     0      3
    #> OTU_97.27330     2      3
    #> OTU_97.27353     1      0
    #> OTU_97.27358     1      0
    #> OTU_97.27384     0      1
    #> OTU_97.27389     0      1
    #> OTU_97.274       2     11
    #> OTU_97.27405     0      1
    #> OTU_97.27417     1      0
    #> OTU_97.27420     2      1
    #> OTU_97.27427     1      0
    #> OTU_97.2746      1      0
    #> OTU_97.27468     0      1
    #> OTU_97.27510     0      1
    #> OTU_97.27529     1      0
    #> OTU_97.27544     0      1
    #> OTU_97.27560     0      1
    #> OTU_97.27588     1      0
    #> OTU_97.27605     0      1
    #> OTU_97.27621     1      0
    #> OTU_97.27631     0      1
    #> OTU_97.27634     0      1
    #> OTU_97.27636     2      0
    #> OTU_97.27699     1      0
    #> OTU_97.27701     3      2
    #> OTU_97.27711     1      0
    #> OTU_97.27750     0      1
    #> OTU_97.27771     1      1
    #> OTU_97.278       3      0
    #> OTU_97.27804     0      2
    #> OTU_97.27811     0      1
    #> OTU_97.27816     1      0
    #> OTU_97.27871     0      1
    #> OTU_97.27887     1      0
    #> OTU_97.27897     1      1
    #> OTU_97.27927     2      0
    #> OTU_97.27939     0      1
    #> OTU_97.27973     1      0
    #> OTU_97.27977     1      0
    #> OTU_97.27987     0      1
    #> OTU_97.27994     0      1
    #> OTU_97.2800      0      3
    #> OTU_97.28059     1      0
    #> OTU_97.28063     0      2
    #> OTU_97.28074     0      1
    #> OTU_97.28096     0      1
    #> OTU_97.28107     0      1
    #> OTU_97.28115     1      1
    #> OTU_97.2812      0      5
    #> OTU_97.28137     1      0
    #> OTU_97.28153     1      0
    #> OTU_97.28185     1      0
    #> OTU_97.28217     2      0
    #> OTU_97.28244     0      7
    #> OTU_97.28268     1      0
    #> OTU_97.28290     1      0
    #> OTU_97.28305     0      2
    #> OTU_97.28342     1      0
    #> OTU_97.28348     2      1
    #> OTU_97.28350     2      0
    #> OTU_97.28416     0      6
    #> OTU_97.28466     1      0
    #> OTU_97.28470     1      0
    #> OTU_97.2849      1      0
    #> OTU_97.28494     0      1
    #> OTU_97.28513     0      1
    #> OTU_97.28541     4      0
    #> OTU_97.28595     2      0
    #> OTU_97.28601     1      0
    #> OTU_97.2861      1      0
    #> OTU_97.28614     2      0
    #> OTU_97.28633     0      5
    #> OTU_97.28642     1      0
    #> OTU_97.28656     0      4
    #> OTU_97.28688     0      7
    #> OTU_97.28759     1      0
    #> OTU_97.28788     1      0
    #> OTU_97.28805     0      1
    #> OTU_97.28825     0      1
    #> OTU_97.28841     1      0
    #> OTU_97.28852     0      1
    #> OTU_97.28854     1      0
    #> OTU_97.28862     0      4
    #> OTU_97.28867     1      0
    #> OTU_97.2889      5      0
    #> OTU_97.28898     0      1
    #> OTU_97.2890      0      1
    #> OTU_97.28911     0      1
    #> OTU_97.28946     0      1
    #> OTU_97.28971     0      1
    #> OTU_97.28972     0      1
    #> OTU_97.28976     0      1
    #> OTU_97.28980     1      5
    #> OTU_97.29        1      0
    #> OTU_97.29042     0      4
    #> OTU_97.29047     1      0
    #> OTU_97.2905      0      1
    #> OTU_97.29082     1      0
    #> OTU_97.29110     3      0
    #> OTU_97.29129     0      1
    #> OTU_97.29157     0      1
    #> OTU_97.29158     2      0
    #> OTU_97.29162     0      1
    #> OTU_97.29165     1      0
    #> OTU_97.29209     0      1
    #> OTU_97.29232     1      0
    #> OTU_97.29277     1      0
    #> OTU_97.29302     1      0
    #> OTU_97.29314     1      0
    #> OTU_97.29315     1      0
    #> OTU_97.29342     1      0
    #> OTU_97.29347     0      2
    #> OTU_97.29361     1      0
    #> OTU_97.29370     0      2
    #> OTU_97.29384     5      0
    #> OTU_97.29397     1      0
    #> OTU_97.2940      0      1
    #> OTU_97.29415    10      0
    #> OTU_97.29418     0      1
    #> OTU_97.29433     5      0
    #> OTU_97.29459     1      0
    #> OTU_97.29478     0      1
    #> OTU_97.29529     2      0
    #> OTU_97.29530     1      0
    #> OTU_97.29537     0      2
    #> OTU_97.29540     1      0
    #> OTU_97.29545     0      3
    #> OTU_97.29560     3      0
    #> OTU_97.29574     1      0
    #> OTU_97.29580     1      0
    #> OTU_97.29589     0      1
    #> OTU_97.29595     1      0
    #> OTU_97.29641     3      0
    #> OTU_97.29646     0      2
    #> OTU_97.29665     1      0
    #> OTU_97.29674     0      2
    #> OTU_97.29676     0      1
    #> OTU_97.29686     0      2
    #> OTU_97.29692     0      1
    #> OTU_97.29718     1      0
    #> OTU_97.2972      3      0
    #> OTU_97.29720     0      1
    #> OTU_97.29722     0      2
    #> OTU_97.29729     1      1
    #> OTU_97.29734     1      0
    #> OTU_97.29749     1      0
    #> OTU_97.29771     1      0
    #> OTU_97.29775     0      1
    #> OTU_97.29790     0      1
    #> OTU_97.29798     3      0
    #> OTU_97.29852     0      1
    #> OTU_97.29880     0      2
    #> OTU_97.29888     0      1
    #> OTU_97.29899     3      0
    #> OTU_97.29953     1      0
    #> OTU_97.2996      1      0
    #> OTU_97.29976     1      0
    #> OTU_97.30        0      1
    #> OTU_97.30064     0      1
    #> OTU_97.3010      1      0
    #> OTU_97.30112     0      1
    #> OTU_97.30114     1      0
    #> OTU_97.30121     0      1
    #> OTU_97.30150     1      0
    #> OTU_97.30180     1      0
    #> OTU_97.30196     0      1
    #> OTU_97.30215     0      3
    #> OTU_97.30220     1      0
    #> OTU_97.30257     8      0
    #> OTU_97.30275     1      0
    #> OTU_97.30277     0      1
    #> OTU_97.30293     1      0
    #> OTU_97.30294     0      2
    #> OTU_97.30296     0      1
    #> OTU_97.30301     0      1
    #> OTU_97.30307     1      2
    #> OTU_97.30309     0      1
    #> OTU_97.30313     1      0
    #> OTU_97.30317     4      0
    #> OTU_97.30325     0      1
    #> OTU_97.30326     1      0
    #> OTU_97.3033      1      0
    #> OTU_97.30359     1      0
    #> OTU_97.30360     0      1
    #> OTU_97.30394     0      2
    #> OTU_97.30428     0      1
    #> OTU_97.30431     0      1
    #> OTU_97.3046      1     11
    #> OTU_97.30481     3      0
    #> OTU_97.30516     1      0
    #> OTU_97.30521     0      2
    #> OTU_97.30525     1      0
    #> OTU_97.30535     1      0
    #> OTU_97.30551    18      0
    #> OTU_97.30552     1      0
    #> OTU_97.3058      0      1
    #> OTU_97.30593     1      0
    #> OTU_97.30598     0      1
    #> OTU_97.30610     0      1
    #> OTU_97.30632     0      1
    #> OTU_97.30638     1      5
    #> OTU_97.30654     1      0
    #> OTU_97.30672     1      0
    #> OTU_97.30676     2      0
    #> OTU_97.30698     0      1
    #> OTU_97.30700     0      1
    #> OTU_97.30703     0      1
    #> OTU_97.30723     1      0
    #> OTU_97.30737     1      0
    #> OTU_97.30750     7      0
    #> OTU_97.30766     0      6
    #> OTU_97.30777     1      2
    #> OTU_97.30789     0      3
    #> OTU_97.30796     1      0
    #> OTU_97.3084      0      3
    #> OTU_97.30858     0      2
    #> OTU_97.3088      0      1
    #> OTU_97.30911     1      0
    #> OTU_97.30928     1      0
    #> OTU_97.30978     1      0
    #> OTU_97.30983     0      1
    #> OTU_97.31        1      0
    #> OTU_97.31000     0      1
    #> OTU_97.31031     0      1
    #> OTU_97.31074     1      0
    #> OTU_97.31076     1      0
    #> OTU_97.311       0      1
    #> OTU_97.31107     0      2
    #> OTU_97.31109     1      0
    #> OTU_97.31118     0      2
    #> OTU_97.3112      0      2
    #> OTU_97.31134     1      0
    #> OTU_97.3116      0      3
    #> OTU_97.3117      1      0
    #> OTU_97.31179     3      0
    #> OTU_97.31199     1      0
    #> OTU_97.31207     2      3
    #> OTU_97.31214     2      0
    #> OTU_97.3124      0      1
    #> OTU_97.31247    23      0
    #> OTU_97.31261     3      0
    #> OTU_97.31265     8      0
    #> OTU_97.31271     0      5
    #> OTU_97.31293     1      0
    #> OTU_97.31296     1      0
    #> OTU_97.31301     0      1
    #> OTU_97.31315     0      4
    #> OTU_97.31364     0      1
    #> OTU_97.31370     0      1
    #> OTU_97.31383     0      1
    #> OTU_97.31387     1      0
    #> OTU_97.31406     1      0
    #> OTU_97.31407     3      0
    #> OTU_97.3142      1      2
    #> OTU_97.31433     1      1
    #> OTU_97.31458    22      0
    #> OTU_97.31471     2      0
    #> OTU_97.31488     0      2
    #> OTU_97.31490     1      0
    #> OTU_97.31497     0      2
    #> OTU_97.31539     0      1
    #> OTU_97.31550     0      1
    #> OTU_97.31562     0      3
    #> OTU_97.31585     0      1
    #> OTU_97.31586     0      1
    #> OTU_97.31603     0      1
    #> OTU_97.31612     2      0
    #> OTU_97.31618     2      0
    #> OTU_97.31637     2      0
    #> OTU_97.31657     0      1
    #> OTU_97.31658     1      2
    #> OTU_97.31688     0      2
    #> OTU_97.31697     0      1
    #> OTU_97.31704     0      1
    #> OTU_97.31709     1      0
    #> OTU_97.31717     0      1
    #> OTU_97.31742     3      1
    #> OTU_97.31768     1      0
    #> OTU_97.31801     0      4
    #> OTU_97.31818     0      2
    #> OTU_97.3182      1      0
    #> OTU_97.31875     5      0
    #> OTU_97.31891     0      1
    #> OTU_97.31895     0      2
    #> OTU_97.3193      0      2
    #> OTU_97.31959     1      0
    #> OTU_97.31969     1      0
    #> OTU_97.31975     1      0
    #> OTU_97.3198      1      0
    #> OTU_97.31986     0      1
    #> OTU_97.31996     1      0
    #> OTU_97.32        3     49
    #> OTU_97.32002     2     16
    #> OTU_97.32024     0      1
    #> OTU_97.32026     1      0
    #> OTU_97.32063     1      0
    #> OTU_97.3207      3      0
    #> OTU_97.32083     0      4
    #> OTU_97.32094     2      0
    #> OTU_97.321       1      0
    #> OTU_97.32105     1      0
    #> OTU_97.32106     0      1
    #> OTU_97.32117     1      0
    #> OTU_97.32137     0      9
    #> OTU_97.32140     0      1
    #> OTU_97.32190     2      0
    #> OTU_97.32218     1      0
    #> OTU_97.32228     0      7
    #> OTU_97.32230     0      1
    #> OTU_97.32238     3      0
    #> OTU_97.3227      0      1
    #> OTU_97.32295     1      3
    #> OTU_97.32296     0      3
    #> OTU_97.32298     1      0
    #> OTU_97.32318     1      0
    #> OTU_97.32328     0      4
    #> OTU_97.32339     0      6
    #> OTU_97.3236      0      1
    #> OTU_97.32369     0      2
    #> OTU_97.32370     0      2
    #> OTU_97.32383     0      3
    #> OTU_97.32413     6      0
    #> OTU_97.32416     1      1
    #> OTU_97.32420     0      7
    #> OTU_97.32441     1      0
    #> OTU_97.32455     0      3
    #> OTU_97.32470     1      1
    #> OTU_97.32477     0      2
    #> OTU_97.32484     0      1
    #> OTU_97.32506     1      0
    #> OTU_97.32525     0      2
    #> OTU_97.32533     0      4
    #> OTU_97.32534     0      1
    #> OTU_97.32537     1      0
    #> OTU_97.32541     1      0
    #> OTU_97.32560     1      0
    #> OTU_97.32620     0      1
    #> OTU_97.32621     7      0
    #> OTU_97.32683     0      1
    #> OTU_97.32700     0      1
    #> OTU_97.32701     0      4
    #> OTU_97.32761     1      0
    #> OTU_97.32763     2      0
    #> OTU_97.32764     0      2
    #> OTU_97.32767     2      4
    #> OTU_97.32788     1      0
    #> OTU_97.32830     0      8
    #> OTU_97.32838     1      0
    #> OTU_97.32873     7      1
    #> OTU_97.32879     0      1
    #> OTU_97.32925     1      0
    #> OTU_97.32929     0      1
    #> OTU_97.32956     0      2
    #> OTU_97.32980     6      0
    #> OTU_97.32990     0      1
    #> OTU_97.33001     0      4
    #> OTU_97.33021     0      1
    #> OTU_97.3304      4      0
    #> OTU_97.33050     0      1
    #> OTU_97.33070     2      0
    #> OTU_97.331       1      0
    #> OTU_97.33109     0      1
    #> OTU_97.33123     4      0
    #> OTU_97.33127     0      1
    #> OTU_97.33181     0      1
    #> OTU_97.33207     0      2
    #> OTU_97.33208     2      0
    #> OTU_97.33210     3      0
    #> OTU_97.33214     1      0
    #> OTU_97.3322      1      0
    #> OTU_97.33221     1      0
    #> OTU_97.33227     0      2
    #> OTU_97.33241     1      0
    #> OTU_97.33268    23      0
    #> OTU_97.3327      1      0
    #> OTU_97.33303     3      0
    #> OTU_97.33308     1      0
    #> OTU_97.33310     1      2
    #> OTU_97.33340     5      2
    #> OTU_97.33347     0      1
    #> OTU_97.33350     1      0
    #> OTU_97.33354     3      0
    #> OTU_97.33404     0      1
    #> OTU_97.33409     0      1
    #> OTU_97.33436     1      0
    #> OTU_97.33469     2      0
    #> OTU_97.33494     4      0
    #> OTU_97.335       0      1
    #> OTU_97.33508     0      5
    #> OTU_97.33549     0      2
    #> OTU_97.33551     0      6
    #> OTU_97.33579     0      3
    #> OTU_97.33581     0      3
    #> OTU_97.33601     1      0
    #> OTU_97.33614     1      0
    #> OTU_97.33622     0      1
    #> OTU_97.33670     1      0
    #> OTU_97.33680     0      1
    #> OTU_97.33710     0      1
    #> OTU_97.33723     0      1
    #> OTU_97.33728     0      1
    #> OTU_97.33744     0      1
    #> OTU_97.33749     2      2
    #> OTU_97.33762     0      1
    #> OTU_97.33772     0      2
    #> OTU_97.33810     0      2
    #> OTU_97.33814     0      1
    #> OTU_97.33815     0      1
    #> OTU_97.33840     2      0
    #> OTU_97.33847     1      1
    #> OTU_97.3388      1      1
    #> OTU_97.33881     1      0
    #> OTU_97.33886     0      2
    #> OTU_97.33888     1      0
    #> OTU_97.33930     2      0
    #> OTU_97.33934     7      0
    #> OTU_97.33942     1      0
    #> OTU_97.33944     2      0
    #> OTU_97.33946     4      0
    #> OTU_97.33948     1      0
    #> OTU_97.33956     2      0
    #> OTU_97.33958     0      6
    #> OTU_97.33966   232      0
    #> OTU_97.33972     2      0
    #> OTU_97.33975     1      0
    #> OTU_97.3398      0      3
    #> OTU_97.33980     1      0
    #> OTU_97.33988     0      3
    #> OTU_97.33997     0      1
    #> OTU_97.34003     0      1
    #> OTU_97.34018     1      0
    #> OTU_97.34038     1      0
    #> OTU_97.34054     1      0
    #> OTU_97.34055     0      1
    #> OTU_97.34060     2      0
    #> OTU_97.34073     1      0
    #> OTU_97.34076     1      0
    #> OTU_97.34087     1      0
    #> OTU_97.34094     0      1
    #> OTU_97.34114     1      0
    #> OTU_97.34123     1      0
    #> OTU_97.34132     0      2
    #> OTU_97.34134     1      1
    #> OTU_97.34143     1      0
    #> OTU_97.34144     0      1
    #> OTU_97.34149     0      1
    #> OTU_97.34156     0      1
    #> OTU_97.34169     1      0
    #> OTU_97.34173     1      1
    #> OTU_97.34194     0      1
    #> OTU_97.34213     1      0
    #> OTU_97.34227     2      0
    #> OTU_97.34243     0      1
    #> OTU_97.34246     8      0
    #> OTU_97.34260     0      3
    #> OTU_97.34263     1      0
    #> OTU_97.34266     1      0
    #> OTU_97.34297     3      0
    #> OTU_97.34311     0      1
    #> OTU_97.34325     2      0
    #> OTU_97.34330     1      0
    #> OTU_97.34337     0      2
    #> OTU_97.34346     2     11
    #> OTU_97.34353     1      0
    #> OTU_97.34362     2      0
    #> OTU_97.34372     2      4
    #> OTU_97.34380     1      0
    #> OTU_97.34383     0      7
    #> OTU_97.34388     0      1
    #> OTU_97.34394     0      2
    #> OTU_97.34398     1      0
    #> OTU_97.34404     1      4
    #> OTU_97.34413     1      1
    #> OTU_97.34432     0      3
    #> OTU_97.34435     2      0
    #> OTU_97.34438     1      0
    #> OTU_97.34445     0      1
    #> OTU_97.34468     0      1
    #> OTU_97.34477     2      0
    #> OTU_97.34481     0      3
    #> OTU_97.34510    23      0
    #> OTU_97.34521     1      0
    #> OTU_97.34522     0      5
    #> OTU_97.34527     0      9
    #> OTU_97.34534     2      0
    #> OTU_97.34537     0      2
    #> OTU_97.34538     0      9
    #> OTU_97.34541     1      4
    #> OTU_97.34556     1      0
    #> OTU_97.34560     0      1
    #> OTU_97.34565     1      0
    #> OTU_97.34568     0      1
    #> OTU_97.34572     4      0
    #> OTU_97.34600     0      1
    #> OTU_97.34616    91      0
    #> OTU_97.34617     1      0
    #> OTU_97.34657     0      3
    #> OTU_97.34666     2      0
    #> OTU_97.34681     0      1
    #> OTU_97.34686     4      0
    #> OTU_97.34698     2      0
    #> OTU_97.34705     1      0
    #> OTU_97.34719     1      0
    #> OTU_97.34730     1      0
    #> OTU_97.34732     1      0
    #> OTU_97.34737     1      2
    #> OTU_97.34747     0     17
    #> OTU_97.34756     1      0
    #> OTU_97.34759    41      0
    #> OTU_97.34783     0      3
    #> OTU_97.34807     0      1
    #> OTU_97.34808     2      0
    #> OTU_97.34824     1      0
    #> OTU_97.34825     0      9
    #> OTU_97.34828     1      0
    #> OTU_97.34834     1      0
    #> OTU_97.34836     0      2
    #> OTU_97.34843     0      1
    #> OTU_97.34846     2      0
    #> OTU_97.34847     0      1
    #> OTU_97.34856     1      0
    #> OTU_97.34868     0      2
    #> OTU_97.34879     1      0
    #> OTU_97.34889     2      0
    #> OTU_97.34920    24      0
    #> OTU_97.34950     0      3
    #> OTU_97.34954     0      6
    #> OTU_97.34968     2      0
    #> OTU_97.34977     1      0
    #> OTU_97.34979     1      0
    #> OTU_97.35012    16      0
    #> OTU_97.35014     3      0
    #> OTU_97.35020     0      9
    #> OTU_97.35021     0      2
    #> OTU_97.35027     5      0
    #> OTU_97.35088     0      2
    #> OTU_97.35089     0      2
    #> OTU_97.35097     5      0
    #> OTU_97.35110     1      0
    #> OTU_97.35113     4      0
    #> OTU_97.35127     1      0
    #> OTU_97.35134     0      6
    #> OTU_97.35161     0      7
    #> OTU_97.35164     0      4
    #> OTU_97.35176     0      1
    #> OTU_97.35177     0      3
    #> OTU_97.35185     1      0
    #> OTU_97.35197     1      2
    #> OTU_97.35204     0      4
    #> OTU_97.35207     0     22
    #> OTU_97.35208     2      0
    #> OTU_97.35210     0      1
    #> OTU_97.35214     1      0
    #> OTU_97.35255     4      0
    #> OTU_97.35261     0      1
    #> OTU_97.3528      1      0
    #> OTU_97.35311     0     14
    #> OTU_97.35328     0      1
    #> OTU_97.35347     9      0
    #> OTU_97.35354    20      0
    #> OTU_97.35367     0      8
    #> OTU_97.35375     0      1
    #> OTU_97.35402     0      1
    #> OTU_97.35404     0      1
    #> OTU_97.35408     1      2
    #> OTU_97.35442     0      1
    #> OTU_97.35457     0      2
    #> OTU_97.35477     1      0
    #> OTU_97.35498     0      1
    #> OTU_97.35522     0      1
    #> OTU_97.3553      0      1
    #> OTU_97.35534     1      2
    #> OTU_97.35551     0      3
    #> OTU_97.35552     1      0
    #> OTU_97.35557     0      2
    #> OTU_97.35564     0      3
    #> OTU_97.3557      1      0
    #> OTU_97.35590     0      1
    #> OTU_97.35610     0      1
    #> OTU_97.35638     0      3
    #> OTU_97.35656     1      0
    #> OTU_97.35682     0      1
    #> OTU_97.35714     1      0
    #> OTU_97.35726     0      1
    #> OTU_97.35755     1      0
    #> OTU_97.35765     0      1
    #> OTU_97.35824     0      2
    #> OTU_97.3586      0      1
    #> OTU_97.3594      1     18
    #> OTU_97.35945     0      1
    #> OTU_97.35952     0      1
    #> OTU_97.35956     2      0
    #> OTU_97.3596      0      2
    #> OTU_97.35960     1      0
    #> OTU_97.35965     0      1
    #> OTU_97.35970     1      0
    #> OTU_97.35988     0      4
    #> OTU_97.35992     0      3
    #> OTU_97.36        3      1
    #> OTU_97.36035     1      0
    #> OTU_97.36104     0      5
    #> OTU_97.36108     1      0
    #> OTU_97.36125     0      1
    #> OTU_97.36154   116      0
    #> OTU_97.36155     2      0
    #> OTU_97.36157     1      4
    #> OTU_97.3616      2     16
    #> OTU_97.3617      0      2
    #> OTU_97.36177     0     10
    #> OTU_97.36192     1      0
    #> OTU_97.36204     0      1
    #> OTU_97.36208     0      2
    #> OTU_97.36238     1      0
    #> OTU_97.36271     2      0
    #> OTU_97.36272     6      0
    #> OTU_97.36278     0      4
    #> OTU_97.36283     1      1
    #> OTU_97.36286   124      0
    #> OTU_97.36305     0      1
    #> OTU_97.3631      0      1
    #> OTU_97.36314     0      1
    #> OTU_97.36338     1      0
    #> OTU_97.36341     0      1
    #> OTU_97.36354     0      1
    #> OTU_97.36382     1      0
    #> OTU_97.36424     2      0
    #> OTU_97.36432     1      0
    #> OTU_97.36465     6      2
    #> OTU_97.3651      1      0
    #> OTU_97.36510     0      2
    #> OTU_97.36567     1      0
    #> OTU_97.36579     3      0
    #> OTU_97.36597     1      0
    #> OTU_97.36605     0      1
    #> OTU_97.36643     1      0
    #> OTU_97.3666      0      1
    #> OTU_97.36661     1      1
    #> OTU_97.36692     2      0
    #> OTU_97.36695     0      1
    #> OTU_97.3675      0      1
    #> OTU_97.36799     0      4
    #> OTU_97.36838     3      0
    #> OTU_97.36839     3      0
    #> OTU_97.36847     0      1
    #> OTU_97.36877     1      0
    #> OTU_97.36910     1      0
    #> OTU_97.36941     5      0
    #> OTU_97.36958     7      1
    #> OTU_97.3698      0      1
    #> OTU_97.36981     6      0
    #> OTU_97.36990     1      0
    #> OTU_97.37007     1      0
    #> OTU_97.3707      1      0
    #> OTU_97.37082     2      0
    #> OTU_97.37085     1      0
    #> OTU_97.37088     0      1
    #> OTU_97.3710      0      1
    #> OTU_97.37104     2      0
    #> OTU_97.37120     0      1
    #> OTU_97.37130     1      0
    #> OTU_97.37155     1      0
    #> OTU_97.37173     1      0
    #> OTU_97.37175     0      6
    #> OTU_97.37178     1      0
    #> OTU_97.37201     0      1
    #> OTU_97.37205     1      0
    #> OTU_97.37246     0      3
    #> OTU_97.37259     0     12
    #> OTU_97.37270     1      0
    #> OTU_97.37271     1      0
    #> OTU_97.37274     1      0
    #> OTU_97.37299     1      0
    #> OTU_97.37314     0     26
    #> OTU_97.37317     0     10
    #> OTU_97.37320     0      1
    #> OTU_97.37369     0      3
    #> OTU_97.37383    51      0
    #> OTU_97.37394     0      2
    #> OTU_97.37402     0      1
    #> OTU_97.37412     0      1
    #> OTU_97.37418     0      1
    #> OTU_97.3744      1      0
    #> OTU_97.37443     0     15
    #> OTU_97.37448     0     15
    #> OTU_97.37491     1     33
    #> OTU_97.37497     1      0
    #> OTU_97.37504     1     36
    #> OTU_97.3752      0      2
    #> OTU_97.37533     1      0
    #> OTU_97.37545     1      0
    #> OTU_97.37552     5      0
    #> OTU_97.37577     0      1
    #> OTU_97.37602     0      1
    #> OTU_97.37625     0      1
    #> OTU_97.37654     0      4
    #> OTU_97.37668     0      1
    #> OTU_97.37682     0      1
    #> OTU_97.37686     1      0
    #> OTU_97.37690     1      0
    #> OTU_97.37721     1     24
    #> OTU_97.37734     0      3
    #> OTU_97.37770    53    351
    #> OTU_97.37794     0      2
    #> OTU_97.37802     0      1
    #> OTU_97.37807     3      0
    #> OTU_97.37815     1      0
    #> OTU_97.37827     0      2
    #> OTU_97.37841     0     42
    #> OTU_97.37843     0      2
    #> OTU_97.37847     5     78
    #> OTU_97.37856    12      2
    #> OTU_97.37867     0      4
    #> OTU_97.37869     0      1
    #> OTU_97.37875     0      1
    #> OTU_97.37877     0      1
    #> OTU_97.37887     0      5
    #> OTU_97.37901     3      0
    #> OTU_97.37929     0      1
    #> OTU_97.37936     0     36
    #> OTU_97.37951     1      6
    #> OTU_97.37954     9      0
    #> OTU_97.37969     1      0
    #> OTU_97.37990     4      6
    #> OTU_97.38034     1      5
    #> OTU_97.38044     0      2
    #> OTU_97.38056     1      0
    #> OTU_97.38106     0      1
    #> OTU_97.38133     0      1
    #> OTU_97.38137     3      0
    #> OTU_97.38169     1      0
    #> OTU_97.38187    13     50
    #> OTU_97.38219     0      1
    #> OTU_97.38222     2      0
    #> OTU_97.38230    24      0
    #> OTU_97.38244     0      1
    #> OTU_97.38269     0      1
    #> OTU_97.38331     1      0
    #> OTU_97.38344     2      0
    #> OTU_97.38354     1      0
    #> OTU_97.38375     1      0
    #> OTU_97.38383     1      0
    #> OTU_97.38405     0      1
    #> OTU_97.38420     0      3
    #> OTU_97.38437     2      0
    #> OTU_97.38446     0      8
    #> OTU_97.3849      1      0
    #> OTU_97.3850      1      0
    #> OTU_97.38530     8      3
    #> OTU_97.38586     0      2
    #> OTU_97.38593     0      1
    #> OTU_97.38612     1      0
    #> OTU_97.38639     0     12
    #> OTU_97.38646     0      2
    #> OTU_97.3872      3      0
    #> OTU_97.38765     2      0
    #> OTU_97.38778     0      1
    #> OTU_97.38845     7      0
    #> OTU_97.38864     1      0
    #> OTU_97.38871     1      0
    #> OTU_97.38903     1      0
    #> OTU_97.38908     1      1
    #> OTU_97.38913     0      1
    #> OTU_97.38934     1      0
    #> OTU_97.38943     0      3
    #> OTU_97.38948     0      1
    #> OTU_97.38957     1      0
    #> OTU_97.38969     0      2
    #> OTU_97.38996     0      1
    #> OTU_97.38999     0      1
    #> OTU_97.39022     1      0
    #> OTU_97.39042     0      2
    #> OTU_97.39085     1      2
    #> OTU_97.39091     1      0
    #> OTU_97.39101     2      0
    #> OTU_97.39106     0      2
    #> OTU_97.39108     0      2
    #> OTU_97.39112     1      0
    #> OTU_97.39115     4      1
    #> OTU_97.39125     1      0
    #> OTU_97.39134     0      4
    #> OTU_97.39150     2      0
    #> OTU_97.39151     1      0
    #> OTU_97.39153     0      1
    #> OTU_97.39182    27      5
    #> OTU_97.39211     0      1
    #> OTU_97.39213     0      1
    #> OTU_97.39219     0      2
    #> OTU_97.39231     0      1
    #> OTU_97.39238     0      1
    #> OTU_97.39245     0      2
    #> OTU_97.39246     2      2
    #> OTU_97.39262     1      0
    #> OTU_97.39296     0      1
    #> OTU_97.39300     0      1
    #> OTU_97.39306     1      0
    #> OTU_97.39312     0     15
    #> OTU_97.3932      0      4
    #> OTU_97.39346     1      2
    #> OTU_97.39353     2      4
    #> OTU_97.39365     0      2
    #> OTU_97.39414     0      2
    #> OTU_97.39423     0     27
    #> OTU_97.3944      0      2
    #> OTU_97.39450     0      1
    #> OTU_97.39485     1      0
    #> OTU_97.39489     3      0
    #> OTU_97.39491     1      0
    #> OTU_97.39497     0      8
    #> OTU_97.39511     1      0
    #> OTU_97.39522     0      4
    #> OTU_97.39532     0      1
    #> OTU_97.39533     2      0
    #> OTU_97.39544     5      0
    #> OTU_97.39579     1      0
    #> OTU_97.39584     1      0
    #> OTU_97.39598     1      0
    #> OTU_97.396       0      4
    #> OTU_97.39601     0      4
    #> OTU_97.39645     0      5
    #> OTU_97.39651     8      0
    #> OTU_97.39679     0      3
    #> OTU_97.39685     0      2
    #> OTU_97.39721     0      2
    #> OTU_97.39752     0      7
    #> OTU_97.39787     3      1
    #> OTU_97.39792     1      0
    #> OTU_97.39801     0      1
    #> OTU_97.39819    13      0
    #> OTU_97.39824     3      0
    #> OTU_97.39846     4      5
    #> OTU_97.39887     0     10
    #> OTU_97.39904     0      4
    #> OTU_97.39912     1      0
    #> OTU_97.39950     0      4
    #> OTU_97.39974     0      1
    #> OTU_97.39998     0      7
    #> OTU_97.40        1      0
    #> OTU_97.40004     0      2
    #> OTU_97.40005     7      0
    #> OTU_97.40008     0      9
    #> OTU_97.40022     0      1
    #> OTU_97.40024     0      4
    #> OTU_97.40039     0      1
    #> OTU_97.40055     4      1
    #> OTU_97.40057     1      0
    #> OTU_97.40117     0      2
    #> OTU_97.40139     0      2
    #> OTU_97.40164     2      2
    #> OTU_97.40173     0      1
    #> OTU_97.40176     0      4
    #> OTU_97.40182     0      1
    #> OTU_97.40209     0      1
    #> OTU_97.40223     2     32
    #> OTU_97.40226     0      1
    #> OTU_97.40277     0      1
    #> OTU_97.40279     3      0
    #> OTU_97.40307     0      1
    #> OTU_97.40308     2      9
    #> OTU_97.40329     3     33
    #> OTU_97.40335     0      2
    #> OTU_97.40372     1      0
    #> OTU_97.4038      1      0
    #> OTU_97.40389     0      1
    #> OTU_97.40405     1      4
    #> OTU_97.40434     2      0
    #> OTU_97.40437     3      0
    #> OTU_97.40443     0      3
    #> OTU_97.40457     2      2
    #> OTU_97.40468     3      0
    #> OTU_97.40500     1      0
    #> OTU_97.40524     1      1
    #> OTU_97.40529     0      4
    #> OTU_97.40532     4      0
    #> OTU_97.40536     3      0
    #> OTU_97.40539     1      0
    #> OTU_97.40548     0      3
    #> OTU_97.40576     2      0
    #> OTU_97.40580     1      0
    #> OTU_97.40593     9     66
    #> OTU_97.40598     9      7
    #> OTU_97.40605     5      0
    #> OTU_97.40612     0      6
    #> OTU_97.40646     1      0
    #> OTU_97.40671     0      1
    #> OTU_97.40685     1      0
    #> OTU_97.40695     1      0
    #> OTU_97.40720     0      1
    #> OTU_97.40740     0      9
    #> OTU_97.40751     5      0
    #> OTU_97.40753     7      0
    #> OTU_97.40790     0      8
    #> OTU_97.40794     0      2
    #> OTU_97.40815     0      1
    #> OTU_97.40860     0      1
    #> OTU_97.40894     1      0
    #> OTU_97.40897     1      0
    #> OTU_97.40917     0      3
    #> OTU_97.40922     0      3
    #> OTU_97.40925     0      1
    #> OTU_97.4093      1      0
    #> OTU_97.40944     0      1
    #> OTU_97.40948     1      0
    #> OTU_97.40954     0      1
    #> OTU_97.40986     0      1
    #> OTU_97.40991     1      0
    #> OTU_97.40997     0      1
    #> OTU_97.41007     0      1
    #> OTU_97.41028     0      2
    #> OTU_97.41030     0      1
    #> OTU_97.41045     1      0
    #> OTU_97.4105      1      0
    #> OTU_97.41051     0      3
    #> OTU_97.41052     0      4
    #> OTU_97.41055    27      0
    #> OTU_97.41058     1      1
    #> OTU_97.41096     0      1
    #> OTU_97.41123     0      1
    #> OTU_97.4113      2      0
    #> OTU_97.41131     0      1
    #> OTU_97.41136     0      1
    #> OTU_97.41137     4      0
    #> OTU_97.41153     1      0
    #> OTU_97.41166     1      0
    #> OTU_97.41199     0      5
    #> OTU_97.41224     1      0
    #> OTU_97.41226     0      1
    #> OTU_97.41230     0      2
    #> OTU_97.41252     0      3
    #> OTU_97.41259     0      1
    #> OTU_97.41268     2      0
    #> OTU_97.41275     0      1
    #> OTU_97.4128      0      1
    #> OTU_97.41286     1      0
    #> OTU_97.41293     0      5
    #> OTU_97.41297     1      0
    #> OTU_97.41307     0      1
    #> OTU_97.41329     2      0
    #> OTU_97.41334    15      0
    #> OTU_97.41335     1      0
    #> OTU_97.41353     0      1
    #> OTU_97.41354     0      1
    #> OTU_97.41395     0      3
    #> OTU_97.41398     0      2
    #> OTU_97.41419     1      0
    #> OTU_97.41422     2      0
    #> OTU_97.41426     1      0
    #> OTU_97.41427     0      2
    #> OTU_97.4143      0      2
    #> OTU_97.41432     1      0
    #> OTU_97.41449     0      1
    #> OTU_97.41451     1     34
    #> OTU_97.41453     1      0
    #> OTU_97.41455     0      1
    #> OTU_97.41456     0      7
    #> OTU_97.41471     1      0
    #> OTU_97.41479     0      1
    #> OTU_97.41485     0      1
    #> OTU_97.41498     1      0
    #> OTU_97.41499     9      2
    #> OTU_97.41500     1      1
    #> OTU_97.41501     1      0
    #> OTU_97.41533     0      1
    #> OTU_97.41539     0      1
    #> OTU_97.41549     1      0
    #> OTU_97.41564     6      0
    #> OTU_97.41580     2      0
    #> OTU_97.41584     0      3
    #> OTU_97.41593     5     18
    #> OTU_97.41606     1      0
    #> OTU_97.41617     1      0
    #> OTU_97.41618     1      0
    #> OTU_97.4162      0      1
    #> OTU_97.41621     6      0
    #> OTU_97.41648     1      0
    #> OTU_97.41649     0      1
    #> OTU_97.41654     0      1
    #> OTU_97.41676     1      0
    #> OTU_97.41685     1      0
    #> OTU_97.41686     0      1
    #> OTU_97.41687     0      1
    #> OTU_97.41694     7      0
    #> OTU_97.4170      0      1
    #> OTU_97.41716     1      0
    #> OTU_97.4172      0      2
    #> OTU_97.41744     1      1
    #> OTU_97.41770     0      3
    #> OTU_97.41792     0      1
    #> OTU_97.41794     0      1
    #> OTU_97.41796     1      0
    #> OTU_97.41805     3      1
    #> OTU_97.41809     0      2
    #> OTU_97.41810     1      0
    #> OTU_97.41813     1      0
    #> OTU_97.41839     1      2
    #> OTU_97.4184      0      2
    #> OTU_97.41842     0      1
    #> OTU_97.41846     1      1
    #> OTU_97.41849     6     12
    #> OTU_97.41867     1      0
    #> OTU_97.41870    28      0
    #> OTU_97.41871     2      0
    #> OTU_97.41881     8      0
    #> OTU_97.41889     1      2
    #> OTU_97.41900     9      1
    #> OTU_97.41913     1      0
    #> OTU_97.41917     4      1
    #> OTU_97.41937     0      2
    #> OTU_97.41950     0      1
    #> OTU_97.41955     2      0
    #> OTU_97.41956     0     23
    #> OTU_97.41965     1      0
    #> OTU_97.41969     1      3
    #> OTU_97.4197      0      1
    #> OTU_97.41970     1      0
    #> OTU_97.41974     0      7
    #> OTU_97.41988     1      0
    #> OTU_97.41993     0      4
    #> OTU_97.41996     1      0
    #> OTU_97.41998     0      1
    #> OTU_97.42        3      3
    #> OTU_97.42004     0      1
    #> OTU_97.42021     0      2
    #> OTU_97.42031     1      0
    #> OTU_97.42032     1      0
    #> OTU_97.42046     2      0
    #> OTU_97.42052     0      1
    #> OTU_97.42059     0      1
    #> OTU_97.42065     4      0
    #> OTU_97.42071    10      0
    #> OTU_97.42072     0      1
    #> OTU_97.42074     4      0
    #> OTU_97.42085     1      0
    #> OTU_97.4209      0      1
    #> OTU_97.42091     0      1
    #> OTU_97.42112     1      0
    #> OTU_97.42113     2      0
    #> OTU_97.42114     4      0
    #> OTU_97.4212      1      0
    #> OTU_97.42127     1      0
    #> OTU_97.42132     0      2
    #> OTU_97.42136     1      6
    #> OTU_97.42141     0      1
    #> OTU_97.42152     0      1
    #> OTU_97.42166     0      1
    #> OTU_97.42179     1      0
    #> OTU_97.42181     1      0
    #> OTU_97.42192     1      0
    #> OTU_97.42194     1      2
    #> OTU_97.42200     1      0
    #> OTU_97.42206     4      1
    #> OTU_97.42207     1      0
    #> OTU_97.42209     1      0
    #> OTU_97.42210     1      1
    #> OTU_97.42219     0      1
    #> OTU_97.42235     0      1
    #> OTU_97.42236     1      0
    #> OTU_97.42237     1      8
    #> OTU_97.42242     2      0
    #> OTU_97.42243     1      6
    #> OTU_97.42246     0      1
    #> OTU_97.42255     1      0
    #> OTU_97.42256     5      0
    #> OTU_97.42259    13      0
    #> OTU_97.42266     1      2
    #> OTU_97.42275     1      0
    #> OTU_97.42278     0      7
    #> OTU_97.42301     1      3
    #> OTU_97.42321     0      2
    #> OTU_97.42326     0      1
    #> OTU_97.42334     1      0
    #> OTU_97.42342    20      1
    #> OTU_97.42346     1      0
    #> OTU_97.42349     1      0
    #> OTU_97.42356    40    502
    #> OTU_97.42366     1      0
    #> OTU_97.42377     0      1
    #> OTU_97.42384     2      0
    #> OTU_97.42393     1      0
    #> OTU_97.42397     1      0
    #> OTU_97.42399     0      1
    #> OTU_97.42404     6      0
    #> OTU_97.42405     2      1
    #> OTU_97.42406     0      4
    #> OTU_97.42408     1      0
    #> OTU_97.42421     2      1
    #> OTU_97.42422    11    140
    #> OTU_97.42423     0      2
    #> OTU_97.42425     3      0
    #> OTU_97.42430     3      0
    #> OTU_97.42432     3      0
    #> OTU_97.42440     0      3
    #> OTU_97.42441     2      0
    #> OTU_97.42450     0      2
    #> OTU_97.42451     1      0
    #> OTU_97.42457     4      0
    #> OTU_97.42462     1      0
    #> OTU_97.42470     0      3
    #> OTU_97.4248      0      3
    #> OTU_97.42481     0      2
    #> OTU_97.42482    12     59
    #> OTU_97.42490     1      0
    #> OTU_97.42494     1      2
    #> OTU_97.42502   140      0
    #> OTU_97.42512     1      0
    #> OTU_97.42514     2      0
    #> OTU_97.42536     5      0
    #> OTU_97.42543     0      2
    #> OTU_97.42544     1      0
    #> OTU_97.42549     0      1
    #> OTU_97.42554     0     23
    #> OTU_97.42560     0      1
    #> OTU_97.42573     0      2
    #> OTU_97.42582     0      1
    #> OTU_97.42589     1      4
    #> OTU_97.42597     0      2
    #> OTU_97.42603     0      6
    #> OTU_97.42609    12      0
    #> OTU_97.42615     0      3
    #> OTU_97.42626     9      6
    #> OTU_97.42628     4      2
    #> OTU_97.42632     4      3
    #> OTU_97.42635     0      2
    #> OTU_97.42636     0      3
    #> OTU_97.42637     0      4
    #> OTU_97.42648     0      1
    #> OTU_97.42653     0      1
    #> OTU_97.42654     1      0
    #> OTU_97.42658     0     23
    #> OTU_97.42661     1      0
    #> OTU_97.42662     0     10
    #> OTU_97.42663     6     91
    #> OTU_97.42675     0      1
    #> OTU_97.42676     0      6
    #> OTU_97.42688     0      8
    #> OTU_97.42691     0      1
    #> OTU_97.42694     0      1
    #> OTU_97.42708     0      1
    #> OTU_97.4271      1      0
    #> OTU_97.42713     0      1
    #> OTU_97.42774     0      2
    #> OTU_97.42792     1      0
    #> OTU_97.42796     3      0
    #> OTU_97.42798     0      1
    #> OTU_97.42805    28     18
    #> OTU_97.42806     0      1
    #> OTU_97.42823     2      0
    #> OTU_97.42824     1      0
    #> OTU_97.42838     5      8
    #> OTU_97.42844     2      0
    #> OTU_97.42852     6      1
    #> OTU_97.42854     1     58
    #> OTU_97.42855     3      2
    #> OTU_97.42856     0      1
    #> OTU_97.42861     0      1
    #> OTU_97.42864    57    139
    #> OTU_97.42866     1      5
    #> OTU_97.42870     0      3
    #> OTU_97.42876     0      1
    #> OTU_97.42878     1      0
    #> OTU_97.42882     4      0
    #> OTU_97.42886     0      1
    #> OTU_97.42889     1      1
    #> OTU_97.42895     2      1
    #> OTU_97.42905     2      0
    #> OTU_97.42911     0      1
    #> OTU_97.42917     0      5
    #> OTU_97.42930     0      5
    #> OTU_97.42934     1      0
    #> OTU_97.42938    12      4
    #> OTU_97.42939     1      1
    #> OTU_97.42941     1      0
    #> OTU_97.42944     0      3
    #> OTU_97.42946     1      2
    #> OTU_97.42948     1      1
    #> OTU_97.42949     0      2
    #> OTU_97.42961     0      2
    #> OTU_97.42962     0      3
    #> OTU_97.42967     0      1
    #> OTU_97.42972     0      1
    #> OTU_97.42979     0      2
    #> OTU_97.42986     0      4
    #> OTU_97.42987     1     19
    #> OTU_97.42989     5      3
    #> OTU_97.42996     0      1
    #> OTU_97.43000     2      1
    #> OTU_97.43007     0     18
    #> OTU_97.43010     1     43
    #> OTU_97.43012     2     24
    #> OTU_97.43017     2      8
    #> OTU_97.43018     0      1
    #> OTU_97.43021     0      1
    #> OTU_97.43023     1      0
    #> OTU_97.43028     6     59
    #> OTU_97.43046     0      5
    #> OTU_97.43049     8      5
    #> OTU_97.43057     1      0
    #> OTU_97.43059     0      2
    #> OTU_97.43066     1      0
    #> OTU_97.43072    15     21
    #> OTU_97.43084     1      0
    #> OTU_97.43088     3      0
    #> OTU_97.43092     0     14
    #> OTU_97.43093     0      1
    #> OTU_97.43096     1      0
    #> OTU_97.43097     0      1
    #> OTU_97.43098    18     31
    #> OTU_97.43102     1      0
    #> OTU_97.43117     1      1
    #> OTU_97.43120     4      0
    #> OTU_97.43137     1      1
    #> OTU_97.43143     1      0
    #> OTU_97.43145     1      0
    #> OTU_97.43147   139      2
    #> OTU_97.4315      0      1
    #> OTU_97.43152     1      1
    #> OTU_97.43185     0      1
    #> OTU_97.43205     0      4
    #> OTU_97.43248    16      0
    #> OTU_97.43258     0      2
    #> OTU_97.43278     1      0
    #> OTU_97.4328      3      0
    #> OTU_97.43303     0      1
    #> OTU_97.43332     0     13
    #> OTU_97.43344     2      0
    #> OTU_97.43346     4     69
    #> OTU_97.43358     1      0
    #> OTU_97.43386     3      0
    #> OTU_97.43400     0      2
    #> OTU_97.43405     0      2
    #> OTU_97.43417     9      0
    #> OTU_97.43427     0      4
    #> OTU_97.43436     1      0
    #> OTU_97.43438     0      1
    #> OTU_97.43450     0      2
    #> OTU_97.43451     0      1
    #> OTU_97.43466     1      0
    #> OTU_97.4348      1      0
    #> OTU_97.43496     0      1
    #> OTU_97.43505     1      0
    #> OTU_97.43510     0      1
    #> OTU_97.43542     0      1
    #> OTU_97.43549     4      0
    #> OTU_97.43578     3      0
    #> OTU_97.4358      1      0
    #> OTU_97.43593     0      2
    #> OTU_97.43676     0      1
    #> OTU_97.43679     1      0
    #> OTU_97.43685    16      1
    #> OTU_97.4370      1      0
    #> OTU_97.43750     1      0
    #> OTU_97.43761     1      0
    #> OTU_97.43810     0      1
    #> OTU_97.43848     0      1
    #> OTU_97.43859     0     11
    #> OTU_97.43861     1      0
    #> OTU_97.43865     0      1
    #> OTU_97.4387      0      4
    #> OTU_97.43873     0      1
    #> OTU_97.43876     6      0
    #> OTU_97.43881     2      6
    #> OTU_97.43899     0      1
    #> OTU_97.43910     1      0
    #> OTU_97.43924     6     10
    #> OTU_97.43952     0      4
    #> OTU_97.43971     4      5
    #> OTU_97.43982     5      0
    #> OTU_97.43983     2      0
    #> OTU_97.44036     3      1
    #> OTU_97.4404      0      1
    #> OTU_97.44048     4      5
    #> OTU_97.44049     1      0
    #> OTU_97.4407      0      2
    #> OTU_97.44071     1      0
    #> OTU_97.44072     1      0
    #> OTU_97.44090     1      0
    #> OTU_97.44093     1      1
    #> OTU_97.44094     1      0
    #> OTU_97.44106     1      0
    #> OTU_97.44116     0      2
    #> OTU_97.44117     0      2
    #> OTU_97.44129     5      2
    #> OTU_97.44130     0      1
    #> OTU_97.44132     0      1
    #> OTU_97.44134     2      0
    #> OTU_97.44157    37      0
    #> OTU_97.44158     3      1
    #> OTU_97.44175     0      2
    #> OTU_97.4418      1      0
    #> OTU_97.44181     1      0
    #> OTU_97.44188     1      5
    #> OTU_97.44204     0      1
    #> OTU_97.44215     0      1
    #> OTU_97.44220     1      0
    #> OTU_97.44228    21     18
    #> OTU_97.44232     0      1
    #> OTU_97.44241     1      0
    #> OTU_97.44267     1      0
    #> OTU_97.44294     4      1
    #> OTU_97.44307     1      2
    #> OTU_97.44308     0      1
    #> OTU_97.44314     1      0
    #> OTU_97.44315    20      1
    #> OTU_97.44316     1      2
    #> OTU_97.44324     1      0
    #> OTU_97.44340    12      0
    #> OTU_97.44368     0      8
    #> OTU_97.44375   151      0
    #> OTU_97.44380    45      0
    #> OTU_97.44389     1      0
    #> OTU_97.44399     0      2
    #> OTU_97.44406     4     34
    #> OTU_97.44413     1      0
    #> OTU_97.44418     0      1
    #> OTU_97.44421     1      0
    #> OTU_97.44422     2      0
    #> OTU_97.44423    26      0
    #> OTU_97.44427     8     13
    #> OTU_97.44434     1      0
    #> OTU_97.44437     0      1
    #> OTU_97.44440     1      0
    #> OTU_97.44447     0      3
    #> OTU_97.44452     1      5
    #> OTU_97.44463     5      0
    #> OTU_97.44478    11      0
    #> OTU_97.44482     0      1
    #> OTU_97.44484     0      2
    #> OTU_97.44485     8      0
    #> OTU_97.44487    25      0
    #> OTU_97.44495     1      4
    #> OTU_97.44502     1      0
    #> OTU_97.44507     4      1
    #> OTU_97.44512     2      0
    #> OTU_97.44514    71      0
    #> OTU_97.44521    30      0
    #> OTU_97.44522     3      0
    #> OTU_97.44523    10      2
    #> OTU_97.44525     0      1
    #> OTU_97.44532     1      6
    #> OTU_97.44545     1      2
    #> OTU_97.44550     9      3
    #> OTU_97.44564   620      0
    #> OTU_97.44565     0     22
    #> OTU_97.44566     6      0
    #> OTU_97.44568     0      1
    #> OTU_97.44574     0      1
    #> OTU_97.44581     0      2
    #> OTU_97.44592     0      1
    #> OTU_97.44594   125     40
    #> OTU_97.44604     7      0
    #> OTU_97.44620     7      0
    #> OTU_97.44629    85      0
    #> OTU_97.44630     0      2
    #> OTU_97.44656     8     21
    #> OTU_97.44657     1      0
    #> OTU_97.44660     0      2
    #> OTU_97.44665     3     10
    #> OTU_97.44676     1      1
    #> OTU_97.44678     0      1
    #> OTU_97.44693     7    163
    #> OTU_97.44704     3      0
    #> OTU_97.44707     1      0
    #> OTU_97.44722     1      0
    #> OTU_97.44747     0      2
    #> OTU_97.44769     0     11
    #> OTU_97.44792     5      0
    #> OTU_97.44798     1      5
    #> OTU_97.44820    16      2
    #> OTU_97.44826     1      1
    #> OTU_97.44833    51      1
    #> OTU_97.44838     1      0
    #> OTU_97.44842     1      0
    #> OTU_97.44851    93     80
    #> OTU_97.44855     0      1
    #> OTU_97.44856     1      3
    #> OTU_97.44864     1      0
    #> OTU_97.44868     1      0
    #> OTU_97.44869     0      1
    #> OTU_97.44881     1      0
    #> OTU_97.44883     0      4
    #> OTU_97.44886     1      1
    #> OTU_97.44892     1      0
    #> OTU_97.44894     0      1
    #> OTU_97.44898     3      0
    #> OTU_97.44905     6      0
    #> OTU_97.44910     1      0
    #> OTU_97.44920     2      0
    #> OTU_97.44922     1      0
    #> OTU_97.44931     0      2
    #> OTU_97.44935     1      1
    #> OTU_97.44937     0      3
    #> OTU_97.44941    84    115
    #> OTU_97.44942     1      0
    #> OTU_97.44943     2      0
    #> OTU_97.44944    14     39
    #> OTU_97.44946     1      1
    #> OTU_97.44950     1      1
    #> OTU_97.44953     5      0
    #> OTU_97.44958    35     58
    #> OTU_97.44965     1      0
    #> OTU_97.4497      0      1
    #> OTU_97.44975     1      0
    #> OTU_97.44981     0      4
    #> OTU_97.44996     1      0
    #> OTU_97.44997     1      6
    #> OTU_97.45001     3      4
    #> OTU_97.45007     2      1
    #> OTU_97.45011    13      0
    #> OTU_97.45028     3      7
    #> OTU_97.45033     4      1
    #> OTU_97.45036     6      0
    #> OTU_97.45053    14     12
    #> OTU_97.45058    13      0
    #> OTU_97.45063     1      1
    #> OTU_97.45087     1      0
    #> OTU_97.45096     0      1
    #> OTU_97.45103     1      6
    #> OTU_97.45114     0      1
    #> OTU_97.45120    13      4
    #> OTU_97.45122   131      0
    #> OTU_97.45123     2      0
    #> OTU_97.45124     1      0
    #> OTU_97.45127     0      4
    #> OTU_97.45128     1      2
    #> OTU_97.45141     1      8
    #> OTU_97.45143     1      5
    #> OTU_97.45146     0      9
    #> OTU_97.45148    97     54
    #> OTU_97.45166     4      0
    #> OTU_97.45195    11      0
    #> OTU_97.45204     0      1
    #> OTU_97.45206    39      0
    #> OTU_97.45209     1      0
    #> OTU_97.45210     4      0
    #> OTU_97.45213     0      3
    #> OTU_97.45223    27     10
    #> OTU_97.45233     0      2
    #> OTU_97.45235     3      0
    #> OTU_97.45240     0      1
    #> OTU_97.45246  1606     14
    #> OTU_97.45250    57      0
    #> OTU_97.45251     0      1
    #> OTU_97.45257     1      1
    #> OTU_97.45261     0      1
    #> OTU_97.45266     0      1
    #> OTU_97.45267     2      0
    #> OTU_97.45276     0      2
    #> OTU_97.45277     1      7
    #> OTU_97.45293     0      5
    #> OTU_97.45294     1      0
    #> OTU_97.45297    23      2
    #> OTU_97.45303    18      0
    #> OTU_97.45307    19      3
    #> OTU_97.45310     3      5
    #> OTU_97.45311     5      1
    #> OTU_97.45321     0     18
    #> OTU_97.45324     2      1
    #> OTU_97.45326     1      5
    #> OTU_97.45328     0     44
    #> OTU_97.45337    11      2
    #> OTU_97.45338     0      1
    #> OTU_97.45339     0      1
    #> OTU_97.45341     0      1
    #> OTU_97.45345     1      0
    #> OTU_97.45347     1      0
    #> OTU_97.45358     4     22
    #> OTU_97.45362     7      0
    #> OTU_97.45365   390    260
    #> OTU_97.45366     2      1
    #> OTU_97.45369     0      1
    #> OTU_97.45371     0      1
    #> OTU_97.45372     6      6
    #> OTU_97.45390     0      1
    #> OTU_97.45396     7      1
    #> OTU_97.45398     1      7
    #> OTU_97.45412     7      0
    #> OTU_97.45414     1      0
    #> OTU_97.45415     0      1
    #> OTU_97.45423     0      1
    #> OTU_97.45424     0      3
    #> OTU_97.45429    56     45
    #> OTU_97.45439     0      2
    #> OTU_97.45444     0      2
    #> OTU_97.4563      0      1
    #> OTU_97.4582      1      0
    #> OTU_97.4633      0      1
    #> OTU_97.4635      0     20
    #> OTU_97.4651      0      1
    #> OTU_97.4657      0      1
    #> OTU_97.47       10      0
    #> OTU_97.4724      0      1
    #> OTU_97.4732      3      0
    #> OTU_97.4735      0      1
    #> OTU_97.4740      0      1
    #> OTU_97.4758      4      0
    #> OTU_97.4769      1      0
    #> OTU_97.4771      3      0
    #> OTU_97.479      11      0
    #> OTU_97.4793      0      3
    #> OTU_97.48        3      6
    #> OTU_97.486       5      2
    #> OTU_97.4862      1      0
    #> OTU_97.487       1      3
    #> OTU_97.4870      0      1
    #> OTU_97.4910      0      1
    #> OTU_97.4925      0      1
    #> OTU_97.4972      1      0
    #> OTU_97.50        9      1
    #> OTU_97.5017      0      1
    #> OTU_97.5031      0      2
    #> OTU_97.5033      1      2
    #> OTU_97.5053      1      0
    #> OTU_97.5077      0      1
    #> OTU_97.5089      0      2
    #> OTU_97.5092      1      0
    #> OTU_97.5195      0      1
    #> OTU_97.52        3      0
    #> OTU_97.5202      0      2
    #> OTU_97.5216      1      0
    #> OTU_97.5231      1      2
    #> OTU_97.5236      0      1
    #> OTU_97.5240      0      1
    #> OTU_97.5254      1      0
    #> OTU_97.5265      1      0
    #> OTU_97.5276      1      0
    #> OTU_97.5290      1      0
    #> OTU_97.5299      1      0
    #> OTU_97.53        2      5
    #> OTU_97.5301      0      1
    #> OTU_97.5315      2      0
    #> OTU_97.5350      2      0
    #> OTU_97.5351      1      0
    #> OTU_97.540       0      1
    #> OTU_97.5424      1      0
    #> OTU_97.543       1      6
    #> OTU_97.5439      1      0
    #> OTU_97.55        3      0
    #> OTU_97.550       1      0
    #> OTU_97.5520      1      0
    #> OTU_97.5530      0      1
    #> OTU_97.557       7      1
    #> OTU_97.5576      0      1
    #> OTU_97.56        0      3
    #> OTU_97.5612      1      0
    #> OTU_97.5692      1      0
    #> OTU_97.57        1      0
    #> OTU_97.5741      1      0
    #> OTU_97.5756      0      1
    #> OTU_97.5783      0     25
    #> OTU_97.5805      0      4
    #> OTU_97.5811      0      1
    #> OTU_97.582       1      0
    #> OTU_97.59        1      9
    #> OTU_97.5917      1      0
    #> OTU_97.594       0      1
    #> OTU_97.5977      2      5
    #> OTU_97.6         0      4
    #> OTU_97.60        3      0
    #> OTU_97.6024      0      1
    #> OTU_97.6029      1      0
    #> OTU_97.6058      1      0
    #> OTU_97.6076      1      0
    #> OTU_97.6088      2      3
    #> OTU_97.61        1      2
    #> OTU_97.6101      1      0
    #> OTU_97.6102      0      1
    #> OTU_97.6108      0      1
    #> OTU_97.611       1      0
    #> OTU_97.6144      0      2
    #> OTU_97.6150      1      0
    #> OTU_97.6152      0      2
    #> OTU_97.6162      0      1
    #> OTU_97.6190      0      2
    #> OTU_97.62        4      6
    #> OTU_97.6228      2      0
    #> OTU_97.6234     10      0
    #> OTU_97.63        4      0
    #> OTU_97.6326      1      0
    #> OTU_97.6340      0      2
    #> OTU_97.6351      4      1
    #> OTU_97.6360      0      1
    #> OTU_97.6403      1      0
    #> OTU_97.6405      0      2
    #> OTU_97.6418      0      1
    #> OTU_97.65        0      1
    #> OTU_97.6517      0      1
    #> OTU_97.6522      1      1
    #> OTU_97.6554      0      1
    #> OTU_97.6559      0      3
    #> OTU_97.6575      0      1
    #> OTU_97.6654      0      1
    #> OTU_97.6668      1      0
    #> OTU_97.6678      2      0
    #> OTU_97.6688      1      0
    #> OTU_97.671       0      1
    #> OTU_97.6712      0      8
    #> OTU_97.6733      2      0
    #> OTU_97.675       1      0
    #> OTU_97.6766      0      1
    #> OTU_97.6779      1      0
    #> OTU_97.6786      1      0
    #> OTU_97.6793      2      0
    #> OTU_97.6806      1      0
    #> OTU_97.6807      0      1
    #> OTU_97.6825      0      2
    #> OTU_97.6826      1      0
    #> OTU_97.6880      2      0
    #> OTU_97.6882      2      0
    #> OTU_97.6885      2      0
    #> OTU_97.6889      1      0
    #> OTU_97.6901      0      1
    #> OTU_97.6957      0      1
    #> OTU_97.6976      0      2
    #> OTU_97.6977      2      0
    #> OTU_97.6981      0      1
    #> OTU_97.6989      1      0
    #> OTU_97.7010      0      1
    #> OTU_97.704       0      1
    #> OTU_97.7068      0      2
    #> OTU_97.7102      1      2
    #> OTU_97.7109      0      1
    #> OTU_97.7167      0      1
    #> OTU_97.7169      0      1
    #> OTU_97.718       0      4
    #> OTU_97.725       1      0
    #> OTU_97.7260      2      1
    #> OTU_97.73        1      5
    #> OTU_97.7301      0      2
    #> OTU_97.741       1      0
    #> OTU_97.7467      0      1
    #> OTU_97.7491      3      0
    #> OTU_97.760       1      0
    #> OTU_97.7612      1      0
    #> OTU_97.762       2      0
    #> OTU_97.769       1      0
    #> OTU_97.7696      1      0
    #> OTU_97.7730      0      1
    #> OTU_97.7750      1      1
    #> OTU_97.7756      1      0
    #> OTU_97.7762      1      0
    #> OTU_97.7821      0      1
    #> OTU_97.7835      0      1
    #> OTU_97.7844      0      1
    #> OTU_97.7846      3      2
    #> OTU_97.7859      0      3
    #> OTU_97.7870      0      1
    #> OTU_97.7888      1      0
    #> OTU_97.7943      0      2
    #> OTU_97.7950      0      1
    #> OTU_97.8057      0      1
    #> OTU_97.8064      0      1
    #> OTU_97.8070      1      1
    #> OTU_97.8083      0      1
    #> OTU_97.8090      0      7
    #> OTU_97.8114      0      1
    #> OTU_97.8161      0      1
    #> OTU_97.8165      0     21
    #> OTU_97.8168      2      1
    #> OTU_97.8197      0      1
    #> OTU_97.8198      0      2
    #> OTU_97.82        2      5
    #> OTU_97.8224      1      0
    #> OTU_97.8260      1      0
    #> OTU_97.829       1      0
    #> OTU_97.8291      0      1
    #> OTU_97.83        1      2
    #> OTU_97.8334     23      0
    #> OTU_97.8347      0      1
    #> OTU_97.8354      0     19
    #> OTU_97.8365      1      0
    #> OTU_97.837       0      2
    #> OTU_97.84        9      0
    #> OTU_97.8400      1      0
    #> OTU_97.8403      0      3
    #> OTU_97.8425      0      1
    #> OTU_97.8475      1      0
    #> OTU_97.8545      1      0
    #> OTU_97.8583      1      0
    #> OTU_97.862       0      1
    #> OTU_97.8663      0      1
    #> OTU_97.8702      2      0
    #> OTU_97.8711      0      1
    #> OTU_97.8778      0      1
    #> OTU_97.88        2      3
    #> OTU_97.8819      0      1
    #> OTU_97.8859      0      3
    #> OTU_97.887       1      0
    #> OTU_97.8870      2      0
    #> OTU_97.8873      0      2
    #> OTU_97.8918      0     13
    #> OTU_97.8982      1      0
    #> OTU_97.899       0     38
    #> OTU_97.9        14      0
    #> OTU_97.900       1      0
    #> OTU_97.9000      1      0
    #> OTU_97.901       1      0
    #> OTU_97.9096      0      1
    #> OTU_97.91        1      0
    #> OTU_97.920       0      1
    #> OTU_97.9206      1      0
    #> OTU_97.9323      4      0
    #> OTU_97.934       0      1
    #> OTU_97.9343      1      0
    #> OTU_97.9353      0      1
    #> OTU_97.939       0      5
    #> OTU_97.951       0      1
    #> OTU_97.9536      0      2
    #> OTU_97.9559      9      0
    #> OTU_97.9596      0      2
    #> OTU_97.9659      1      0
    #> OTU_97.9663      0      1
    #> OTU_97.9669      0      1
    #> OTU_97.9679      0      3
    #> OTU_97.97        1      0
    #> OTU_97.9720      0      3
    #> OTU_97.9740      3      6
    #> OTU_97.976       0      1
    #> OTU_97.9879      0      1
    #> OTU_97.988       0      1
    #> OTU_97.99        1      0
    #> OTU_97.993       1      0
    #> OTU_97.9945      1      0
    #> OTU_97.997       2      1

The `iNEXTseq()` function returns the `"iNEXTseq"` object including two
data frames for each regions:

- C (for UniFrac distance measured by Sorensen-type dissimilarity)
- U (for UniFrac distance measured by Jaccard-type dissimilarity)

## Rarefaction/Extrapolation Via Examples

Run the `iNEXTseq()` function with `tongue_cheek` data to compute
UniFrac distance standardized by sample coverage. (Here we only show the
first six rows for each output data frame)

``` r
data("tongue_cheek")
data("tongue_cheek_tree")

out = iNEXTseq(data = tongue_cheek, q = c(0, 1, 2), nboot = 10, 
                            PDtree = tongue_cheek_tree, PDreftime = NULL)
```

    #> Warning: UNRELIABLE VALUE: One of the 'future.apply' iterations
    #> ('future_lapply-1') unexpectedly generated random numbers without declaring so.
    #> There is a risk that those random numbers are not statistically sound and the
    #> overall results might be invalid. To fix this, specify 'future.seed=TRUE'. This
    #> ensures that proper, parallel-safe random numbers are produced via the
    #> L'Ecuyer-CMRG method. To disable this check, use 'future.seed = NULL', or set
    #> option 'future.rng.onMisuse' to "ignore".
    #> list()

The output contains two data frames: `C` for Sorensen-type, `U` for
Jaccard-type. For each data frame, it includes the UniFrac distance
estimate (`Estimate`), the diversity order (`Order.q`), `Method`
(Interpolated, Observed, or Extrapolated, depending on whether the size
`m` is less than, equal to, or greater than the reference sample size),
the sample coverage estimate (`SC`), the sample size (`Size`), the
standard error from bootstrap replications (`s.e.`), the 95% lower and
upper confidence limits of diversity (`LCL`, `UCL`), and the name of
region (`Region`). These UniFrac distance estimates with confidence
intervals are used for plotting the R/E curve.

## GRAPHIC DISPLAYS: FUNCTION ggiNEXTseq()

The function `ggiNEXTseq()`, which extends `ggplot2` to the `"iNEXTseq"`
object with default arguments, is described as follows:

``` r
ggiNEXTseq(output, scale = "fixed", transp = 0.4)  
```

<table style="width:100%;">
<colgroup>
<col width="20%">
<col width="80%">
</colgroup>
<thead>
<tr class="header">
<th align="center">
Argument
</th>
<th align="left">
Description
</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="center">
<code>output</code>
</td>
<td align="left">
the output of <code>iNEXTseq</code>.
</td>
</tr>
<tr class="even">
<td align="center">
<code>scale</code>
</td>
<td align="left">
Are scales shared across all facets (the default, <code>“fixed”</code>),
or do they vary across rows (<code>“free_x”</code>), columns
(<code>“free_y”</code>), or both rows and columns (<code>“free”</code>)?
</td>
</tr>
<tr class="odd">
<td align="center">
<code>transp</code>
</td>
<td align="left">
a value between 0 and 1 controlling transparency. <code>transp =
0</code> is completely transparent, default is <code>0.4</code>.
</td>
</tbody>
</table>

The `ggiNEXTseq()` function is a wrapper around the `ggplot2` package to
create a R/E curve using a single line of code. The resulting object is
of class `"ggplot"`, so it can be manipulated using the `ggplot2` tools.

``` r
out = iNEXTseq(data = tongue_cheek, q = c(0, 1, 2), nboot = 10, 
                   PDtree = tongue_cheek_tree, PDreftime = NULL)
ggiNEXTseq(out)
```

``` r
ggiNEXTseq(out)
```

<img src="man/figures/README-unnamed-chunk-10-1.png" width="100%" style="display: block; margin: auto;" />

## References

- Chiu, C.-H., Jost, L. and Chao\*, A. (2014). Phylogenetic beta
  diversity, similarity, and differentiation measures based on Hill
  numbers. Ecological Monographs 84, 21-44.
