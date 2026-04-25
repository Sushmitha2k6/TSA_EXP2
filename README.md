# Ex.No: 02 LINEAR AND POLYNOMIAL TREND ESTIMATION
Date:
### AIM:
To Implement Linear and Polynomial Trend Estiamtion Using Python.

### ALGORITHM:
Import necessary libraries (NumPy, Matplotlib)

Load the dataset

Calculate the linear trend values using least square method

Calculate the polynomial trend values using least square method

End the program
### PROGRAM:
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Load dataset
df = pd.read_csv('/content/test.csv')

# Clean column names
df.columns = df.columns.str.strip().str.lower()

# Convert date column
df['order_date'] = pd.to_datetime(df['order_date'])

# Combine duplicate dates (important)
df = df.groupby('order_date')['total_revenue'].sum().to_frame()

# Resample yearly (fixed 'Y' → 'YE')
resampled_data = df.resample('YE').sum()

# Convert index to year
resampled_data.index = resampled_data.index.year
resampled_data.reset_index(inplace=True)
resampled_data.rename(columns={'order_date': 'Year'}, inplace=True)

# Extract values
years = resampled_data['Year'].tolist()
revenue = resampled_data['total_revenue'].tolist()

# =========================
# 📈 Linear Trend
# =========================
X = [i - years[len(years)//2] for i in years]

n = len(X)

# Handle small dataset safely
if n < 2:
    print("Not enough data for trend analysis")
else:
    x2 = [i**2 for i in X]
    xy = [i*j for i, j in zip(X, revenue)]

    b = (n*sum(xy) - sum(revenue)*sum(X)) / (n*sum(x2) - (sum(X)**2))
    a = (sum(revenue) - b*sum(X)) / n

    linear_trend = [a + b*X[i] for i in range(n)]

    # =========================
    # 📊 Polynomial Trend (SAFE METHOD)
    # =========================
    coeffs = np.polyfit(X, revenue, 2)   # avoids singular matrix error
    a_poly, b_poly, c_poly = coeffs

    poly_trend = [a_poly*(X[i]**2) + b_poly*X[i] + c_poly for i in range(n)]

    # =========================
    # 📉 Visualization
    # =========================
    print(f"Linear Trend: y = {a:.2f} + {b:.2f}x")
    print(f"Polynomial Trend: y = {a_poly:.2f}x² + {b_poly:.2f}x + {c_poly:.2f}")

    resampled_data['Linear Trend'] = linear_trend
    resampled_data['Polynomial Trend'] = poly_trend

    resampled_data.set_index('Year', inplace=True)

    # Plot
    resampled_data['total_revenue'].plot(marker='o', label='Actual Revenue')
    resampled_data['Linear Trend'].plot(linestyle='--', label='Linear Trend')
    resampled_data['Polynomial Trend'].plot(marker='o', label='Polynomial Trend')

    plt.title('Amazon Sales Trend Analysis')
    plt.xlabel('Year')
    plt.ylabel('Revenue')
    plt.legend()
    plt.grid(True)

    plt.show()
```
### OUTPUT
<img width="708" height="551" alt="image" src="https://github.com/user-attachments/assets/9d2699ed-c45f-45b9-bc85-7906a48c7521" />


### RESULT:
Thus the python program for linear and Polynomial Trend Estiamtion has been executed successfully.
