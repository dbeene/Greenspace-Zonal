# Greenspace-Zonal
Jupyter Notebook to calculate zone-constrained entropy-based greenspace measure

The health benefits of greenspace are detailed in a vast body of literature, but there remains little agreement over how it should be measured across diverse contexts. Currently, two dominant approaches for quantifying greenspace are 1) direct measures of the normalized differential vegetation index (NDVI), a normalized ratio of near-infrared and red bands in remotely sensed imagery, and 2) diversity of land use/land cover (LU/LC) types. Both measures tend to perform poorly in rural areas.

Because of the geographical breadth and diversity of ECHO cohort study sites, a universal measure of greenspace that is more sensitive to accessible greenspaces across rural and urban contexts is needed for ongoing and future analyses. We are developing a 1-km spatial resolution nationwide greenspace dataset for every year dating back to 2014 that a) considers where people live by including a “residential coefficient” to an entropy index, b) applies a correction to places where LU/LC is 100% a direct measure of greenspace (e.g., parks, forest, or shrubland) meaning that entropy is 0, and c) is sensitive to “effective greenspace,” or places where people are more likely to directly interact with natural landscapes. 

The measure uses the USDA National Agricultural Statistics Service (NASS) Cropland Data Layer (CDL), a 30-m resolution annual classification of cultivated crops and other LU/LC classes, including developed land, forests, shrubland, and barren land. The CDL product is derived using an NDVI discrimination model that classifies pixels based on a time series of green-up and senescence of vegetation. Our model targets 10 CDL classes as greenspace and calculates the diversity of LU/LC 30-m pixels within a 1-km fishnet. Those classes are: 141 – deciduous forest, 142 – evergreen forest, 143 – mixed forest, 152 – shrubland, 176 – grassland/pasture, 190 – woody wetlands, 195 – herbaceous wetlands, 87 – wetlands, and 121 – developed/open space. Because the emphasis of the current measure is effective greenspace, this approach excludes agricultural land from the direct measurement of greenspace. Diversity is calculated using a normalized Shannon’s entropy index (EI) as:

$$
EI = \frac{-\sum P_{ij} \ln(P_{ij})}{\ln n_j}
$$

Where:

- \( P_{ij} \) is the proportion of land use \( i \) (e.g., greenspace) in area \( j \) (e.g., 1-km grid cells or other polygon), and
- \( n_j \) is the number of distinct land uses in area \( j \).

However, this approach does not account for the presence of residential areas, which is a crucial consideration for predicting physical activity/interaction with greenspaces, so a residential area coefficient, \( R \), is calculated as:

$$
R = \frac{r_j}{T_j}
$$

where \( r_j \) is the number of residential pixels (CDL class 122 – developed/low intensity) in area \( j \), and \( T_j \) is the number of pixels in area \( j \). Finally, because entropy measures emphasize diversity of LU/LC, we apply a random correction to 1-km cells where all CDL pixels are classified as greenspace. This ensures that larger homogeneous greenspaces that may be less immediately accessible than neighborhood parks or other greenspaces that are adjacent to residential areas are still characterized as accessible greenspace but are overall weighted lower given the absence of residential land cover.

We do this by randomly adjusting the number of target greenspace pixels \( n_j \) if it equals \( T_j \), the number of pixels in a grid cell. The adjusted proportion of matching greenspace pixels, \( \tilde{P}_{ij} \), is notated as:

$$
\tilde{P}_{ij} = \frac{T_j - \delta_j}{T_j}
$$

where \( \delta_j \) is a random adjustment term defined as \( \delta_j \in \lfloor 1,10 \rfloor \) applied when \( n_j = T_j \). The adjusted entropy index is thus:

$$
\tilde{EI} = \frac{\sum P_{ij} \ln(P_{ij})}{\ln n_j} \cdot (1 + R_j)
$$

We calculate greenspace using the adjusted entropy index in a Jupyter Notebook (Python version 3.11.8) using ArcPy (version 3.3).
