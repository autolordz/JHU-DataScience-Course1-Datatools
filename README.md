# JHU-DataScience-Course1-Datatools
JHU DataScience Specialization/Cousers The Data Scientist’s Toolbox/Week??/Course Project


# Datatools Course Project 数据科学工具

由于这个非常入门的课程，共4Week每周一个小quiz，这个应该是最后的Project忘记了
主要是熟练掌握一些R语言的数据处理工具例如 xlsx,XML等格式，以及readr这些有用的R包用法
以下代码下载资源比较大暂不执行有兴趣的读者可以自己尝试



## 读取csv格式 2006年美国社区调查（ACS）

The American Community Survey distributes downloadable data about United States communities. Download the 2006 microdata survey about housing for the state of Idaho using download.file() from here:

- (pid, Population CSV file)
- (hid, Household CSV file)

[说明书 PUMS](https://www.nber.org/acs/PUMS/README)
[说明书 DATA DICTIONARY - 2006 HOUSING](https://www.nber.org/acs/PUMS/2006/PUMSDataDict06.txt)
[PDF](https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FPUMSDataDict06.pdf)


```r
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06pid.csv"
download.file(fileUrl,destfile = "Fss06pid.csv",method = "libcurl")
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv"
download.file(fileUrl,destfile = "Fss06hid.csv",method = "libcurl")
```


### 读取几十上百m的数据算大文件，运算应该考虑花销


```
## Unit: milliseconds
##                                           expr       min        lq
##  PID <- fread(file = "Fss06pid.csv", fill = T)  13.40045  13.40045
##                read_csv(file = "Fss06pid.csv") 627.03159 627.03159
##                read.csv(file = "Fss06pid.csv") 422.56456 422.56456
##       mean    median        uq       max neval
##   13.40045  13.40045  13.40045  13.40045     1
##  627.03159 627.03159 627.03159 627.03159     1
##  422.56456 422.56456 422.56456 422.56456     1
```

```
## Unit: milliseconds
##                                           expr       min        lq
##  HID <- fread(file = "Fss06hid.csv", fill = T)  17.08404  17.08404
##                read_csv(file = "Fss06hid.csv") 519.85311 519.85311
##                read.csv(file = "Fss06hid.csv") 971.56116 971.56116
##       mean    median        uq       max neval
##   17.08404  17.08404  17.08404  17.08404     1
##  519.85311 519.85311 519.85311 519.85311     1
##  971.56116 971.56116 971.56116 971.56116     1
```

```
## Unit: microseconds
##                                       expr      min        lq       mean
##         tapply(PID$pwgtp15, PID$SEX, mean)  159.973  203.6820  262.39322
##  sapply(split(PID$pwgtp15, PID$SEX), mean)  137.322  162.9805  250.35640
##          mean(PID[PID$SEX == 1, ]$pwgtp15) 1533.888 1615.2890 2570.51404
##            mean(PID$pwgtp15, by = PID$SEX)   11.326   16.1045   20.48577
##          median(PID$pwgtp15, by = PID$SEX)   49.550   64.7685   91.37941
##         mean(PID[PID$SEX == 1, ]$SERIALNO) 1548.398 1624.1370 3693.22785
##       median(PID[PID$SEX == 1, ]$SERIALNO) 1630.508 1709.9625 2321.75111
##     median        uq        max neval cld
##   267.5640  289.3300    441.692   100  a 
##   215.8915  246.1520   3571.401   100  a 
##  1694.3900 4332.1510   6311.799   100   b
##    18.2280   22.4745     78.571   100  a 
##    85.2955  109.8930    249.160   100  a 
##  1740.4000 2707.6610 127876.038   100   b
##  1751.5480 2145.2830   6184.035   100   b
```


```r
pander(arrange(A,mean))
```



|                     expr                      |  min  |  lq   | mean  |
|:---------------------------------------------:|:-----:|:-----:|:-----:|
| PID <- fread(file = "Fss06pid.csv", fill = T) | 13.4  | 13.4  | 13.4  |
|        read.csv(file = "Fss06pid.csv")        | 422.6 | 422.6 | 422.6 |
|        read_csv(file = "Fss06pid.csv")        |  627  |  627  |  627  |

Table: Table continues below

 

| median |  uq   |  max  | neval |
|:------:|:-----:|:-----:|:-----:|
|  13.4  | 13.4  | 13.4  |   1   |
| 422.6  | 422.6 | 422.6 |   1   |
|  627   |  627  |  627  |   1   |

```r
pander(arrange(B,mean))
```



|                     expr                      |  min  |  lq   | mean  |
|:---------------------------------------------:|:-----:|:-----:|:-----:|
| HID <- fread(file = "Fss06hid.csv", fill = T) | 17.08 | 17.08 | 17.08 |
|        read_csv(file = "Fss06hid.csv")        | 519.9 | 519.9 | 519.9 |
|        read.csv(file = "Fss06hid.csv")        | 971.6 | 971.6 | 971.6 |

Table: Table continues below

 

| median |  uq   |  max  | neval |
|:------:|:-----:|:-----:|:-----:|
| 17.08  | 17.08 | 17.08 |   1   |
| 519.9  | 519.9 | 519.9 |   1   |
| 971.6  | 971.6 | 971.6 |   1   |

### 简单计算求和时间


```r
pander(arrange(select(C,expr,mean),mean))
```



|                   expr                    | mean  |
|:-----------------------------------------:|:-----:|
|      mean(PID$pwgtp15, by = PID$SEX)      | 20.49 |
|     median(PID$pwgtp15, by = PID$SEX)     | 91.38 |
| sapply(split(PID$pwgtp15, PID$SEX), mean) | 250.4 |
|    tapply(PID$pwgtp15, PID$SEX, mean)     | 262.4 |
|   median(PID[PID$SEX == 1, ]$SERIALNO)    | 2322  |
|     mean(PID[PID$SEX == 1, ]$pwgtp15)     | 2571  |
|    mean(PID[PID$SEX == 1, ]$SERIALNO)     | 3693  |

### Quiz 1

How many housing units in this survey were worth more than $1,000,000?

- 159
- 164
- **53**
- 24

## 读取xls,xlsx格式 美国国家天然气采购计划

[说明书 Natural Gas Acquisition Program](https://catalog.data.gov/dataset/natural-gas-acquisition-program)



```r
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FDATA.gov_NGAP.xlsx"
download.file(fileurl,destfile = "gov_NGAP.xlsx",mode='wb')
```


```r
NGAP <- read.xlsx(file="gov_NGAP.xlsx",
                  sheetIndex=1,
                  colIndex=7:15,
                  rowIndex = 18:23,
                  header=T)
NGAP <- as.tbl(NGAP)
pander(head(NGAP))
```



|  Zip  | CuCurrent | PaCurrent | PoCurrent |   Contact    | Ext |
|:-----:|:---------:|:---------:|:---------:|:------------:|:---:|
| 74136 |     0     |     1     |     0     | 918-491-6998 |  0  |
| 30329 |     1     |     0     |     0     | 404-321-5711 | NA  |
| 74136 |     1     |     0     |     0     | 918-523-2516 |  0  |
| 80203 |     0     |     1     |     0     | 303-864-1919 |  0  |
| 80120 |     1     |     0     |     0     | 345-098-8890 | 456 |

Table: Table continues below

 

|     Fax      | email | Status |
|:------------:|:-----:|:------:|
| 918-491-6659 |  NA   |   1    |
|      NA      |  NA   |   1    |
| 918-523-2522 |  NA   |   1    |
|      NA      |  NA   |   1    |
|      NA      |  NA   |   1    |

```r
cat("Sum of Zipcode and")
```

```
## Sum of Zipcode and
```

```r
sum(NGAP$Zip*NGAP$Ext,na.rm=T)
```

```
## [1] 36534720
```

### Quiz 3

Read rows 18-23 and columns 7-15 into R and assign the result to a variable called:

What is the value of:

sum(dat\(Zip*dat\)Ext,na.rm=T)

(original data source: http://catalog.data.gov/dataset/natural-gas-acquisition-program)

1. **36534720**
2. 338924
3. 33544718
4. 0

## 读取xml格式 巴的摩尔餐厅数据

[说明书 data.baltimorecity.gov](https://catalog.data.gov/dataset/restaurants-15baa)


```r
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Frestaurants.xml"
download.file(fileUrl,destfile = "Frestaurants.xml",mode='wb')
```


```r
system.time(DOC <- xmlTreeParse("Frestaurants.xml", useInternal = TRUE))
```

```
##    user  system elapsed 
##    0.02    0.00    0.02
```

```r
rootNode <- xmlRoot(DOC)
# 邮编是"21231" 的记录有多少条
sum(xpathSApply(rootNode, "//zipcode", xmlValue) == "21231")
```

```
## [1] 127
```

### Quiz 4

How many restaurants have zipcode 21231?

- **127**
- 17
- 28
- 156


<center>**THE END**</center>
