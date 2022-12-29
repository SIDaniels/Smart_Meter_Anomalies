# Smart Meter Anomaly Detection
## Background
The built environment will require major energy reforms in order to reach net zero targets by 2050 and the Paris Agreement climate goal of a global warming threshold at 1.5C. Currently, building operations are powered by non-renewable energy sources, namely fossil fuels, which contribute 28% of all greenhouse gas emissions. On average,  [30% of energy](https://www.energy.gov/eere/buildings/about-commercial-buildings-integration-program) consumed by buildings is wasted, which provides a wide window for improvement in energy efficiency. While this is in large part due to inefficiencies in heating/cooling, lighting, ventilation, and the building envelope ([ref](https://www.energy.gov/sites/prod/files/2017/03/f34/qtr-2015-chapter5.pdf)) , including smart sensor technology for real-time feedback on aberrant energy use is also a critical factor to reducing waste. As countries inch towards decarbonizing the energy supply to the grid, greater energy efficiency will also help reduce the overall load. [The World Economic Forum reports](https://www.weforum.org/agenda/2021/09/how-to-build-zero-carbon-buildings/) that two dollars of energy supply are saved for every dollar invested in energy saving measures and minimizes the costs to reach growing energy demands. Digitalization of metering, sensors, and controls can provide the necessary feedback loops for remote management of buildings’ energy use. “Smart” metering is one component of advanced metering infrastructure (AMI), which also includes communications networks and data management systems that enables two-way communication between the utility and its customers- thereby creating a new layer of improving energy efficiency for overall emissions reductions. Smart metering has become ubiquitous over the past several years, as a crucial component of the energy transition. By 2020, over 100 million smart meters were installed nationwide, which is over 65% of the market. Even though energy utilities now have greater access to this near real-time data, there are only [a few utilities](https://www.aceee.org/blog-post/2020/01/smart-meters-gain-popularity-most-utilities-dont-optimize-their-potential-save) that are using it for optimization. 
The task is to build an anomaly detection model using data obtained initially from American Society of Heating, Refrigerating and Air-Conditioning Engineers (ASHRAE). With near real-time detection of anomalous measurements, a  utility can oversee any unusual activity that may be an early indicator of malfunctions, outages, leaks or other hazards to energy efficiency. Ultimately, the utilities could even build in energy-use notifications to building managers for added benefits of immediate building safety and cost-savings. 
## Design
 A [Kaggle competition on smart metering data](https://www.kaggle.com/competitions/ashrae-energy-prediction/overview) for commercial buildings was originally posted by ASHRAE three years ago and contains metering of chilled water, electric, hot water, and steam meters. Recently, anomaly tags have been appended to a training dataset by te Large-scale Energy Anomaly Detection (LEAD) team for solely the electric metering and posted in Kaggle as [a new competition](https://www.kaggle.com/competitions/energy-anomaly-detection/overview/evaluation). Labeling involved two different strategies; 1) Point anomalies - screening each distinct instance for unusual activity as compared to its immediate neighbors and overall consumption and 2) Sequential anomalies - multiple instances or a series of unusual activity as compared to the neighboring time points or globally. Full details of this labeling process can be found [here](https://arxiv.org/pdf/2203.17256.pdf). It is one of few smart meter datasets to include a form of labeling and contains large training dataset of year-long hourly electricity meter readings with anomaly annotations for 200 buildings with each instance labeled as anomalous (1) or normal (0). Features of the dataset include building type (e.g.., education, office, parking), square feet, year built, floor count, air temperature with several different lags, cloud coverage, wind measurements, weekday/weekend/holiday tags. 
## Objective
The objective is to build a model that correctly indicates anomalous measurements in a separate test dataset. The proposed anomaly detection system must minimize the false positive rate, so that the efforts of investigating erratic sensor behavior can be cost-effective. While this precision is given priority, the false negative rate should also not be sacrificed, hence maximizing F1 score (balancing precision and recall) will be of greatest interest.
## Data
There are over 1 million observations of electric sensor meter readings acquired from 200 unique buildings (with 13 features) along with associated weather data within proximity (with 7 features) during one year. There is a roughly balanced mix of categorical and continuous variables, with designated time intervals (hour, weekday, month) requiring a transformation to cyclical features. Categorical features used for these initial models (i.e., KNN, logistic regression) were changed to dummy variables and numerical features were standardized. ASHRAE feature engineered lag-variables for temperature. Additionally, ASHRAE designed multiple stacked features that essentially grouped the meter readings by a given category and used a Bayesian approach to determine the mean of each sub-distribution. The train/test split was divided using building ID in order to minimize overfitting due to this feature. 
## Algorithms
### Feature Engineering
- Multiple fields were removed due to the data split on building ID and the lack of need for 
- Categorical features were converted to a matrix of dummy variables
- Imputations were made for null value meter readings (only present for non-anomaly data), assuming the mean of each unique building. 
### Models
K-nearest neighbors (KNN) and logistic regression were initially used for baseline modeling, with KNN providing decent performance (F1-score = )  and logistic regression yielding no detection of anomalies. Weighting and reducing the threshold for the logistic regression model allowed for greater recall but still minimal precision. Decision trees were employed, with the primary objective of improving upon the F1 score. Random Forest and XGBoost both showed significant improvement over KNN and logistic regression.
### Model Evaluation and Selection
The training dataset included 1399843 entries of sensor meter readings while the test set contained 349651 entries (~25%). Scores reported below were calculated with 5-fold cross validation on the training portion only. Predictions on the test (holdout) set were used as a final determination of F1-score. The top 5 features of importance in obtaining the most optimal metrics includes the meter readings (in energy consumption in kWh), daily standard deviation of the meter readings, the square footage of the building, the month of the year (using the cyclical sin/cos features), and a stacked feature (meter reading per weather monitoring instrument site ID).

#### Random forest 5-fold CV scores: 
40 features (including dummy variables for primary building use)  
Training Results:  
Accuracy: 0.99  
F1: 0.66  
Precision: 0.87  
Recall: 0.54  

Test Results:  
Accuracy: 0.99  
F1: 0.80  
Precision: 0.93  
Recall: 0.70  
## Tools
- Numpy and Pandas - data manipulation
- Scikit-learn classification models
- Matplotlib, plotly and Seaborn for visualizing model results


