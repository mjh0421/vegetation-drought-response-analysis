Python code for vegetation drought-response analysis, including data resampling, calculation of vegetation drought-response intensity (Rmax) and optimal drought accumulation timescale (Topt), identification of water-deficit and water-surplus regions, maximum-statistic block permutation and BH-FDR significance testing, trend-preserving residual moving-block bootstrap, temporal trend analysis, and XGBoost-SHAP analysis with nested spatial cross-validation.

## Input and preprocessing requirements

All input rasters used within a given workflow should share the same spatial grid, including coordinate reference system (CRS), spatial resolution, spatial extent, raster dimensions, and pixel alignment. Missing or invalid values are treated as NaN where applicable.

Vegetation, SPEI, environmental predictor, and trend rasters should be mapped to the corresponding manuscript variables according to the file-to-variable mapping documented in the relevant script header comments.

Detailed definitions of the datasets, variable construction, preprocessing procedures, masking criteria, and statistical methods are provided in the Methods and Supplementary Information.

## Execution order

The analyses were conducted in the following order:

1. **Generate moving-window Rmax and Topt**
   - Calculate vegetation–SPEI correlations for the seven growing-season months (April–October) and 24 SPEI accumulation timescales.
   - Select the maximum positive correlation as Rmax and the corresponding SPEI accumulation timescale as Topt for each 17-year moving window.
   - Repeat the analysis using 21-year moving windows as a window-length sensitivity test.

2. **Assess Rmax significance and Topt uncertainty**
   - Apply the 2-year block maximum-statistic permutation test to assess the significance of Rmax while accounting for selection across the 168 month × SPEI-timescale combinations.
   - Apply the trend-preserving residual moving-block bootstrap to quantify uncertainty in Rmax, evaluate the stability of Topt selection, and propagate Topt selection uncertainty into its temporal trend.

3. **Calculate temporal trends**
   - Retain pixels with complete estimates across all 24 windows in the 17-year analysis and all 20 windows in the 21-year sensitivity analysis.
   - Calculate pixel-level Theil–Sen slopes for Rmax and Topt.
   - Assess trend significance using the Hamed–Rao modified Mann–Kendall test.

4. **Water-availability classification**
   - Classify pixels as water-deficit or water-surplus according to the relative numbers of significant positive and negative vegetation–SPEI correlations.
   - Apply BH-FDR correction across the 168 month × SPEI-timescale correlations within each pixel.
   - Repeat the classification using block-permutation significance testing followed by BH-FDR correction to account for temporal dependence and multiple testing.

5. **Class-specific level and trend comparisons**
   - Compare class-specific Rmax and Topt levels under the block-permutation + BH-FDR classification.
   - Calculate class-specific temporal trends under the original, BH-FDR, and block-permutation + BH-FDR water-availability classifications.
   - Repeat the class-specific trend analysis across vegetation indices and moving-window lengths as sensitivity analyses.

6. **Fixed-month Topt and selected-month analysis**
   - Track the calendar month associated with the joint maximum vegetation–SPEI correlation.
   - Recalculate Topt separately within each growing-season month by selecting the maximum positive correlation across the 24 SPEI accumulation timescales.
   - Retain only pixels with valid fixed-month Topt estimates across all 24 17-year moving windows for trend analysis.
   - Generate the fixed-month Topt trend summary reported in Table S4.

7. **XGBoost–SHAP analysis**
   - Use the complete-case Rmax and Topt trend rasters as target variables.
   - Fit XGBoost models using the retained climatic and environmental predictors.
   - Evaluate model performance using nested spatial cross-validation.
   - Calculate SHAP values to assess nonlinear associations between the predictors and the spatial variation in Rmax and Topt trends.
   - The exact target and predictor file mappings used in the manuscript are documented in the header comments of the XGBoost script.

8. **Figure and table preparation**
   - Python scripts generate the raster, CSV, and statistical outputs underlying the reported analyses.
   - Final map visualization was performed in ArcGIS Pro.
   - Final statistical figure preparation and figure assembly were performed in Origin.
   - The repository is intended to reproduce the analytical workflows and the intermediate outputs underlying the manuscript results, rather than to provide a fully automated end-to-end figure-generation pipeline.

## Correspondence between workflow outputs and manuscript items

- **Fig. S15**: water-availability classification based on Pearson significance followed by BH-FDR correction.
- **Fig. S16**: water-availability classification based on block-permutation significance testing followed by BH-FDR correction.
- **Fig. S17**: class-specific Rmax and Topt temporal trends used to assess sensitivity to moving-window length and vegetation index under the original water-availability classification.
- **Fig. S18**: class-specific Rmax and Topt temporal trends in water-deficit and water-surplus regions defined using Pearson significance followed by BH-FDR correction.
- **Fig. S19**: class-specific Rmax and Topt temporal trends in water-deficit and water-surplus regions defined using block-permutation significance testing followed by BH-FDR correction.
- **Table S4**: fixed-month Topt trend analysis across April–October.

## Notes on figure reproducibility

The analytical scripts reproduce the numerical and spatial outputs used to construct the manuscript figures and tables. Final cartographic styling, panel arrangement, annotation, and figure formatting were performed separately in ArcGIS Pro and Origin. Therefore, the scripts generate the underlying raster and tabular products rather than the final publication-ready figure layouts.
