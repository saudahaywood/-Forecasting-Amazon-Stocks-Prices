
# **Forecasting Amazon Stock Prices: A Time-Series Analysis**

## **Project Overview**
This project aims to forecast **Amazon’s stock prices** by predicting the **Volume Weighted Average Price (VWAP)** using historical stock data. The **ARIMA model** is implemented in **R** to capture trends and make data-driven stock price predictions. The insights derived from this analysis can be useful for **investors and financial analysts** looking to optimize their trading strategies.

## **Dataset**
- **Source:** [NASDAQ](https://www.nasdaq.com/market-activity/stocks/amzn/historical)
- **Features:**
  - **Date:** Trading date
  - **Close:** Closing stock price
  - **Volume:** Number of shares traded
  - **Open, High, Low:** Stock price movements within a trading day
  - **VWAP:** A key indicator representing the **average price based on volume and price movements**

## **Methodology**
### **Step 1: Load the Data**
- Convert the **Date column** to datetime format for time-series analysis.
- Sort the data by **Date**.
- View the first few rows of the dataset.

### **Step 2: Feature Engineering**
To improve forecasting accuracy, rolling statistics were created:
- **Rolling Means:** 3-day and 7-day moving averages for **Close, Volume, Open, High, and Low prices**.
- **Rolling Standard Deviations:** 3-day and 7-day moving standard deviations for key variables.
- **Handling Missing Values:** Rows with missing values were removed.

### **Step 3: Train-Test Split**
- **80%** of the dataset was used for training.
- **20%** was reserved for testing.

### **Step 4: Time-Series Modeling with ARIMA**
- Used **Auto.ARIMA** in **R** to automatically select the best model configuration based on **Akaike Information Criterion (AIC)**.
- Trained the **ARIMA model** on VWAP values.
- Forecasted stock prices for the test dataset.

### **Step 5: Plot Actual vs. Forecasted Values**
- Used **ggplot2** to visualize the actual VWAP vs. predicted VWAP values.
- The plotted time series shows how well the model forecasts Amazon’s stock trends.

### **Step 6: Model Evaluation**
The performance of the model was assessed using the following metrics:
- **Root Mean Squared Error (RMSE):** **5.133**
- **Mean Absolute Error (MAE):** **4.329**

These results indicate that the predicted VWAP deviates by approximately **4.33 to 5.13 units** from actual values, demonstrating reasonable forecasting accuracy.

## **Challenges and Limitations**
### **Challenges**
- Initial implementation in **Python** faced issues with datetime indexing errors.
- Switching to **R** provided smoother implementation and model training.

### **Limitations**
- **No External Market Factors Considered:** The model does not account for **macroeconomic events, earnings reports, or news sentiment**.
- **Limited to Historical Patterns:** The model does not adapt well to **sudden stock market shifts** influenced by external factors.

## **Future Enhancements**
To improve the model, the following enhancements are planned:
1. **Integrate External Variables:** Include **market sentiment, economic indicators, and earnings reports**.
2. **Explore Machine Learning Models:** Use **LSTMs (Long Short-Term Memory networks)** for advanced time-series predictions.
3. **Expand to Other Stocks:** Apply similar methodologies to other companies.
4. **Automate Data Updates:** Implement **API-based real-time data retrieval** for continuous forecasting.

## **Implementation Plan**
1. **Automate Data Collection** using stock market APIs.
2. **Model Deployment:** Train and validate the model periodically.
3. **Performance Monitoring:** Regularly update and retrain the model.

## **Ethical Considerations**
- **Transparency:** Clearly communicate model limitations to prevent misleading financial decisions.
- **Fairness:** Ensure the model does not favor particular market participants.
- **Responsible Use:** Prevent misuse of forecasts in **manipulative trading strategies**.

## **Conclusion**
The **ARIMA model** successfully predicted **Amazon’s VWAP**, providing valuable insights for investors. While the model performed well, further enhancements—such as integrating **additional data sources** and using **machine learning-based models**—can improve accuracy. By continuously updating and refining the model, a **more robust financial forecasting system** can be developed.

## **Required Packages**
To run this project, install the following **R** packages:
```r
install.packages(c("tidyverse", "zoo", "forecast", "ggplot2"))
```

**Libraries used**:
```r
library(tidyverse)
library(zoo)
library(forecast)
library(ggplot2)
```

## **Running the Project**
1. **Load dataset**:
   ```r
   df <- read_csv('amazon_stock.csv') %>%
         mutate(Date = mdy(Date)) %>%
         arrange(Date)
   ```
2. **Preprocess data**:
   ```r
   df <- df %>%
        mutate(
          Close_rolling_mean_3 = rollmean(Close, k = 3, fill = NA, align = "right"),
          Close_rolling_mean_7 = rollmean(Close, k = 7, fill = NA, align = "right"),
          Close_rolling_std_3 = rollapply(Close, width = 3, FUN = sd, fill = NA, align = "right"),
          Close_rolling_std_7 = rollapply(Close, width = 7, FUN = sd, fill = NA, align = "right")
        ) %>%
        na.omit()
   ```
3. **Split the data into training and testing sets**:
   ```r
   train_size <- floor(0.8 * nrow(df))
   training_data <- df[1:train_size, ]
   test_data <- df[(train_size + 1):nrow(df), ]
   ```
4. **Train the ARIMA model**:
   ```r
   arima_model <- auto.arima(training_data$VWAP)
   forecast_values <- forecast(arima_model, h = nrow(test_data))
   test_data$Forecast_ARIMA <- as.numeric(forecast_values$mean)
   ```
5. **Visualize predictions**:
   ```r
   ggplot(test_data, aes(x = Date)) +
       geom_line(aes(y = VWAP, color = "Actual")) +
       geom_line(aes(y = Forecast_ARIMA, color = "Forecasted")) +
       labs(title = "VWAP vs Forecasted VWAP",
            x = "Date",
            y = "Price",
            color = "Legend") +
       theme_minimal()
   ```
6. **Evaluate the model**:
   ```r
   rmse <- sqrt(mean((test_data$VWAP - test_data$Forecast_ARIMA)^2, na.rm = TRUE))
   mae <- mean(abs(test_data$VWAP - test_data$Forecast_ARIMA), na.rm = TRUE)
   cat("RMSE:", rmse, "\n")
   cat("MAE:", mae, "\n")
   ```

## **References**
- [NASDAQ Historical Data](https://www.nasdaq.com/market-activity/stocks/amzn/historical)
