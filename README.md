# Spark를 이용한 RDD 연산실습


## Spark 내려 받기

- windows terminal or mac terminal을 연다. 
- 소스 코드를 다운 받을 적당한 디렉토리로 이동한다.  
- git 명령어를 이용해 Spark를 내려 받는다. 

```
git clone https://github.com/CUKykkim/docker-spark.git
```


## Spark 수행하기

- git을 통해 다운 받은 디렉토리 안으로 들어간다. 

```
cd docker-spark
```

- `docker-compose` 명령어를 이용해 스파크 컨테이너를 띄운다. 
  
```
docker-compose up
```

- 컨테이너가 모두 수행이 되면 컨테이너는 다음과 같은 상태가 됨

```
docker ps
```

```
CONTAINER ID   IMAGE                    COMMAND                  CREATED              STATUS          PORTS
                   NAMES
edb3f8d728c9   ykkim77/spark-worker-1   "/bin/bash /worker.sh"   42 seconds ago       Up 36 seconds   0.0.0.0:8081->8081/tcp, :::8081->8081/tcp
                   spark-worker-1
349f58d01f67   ykkim77/spark-master     "/bin/bash /master.sh"   About a minute ago   Up 39 seconds   0.0.0.0:7077->7077/tcp, :::7077->7077/tcp, 6066/tcp, 0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   spark-master
```


- terminal 탭을 하나 더 열어, spark-master 컨테이너로 진입

```
docker exec -it spark-master /bin/bash
```

- spark가 설치된 경로의 디렉토리로 이동

```
cd  ~/../spark/bin/
```

- python으로 spark을 연산을 수행할 수 있는 스파크쉘 수행

```
./pyspark
```

```
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version 3.1.1
      /_/

Using Python version 3.7.10 (default, Mar  2 2021 09:06:08)
Spark context Web UI available at http://349f58d01f67:4040
Spark context available as 'sc' (master = local[*], app id = local-1632992284633).
SparkSession available as 'spark'.
```


## RDD 연산 수행하기

- 다양한 RDD 연산의 종류는 [스파크 공식 문서](https://spark.apache.org/docs/1.2.0/programming-guide.html#transformations)를 참고

### 리스트 기반의 데이터를 RDD로 생성후 연산하기

```
data1 = [1, 2, 3, 4, 5]
data2 = [4, 5, 6, 7, 8]
rdd1 = sc.parallelize(data1)   # rdd1 생성
rdd2 = sc.parallelize(data2)   # rdd2 생성
rdd3 = rdd1.union(rdd2)        # rdd1과 rdd2를 합집합 하여 rdd3를 생성

rdd3.collect()     # rdd3에 대해 Action 수행

```

### 파일 기반으로 workdcount (단어수 세기) 만들기

```
wcRdd = spark.sparkContext.textFile(os.path.join('input.txt'))
wcRdd.collect()

wcRdd1 = wcRdd.map(lambda x:x.split(' '))
wcRdd1.collect()

wcRdd2 = wcRdd1.map(lambda x:(x,1))
wcRdd2.collect()

wcRdd3 = wcRdd2.groupByKey()
wcRdd3.collect()

wcRdd4 = wcRdd3.mapValues(sum)
wcRdd4.collect()

```


## spark 컨테이너 종료

```
docker-compose down
```



```
from pyspark.sql import SparkSession
from pyspark.sql.functions import col


spark: SparkSession = SparkSession.builder \
    .master("local[1]") \
    .appName("SparkByExamples.com") \
    .getOrCreate()

df = spark.read.csv('chipotle.csv', header = True, inferSchema = True)


df.printSchema()
df.show()


print("Total number of :",df.filter(df.choice_description.isNull()).count())



df1= df.na.drop(how="any")
df1.show(truncate=False)
df1.dropDuplicates(['item_name']).show()
df.show()
```

## iris 데이터로 주성분 분석하기

- iris 데이터셋
```
 caseno	        일련번호
 Sepal Length	꽃받침의 길이 정보
 Sepal Width	꽃받침의 너비 정보
 Petal Length	꽃잎의 길이 정보
 Petal Width	꽃잎의 너비 정보  
 Species	    꽃의 종류 정보  setosa / versicolor / virginica 3종류
```
![Large53](./images/Large53.jpg)
