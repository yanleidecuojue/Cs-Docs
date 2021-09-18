#### 一.Python基础
##### 1.Python安装
```python
1.anaconda官网下载安装包进行安装
2.修改国内镜像源
3.conda create -n basic python=3.7.4
```
#### 2.Python基本数据类型
```python
>>> tuple
<class 'tuple'>
>>> list
<class 'list'>
>>> dict
<class 'dict'>
>>> set
<class 'set'>

以上四种数据类型的数据均可以通过以下方式遍历
for index,item in enumerate(data):
  print(index,item)
```
#### 3.pandas & numpy
##### pandas 例子
###### 1.创建excel文件，内容如下
excel1.xlsx

|                |  A             | B              | C              |
| :------------- | :------------- | :------------- | :------------- |
| 第一行         |  34            | 31             |  45            |
| 第二行         |  12            | 18             |  测试数据       |

excel2.xlsx

|                |  第一列        |  第二列        |   第三列        |
| :------------- | :------------- | :------------- | :------------- |
| A              |  45            | 1              |  5             |
| B              |  2             | 8              |  测试数据      |

```python
pip install pandas
pip install openpyxl
import pandas as pd

df = pd.read_excel('./excel1.xlsx')

# 显示原始数据
print('原始数据:\n', df)

# DataFrame获取某列数据
print('获取某列数据:\n', df['C'], type(df['C']))

# 获取某个位置的数据 - 1
print('获取某个位置的数据 - 1:\n', df.iloc[1,1])

# 获取某个位置数据 - 2
print('获取某个位置数据 - 2:\n', df.loc[1, 'B'])

# 遍历
```

##### 参考网址
https://pandas.pydata.org/docs/user_guide/index.html
https://numpy.org/doc/stable/user/index.html
