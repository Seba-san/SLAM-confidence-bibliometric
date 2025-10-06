# Generation of Figure 1: The Shifting Focus of SLAM Research

This document outlines the methodology, data, and code used to generate Figure 1, "The Shifting Focus of SLAM Research: A Relative Decline in Uncertainty Estimation," for our paper, "The SLAM Confidence Trap."

## Methodology

The data for this bibliometric analysis was collected from Google Scholar by performing annual publication counts from 1995 to 2025. The process was designed to capture the relative publication trends of different SLAM paradigms.

### 1. Data Source
All publication counts were obtained from **Google Scholar**. The queries were executed for each year in the specified range.

### 2. Search Queries
A base query was first established to determine the total number of relevant SLAM publications per year. Subsequently, specific queries were formulated to count publications related to three key research themes: Filter-based methods, Optimization-based methods, and the explicit treatment of Uncertainty.

**Base Query:**
The foundational query to identify the total corpus of SLAM literature was:
```
"SLAM" OR "Simultaneous Localization and Mapping" OR ("map building" AND "mobile robot")
```

**Categorical Queries:**

*   **Filter-based Methods (`Filter`):** To capture literature related to traditional probabilistic filtering approaches.
    ```
    "EKF SLAM" OR ("Kalman" AND "SLAM") OR ("Particle Filter" AND "SLAM") OR ("Rao-Blackwellized" AND "SLAM") OR ("RBPF" AND "SLAM")
    ```

*   **Optimization-based Methods (`Graph`):** To capture literature related to modern graph-based and bundle adjustment techniques.
    ```
    ("Graph SLAM" OR "Pose Graph Optimization" OR ("Bundle Adjustment" AND "SLAM") OR "g2o" OR "GTSAM" OR "Ceres Solver" OR "iSAM" OR ("Factor Graph" AND "SLAM") OR ("Non-Linear Least Squares" AND "SLAM") OR "Smoothing and Mapping" OR (("Loop Closure" OR "Loop Closing") AND "SLAM"))
    ```

*   **Uncertainty (`Uncertainty`):** To capture literature that explicitly addresses uncertainty, covariance, or probabilistic consistency, while excluding the recent surge in learning-based uncertainty.
    ```
    (("covariance" OR "uncertainty") AND "SLAM") -"neural" -"learning"
    ```

### 3. Journal & Conference Filtering
To ensure the quality and relevance of the search results, all queries were restricted to a curated list of top-tier robotics, computer vision, and AI journals and conferences:
```
source:"IEEE Robotics and Automation Letters" OR source:"IEEE International Conference on Robotics and Automation" OR source:"Conference on Robot Learning" OR source:"Science Robotics" OR source:"Robotics and Computer-Integrated Manufacturing" OR source:"IEEE Transactions on Robotics" OR source:"Intelligent Robots and Systems (IROS)" OR source:"Robotics Science and Systems" OR source:"IEEE Transactions on Automation Science and Engineering" OR source:"IEEE/ASME Transactions on Mechatronics" OR source:"Advanced Intelligent Systems" OR source:"The International Journal of Robotics Research" OR source:"International Journal of Social Robotics" OR source:"Robotics and Autonomous Systems" OR source:"Frontiers in Robotics and AI" OR source:"Soft Robotics" OR source:"Journal of Field Robotics" OR source:"Journal of Intelligent & Robotic Systems" OR source:"ACM/IEEE International Conference on Human Robot Interaction" OR source:"Robotics" OR source:"Intelligent Robots and Systems" OR source:"Artificial Intelligence" OR source:"Conference on Artificial Intelligence" OR source:"Journal of Artificial Intelligence Research" OR source:"IEEE Transactions on Pattern Analysis and Machine Intelligence" OR source:"International Journal of Computer Vision" OR source:"Autonomous robot vehicles" OR source:"IROS"
```

**Note on Query Execution:** Due to the length constraints of the search engine, the `Graph` query was split into two halves. Each half was run with the full list of journal filters, and the results were summed to obtain the final count for each year.

## Data

The following table contains the raw publication counts obtained for each category per year. The "Proportion of Publications (%)" shown in the final figure was calculated by dividing the count for each category by the `Base` count for that year and multiplying by 100.

| Year | Base | Uncertainty / Covariance | Filter-based | Optimization-based |
| :--: | :--: | :--: | :--: | :--: |
| 1995 | 47 | 0 | 0 | 0 |
| 1996 | 41 | 2 | 2 | 0 |
| 1997 | 49 | 0 | 1 | 0 |
| 1998 | 70 | 2 | 4 | 0 |
| 1999 | 60 | 2 | 3 | 0 |
| 2000 | 79 | 15 | 14 | 0 |
| 2001 | 106 | 13 | 22 | 3 |
| 2002 | 211 | 37 | 53 | 7 |
| 2003 | 217 | 52 | 72 | 7 |
| 2004 | 259 | 81 | 119 | 12 |
| 2005 | 318 | 101 | 158 | 15 |
| 2006 | 344 | 122 | 187 | 24 |
| 2007 | 366 | 157 | 230 | 44 |
| 2008 | 437 | 134 | 215 | 40 |
| 2009 | 422 | 164 | 224 | 66 |
| 2010 | 488 | 178 | 270 | 130 |
| 2011 | 424 | 168 | 255 | 157 |
| 2012 | 424 | 177 | 266 | 177 |
| 2013 | 468 | 202 | 288 | 231 |
| 2014 | 477 | 195 | 288 | 183 |
| 2015 | 467 | 207 | 332 | 312 |
| 2016 | 462 | 179 | 280 | 289 |
| 2017 | 464 | 216 | 305 | 292 |
| 2018 | 497 | 204 | 333 | 375 |
| 2019 | 586 | 213 | 383 | 400 |
| 2020 | 620 | 198 | 419 | 471 |
| 2021 | 633 | 169 | 451 | 521 |
| 2022 | 769 | 190 | 515 | 612 |
| 2023 | 689 | 152 | 450 | 441 |
| 2024 | 878 | 196 | 608 | 762 |
| 2025 | 544 | 81 | 431 | 390 |

## MATLAB Plotting Script

The following MATLAB script was used to process the raw data and generate the final figure. The script normalizes the data, fits a polynomial trendline to each series, and plots the trend along with a 68.2% confidence interval (equivalent to one standard deviation) to visualize the underlying trend and its uncertainty.

```matlab
%% FINAL SCRIPT: "SLAM CONFIDENCE TRAP" WITH CONFIDENCE BANDS
% This script is robust and ensures maximum compatibility and stability.
% Key features:
% 1. Uses 'Normalize', 'on' in fit() to prevent "badly conditioned" warnings.
% 2. Employs standard plot() after manual evaluation of the fit object for full control over styling.

% --- 1. Initialization and Data Loading ---
clear; clc; close all;

Year = 1995:2025;
Total_Publications = [47, 41, 49, 70, 60, 79, 106, 211, 217, 259, 318, 344, 366, 437, 422, 488, 424, 424, 468, 477, 467, 462, 464, 497, 586, 620, 633, 769, 689, 878, 544];
Uncertainty_Covariance = [0, 2, 0, 2, 2, 15, 13, 37, 52, 81, 101, 122, 157, 134, 164, 178, 168, 177, 202, 195, 207, 179, 216, 204, 213, 198, 169, 190, 152, 196, 81];
Filter_Based = [0, 2, 1, 4, 3, 14, 22, 53, 72, 119, 158, 187, 230, 215, 224, 270, 255, 266, 288, 288, 332, 280, 305, 333, 383, 419, 451, 515, 450, 608, 431];
Optimization_Based = [0, 0, 0, 0, 0, 0, 3, 7, 7, 12, 15, 24, 44, 40, 66, 130, 157, 177, 231, 183, 312, 289, 292, 375, 400, 471, 521, 612, 441, 762, 390];

% Normalize data to percentage (%)
Uncertainty_Covariance_p = (Uncertainty_Covariance ./ Total_Publications) * 100;
Filter_Based_p = (Filter_Based ./ Total_Publications) * 100;
Optimization_Based_p = (Optimization_Based ./ Total_Publications) * 100;


% --- 2. Visual Style Configuration ---
figure(1); clf;
hold on;

% Color palette and styles
color_uncertainty  = [153, 119, 0] / 255;
color_filter       = [187, 85, 102] / 255;
color_optimization = [0, 68, 136] / 255;
color_grey         = [187, 187, 187] / 255;
fontName           = 'Helvetica';
lineWidth_trend    = 2;
confidence_alpha   = 0.15;
fit_type           = 'poly7'; % 7th-degree polynomial for a smooth fit

% --- 3. Trend Calculation and Plotting with Confidence Bands ---
all_data = {Uncertainty_Covariance_p, Filter_Based_p, Optimization_Based_p};
all_colors = {color_uncertainty, color_filter, color_optimization};
all_names = {'Uncertainty / Covariance', 'Filter-based', 'Optimization-based'};

for i = 1:length(all_data)
    data = all_data{i};
    color = all_colors{i};
    name = all_names{i};
    
    year_col = Year(:);
    data_col = data(:);
    
    % Fit the model with normalization for numerical stability
    fit_object = fit(year_col, data_col, fit_type, 'Normalize', 'on');
    
    % Calculate the 68.2% confidence intervals (one sigma) for the trend
    conf_intervals = predint(fit_object, year_col, 0.682, 'observation', 'off');
    
    % Draw the confidence band
    x_coords = [year_col; flipud(year_col)];
    y_coords = [conf_intervals(:,2); flipud(conf_intervals(:,1))];
    fill(x_coords, y_coords, color, 'FaceAlpha', confidence_alpha, 'EdgeColor', 'none', 'HandleVisibility', 'off');
    
    % Evaluate the fit to get the trend line values
    y_trend = fit_object(year_col);
    
    % Plot the main trend line using the standard plot function for full control
    plot(year_col, y_trend, 'Color', color, 'LineWidth', lineWidth_trend, 'DisplayName', name);
    
    % Plot the raw data points and a connecting dashed line
    plot(year_col, data_col, '.', 'Color', color, 'MarkerSize', 10, 'HandleVisibility', 'off');
    plot(year_col, data_col, '--', 'Color', color_grey, 'HandleVisibility', 'off');
end


% --- 4. Final Plot Refinements and Annotations ---
ax = gca;
ax.FontName = fontName;
ax.FontSize = 12;
grid on; box on;
ax.GridLineStyle = ':';
ax.GridAlpha = 0.5;

title({'The Shifting Focus of SLAM Research:', 'A Relative Decline in Uncertainty Estimation'}, 'FontWeight', 'bold', 'FontSize', 14);
xlabel('Year', 'FontSize', 12);
ylabel('Proportion of Publications (%)', 'FontSize', 12);
xlim([min(Year)-0.5, max(Year)+0.5]);
ylim([-5, 90]);

lgd = legend('show', 'Location', 'northwest');
lgd.FontSize = 12;
lgd.Box = 'on'; % Changed to 'on' for better visibility

hold off;
```

## How to Cite

If you use the insights, data, or methodology from this study in your research, please cite our paper. We appreciate the acknowledgment of our work.

### BibTeX

For users of LaTeX, the following BibTeX entry can be used:

```bibtex
@inproceedings{Sansoni2025SLAM,
  author    = {Sansoni, Sebastian and Tosetti, Santiago Ram\'{o}n},
  title     = {The {SLAM} Confidence Trap},
  booktitle = {Proceedings of the International Conference on Advanced Robotics (ICAR)},
  year      = {2025}
}
```

### Plain Text Citation

For other formats, you can use the following plain text citation:

> S. Sansoni and S. R. Tosetti, "The SLAM Confidence Trap," in *Proceedings of the International Conference on Advanced Robotics (ICAR)*, 2025.
