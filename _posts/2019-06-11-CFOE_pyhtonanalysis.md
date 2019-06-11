---
published: true
title: Calculating and Comparing Product Production Time for VS One-Health Products
layout: post
---

<center> <h1> Calculating and Comparing Product Production Time for VS One-Health Products</h1> </center>
<b>Sheldon Waugh MSc, PhD<br>
Epidemiologist<br>
One-Health Division<br> 
Veterinary Services and Public Health Sanitation Directorate<br>
US Army Public Health Center<br>

### This Python notebook is a workable and scalable example on how to make a notebook that cleans and analyzes data in a transparent way.
We have an excel workbook that details the progress of all One-Health/Veterinary Services products to include posters, brochures and newsletters. 
Taking a look at the initial structure of this spreadsheet, we have missing dates or progress for certain products. This may be due to certain products not needing certain levels of approval. It may be advantageous for us to split the data by product and then look at the time related to each product.<br>
For my sanity, I will be getting rid of the incomplete records.

# Analysis

First let's go ahead and load the .csv into python


```python
import pandas as pd # import the module and alias it as pd

timedateVOH_data = pd.read_csv('C:\Users\waugh\Dropbox\Documents\One_Health\VS_Product_Analysis_dates.csv', parse_dates=True)
timedateVOH_data.head()
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
      <th>Type</th>
      <th>Draft Review</th>
      <th>SME Content Review</th>
      <th>PMD Content Review</th>
      <th>VID Design Work/Edits</th>
      <th>Submitted to CRC</th>
      <th>CRC Review Complete</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Brochure</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5/17/2016</td>
      <td>5/23/2016</td>
      <td>6/1/2016</td>
      <td>6/7/2016</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Brochure</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5/17/2016</td>
      <td>5/23/2016</td>
      <td>6/3/2016</td>
      <td>6/13/2016</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Brochure</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5/17/2016</td>
      <td>10/20/2016</td>
      <td>11/1/2016</td>
      <td>12/20/2016</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Brochure</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7/5/2016</td>
      <td>7/5/2016</td>
      <td>8/19/2016</td>
      <td>9/2/2016</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Brochure</td>
      <td>5/9/2017</td>
      <td>5/16/2017</td>
      <td>6/6/2017</td>
      <td>6/23/2017</td>
      <td>8/4/2017</td>
      <td>10/10/2017</td>
    </tr>
  </tbody>
</table>
</div>




```python
timedateVOH_data.describe()
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
      <th>Type</th>
      <th>Draft Review</th>
      <th>SME Content Review</th>
      <th>PMD Content Review</th>
      <th>VID Design Work/Edits</th>
      <th>Submitted to CRC</th>
      <th>CRC Review Complete</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>81</td>
      <td>52</td>
      <td>48</td>
      <td>62</td>
      <td>70</td>
      <td>75</td>
      <td>69</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>3</td>
      <td>36</td>
      <td>29</td>
      <td>31</td>
      <td>33</td>
      <td>33</td>
      <td>21</td>
    </tr>
    <tr>
      <th>top</th>
      <td>Newsletter</td>
      <td>6/1/2017</td>
      <td>4/20/2016</td>
      <td>7/11/2016</td>
      <td>4/20/2016</td>
      <td>8/29/2017</td>
      <td>8/15/2016</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>41</td>
      <td>4</td>
      <td>5</td>
      <td>8</td>
      <td>6</td>
      <td>8</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>



We see that the dates we have on the csv file did not read into python as a datetime object. Without this, we're unable to determine the elapsed time for each column.

Lets fix for the CRC columns at least so since the product wouldn't exist without CRC approval.


```python
df_clean = timedateVOH_data[pd.notnull(timedateVOH_data['Submitted to CRC '])]
df_clean_1 = df_clean[pd.notnull(df_clean['CRC Review Complete'])]
df_clean_1
df_clean_2 = df_clean_1.copy()

df_clean_2['CRC Review Complete'] =  pd.to_datetime(df_clean_2['CRC Review Complete'], format = '%m/%d/%Y')
df_clean_2['Submitted to CRC '] =  pd.to_datetime(df_clean_2['Submitted to CRC '], format = '%m/%d/%Y')

df_clean_2['CRCtime'] = df_clean_2['CRC Review Complete'] - df_clean_2['Submitted to CRC ']

df_clean_2.head()

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
      <th>Type</th>
      <th>Draft Review</th>
      <th>SME Content Review</th>
      <th>PMD Content Review</th>
      <th>VID Design Work/Edits</th>
      <th>Submitted to CRC</th>
      <th>CRC Review Complete</th>
      <th>CRCtime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Brochure</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5/17/2016</td>
      <td>5/23/2016</td>
      <td>2016-06-01</td>
      <td>2016-06-07</td>
      <td>6 days</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Brochure</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5/17/2016</td>
      <td>5/23/2016</td>
      <td>2016-06-03</td>
      <td>2016-06-13</td>
      <td>10 days</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Brochure</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5/17/2016</td>
      <td>10/20/2016</td>
      <td>2016-11-01</td>
      <td>2016-12-20</td>
      <td>49 days</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Brochure</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7/5/2016</td>
      <td>7/5/2016</td>
      <td>2016-08-19</td>
      <td>2016-09-02</td>
      <td>14 days</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Brochure</td>
      <td>5/9/2017</td>
      <td>5/16/2017</td>
      <td>6/6/2017</td>
      <td>6/23/2017</td>
      <td>2017-08-04</td>
      <td>2017-10-10</td>
      <td>67 days</td>
    </tr>
  </tbody>
</table>
</div>



Now that we finished the column, we now have the calculated the elapsed time!
We see, however, that certain products don't have full processing times through all columns. 
This probably means that certain products may not need certain approvals. 
So from now on, let's go ahead and split the created data.frame by the two main One-Health Products (Newsletters and Brochures).


```python
#Create the dataframe subset
df_clean_2_News = df_clean_2.loc[df_clean_2['Type'] == 'Newsletter']
df_clean_2_News.head()

#Erase rows with NaN values
df_clean_3_News = df_clean_2_News[pd.notnull(df_clean_2_News['VID Design Work/Edits'])]
df_clean_4_News = df_clean_3_News[pd.notnull(df_clean_3_News['PMD Content Review'])]
df_clean_5_News = df_clean_4_News[pd.notnull(df_clean_4_News['SME Content Review'])]
df_clean_6_News = df_clean_5_News[pd.notnull(df_clean_5_News['Draft Review'])]

#Cenvert strings to datetime objects
df_clean_6_News['VID Design Work/Edits'] =  pd.to_datetime(df_clean_6_News['VID Design Work/Edits'], format = '%m/%d/%Y')
df_clean_6_News['PMD Content Review'] =  pd.to_datetime(df_clean_6_News['PMD Content Review'], format = '%m/%d/%Y')
df_clean_6_News['SME Content Review'] =  pd.to_datetime(df_clean_6_News['SME Content Review'], format = '%m/%d/%Y')
df_clean_6_News['Draft Review'] =  pd.to_datetime(df_clean_6_News['Draft Review'], format = '%m/%d/%Y')

df_clean_6_News.head()

#Create the elapsed time columns
df_clean_6_News['VID_Edittime'] = df_clean_6_News['Submitted to CRC '] - df_clean_6_News['VID Design Work/Edits']
df_clean_6_News['PMD_Reviewtime'] = df_clean_6_News['VID Design Work/Edits'] - df_clean_6_News['PMD Content Review']
df_clean_6_News['SME_Reviewtime'] = df_clean_6_News['PMD Content Review'] - df_clean_6_News['SME Content Review']
df_clean_6_News['Draft_Reviewtime'] = df_clean_6_News['SME Content Review'] - df_clean_6_News['Draft Review']

df_clean_7_News = df_clean_6_News.drop(df_clean_6_News[df_clean_6_News.PMD_Reviewtime == '-10 days'].index)
df_clean_8_News = df_clean_7_News.drop(df_clean_7_News[df_clean_7_News.PMD_Reviewtime == '-6 days'].index)
#df_clean_6_News['PMD_Reviewtime'].total_seconds()

#Good to go!
df_clean_8_News
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
      <th>Type</th>
      <th>Draft Review</th>
      <th>SME Content Review</th>
      <th>PMD Content Review</th>
      <th>VID Design Work/Edits</th>
      <th>Submitted to CRC</th>
      <th>CRC Review Complete</th>
      <th>CRCtime</th>
      <th>VID_Edittime</th>
      <th>PMD_Reviewtime</th>
      <th>SME_Reviewtime</th>
      <th>Draft_Reviewtime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>40</th>
      <td>Newsletter</td>
      <td>2016-04-13</td>
      <td>2016-04-20</td>
      <td>2016-04-20</td>
      <td>2016-04-20</td>
      <td>2016-04-28</td>
      <td>2016-05-09</td>
      <td>11 days</td>
      <td>8 days</td>
      <td>0 days</td>
      <td>0 days</td>
      <td>7 days</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Newsletter</td>
      <td>2016-04-15</td>
      <td>2016-04-20</td>
      <td>2016-04-20</td>
      <td>2016-04-20</td>
      <td>2016-04-28</td>
      <td>2016-05-09</td>
      <td>11 days</td>
      <td>8 days</td>
      <td>0 days</td>
      <td>0 days</td>
      <td>5 days</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Newsletter</td>
      <td>2016-04-15</td>
      <td>2016-04-20</td>
      <td>2016-04-20</td>
      <td>2016-04-20</td>
      <td>2016-04-28</td>
      <td>2016-05-09</td>
      <td>11 days</td>
      <td>8 days</td>
      <td>0 days</td>
      <td>0 days</td>
      <td>5 days</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Newsletter</td>
      <td>2016-04-15</td>
      <td>2016-04-20</td>
      <td>2016-04-20</td>
      <td>2016-04-20</td>
      <td>2016-04-28</td>
      <td>2016-05-09</td>
      <td>11 days</td>
      <td>8 days</td>
      <td>0 days</td>
      <td>0 days</td>
      <td>5 days</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Newsletter</td>
      <td>2016-05-17</td>
      <td>2016-05-17</td>
      <td>2016-05-17</td>
      <td>2016-05-17</td>
      <td>2016-06-03</td>
      <td>2016-06-07</td>
      <td>4 days</td>
      <td>17 days</td>
      <td>0 days</td>
      <td>0 days</td>
      <td>0 days</td>
    </tr>
    <tr>
      <th>54</th>
      <td>Newsletter</td>
      <td>2016-07-01</td>
      <td>2016-07-05</td>
      <td>2016-07-11</td>
      <td>2016-07-11</td>
      <td>2016-07-22</td>
      <td>2016-08-15</td>
      <td>24 days</td>
      <td>11 days</td>
      <td>0 days</td>
      <td>6 days</td>
      <td>4 days</td>
    </tr>
    <tr>
      <th>56</th>
      <td>Newsletter</td>
      <td>2016-06-27</td>
      <td>2016-06-27</td>
      <td>2016-07-11</td>
      <td>2016-07-11</td>
      <td>2016-07-22</td>
      <td>2016-08-15</td>
      <td>24 days</td>
      <td>11 days</td>
      <td>0 days</td>
      <td>14 days</td>
      <td>0 days</td>
    </tr>
    <tr>
      <th>58</th>
      <td>Newsletter</td>
      <td>2016-11-17</td>
      <td>2016-11-23</td>
      <td>2016-11-28</td>
      <td>2016-11-29</td>
      <td>2017-01-27</td>
      <td>2017-03-30</td>
      <td>62 days</td>
      <td>59 days</td>
      <td>1 days</td>
      <td>5 days</td>
      <td>6 days</td>
    </tr>
    <tr>
      <th>59</th>
      <td>Newsletter</td>
      <td>2016-11-08</td>
      <td>2016-11-23</td>
      <td>2016-11-28</td>
      <td>2016-11-29</td>
      <td>2017-01-27</td>
      <td>2017-03-30</td>
      <td>62 days</td>
      <td>59 days</td>
      <td>1 days</td>
      <td>5 days</td>
      <td>15 days</td>
    </tr>
    <tr>
      <th>61</th>
      <td>Newsletter</td>
      <td>2017-02-25</td>
      <td>2017-03-15</td>
      <td>2017-03-15</td>
      <td>2017-03-17</td>
      <td>2017-03-17</td>
      <td>2017-05-08</td>
      <td>52 days</td>
      <td>0 days</td>
      <td>2 days</td>
      <td>0 days</td>
      <td>18 days</td>
    </tr>
    <tr>
      <th>62</th>
      <td>Newsletter</td>
      <td>2017-02-10</td>
      <td>2017-03-13</td>
      <td>2017-03-15</td>
      <td>2017-03-20</td>
      <td>2017-04-04</td>
      <td>2017-06-01</td>
      <td>58 days</td>
      <td>15 days</td>
      <td>5 days</td>
      <td>2 days</td>
      <td>31 days</td>
    </tr>
    <tr>
      <th>63</th>
      <td>Newsletter</td>
      <td>2017-02-10</td>
      <td>2017-03-10</td>
      <td>2017-03-13</td>
      <td>2017-03-15</td>
      <td>2017-04-04</td>
      <td>2017-06-01</td>
      <td>58 days</td>
      <td>20 days</td>
      <td>2 days</td>
      <td>3 days</td>
      <td>28 days</td>
    </tr>
    <tr>
      <th>64</th>
      <td>Newsletter</td>
      <td>2017-03-01</td>
      <td>2017-03-16</td>
      <td>2017-03-16</td>
      <td>2017-03-20</td>
      <td>2017-04-04</td>
      <td>2017-06-01</td>
      <td>58 days</td>
      <td>15 days</td>
      <td>4 days</td>
      <td>0 days</td>
      <td>15 days</td>
    </tr>
    <tr>
      <th>66</th>
      <td>Newsletter</td>
      <td>2017-06-01</td>
      <td>2017-06-23</td>
      <td>2017-06-29</td>
      <td>2017-07-25</td>
      <td>2017-08-29</td>
      <td>2017-08-29</td>
      <td>0 days</td>
      <td>35 days</td>
      <td>26 days</td>
      <td>6 days</td>
      <td>22 days</td>
    </tr>
    <tr>
      <th>68</th>
      <td>Newsletter</td>
      <td>2017-06-01</td>
      <td>2017-06-23</td>
      <td>2017-06-28</td>
      <td>2017-07-25</td>
      <td>2017-08-29</td>
      <td>2017-08-29</td>
      <td>0 days</td>
      <td>35 days</td>
      <td>27 days</td>
      <td>5 days</td>
      <td>22 days</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Newsletter</td>
      <td>2016-07-01</td>
      <td>2016-07-05</td>
      <td>2016-07-11</td>
      <td>2017-08-28</td>
      <td>2017-08-29</td>
      <td>2017-08-29</td>
      <td>0 days</td>
      <td>1 days</td>
      <td>413 days</td>
      <td>6 days</td>
      <td>4 days</td>
    </tr>
    <tr>
      <th>71</th>
      <td>Newsletter</td>
      <td>2016-06-07</td>
      <td>2016-06-30</td>
      <td>2016-07-11</td>
      <td>2017-08-28</td>
      <td>2017-08-29</td>
      <td>2017-08-29</td>
      <td>0 days</td>
      <td>1 days</td>
      <td>413 days</td>
      <td>11 days</td>
      <td>23 days</td>
    </tr>
    <tr>
      <th>72</th>
      <td>Newsletter</td>
      <td>2016-06-27</td>
      <td>2016-06-27</td>
      <td>2016-07-11</td>
      <td>2017-08-28</td>
      <td>2017-08-29</td>
      <td>2017-08-29</td>
      <td>0 days</td>
      <td>1 days</td>
      <td>413 days</td>
      <td>14 days</td>
      <td>0 days</td>
    </tr>
    <tr>
      <th>77</th>
      <td>Newsletter</td>
      <td>2018-08-15</td>
      <td>2018-08-24</td>
      <td>2018-08-24</td>
      <td>2018-08-29</td>
      <td>2018-08-30</td>
      <td>2018-09-04</td>
      <td>5 days</td>
      <td>1 days</td>
      <td>5 days</td>
      <td>0 days</td>
      <td>9 days</td>
    </tr>
    <tr>
      <th>78</th>
      <td>Newsletter</td>
      <td>2018-08-19</td>
      <td>2018-08-30</td>
      <td>2018-08-29</td>
      <td>2018-08-29</td>
      <td>2018-08-30</td>
      <td>2018-09-04</td>
      <td>5 days</td>
      <td>1 days</td>
      <td>0 days</td>
      <td>-1 days</td>
      <td>11 days</td>
    </tr>
    <tr>
      <th>79</th>
      <td>Newsletter</td>
      <td>2018-08-15</td>
      <td>2018-08-24</td>
      <td>2018-08-24</td>
      <td>2018-08-29</td>
      <td>2018-08-30</td>
      <td>2018-09-04</td>
      <td>5 days</td>
      <td>1 days</td>
      <td>5 days</td>
      <td>0 days</td>
      <td>9 days</td>
    </tr>
    <tr>
      <th>80</th>
      <td>Newsletter</td>
      <td>2018-08-16</td>
      <td>2018-08-23</td>
      <td>2018-08-24</td>
      <td>2018-08-29</td>
      <td>2018-08-30</td>
      <td>2018-09-04</td>
      <td>5 days</td>
      <td>1 days</td>
      <td>5 days</td>
      <td>1 days</td>
      <td>7 days</td>
    </tr>
  </tbody>
</table>
</div>



Now lets take a look at the summary data.....


```python
df_clean_8_News.describe()
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
      <th>CRCtime</th>
      <th>VID_Edittime</th>
      <th>PMD_Reviewtime</th>
      <th>SME_Reviewtime</th>
      <th>Draft_Reviewtime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>21 days 04:21:49.090909</td>
      <td>14 days 08:43:38.181818</td>
      <td>60 days 02:10:54.545454</td>
      <td>3 days 12:00:00</td>
      <td>11 days 04:21:49.090909</td>
    </tr>
    <tr>
      <th>std</th>
      <td>24 days 06:22:33.479019</td>
      <td>17 days 14:47:37.238031</td>
      <td>143 days 17:24:49.094471</td>
      <td>4 days 14:02:38.929146</td>
      <td>9 days 05:32:01.438379</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0 days 00:00:00</td>
      <td>0 days 00:00:00</td>
      <td>0 days 00:00:00</td>
      <td>-1 days +00:00:00</td>
      <td>0 days 00:00:00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>4 days 06:00:00</td>
      <td>1 days 00:00:00</td>
      <td>0 days 00:00:00</td>
      <td>0 days 00:00:00</td>
      <td>5 days 00:00:00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>11 days 00:00:00</td>
      <td>8 days 00:00:00</td>
      <td>2 days 00:00:00</td>
      <td>1 days 12:00:00</td>
      <td>8 days 00:00:00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>45 days 00:00:00</td>
      <td>16 days 12:00:00</td>
      <td>5 days 00:00:00</td>
      <td>5 days 18:00:00</td>
      <td>17 days 06:00:00</td>
    </tr>
    <tr>
      <th>max</th>
      <td>62 days 00:00:00</td>
      <td>59 days 00:00:00</td>
      <td>413 days 00:00:00</td>
      <td>14 days 00:00:00</td>
      <td>31 days 00:00:00</td>
    </tr>
  </tbody>
</table>
</div>




```python
print(df_clean_8_News.CRCtime.median())
print(df_clean_8_News.VID_Edittime.median())
print(df_clean_8_News.PMD_Reviewtime.median())
print(df_clean_8_News.SME_Reviewtime.median())
print(df_clean_8_News.Draft_Reviewtime.median())
```

    11 days 00:00:00
    8 days 00:00:00
    2 days 00:00:00
    1 days 12:00:00
    8 days 00:00:00
    

Yikes, we have some weird data that led to some negative elapsed times. 
We could do one of three things:
- Delete the records (rows) with the offending dates.
- Delete the PMD_Reviewtime column and analyze the other criteria.
- Leave the data unedited and ask MAJ Watkins about the dates.
Let's go ahead and do the other products.

This type of discrepancy also demonstrates the lack of an effective way of recording data. In order to effectively produce an analysis and report of any substance, our data must be reliable.<br>

A recommendation for the future is that an accurate working document <b> MUST </b> be established in order to produce reliable production time data and reports.<br>

<b>For this analysis, I've decided to delete the offending products with a negative elapsed time.</b>


```python
#Create the dataframe subset
df_clean_2_1 = df_clean_2.copy()
df_clean_2_bro = df_clean_2_1.loc[df_clean_2['Type'] == 'Brochure']
df_clean_2_bro.head()
pd.options.mode.chained_assignment = None

#Erase rows with NaN values
df_clean_3_bro = df_clean_2_bro[pd.notnull(df_clean_2_bro['VID Design Work/Edits'])]
df_clean_4_bro = df_clean_3_bro[pd.notnull(df_clean_3_bro['PMD Content Review'])]
df_clean_5_bro = df_clean_4_bro[pd.notnull(df_clean_4_bro['SME Content Review'])]
df_clean_6_bro = df_clean_5_bro[pd.notnull(df_clean_5_bro['Draft Review'])]

#Cenvert strings to datetime objects
df_clean_6_bro['VID Design Work/Edits'] =  pd.to_datetime(df_clean_6_bro['VID Design Work/Edits'], format = '%m/%d/%Y')
df_clean_6_bro['PMD Content Review'] =  pd.to_datetime(df_clean_6_bro['PMD Content Review'], format = '%m/%d/%Y')
df_clean_6_bro['SME Content Review'] =  pd.to_datetime(df_clean_6_bro['SME Content Review'], format = '%m/%d/%Y')
df_clean_6_bro['Draft Review'] =  pd.to_datetime(df_clean_6_bro['Draft Review'], format = '%m/%d/%Y')

df_clean_6_News.head()

#Create the elapsed time columns
df_clean_6_bro['VID_Edittime'] = df_clean_6_bro['Submitted to CRC '] - df_clean_6_bro['VID Design Work/Edits']
df_clean_6_bro['PMD_Reviewtime'] = df_clean_6_bro['VID Design Work/Edits'] - df_clean_6_bro['PMD Content Review']
df_clean_6_bro['SME_Reviewtime'] = df_clean_6_bro['PMD Content Review'] - df_clean_6_bro['SME Content Review']
df_clean_6_bro['Draft_Reviewtime'] = df_clean_6_bro['SME Content Review'] - df_clean_6_bro['Draft Review']

#Good to go!
df_clean_6_bro
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
      <th>Type</th>
      <th>Draft Review</th>
      <th>SME Content Review</th>
      <th>PMD Content Review</th>
      <th>VID Design Work/Edits</th>
      <th>Submitted to CRC</th>
      <th>CRC Review Complete</th>
      <th>CRCtime</th>
      <th>VID_Edittime</th>
      <th>PMD_Reviewtime</th>
      <th>SME_Reviewtime</th>
      <th>Draft_Reviewtime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>Brochure</td>
      <td>2017-05-09</td>
      <td>2017-05-16</td>
      <td>2017-06-06</td>
      <td>2017-06-23</td>
      <td>2017-08-04</td>
      <td>2017-10-10</td>
      <td>67 days</td>
      <td>42 days</td>
      <td>17 days</td>
      <td>21 days</td>
      <td>7 days</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Brochure</td>
      <td>2016-11-23</td>
      <td>2016-12-01</td>
      <td>2016-12-02</td>
      <td>2016-12-06</td>
      <td>2017-01-30</td>
      <td>2017-03-30</td>
      <td>59 days</td>
      <td>55 days</td>
      <td>4 days</td>
      <td>1 days</td>
      <td>8 days</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Brochure</td>
      <td>2016-12-07</td>
      <td>2017-02-09</td>
      <td>2017-02-10</td>
      <td>2017-05-17</td>
      <td>2017-06-06</td>
      <td>2017-06-30</td>
      <td>24 days</td>
      <td>20 days</td>
      <td>96 days</td>
      <td>1 days</td>
      <td>64 days</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Brochure</td>
      <td>2016-12-09</td>
      <td>2017-01-26</td>
      <td>2017-01-27</td>
      <td>2017-01-30</td>
      <td>2017-02-02</td>
      <td>2017-03-30</td>
      <td>56 days</td>
      <td>3 days</td>
      <td>3 days</td>
      <td>1 days</td>
      <td>48 days</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Brochure</td>
      <td>2016-09-06</td>
      <td>2016-09-09</td>
      <td>2016-09-09</td>
      <td>2016-09-10</td>
      <td>2016-11-29</td>
      <td>2017-01-02</td>
      <td>34 days</td>
      <td>80 days</td>
      <td>1 days</td>
      <td>0 days</td>
      <td>3 days</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Brochure</td>
      <td>2017-03-01</td>
      <td>2017-03-10</td>
      <td>2017-03-13</td>
      <td>2017-04-10</td>
      <td>2017-06-05</td>
      <td>2017-06-27</td>
      <td>22 days</td>
      <td>56 days</td>
      <td>28 days</td>
      <td>3 days</td>
      <td>9 days</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_clean_6_bro.describe()
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
      <th>CRCtime</th>
      <th>VID_Edittime</th>
      <th>PMD_Reviewtime</th>
      <th>SME_Reviewtime</th>
      <th>Draft_Reviewtime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>43 days 16:00:00</td>
      <td>42 days 16:00:00</td>
      <td>24 days 20:00:00</td>
      <td>4 days 12:00:00</td>
      <td>23 days 04:00:00</td>
    </tr>
    <tr>
      <th>std</th>
      <td>19 days 09:32:32.152061</td>
      <td>27 days 14:37:12.911653</td>
      <td>36 days 08:56:33.395270</td>
      <td>8 days 03:25:10.375190</td>
      <td>26 days 00:15:41.341229</td>
    </tr>
    <tr>
      <th>min</th>
      <td>22 days 00:00:00</td>
      <td>3 days 00:00:00</td>
      <td>1 days 00:00:00</td>
      <td>0 days 00:00:00</td>
      <td>3 days 00:00:00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>26 days 12:00:00</td>
      <td>25 days 12:00:00</td>
      <td>3 days 06:00:00</td>
      <td>1 days 00:00:00</td>
      <td>7 days 06:00:00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>45 days 00:00:00</td>
      <td>48 days 12:00:00</td>
      <td>10 days 12:00:00</td>
      <td>1 days 00:00:00</td>
      <td>8 days 12:00:00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>58 days 06:00:00</td>
      <td>55 days 18:00:00</td>
      <td>25 days 06:00:00</td>
      <td>2 days 12:00:00</td>
      <td>38 days 06:00:00</td>
    </tr>
    <tr>
      <th>max</th>
      <td>67 days 00:00:00</td>
      <td>80 days 00:00:00</td>
      <td>96 days 00:00:00</td>
      <td>21 days 00:00:00</td>
      <td>64 days 00:00:00</td>
    </tr>
  </tbody>
</table>
</div>




```python
print(df_clean_6_bro.CRCtime.median())
print(df_clean_6_bro.VID_Edittime.median())
print(df_clean_6_bro.PMD_Reviewtime.median())
print(df_clean_6_bro.SME_Reviewtime.median())
print(df_clean_6_bro.Draft_Reviewtime.median())
```

    45 days 00:00:00
    48 days 12:00:00
    10 days 12:00:00
    1 days 00:00:00
    8 days 12:00:00
    

<center> <h3>Plots and Figures</h3> </center> <br>
Now lets go ahead and produce some figures to visualize our data and demonstrate which production stage takes the most and least amount of time.<br>
First let's take a look at the brochure products...


```python
import matplotlib.pyplot as plt; plt.rcdefaults()
import numpy as np
import matplotlib.pyplot as plt

objects = ('CRC', 'VID', 'PMD Review', 'SME Review', 'Draft Review')
y_pos = np.arange(len(objects))
performance = [df_clean_6_bro.CRCtime.dt.days.median(),df_clean_6_bro.VID_Edittime.dt.days.median(),df_clean_6_bro.PMD_Reviewtime.dt.days.median(),df_clean_6_bro.SME_Reviewtime.dt.days.median(),df_clean_6_bro.Draft_Reviewtime.dt.days.median()]

plt.bar(y_pos, performance, align='center', alpha=0.5)
plt.xticks(y_pos, objects)
plt.ylabel('Days (Median)')
plt.title('VS (One-Health) Product Production Time (Brochure)')
for i, v in enumerate(performance):
    plt.text(i-.15, v+1 , str(v), color='blue', fontweight='bold', va='center')

plt.show()
```

![png](https://waughr.us/images/output_17_0.png)


Now let's take a look at the Newsletters...


```python
import matplotlib.pyplot as plt; plt.rcdefaults()
import numpy as np
import matplotlib.pyplot as plt

objects = ('CRC', 'VID', 'PMD Review', 'SME Review', 'Draft Review')
y_pos = np.arange(len(objects))
performance = [df_clean_8_News.CRCtime.dt.days.median(),df_clean_8_News.VID_Edittime.dt.days.median(),df_clean_8_News.PMD_Reviewtime.dt.days.median(),df_clean_8_News.SME_Reviewtime.dt.days.median(),df_clean_8_News.Draft_Reviewtime.dt.days.median()]

plt.bar(y_pos, performance, align='center', alpha=0.5, color=['green','green','green','green','green'])
plt.xticks(y_pos, objects)
plt.ylabel('Days (Median)')
plt.title('VS (One-Health) Product Production Time (Newsletter)')
for i, v in enumerate(performance):
    plt.text(i-.15, v+.18 , str(v), color='blue', fontweight='bold', va='center')

plt.show()
```


![png](https://waughr.us/images/output_19_0.png)


# Discussion
<center> <h2>So what can we say about these graphs and analyses?</h2> </center> <br>
  With a first look, the data analysis and subsequent graphs, doesn't really tell us much about spent time due to the large discrepancies. We can see that VID processing and Review and the CRC Review process takes up the majority of time of One-Health Product development, with a wait time of 48.5 and 45 days, respectively.<br>
  <br>
  We see that comparing the brochures and newsletters, the newsletters take a shorter median amount of production time. The reasoning behind this may possibly be due to the higher frequency of newsletter production, increasing the potential efficiency of the newsletter production process.<br>
  <br>
  With a potential brochure program utilizing First Year Graduate Veterinary Education (FYGVE) students as potential authors, a strong and defined review process is necessary to eliminate potential hang up in the review process. 

#### This shows that our processing time is really dependent on the work and collaboration of other directorates regarding visual output and CRC review. 

  With the subject matter, draft and PMD review lasting roughly 75% less than VID and CRC time, recommendations should:
- Include discussions, focus groups and meetings to potentially streamline the CRC process; potentially decreasing the time at certain approval levels
- Require submitters of VS One-Health products to have a clear visual outline of what their product should look like before VID work order submission
    -  A clear and defined endstate by the customer will assist the VID designer in obtaining a product that the VID directorate and the customer will agree upon, potentially decreasing the production and processing time. Additionally, focus groups, surveys and interviews should be done within VID to discuss other potential bottlenecks during the production process.
    
    -  Additionally, with our aforementioned FYGVE brochure program, a dedicated person within the One-Health Division should be responsible for the overall setup and design of the brochures. With this, this would increase positive interaction with VID and decrease the VID production time potentially decreasing the overall production time, significantly.   

# Conclusion
In conclusion, my analysis shows that VID production and CRC approval take up more than 50 percent of the total production time in both One-health newsletters and brochures. I recommend the One-Health Division to open communication between VID and CRC to develop potential ideas to decrease processing and approval times. 
