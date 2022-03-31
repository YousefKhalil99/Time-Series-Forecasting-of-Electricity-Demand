# Time-Series-Forecasting-of-Electricity-Demand

This project provides a model to forecast electricity demand in the UK one day ahead. To predict next-day load at each day, the current day load is needed first. Therefore, it is necessary to make recursive predictions where the next-day prediction uses previous ones as predictors. This process is known as recursive multi-step forecasting which can be thought of as a recursive AR(d) model where d is the number of lags used to predict the load. 


The main idea is that log load at time (t-1) is estimated based on the log loads from time (t-2) to time (t-d). Then, the log load at time t will be estimated based on the estimate of log load at time (t-1) and log loads from time (t-2) to (t-d). We add exogenous variables to this model such as average daily temperature, sunlight etc.

## Introduced Exogenous Variables
To improve performance, we introduce the following exogenous variables into the AR model:
1. **Average Daily Temperature and Daily Sunshine**: we calculate the average temperature and Sunshine per day for past values and assume that actual temperature will be very close to the temperature forecast for future dates.
2. **Weekday**: we create a binary variable which is equal to 1 if the day is a weekday and 0 for the weekend.
3. **Heating effect**: we assume that a significant part of the change in electricity load that is associated with temperature is due to household heating during the cold months of the year. Therefore, we create a dummy variable which is equal to 1 during the cold months of the year (October to March) and 0 otherwise.  Additionally, we will create an interaction term between the average temperature and the dummy variable to account for the varying response in electricity load to a change in temperature accoring to the season. The final model equation is:

![image](https://user-images.githubusercontent.com/73074313/161027311-99bebf9e-d4a1-4c11-9dbc-d105b7c1550d.png)


To implement this approach, we use the skforecast library in Python that allows the use of scikit-learn regressors in recursive autoregression. We first standardize the training data with StandardScaler() and train the model with a ridge regression which minimizes the sum of squared residuals like an OLS regression but also uses a loss function which penalizes the size of parameter estimates to decrease the model complexity (overfitting).


Then, we use the trained forecaster to predict the data and produce a 95% prediction interval with a mean, upper bound,lower bound. We measure the performance of this model by what percentage of actual load values in the test data lay within the 95% prediction interval. The model generalized well to new data, with 96% of actual load values being within the 95% prediction interval.

![image](https://user-images.githubusercontent.com/73074313/161027538-85644c3b-c3a2-4154-a97d-06510a2fe8ae.png)






