
<img src="./images/header.png">

***

<h1> <p align="center"> Spatial-temporal differences in cloud cover of satellite observations across the Brazilian Amazon</p> </h1>
<p align="center"> Willian Vieira de Oliveira </p>


<p align="right"> [ Part 2 - Basic processing and statistics of the data ] </p>

### SUMMARY

1. [**About**](./1_Project.md#About)
1. [**The Data**](./1_Project.md#TheData)
    1. [Raw data](./1_Project.md#RawData)
    1. [Processed data](./1_Project.md#ProcessedData)
    1. [Additional data](./1_Project.md#AdditionalData)
1. [**Questions about the data**](./1_Project.md#Questions)
1. [**Basic processing of the data**](./2_Basic_processing.md#BasicProc)
1. [**Statistics**](./2_Basic_processing.md#Stats)
1. [**Exploratory Data Analysis**](./3_EDA.md#EDA)
1. [**Results**](./4_Results_and_Conclusion.md#Results)
1. [**Conclusion**](./4_Results_and_Conclusion.md#Conclusion)
***

<a id='BasicProc'></a>
## 4. Basic processing of the data

### Reading the metadata file




```python
from matplotlib import pyplot as plt
import geopandas as gpd
import pandas as pd
```


```python
filename = 'Output/Metadata/Metadata_L8_S2.csv'

try:
    raw_data = pd.read_csv(filename, encoding='utf-8')
    print("The file was read!")
except Exception as e:
    print(str(e))
```

    The file was read!
    


```python
raw_data#.head()
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
      <th>ProductID</th>
      <th>AcquisitionDate</th>
      <th>Platform</th>
      <th>WRSPath</th>
      <th>WRSRow</th>
      <th>TileNumber</th>
      <th>CloudCover</th>
      <th>Datum</th>
      <th>UTMZone</th>
      <th>SpatialRes_Pan</th>
      <th>SpatialRes_Refletive</th>
      <th>SpatialRes_Thermal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>LC08_L1GT_228058_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>58.0</td>
      <td>NaN</td>
      <td>46.8200</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>1</th>
      <td>LC08_L1TP_228059_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>59.0</td>
      <td>NaN</td>
      <td>18.8900</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>2</th>
      <td>LC08_L1TP_228060_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>60.0</td>
      <td>NaN</td>
      <td>12.8600</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>3</th>
      <td>LC08_L1TP_228061_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>61.0</td>
      <td>NaN</td>
      <td>7.6700</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>4</th>
      <td>LC08_L1TP_228062_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>62.0</td>
      <td>NaN</td>
      <td>8.1000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>5</th>
      <td>LC08_L1TP_228063_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>63.0</td>
      <td>NaN</td>
      <td>0.1000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>6</th>
      <td>LC08_L1TP_228064_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>64.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>7</th>
      <td>LC08_L1TP_228065_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>65.0</td>
      <td>NaN</td>
      <td>0.8000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>8</th>
      <td>LC08_L1TP_228066_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>66.0</td>
      <td>NaN</td>
      <td>3.9400</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>9</th>
      <td>LC08_L1TP_228067_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>67.0</td>
      <td>NaN</td>
      <td>9.8300</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>10</th>
      <td>LC08_L1TP_228068_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>68.0</td>
      <td>NaN</td>
      <td>0.9800</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>11</th>
      <td>LC08_L1TP_228069_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>69.0</td>
      <td>NaN</td>
      <td>0.7900</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>12</th>
      <td>LC08_L1TP_228070_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>70.0</td>
      <td>NaN</td>
      <td>0.1000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>13</th>
      <td>LC08_L1TP_228071_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>71.0</td>
      <td>NaN</td>
      <td>0.1700</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>14</th>
      <td>LC08_L1TP_228072_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>Landsat-8</td>
      <td>228.0</td>
      <td>72.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>15</th>
      <td>LC08_L1TP_004058_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>4.0</td>
      <td>58.0</td>
      <td>NaN</td>
      <td>70.9300</td>
      <td>WGS84</td>
      <td>19</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>16</th>
      <td>LC08_L1TP_004059_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>4.0</td>
      <td>59.0</td>
      <td>NaN</td>
      <td>54.7200</td>
      <td>WGS84</td>
      <td>19</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>17</th>
      <td>LC08_L1TP_004060_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>4.0</td>
      <td>60.0</td>
      <td>NaN</td>
      <td>79.8400</td>
      <td>WGS84</td>
      <td>19</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>18</th>
      <td>LC08_L1GT_004061_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>4.0</td>
      <td>61.0</td>
      <td>NaN</td>
      <td>84.4400</td>
      <td>WGS84</td>
      <td>19</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>19</th>
      <td>LC08_L1GT_004062_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>4.0</td>
      <td>62.0</td>
      <td>NaN</td>
      <td>81.8600</td>
      <td>WGS84</td>
      <td>19</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>20</th>
      <td>LC08_L1TP_004063_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>4.0</td>
      <td>63.0</td>
      <td>NaN</td>
      <td>14.2300</td>
      <td>WGS84</td>
      <td>19</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>21</th>
      <td>LC08_L1TP_004064_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>4.0</td>
      <td>64.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>19</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>22</th>
      <td>LC08_L1TP_004065_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>4.0</td>
      <td>65.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>19</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>23</th>
      <td>LC08_L1TP_004066_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>4.0</td>
      <td>66.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>19</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>24</th>
      <td>LC08_L1TP_004067_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>4.0</td>
      <td>67.0</td>
      <td>NaN</td>
      <td>4.6700</td>
      <td>WGS84</td>
      <td>19</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>25</th>
      <td>LC08_L1GT_221061_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>221.0</td>
      <td>61.0</td>
      <td>NaN</td>
      <td>40.7500</td>
      <td>WGS84</td>
      <td>23</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>26</th>
      <td>LC08_L1TP_221062_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>221.0</td>
      <td>62.0</td>
      <td>NaN</td>
      <td>34.9100</td>
      <td>WGS84</td>
      <td>23</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>27</th>
      <td>LC08_L1TP_221063_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>221.0</td>
      <td>63.0</td>
      <td>NaN</td>
      <td>8.7300</td>
      <td>WGS84</td>
      <td>23</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>28</th>
      <td>LC08_L1TP_221064_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>221.0</td>
      <td>64.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>23</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>29</th>
      <td>LC08_L1TP_221065_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>Landsat-8</td>
      <td>221.0</td>
      <td>65.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>23</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
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
    </tr>
    <tr>
      <th>177653</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MWP</td>
      <td>8.2695</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177654</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MXM</td>
      <td>71.5078</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177655</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MVP</td>
      <td>9.4894</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177656</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MVM</td>
      <td>23.7708</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177657</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MUP</td>
      <td>44.9922</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177658</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MUN</td>
      <td>48.1419</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177659</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MUM</td>
      <td>11.8060</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177660</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MWM</td>
      <td>48.0121</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177661</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MXN</td>
      <td>47.3968</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177662</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MXP</td>
      <td>48.0832</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177663</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MWN</td>
      <td>39.6626</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177664</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21KUB</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177665</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>20KQG</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>20S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177666</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>20KRG</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>20S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177667</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21KTB</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177668</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21KUA</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177669</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21KVB</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177670</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MYU</td>
      <td>0.2791</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177671</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MWT</td>
      <td>3.0382</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177672</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MZV</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177673</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MWV</td>
      <td>44.3405</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177674</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MXV</td>
      <td>9.5568</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177675</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MXT</td>
      <td>2.3223</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177676</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MXU</td>
      <td>1.2880</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177677</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MYT</td>
      <td>3.1379</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177678</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MWU</td>
      <td>10.2529</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177679</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MVV</td>
      <td>44.8977</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177680</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MVU</td>
      <td>18.1768</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177681</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MYV</td>
      <td>34.5259</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177682</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>SENTINEL-2A</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MVT</td>
      <td>5.5611</td>
      <td>WGS84</td>
      <td>21S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
  </tbody>
</table>
<p>177683 rows × 12 columns</p>
</div>



### Tidying up the data


```python
tidy_data = raw_data.copy()
```

#### Table columns and structure

##### Table struture 


```python
print("Number of lines: ", tidy_data.shape[0])
print("Number of columns: ", tidy_data.shape[1])
```

    Number of lines:  177683
    Number of columns:  12
    

##### Columns


```python
tidy_data.columns
```




    Index(['ProductID', 'AcquisitionDate', 'Platform', 'WRSPath', 'WRSRow',
           'TileNumber', 'CloudCover', 'Datum', 'UTMZone', 'SpatialRes_Pan',
           'SpatialRes_Refletive', 'SpatialRes_Thermal'],
          dtype='object')



#### Statistics and distribution of the cloud cover data

Observe that some data present negative cloud cover. *These observations will be removed from the dataframe*.

##### Statistics


```python
tidy_data["CloudCover"].describe()
```




    count    177683.000000
    mean         49.937668
    std          36.960325
    min          -1.000000
    25%          12.155400
    50%          49.936100
    75%          87.789700
    max         100.000000
    Name: CloudCover, dtype: float64



##### Data distribution


```python
from matplotlib import pyplot as plt

# create the figure
fig = plt.figure()
fig.suptitle("Distribution of cloud cover on Landsat-8 and Sentinel-2 scenes \nacquired across the Brazilian Amazon", fontsize=18, fontweight='bold') 

# format the axes
ax = fig.add_subplot()
fig.subplots_adjust(top=0.85)
ax.set_title('Feb/2013 - Jul/2019', fontsize=14, fontweight='bold')
ax.set_xlabel("% OF CLOUD", labelpad=20, fontsize=12, fontweight='bold')
ax.set_ylabel("% OF COUNT", labelpad=20, fontsize=12, fontweight='bold')
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
plt.grid(linestyle="dashed", axis="y")

# data normalization
out = pd.cut(tidy_data["CloudCover"], bins=[-100, -1, 0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100], include_lowest=False)
out_norm = out.value_counts(sort=False, normalize=True).mul(100)
ax = out_norm.plot.bar(rot=0, color="gray", figsize=(14,8) )

# X-axis: Label options
    # Option 1: ["< 0"; (0, 10]; "> 90"]
labels = [str(c) for c in out.cat.categories]
labels[0] = "< 0"
labels[1] = "0"
labels[-1] = "> 90"
    # Option 2: 0 to 10
#labels = [str(c)[1:-1].replace(","," to") for c in out.cat.categories]
    # Option 3: >0<=10
#labels = ["< 0", "Zero cloud", ">0<=10", ">10<=20", ">20<=30", ">30<=40", ">40<=50", ">50<=60", ">60<=70", ">70<=80", ">80<=90", ">90"]

ax.set_xticklabels(labels, color='black', fontsize=11)

# add the y-value related to each bar
for p in ax.patches:
    #ax.annotate(str(round(p.get_height(),2)), (p.get_x() * 1.000, p.get_height() * 1.015))    
    ax.annotate(str(round(p.get_height(),2)), (p.get_x(), p.get_height() + 0.3), color='black', fontweight='bold')    

plt.show()
```


![png](output_18_0.png)


##### Removing observations that present negative cloud cover



```python
# Get the name of indexes for which column "Cloud cover" has negative values
index_delete = tidy_data[tidy_data["CloudCover"] < 0].index



# Delete these row indexes from dataFrame
tidy_data.drop(index_delete, inplace=True)
```


```python
print("There are ", len(index_delete), " observations that present negative cloud cover.")

print("\nNew structure of the dataframe: ", tidy_data.shape)
```

    There are  183  observations that present negative cloud cover.
    
    New structure of the dataframe:  (177500, 12)
    

#### Lets check the statistics and distribution of the data once again

##### Statistics


```python
tidy_data["CloudCover"].describe()
```




    count    177500.000000
    mean         49.990184
    std          36.943149
    min           0.000000
    25%          12.263300
    50%          50.013650
    75%          87.816550
    max         100.000000
    Name: CloudCover, dtype: float64



##### Data distribution


```python
from matplotlib import pyplot as plt

# create the figure
fig = plt.figure()
fig.suptitle("Distribution of cloud cover on Landsat-8 and Sentinel-2 scenes \nacquired across the Brazilian Amazon", fontsize=18, fontweight='bold') 

# format the axes
ax = fig.add_subplot()
fig.subplots_adjust(top=0.85)
ax.set_title('Feb/2013 - Jul/2019', fontsize=14, fontweight='bold')
ax.set_xlabel("% OF CLOUD", labelpad=20, fontsize=12, fontweight='bold')
ax.set_ylabel("% OF COUNT", labelpad=20, fontsize=12, fontweight='bold')
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
plt.grid(linestyle="dashed", axis="y")

# data normalization
out = pd.cut(tidy_data["CloudCover"], bins=[-100, 0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100], include_lowest=False)
out_norm = out.value_counts(sort=False, normalize=True).mul(100)
ax = out_norm.plot.bar(rot=0, color="gray", figsize=(14,8) )

# X-axis: Label options
    # Option 1: ["< 0"; (0, 10]; "> 90"]
labels = [str(c) for c in out.cat.categories]
labels[0] = "0"
labels[-1] = "> 90"
    # Option 2: 0 to 10
#labels = [str(c)[1:-1].replace(","," to") for c in out.cat.categories]
    # Option 3: >0<=10
#labels = ["< 0", "Zero cloud", ">0<=10", ">10<=20", ">20<=30", ">30<=40", ">40<=50", ">50<=60", ">60<=70", ">70<=80", ">80<=90", ">90"]

ax.set_xticklabels(labels, color='black', fontsize=11)

# add the y-value related to each bar
for p in ax.patches:
    #ax.annotate(str(round(p.get_height(),2)), (p.get_x() * 1.000, p.get_height() * 1.015))    
    ax.annotate(str(round(p.get_height(),2)), (p.get_x(), p.get_height() + 0.3), color='black', fontweight='bold')    

plt.show()
```


![png](output_25_0.png)


#### Converting some columns to catagorical factors

##### Platform


```python
tidy_data.loc[tidy_data["Platform"] == "Landsat-8", "Platform"] = "L8"
tidy_data.loc[tidy_data["Platform"] == "SENTINEL-2A", "Platform"] = "S2A"
tidy_data.loc[tidy_data["Platform"] == "SENTINEL-2B", "Platform"] = "S2B"

# Convert the column Platform to type Category 
    # https://stackoverflow.com/questions/38954525/how-to-have-catagorical-factor-variables-in-python
tidy_data["Platform"] = tidy_data["Platform"].astype('category')
```

##### Catalog system for Landsat and Sentinel data

**Landsat** uses the Worldwide Reference System (**WRS**), which is a global system that catalogs Landsat data by Path and Row numbers.


On the other hand, **Sentinel** adopts the UTM system, which divides the Earth's surface into granules. The granules, also called **tiles**, are 100x100 km² orthoimages in UTM/WGS84.


Here, I created a column named "Catalog system" to describe the system related to each observation. This column tells me if I should analyse the columns "WRS Path" and "WRS Row" or just the column "Tile Number".


```python
# Creates the new column
tidy_data.insert(3, "CatalogSystem", None, allow_duplicates=False)
```


```python
# Attributes the data
tidy_data.loc[tidy_data["Platform"] == "L8", "CatalogSystem"] = "WRS"
tidy_data.loc[tidy_data["Platform"] == "S2A", "CatalogSystem"] = "Tile"
tidy_data.loc[tidy_data["Platform"] == "S2B", "CatalogSystem"] = "Tile"
```


```python
# Converts to categorical type
tidy_data["CatalogSystem"] = tidy_data["CatalogSystem"].astype('category')
tidy_data.head()
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
      <th>ProductID</th>
      <th>AcquisitionDate</th>
      <th>Platform</th>
      <th>CatalogSystem</th>
      <th>WRSPath</th>
      <th>WRSRow</th>
      <th>TileNumber</th>
      <th>CloudCover</th>
      <th>Datum</th>
      <th>UTMZone</th>
      <th>SpatialRes_Pan</th>
      <th>SpatialRes_Refletive</th>
      <th>SpatialRes_Thermal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>LC08_L1GT_228058_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>58.0</td>
      <td>NaN</td>
      <td>46.82</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>1</th>
      <td>LC08_L1TP_228059_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>59.0</td>
      <td>NaN</td>
      <td>18.89</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>2</th>
      <td>LC08_L1TP_228060_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>60.0</td>
      <td>NaN</td>
      <td>12.86</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>3</th>
      <td>LC08_L1TP_228061_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>61.0</td>
      <td>NaN</td>
      <td>7.67</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>4</th>
      <td>LC08_L1TP_228062_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>62.0</td>
      <td>NaN</td>
      <td>8.10</td>
      <td>WGS84</td>
      <td>21</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
  </tbody>
</table>
</div>



##### Identifying the hemisphere of each observation

Sentinel data presents the hemisphere along with the UTM Zone. On the other hand, Landsat data do not include this information. However, the framing of the Landsat data is uniform for each orbit. The adjacent east-west scenes have scene center locations at the same nominal latitude. A notation of Row numbers can, therefore, be applied to identify all scenes occurring at the same latitude. **Row 060 corresponds to latitude 0 (equator)**. Row 059 is immediately north of this.

**Obs.:** I considered that the grids that cross the equator line are in the Northern hemisphere.

Read more: https://landsat.gsfc.nasa.gov/the-worldwide-reference-system/


```python
# Creates a new column, named "Hemisphere"
tidy_data.insert(10, "Hemisphere", None, allow_duplicates=False)
```

###### Sentinel-2


```python
# Attributes the hemispher identifier to the new column
tidy_data.loc[tidy_data["Platform"] == "S2A", "Hemisphere"] = tidy_data["UTMZone"].str[-1]
tidy_data.loc[tidy_data["Platform"] == "S2B", "Hemisphere"] = tidy_data["UTMZone"].str[-1]

# Removes the hemispher identifier from the column "UTM Zone"
tidy_data.loc[tidy_data["Platform"] == "S2A", "UTMZone"] = tidy_data["UTMZone"].str[:-1]
tidy_data.loc[tidy_data["Platform"] == "S2B", "UTMZone"] = tidy_data["UTMZone"].str[:-1]
```

###### Landsat-8


```python
tidy_data.loc[(tidy_data["Platform"] == "L8") & (tidy_data["WRSRow"] <= 60), "Hemisphere"] = "N"
tidy_data.loc[(tidy_data["Platform"] == "L8") & (tidy_data["WRSRow"] > 60), "Hemisphere"] = "S"
```


```python
tidy_data
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
      <th>ProductID</th>
      <th>AcquisitionDate</th>
      <th>Platform</th>
      <th>CatalogSystem</th>
      <th>WRSPath</th>
      <th>WRSRow</th>
      <th>TileNumber</th>
      <th>CloudCover</th>
      <th>Datum</th>
      <th>UTMZone</th>
      <th>Hemisphere</th>
      <th>SpatialRes_Pan</th>
      <th>SpatialRes_Refletive</th>
      <th>SpatialRes_Thermal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>LC08_L1GT_228058_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>58.0</td>
      <td>NaN</td>
      <td>46.8200</td>
      <td>WGS84</td>
      <td>21</td>
      <td>N</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>1</th>
      <td>LC08_L1TP_228059_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>59.0</td>
      <td>NaN</td>
      <td>18.8900</td>
      <td>WGS84</td>
      <td>21</td>
      <td>N</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>2</th>
      <td>LC08_L1TP_228060_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>60.0</td>
      <td>NaN</td>
      <td>12.8600</td>
      <td>WGS84</td>
      <td>21</td>
      <td>N</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>3</th>
      <td>LC08_L1TP_228061_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>61.0</td>
      <td>NaN</td>
      <td>7.6700</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>4</th>
      <td>LC08_L1TP_228062_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>62.0</td>
      <td>NaN</td>
      <td>8.1000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>5</th>
      <td>LC08_L1TP_228063_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>63.0</td>
      <td>NaN</td>
      <td>0.1000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>6</th>
      <td>LC08_L1TP_228064_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>64.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>7</th>
      <td>LC08_L1TP_228065_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>65.0</td>
      <td>NaN</td>
      <td>0.8000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>8</th>
      <td>LC08_L1TP_228066_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>66.0</td>
      <td>NaN</td>
      <td>3.9400</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>9</th>
      <td>LC08_L1TP_228067_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>67.0</td>
      <td>NaN</td>
      <td>9.8300</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>10</th>
      <td>LC08_L1TP_228068_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>68.0</td>
      <td>NaN</td>
      <td>0.9800</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>11</th>
      <td>LC08_L1TP_228069_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>69.0</td>
      <td>NaN</td>
      <td>0.7900</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>12</th>
      <td>LC08_L1TP_228070_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>70.0</td>
      <td>NaN</td>
      <td>0.1000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>13</th>
      <td>LC08_L1TP_228071_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>71.0</td>
      <td>NaN</td>
      <td>0.1700</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>14</th>
      <td>LC08_L1TP_228072_20190731_20190731_01_RT</td>
      <td>2019/07/31</td>
      <td>L8</td>
      <td>WRS</td>
      <td>228.0</td>
      <td>72.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>15</th>
      <td>LC08_L1TP_004058_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>4.0</td>
      <td>58.0</td>
      <td>NaN</td>
      <td>70.9300</td>
      <td>WGS84</td>
      <td>19</td>
      <td>N</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>16</th>
      <td>LC08_L1TP_004059_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>4.0</td>
      <td>59.0</td>
      <td>NaN</td>
      <td>54.7200</td>
      <td>WGS84</td>
      <td>19</td>
      <td>N</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>17</th>
      <td>LC08_L1TP_004060_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>4.0</td>
      <td>60.0</td>
      <td>NaN</td>
      <td>79.8400</td>
      <td>WGS84</td>
      <td>19</td>
      <td>N</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>18</th>
      <td>LC08_L1GT_004061_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>4.0</td>
      <td>61.0</td>
      <td>NaN</td>
      <td>84.4400</td>
      <td>WGS84</td>
      <td>19</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>19</th>
      <td>LC08_L1GT_004062_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>4.0</td>
      <td>62.0</td>
      <td>NaN</td>
      <td>81.8600</td>
      <td>WGS84</td>
      <td>19</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>20</th>
      <td>LC08_L1TP_004063_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>4.0</td>
      <td>63.0</td>
      <td>NaN</td>
      <td>14.2300</td>
      <td>WGS84</td>
      <td>19</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>21</th>
      <td>LC08_L1TP_004064_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>4.0</td>
      <td>64.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>19</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>22</th>
      <td>LC08_L1TP_004065_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>4.0</td>
      <td>65.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>19</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>23</th>
      <td>LC08_L1TP_004066_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>4.0</td>
      <td>66.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>19</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>24</th>
      <td>LC08_L1TP_004067_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>4.0</td>
      <td>67.0</td>
      <td>NaN</td>
      <td>4.6700</td>
      <td>WGS84</td>
      <td>19</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>25</th>
      <td>LC08_L1GT_221061_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>221.0</td>
      <td>61.0</td>
      <td>NaN</td>
      <td>40.7500</td>
      <td>WGS84</td>
      <td>23</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>26</th>
      <td>LC08_L1TP_221062_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>221.0</td>
      <td>62.0</td>
      <td>NaN</td>
      <td>34.9100</td>
      <td>WGS84</td>
      <td>23</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>27</th>
      <td>LC08_L1TP_221063_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>221.0</td>
      <td>63.0</td>
      <td>NaN</td>
      <td>8.7300</td>
      <td>WGS84</td>
      <td>23</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>28</th>
      <td>LC08_L1TP_221064_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>221.0</td>
      <td>64.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>23</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>29</th>
      <td>LC08_L1TP_221065_20190730_20190730_01_RT</td>
      <td>2019/07/30</td>
      <td>L8</td>
      <td>WRS</td>
      <td>221.0</td>
      <td>65.0</td>
      <td>NaN</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>23</td>
      <td>S</td>
      <td>15</td>
      <td>30</td>
      <td>30</td>
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
    </tr>
    <tr>
      <th>177653</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MWP</td>
      <td>8.2695</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177654</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MXM</td>
      <td>71.5078</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177655</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MVP</td>
      <td>9.4894</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177656</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MVM</td>
      <td>23.7708</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177657</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MUP</td>
      <td>44.9922</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177658</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MUN</td>
      <td>48.1419</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177659</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MUM</td>
      <td>11.8060</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177660</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MWM</td>
      <td>48.0121</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177661</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MXN</td>
      <td>47.3968</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177662</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MXP</td>
      <td>48.0832</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177663</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MWN</td>
      <td>39.6626</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177664</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21KUB</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177665</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>20KQG</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>20</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177666</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>20KRG</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>20</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177667</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21KTB</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177668</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21KUA</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177669</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21KVB</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177670</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MYU</td>
      <td>0.2791</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177671</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MWT</td>
      <td>3.0382</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177672</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MZV</td>
      <td>0.0000</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177673</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MWV</td>
      <td>44.3405</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177674</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MXV</td>
      <td>9.5568</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177675</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MXT</td>
      <td>2.3223</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177676</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MXU</td>
      <td>1.2880</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177677</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MYT</td>
      <td>3.1379</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177678</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MWU</td>
      <td>10.2529</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177679</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MVV</td>
      <td>44.8977</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177680</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MVU</td>
      <td>18.1768</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177681</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MYV</td>
      <td>34.5259</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
    <tr>
      <th>177682</th>
      <td>S2A_OPER_MSI_L1C_TL_EPA__20160928T053642_A0008...</td>
      <td>2015-08-19</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MVT</td>
      <td>5.5611</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>10</td>
      <td>20</td>
      <td>60</td>
    </tr>
  </tbody>
</table>
<p>177500 rows × 14 columns</p>
</div>



#### Writing the tidy dataframe to file


```python
filename = 'Output/Metadata/Metadata_L8_S2_tidy.csv'

try:
    tidy_data.to_csv(filename, sep=',', index=False, encoding='utf-8')
    print("The dataframe was written to file!")
except Exception as e:
    print(str(e))
```

    The dataframe was written to file!
    

<a id='Stats'></a>
## 5. Statistics

### General statistics regarding the entire dataset


```python
tidy_data.describe(include='all')
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
      <th>ProductID</th>
      <th>AcquisitionDate</th>
      <th>Platform</th>
      <th>CatalogSystem</th>
      <th>WRSPath</th>
      <th>WRSRow</th>
      <th>TileNumber</th>
      <th>CloudCover</th>
      <th>Datum</th>
      <th>UTMZone</th>
      <th>Hemisphere</th>
      <th>SpatialRes_Pan</th>
      <th>SpatialRes_Refletive</th>
      <th>SpatialRes_Thermal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>177500</td>
      <td>177500</td>
      <td>177500</td>
      <td>177500</td>
      <td>34506.000000</td>
      <td>34506.000000</td>
      <td>142994</td>
      <td>177500.000000</td>
      <td>177500</td>
      <td>177500</td>
      <td>177500</td>
      <td>177500.000000</td>
      <td>177500.000000</td>
      <td>177500.000000</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>177500</td>
      <td>3524</td>
      <td>3</td>
      <td>2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>636</td>
      <td>NaN</td>
      <td>1</td>
      <td>6</td>
      <td>2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>top</th>
      <td>L1C_T21LUC_A020985_20190629T141051</td>
      <td>2019-04-10</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MVR</td>
      <td>NaN</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>1</td>
      <td>224</td>
      <td>87444</td>
      <td>142994</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>370</td>
      <td>NaN</td>
      <td>177500</td>
      <td>46345</td>
      <td>149740</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>175.923144</td>
      <td>64.197734</td>
      <td>NaN</td>
      <td>49.990184</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.972000</td>
      <td>21.944000</td>
      <td>54.150085</td>
    </tr>
    <tr>
      <th>std</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>93.754992</td>
      <td>4.003751</td>
      <td>NaN</td>
      <td>36.943149</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.978696</td>
      <td>3.957392</td>
      <td>11.931123</td>
    </tr>
    <tr>
      <th>min</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.000000</td>
      <td>56.000000</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.000000</td>
      <td>20.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>220.000000</td>
      <td>61.000000</td>
      <td>NaN</td>
      <td>12.263300</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.000000</td>
      <td>20.000000</td>
      <td>60.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>225.000000</td>
      <td>64.000000</td>
      <td>NaN</td>
      <td>50.013650</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.000000</td>
      <td>20.000000</td>
      <td>60.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>229.000000</td>
      <td>67.000000</td>
      <td>NaN</td>
      <td>87.816550</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.000000</td>
      <td>20.000000</td>
      <td>60.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>233.000000</td>
      <td>73.000000</td>
      <td>NaN</td>
      <td>100.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>15.000000</td>
      <td>30.000000</td>
      <td>60.000000</td>
    </tr>
  </tbody>
</table>
</div>



### Statistics regarding only Landsat-8 data


```python
tidy_data[tidy_data["Platform"] == "L8"].describe(include='all')
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
      <th>ProductID</th>
      <th>AcquisitionDate</th>
      <th>Platform</th>
      <th>CatalogSystem</th>
      <th>WRSPath</th>
      <th>WRSRow</th>
      <th>TileNumber</th>
      <th>CloudCover</th>
      <th>Datum</th>
      <th>UTMZone</th>
      <th>Hemisphere</th>
      <th>SpatialRes_Pan</th>
      <th>SpatialRes_Refletive</th>
      <th>SpatialRes_Thermal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>34506</td>
      <td>34506</td>
      <td>34506</td>
      <td>34506</td>
      <td>34506.000000</td>
      <td>34506.000000</td>
      <td>0.0</td>
      <td>34506.000000</td>
      <td>34506</td>
      <td>34506</td>
      <td>34506</td>
      <td>34506.0</td>
      <td>34506.0</td>
      <td>34506.000000</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>34506</td>
      <td>2308</td>
      <td>1</td>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>1</td>
      <td>6</td>
      <td>2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>top</th>
      <td>LC08_L1GT_005059_20190705_20190719_01_T2</td>
      <td>2019/03/08</td>
      <td>L8</td>
      <td>WRS</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>1</td>
      <td>20</td>
      <td>34506</td>
      <td>34506</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>34506</td>
      <td>8736</td>
      <td>27335</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>175.923144</td>
      <td>64.197734</td>
      <td>NaN</td>
      <td>50.169757</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>15.0</td>
      <td>30.0</td>
      <td>29.907842</td>
    </tr>
    <tr>
      <th>std</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>93.754992</td>
      <td>4.003751</td>
      <td>NaN</td>
      <td>34.164700</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.660218</td>
    </tr>
    <tr>
      <th>min</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.000000</td>
      <td>56.000000</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>15.0</td>
      <td>30.0</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>220.000000</td>
      <td>61.000000</td>
      <td>NaN</td>
      <td>18.062500</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>15.0</td>
      <td>30.0</td>
      <td>30.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>225.000000</td>
      <td>64.000000</td>
      <td>NaN</td>
      <td>51.030000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>15.0</td>
      <td>30.0</td>
      <td>30.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>229.000000</td>
      <td>67.000000</td>
      <td>NaN</td>
      <td>82.150000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>15.0</td>
      <td>30.0</td>
      <td>30.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>233.000000</td>
      <td>73.000000</td>
      <td>NaN</td>
      <td>100.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>15.0</td>
      <td>30.0</td>
      <td>30.000000</td>
    </tr>
  </tbody>
</table>
</div>



### Statistics regarding only Sentinel-2 data


```python
tidy_data[(tidy_data["Platform"] == "S2A") | (tidy_data["Platform"] == "S2B")].describe(include="all")
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
      <th>ProductID</th>
      <th>AcquisitionDate</th>
      <th>Platform</th>
      <th>CatalogSystem</th>
      <th>WRSPath</th>
      <th>WRSRow</th>
      <th>TileNumber</th>
      <th>CloudCover</th>
      <th>Datum</th>
      <th>UTMZone</th>
      <th>Hemisphere</th>
      <th>SpatialRes_Pan</th>
      <th>SpatialRes_Refletive</th>
      <th>SpatialRes_Thermal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>142994</td>
      <td>142994</td>
      <td>142994</td>
      <td>142994</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>142994</td>
      <td>142994.000000</td>
      <td>142994</td>
      <td>142994</td>
      <td>142994</td>
      <td>142994.0</td>
      <td>142994.0</td>
      <td>142994.0</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>142994</td>
      <td>1216</td>
      <td>2</td>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>636</td>
      <td>NaN</td>
      <td>1</td>
      <td>6</td>
      <td>2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>top</th>
      <td>L1C_T21LUC_A020985_20190629T141051</td>
      <td>2019-04-10</td>
      <td>S2A</td>
      <td>Tile</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21MVR</td>
      <td>NaN</td>
      <td>WGS84</td>
      <td>21</td>
      <td>S</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>1</td>
      <td>224</td>
      <td>87444</td>
      <td>142994</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>370</td>
      <td>NaN</td>
      <td>142994</td>
      <td>37609</td>
      <td>122405</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>49.946851</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.0</td>
      <td>20.0</td>
      <td>60.0</td>
    </tr>
    <tr>
      <th>std</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>37.582852</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>min</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.0</td>
      <td>20.0</td>
      <td>60.0</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.882975</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.0</td>
      <td>20.0</td>
      <td>60.0</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>49.696100</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.0</td>
      <td>20.0</td>
      <td>60.0</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>89.363400</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.0</td>
      <td>20.0</td>
      <td>60.0</td>
    </tr>
    <tr>
      <th>max</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>100.000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.0</td>
      <td>20.0</td>
      <td>60.0</td>
    </tr>
  </tbody>
</table>
</div>



[<p align="left"> **<< Previous notebook** </p>](./1_Project.md)

[<p align="right"> **Next notebook >>** </p>](./3_EDA.md)

***
## References

[1] Rene Beuchle , Hugh D. Eva , Hans-Jürgen Stibig , Catherine Bodart , Andreas Brink , Philippe Mayaux , Desiree Johansson , Frederic Achard & Alan Belward (2011) A satellite data set for tropical forest area change assessment, **International Journal of Remote Sensing**, 32:22, 7009-7031, DOI: 10.1080/01431161.2011.611186

[2] Sano E E, Ferreira L G, Asner G P et al., 2007. Spatial and temporal probabilities of obtaining cloud-free Landsat
images over the Brazilian tropical savanna. **International Journal of Remote Sensing**, 28(12): 2739–2752.

[3] Lima, T.A., Beuchle, R., Langner, A., Grecchi, R.C., Griess, V.C., & Achard, F. (2019). Comparing Sentinel-2 MSI and Landsat 8 OLI Imagery for Monitoring Selective Logging in the Brazilian Amazon. **Remote Sensing**, 11, 961.

[4] Young O.R., Onoda M. (2017) **Satellite Earth Observations in Environmental Problem-Solving**. In: Onoda M., Young O. (eds) Satellite Earth Observations and Their Impact on Society and Policy. Springer, Singapore, DOI: 10.1080/01431161.2011.611186
