---
layout: wide_default
---


```python
import pandas as pd
from statsmodels.formula.api import ols as sm_ols
import numpy as np
import seaborn as sns
from statsmodels.iolib.summary2 import summary_col # nicer tables

```


```python
url = 'https://github.com/LeDataSciFi/ledatascifi-2022/blob/main/data/Fannie_Mae_Plus_Data.gzip?raw=true'
fannie_mae = pd.read_csv(url,compression='gzip') 
fannie_mae["Origination_Date"]
```




    0         2007-02-01
    1         2007-02-01
    2         2007-02-01
    3         2007-02-01
    4         2007-02-01
                 ...    
    135033    2018-06-01
    135034    2018-06-01
    135035    2018-06-01
    135036    2018-06-01
    135037    2018-06-01
    Name: Origination_Date, Length: 135038, dtype: object



## Clean the data and create variables you want


```python
fannie_mae = (fannie_mae
                  # create variables
                  .assign(l_credscore = np.log(fannie_mae['Borrower_Credit_Score_at_Origination']),
                          l_LTV = np.log(fannie_mae['Original_LTV_(OLTV)']),
                          l_int = np.log(fannie_mae['Original_Interest_Rate']),
                          Origination_Date = lambda x: pd.to_datetime(x['Origination_Date']),
                          Origination_Year = lambda x: x['Origination_Date'].dt.year,
                          const = 1
                         )
                  .rename(columns={'Original_Interest_Rate':'int'}) # shorter name will help the table formatting
             )

# create a categorical credit bin var with "pd.cut()"
fannie_mae['creditbins']= pd.cut(fannie_mae['Co-borrower_credit_score_at_origination'],
                                 [0,579,669,739,799,850],
                                 labels=['Very Poor','Fair','Good','Very Good','Exceptional'])
fannie_mae
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Loan_Identifier</th>
      <th>Origination_Channel</th>
      <th>Seller_Name</th>
      <th>int</th>
      <th>Original_UPB</th>
      <th>Original_Loan_Term</th>
      <th>Original_LTV_(OLTV)</th>
      <th>Original_Combined_LTV_(CLTV)</th>
      <th>Number_of_Borrowers</th>
      <th>Original_Debt_to_Income_Ratio</th>
      <th>...</th>
      <th>BOPGSTB</th>
      <th>GOLDAMGBD228NLBM</th>
      <th>CSUSHPISA</th>
      <th>MSPUS</th>
      <th>l_credscore</th>
      <th>l_LTV</th>
      <th>l_int</th>
      <th>Origination_Year</th>
      <th>const</th>
      <th>creditbins</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>9.733730e+11</td>
      <td>B</td>
      <td>OTHER</td>
      <td>6.875</td>
      <td>32000.0</td>
      <td>360.0</td>
      <td>90.0</td>
      <td>90.0</td>
      <td>1.0</td>
      <td>22.0</td>
      <td>...</td>
      <td>-58478.0</td>
      <td>665.1025</td>
      <td>184.601</td>
      <td>257400.0</td>
      <td>6.505784</td>
      <td>4.499810</td>
      <td>1.927892</td>
      <td>2007</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>9.276200e+11</td>
      <td>B</td>
      <td>PNC BANK, N.A.</td>
      <td>5.875</td>
      <td>200000.0</td>
      <td>360.0</td>
      <td>80.0</td>
      <td>80.0</td>
      <td>2.0</td>
      <td>26.0</td>
      <td>...</td>
      <td>-58478.0</td>
      <td>665.1025</td>
      <td>184.601</td>
      <td>257400.0</td>
      <td>6.541030</td>
      <td>4.382027</td>
      <td>1.770706</td>
      <td>2007</td>
      <td>1</td>
      <td>Good</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7.176670e+11</td>
      <td>B</td>
      <td>OTHER</td>
      <td>6.250</td>
      <td>122000.0</td>
      <td>180.0</td>
      <td>80.0</td>
      <td>80.0</td>
      <td>2.0</td>
      <td>31.0</td>
      <td>...</td>
      <td>-58478.0</td>
      <td>665.1025</td>
      <td>184.601</td>
      <td>257400.0</td>
      <td>6.608001</td>
      <td>4.382027</td>
      <td>1.832581</td>
      <td>2007</td>
      <td>1</td>
      <td>Very Good</td>
    </tr>
    <tr>
      <th>3</th>
      <td>9.889510e+11</td>
      <td>C</td>
      <td>AMTRUST BANK</td>
      <td>6.000</td>
      <td>67000.0</td>
      <td>180.0</td>
      <td>77.0</td>
      <td>77.0</td>
      <td>2.0</td>
      <td>17.0</td>
      <td>...</td>
      <td>-58478.0</td>
      <td>665.1025</td>
      <td>184.601</td>
      <td>257400.0</td>
      <td>6.689599</td>
      <td>4.343805</td>
      <td>1.791759</td>
      <td>2007</td>
      <td>1</td>
      <td>Exceptional</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.908850e+11</td>
      <td>R</td>
      <td>OTHER</td>
      <td>5.875</td>
      <td>50000.0</td>
      <td>180.0</td>
      <td>41.0</td>
      <td>41.0</td>
      <td>2.0</td>
      <td>10.0</td>
      <td>...</td>
      <td>-58478.0</td>
      <td>665.1025</td>
      <td>184.601</td>
      <td>257400.0</td>
      <td>6.489205</td>
      <td>3.713572</td>
      <td>1.770706</td>
      <td>2007</td>
      <td>1</td>
      <td>Good</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>135033</th>
      <td>9.204900e+11</td>
      <td>R</td>
      <td>OTHER</td>
      <td>4.625</td>
      <td>200000.0</td>
      <td>240.0</td>
      <td>50.0</td>
      <td>50.0</td>
      <td>1.0</td>
      <td>45.0</td>
      <td>...</td>
      <td>-47431.0</td>
      <td>1299.1500</td>
      <td>202.411</td>
      <td>315600.0</td>
      <td>6.575076</td>
      <td>3.912023</td>
      <td>1.531476</td>
      <td>2018</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>135034</th>
      <td>9.666890e+11</td>
      <td>R</td>
      <td>OTHER</td>
      <td>4.625</td>
      <td>94000.0</td>
      <td>360.0</td>
      <td>47.0</td>
      <td>47.0</td>
      <td>1.0</td>
      <td>39.0</td>
      <td>...</td>
      <td>-47431.0</td>
      <td>1299.1500</td>
      <td>202.411</td>
      <td>315600.0</td>
      <td>6.517671</td>
      <td>3.850148</td>
      <td>1.531476</td>
      <td>2018</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>135035</th>
      <td>6.616280e+11</td>
      <td>R</td>
      <td>OTHER</td>
      <td>4.625</td>
      <td>239000.0</td>
      <td>360.0</td>
      <td>74.0</td>
      <td>74.0</td>
      <td>2.0</td>
      <td>20.0</td>
      <td>...</td>
      <td>-47431.0</td>
      <td>1299.1500</td>
      <td>202.411</td>
      <td>315600.0</td>
      <td>6.669498</td>
      <td>4.304065</td>
      <td>1.531476</td>
      <td>2018</td>
      <td>1</td>
      <td>Good</td>
    </tr>
    <tr>
      <th>135036</th>
      <td>5.102850e+11</td>
      <td>R</td>
      <td>OTHER</td>
      <td>5.000</td>
      <td>93000.0</td>
      <td>360.0</td>
      <td>44.0</td>
      <td>44.0</td>
      <td>1.0</td>
      <td>19.0</td>
      <td>...</td>
      <td>-47431.0</td>
      <td>1299.1500</td>
      <td>202.411</td>
      <td>315600.0</td>
      <td>6.431331</td>
      <td>3.784190</td>
      <td>1.609438</td>
      <td>2018</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>135037</th>
      <td>4.330130e+11</td>
      <td>R</td>
      <td>OTHER</td>
      <td>5.125</td>
      <td>140000.0</td>
      <td>360.0</td>
      <td>94.0</td>
      <td>94.0</td>
      <td>1.0</td>
      <td>36.0</td>
      <td>...</td>
      <td>-47431.0</td>
      <td>1299.1500</td>
      <td>202.411</td>
      <td>315600.0</td>
      <td>6.492240</td>
      <td>4.543295</td>
      <td>1.634131</td>
      <td>2018</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>135038 rows × 42 columns</p>
</div>



## Statsmodels

As before, the psuedocode:
```python
model = sm_ols(<formula>, data=<dataframe>)
result=model.fit()

# you use result to print summary, get predicted values (.predict) or residuals (.resid)
```

Now, let's save each regression's result with a different name, and below this, output them all in one nice table:


```python
# one var: 'y ~ x' means fit y = a + b*X

reg1 = sm_ols('int ~  Borrower_Credit_Score_at_Origination ', data=fannie_mae).fit()

reg1b= sm_ols('int ~  l_credscore  ',  data=fannie_mae).fit()

reg1c= sm_ols('l_int ~  Borrower_Credit_Score_at_Origination  ',  data=fannie_mae).fit()

reg1d= sm_ols('l_int ~  l_credscore  ',  data=fannie_mae).fit()

# multiple variables: just add them to the formula
# 'y ~ x1 + x2' means fit y = a + b*x1 + c*x2
reg2 = sm_ols('int ~  l_credscore + l_LTV ',  data=fannie_mae).fit()

# interaction terms: Just use *
# Note: always include each variable separately too! (not just x1*x2, but x1+x2+x1*x2)
reg3 = sm_ols('int ~  l_credscore + l_LTV + l_credscore*l_LTV',  data=fannie_mae).fit()
      
# categorical dummies: C() 
reg4 = sm_ols('int ~  C(creditbins)  ',  data=fannie_mae).fit()

reg5 = sm_ols('int ~  C(creditbins)  -1', data=fannie_mae).fit()

```

Ok, time to output them:


```python
# now I'll format an output table
# I'd like to include extra info in the table (not just coefficients)
info_dict={'R-squared' : lambda x: f"{x.rsquared:.2f}",
           'Adj R-squared' : lambda x: f"{x.rsquared_adj:.2f}",
           'No. observations' : lambda x: f"{int(x.nobs):d}"}

# q4b1 and q4b2 name the dummies differently in the table, so this is a silly fix
reg4.model.exog_names[1:] = reg5.model.exog_names[1:]  

# This summary col function combines a bunch of regressions into one nice table
print('='*108)
print('                  y = interest rate if not specified, log(interest rate else)')
print(summary_col(results=[reg1,reg1b,reg1c,reg1d,reg2,reg3,reg4,reg5], # list the result obj here
                  float_format='%0.2f',
                  stars = True, # stars are easy way to see if anything is statistically significant
                  model_names=['1','2',' 3 (log)','4 (log)','5','6','7','8'], # these are bad names, lol. Usually, just use the y variable name
                  info_dict=info_dict,
                  regressor_order=[ 'Intercept','Borrower_Credit_Score_at_Origination','l_credscore','l_LTV','l_credscore:l_LTV',
                                  'C(creditbins)[Very Poor]','C(creditbins)[Fair]','C(creditbins)[Good]','C(creditbins)[Vrey Good]','C(creditbins)[Exceptional]']
                  )
     )
```

    ============================================================================================================
                      y = interest rate if not specified, log(interest rate else)
    
    ============================================================================================================
                                            1        2      3 (log) 4 (log)     5         6        7        8   
    ------------------------------------------------------------------------------------------------------------
    Intercept                            11.58*** 45.37*** 2.87***  9.50***  44.13*** -16.81*** 6.65***         
                                         (0.05)   (0.29)   (0.01)   (0.06)   (0.30)   (4.11)    (0.08)          
    Borrower_Credit_Score_at_Origination -0.01***          -0.00***                                             
                                         (0.00)            (0.00)                                               
    l_credscore                                   -6.07***          -1.19*** -5.99*** 3.22***                   
                                                  (0.04)            (0.01)   (0.04)   (0.62)                    
    l_LTV                                                                    0.15***  14.61***                  
                                                                             (0.01)   (0.97)                    
    l_credscore:l_LTV                                                                 -2.18***                  
                                                                                      (0.15)                    
    C(creditbins)[Very Poor]                                                                             6.65***
                                                                                                         (0.08) 
    C(creditbins)[Fair]                                                                         -0.63*** 6.02***
                                                                                                (0.08)   (0.02) 
    C(creditbins)[Good]                                                                         -1.17*** 5.48***
                                                                                                (0.08)   (0.01) 
    C(creditbins)[Exceptional]                                                                  -2.25*** 4.40***
                                                                                                (0.08)   (0.01) 
    C(creditbins)[Very Good]                                                                    -1.65*** 5.00***
                                                                                                (0.08)   (0.01) 
    R-squared                            0.13     0.12     0.13     0.12     0.13     0.13      0.11     0.11   
    R-squared Adj.                       0.13     0.12     0.13     0.12     0.13     0.13      0.11     0.11   
    R-squared                            0.13     0.12     0.13     0.12     0.13     0.13      0.11     0.11   
    Adj R-squared                        0.13     0.12     0.13     0.12     0.13     0.13      0.11     0.11   
    No. observations                     134481   134481   134481   134481   134481   134481    67366    67366  
    ============================================================================================================
    Standard errors in parentheses.
    * p<.1, ** p<.05, ***p<.01
    

# Today. Work in groups. Refer to the lectures. 

You might need to print out a few individual regressions with more decimals.

1. Interpret coefs in model 1-4
    - Model 1 b1: A 1 unit increase in a borrower's credit score is associated with a 1 basis point decrease in the interest rate, holding all else constant
    - Model 1 intercept: For borrowers with a credit score of 0, the expected interest rate is 11.58
    - Model 2 b1: A 1% increase in a borrower's credit score is associated with a 6.07/100 reduction (6 basis point reduction) in the interest rate, holding all else constant
    - Comparing Model 1 and 2:
        - Model 1: 700 to 707 is associated with a 7 bp decrease in the rate
        - Model 2: 700 to 707 is associated with a 6 bp decrease in the rate
    - Model 3 b1: A 1 unit increase in a borrower's credit score is associated with a 100*(beta)% reduction in the interest rate, holding all else constant
        - Model 3: 700 to 707 is associated with a 1.2% decrease in the rate
    - Model 4 b1: A 1% increase in a borrower's credit score is associated with a 1.19 reduction in the interest rate, holding all else constant
        - Model 4: 700 to 707 is associated with a 1.19% decrease in the rate

1. Interpret coefs in model 5
    - Model 5 b1: A 1% increase in a borrower's credit score is associated with a 5.99 (6 basis point reduction) in the interest rate, holding all else constant
    
1. Interpret coefs in model 6 (and visually?)
    
1. Interpret coefs in model 7 (and visually? + comp to table)
1. Interpret coefs in model 8 (and visually? + comp to table)
1. Add l_LTV  to Model 8 and interpret (and visually?)




