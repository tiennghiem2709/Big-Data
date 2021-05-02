# 1.Spark
## 1.1 Giới thiệu: 
- Là một framework để xử lý dữ liệu
- Cung cấp các APIs cho Scala, Java, Python.
- Được xây dựng bằng ngôn ngữ Scala.
- Tốc độ nhanh hơn Mapreduce từ 10 ~ 100 lần

## 1.2 Lợi ích
- Fast processing: xử lí nhanh
- In-memory computation: tính toán trên RAM
- Fault Tolerance: khả năng chống chịu lỗi
- Near Real time stream processing: xử lí dữ liệu gần như thời gian thực
- Support Multiple Languages: Hỗ trợ đa ngôn ngữ.

## 1.3 Thành phần
- Spark core: cung cấp nền tảng cho các sprak Applications
- Spark SQL: xử lí dữ liệu semi-structured/structured data bằng SQL/HQL
- Spark Streaming: xử lí dữ liệu streaming
- Spark ML cung cấp công cụ cho việc phân tích dữ liệu bằng các model ML
- Spark Graph X: xử lí dữ liệu đồ thị
- Spark R: dùng cho việc phân tích dữ liệu trên R
# 2.Spark Properties
  Spark Properties kiểm soát hầu hết các tham số ứng dụng và có thể được thiết lập bằng cách sử dụng đối tượng `SparkConf` hoặc thông qua các thuộc tính hệ thống Java.
  
  Spark Properties kiểm soát hầu hết các cài đặt ứng dụng và được cấu hình riêng cho từng ứng dụng. Các thuộc tính này có thể được cài đặt trực tiếp trên `SparkConf` được chuyển tới `SparkContext`. `SparkConf` cho phép bạn định cấu hình một số thuộc tính phổ biến (ví dụ: URL chính và tên ứng dụng), cũng như các cặp khóa-giá trị tùy ý thông qua phương thức `set()` . Ví dụ: 
    - Có thể khởi tạo một ứng dụng với hai luồng như sau: Lưu ý rằng chúng ta chạy với local[2], nghĩa là hai luồng - đại diện cho sự song song “tối thiểu”, có thể giúp phát hiện các lỗi chỉ tồn tại khi chạy trong ngữ cảnh phân tán.
    -    
 
 ```Javascript
    val conf = new SparkConf()
                  .setMaster("local[2]")
                  .setAppName("CountingSheep")
    val sc = new SparkContext(conf)
   ```
   Tham khảo:
   * [Spark Properties](https://apache.googlesource.com/spark/+/master/docs/configuration.md)
# 3.Spark Rdd
## 3.1 Giới thiệu
<p><b>Resilient Distributed Datasets</b> <i>(RDD)</i> là một cấu trúc dữ liệu cơ bản của Spark. Nó là một tập hợp bất biến phân tán của một đối tượng. Mỗi <i>dataset</i> trong <i>RDD</i> được chia ra thành nhiều phân vùng <i>logical</i>. Có thể được tính toán trên các node khác nhau của một cụm máy chủ <i>(cluster)</i>.</p>

<p><i>RDDs</i> có thể chứa bất kỳ kiểu dữ liệu nào của <i>Python, Java</i> hoặc đối tượng <i>Scala</i>, bao gồm các kiểu dữ liệu do người dùng định nghĩa. Thông thường, <i>RDD</i> chỉ cho phép đọc, phân mục tập hợp của các bản ghi. <i>RDDs</i> có thể được tạo ra qua điều khiển xác định trên dữ liệu trong bộ nhớ hoặc <i>RDDs</i>, <i>RDD</i> là một tập hợp có khả năng chịu lỗi mỗi thành phần có thể được tính toán song song.</p>

## 3.2 Khởi tạo
Để tạo RDD, trước tiên cần tạo **SparkSession**, đây là một điểm vào ứng dụng PySpark. SparkSession có thể được tạo bằng cách sử dụng một *builder()* hoặc *newSession()* là các phương thức của **SparkSession**.

**SparkSession** tạo ra một biến sparkContext. Có thể tạo nhiều đối tượng SparkSession nhưng chỉ một SparkContext cho mỗi JVM (Java virtual machine). Trong trường hợp nếu bạn muốn tạo một SparkContext mới khác, bạn nên dừng Sparkcontext hiện có (sử dụng  *stop()*) trước khi tạo một cái mới.


```python
spark = SparkSession.builder()
      .master("local[2]")
      .appName("Noname")
      .getOrCreate()
```
### 3.2.1 Sử dụng parallelize()

SparkContext có một số chức năng để sử dụng với RDD.

Ví dụ: phương thức parallelize() của nó được sử dụng để tạo RDD từ một danh sách.

```python
import pyspark
from pyspark import SparkConf, SparkContext
from pyspark.sql import SparkSession
import collections
## Create RDD from parallelize
spark = SparkSession.builder().master("local[2]").appName("Noname").getOrCreate()
dataList = [a,b,c,d,e,f]
rdd = spark.sparkContext.parallelize(dataList)
```

### 3.2.2 Sử dụng textFile()

```python
import pyspark
from pyspark import SparkConf, SparkContext
from pyspark.sql import SparkSession
from google.colab import drive
drive.mount('/content/drive')
import collections
## Create RDD from external Data source
spark = SparkSession.builder.master("local[2]").appName("Noname").getOrCreate()
text_file = spark.sparkContext.textFile("drive/MyDrive/BIGDATA/a.txt")
```
Khi bạn có RDD, bạn có thể thực hiện các hoạt động chuyển đổi và hành động. Bất kỳ hoạt động nào bạn thực hiện trên RDD đều chạy song song.
## 3.3 Các Tranformation và Action với Rdd
### 3.3.1 Tranformation

<p>Qua 1 phương thức transformations thì sẽ cho phép tạo mới 1 RDD từ 1 RDD đã tồn tại. Tất cả các transformation đều là lazy, có nghĩa là các transformation này sẽ không thực hiện tính toán trong phương thức ngay mà chúng sẽ được lưu lại thành dữ liệu cơ bản( ví dụ như file) và chúng chỉ thực hiện tính toán khi 1 action được gọi để yêu cầu trả về kết quả cho driver program. Nhờ thiết kế này mà Spark chạy hiệu quả hơn.</p>

Ví dụ :

<ul>
  <li>Map(func)	trả về 1 RDD mới bằng cách truyền mỗi phần tử đầu vào (nguồn) qua hàm</li>
  <li>Filter(func) trả về 1 RDD mới bằng cách chọn những phần tử đầu vào)mà hàm trả về kết quả true.</li>
  <li>FlatMap(func) tương tự map nhưng khác map ở chỗ, mỗi phần tử đầu vào quaflatMap sẽ trả về 0 hoặc nhiều phần tử đầu ra(có thể hiểu qua map sẽ là 1-1)</li>
  <li>Union(otherDataset)	trả về 1 RDD mới là hợp của tập dữ liệu phần tử đầu vào(nguồn) vàcác phần tử của đối(otherDataset).</li>
  <li>Distinct([numTasks]))	Trả về 1 RDD mới chứa mỗi phần tử là duy nhất của tập dữ liệu nguồn(đầu vào).</li>  
</ul>

### 3.3.2 Action
<p>Qua 1 phương thức actions thì sẽ cho phép trả về 1 giá trị cho driver program sau khi chạy tính toán trên tập dữ liệu.<p>
 
Ví dụ :

<ul>
  <li>Reduce tổng hợp các phần tử của tập dữ liệu sử dụng hàm func(có 2 đối vàtrả về 1 kết quả)</li>
  <li>Count()	trả về số phần tử của tập dữ liệu</li>
  <li>First()	trả về phần tử đầu tiên của tập dữ liệu</li>
  <li>countByKey() chỉ cho RDD có kiểu (K,V). Trả về 1 Map (K,Int). Int là chỉ số key.</li>
  <li>Foreach()	Chạy hàm cho mỗi phần tử của tập dữ liệu..</li>  
</ul>


# 4. DataFrame
## 4.1 Giới thiệu
DataFrame thường đề cập đến một cấu trúc dữ liệu, về bản chất là dạng bảng. Nó đại diện cho các hàng, mỗi hàng bao gồm một số quan sát. Các hàng có thể có nhiều định dạng dữ liệu (không đồng nhất), trong khi một cột có thể có dữ liệu cùng loại (đồng nhất). DataFrames thường chứa một số siêu dữ liệu ngoài dữ liệu; ví dụ, tên cột và hàng. Chúng ta có thể nói rằng DataFrames không là gì, ngoài các cấu trúc dữ liệu 2 chiều, tương tự như bảng SQL hoặc bảng tính.
Lợi ích khi làm việc với DataFrame :
  
-- 1.	Xử lý dữ liệu có cấu trúc và bán cấu trúc: DataFrames được thiết kế để xử lý một tập hợp lớn dữ liệu có cấu trúc cũng như bán cấu trúc . Các quan sát trong Spark DataFrame được tổ chức dưới các cột được đặt tên, giúp Apache Spark hiểu sơ đồ của Dataframe. Điều này giúp Spark tối ưu hóa kế hoạch thực hiện trên các truy vấn này. Nó cũng có thể xử lý petabyte dữ liệu.

-- 2.	API DataFrames thường hỗ trợ các phương thức phức tạp để cắt và xử lý dữ liệu. Nó bao gồm các hoạt động như "chọn" các hàng, cột và ô theo tên hoặc theo số, lọc ra các hàng, v.v. Dữ liệu thống kê thường rất lộn xộn và chứa nhiều giá trị thiếu và không chính xác và vi phạm phạm vi. Vì vậy, một tính năng cực kỳ quan trọng của DataFrames là quản lý rõ ràng dữ liệu bị thiếu.


-- 3.	DataFrames đã hỗ trợ cho một loạt các định dạng và nguồn dữ liệu, chúng ta sẽ xem xét vấn đề này sau trong hướng dẫn Pyspark DataFrames này. Họ có thể lấy dữ liệu từ nhiều nguồn khác nhau.

-- 4.	Hỗ trợ nhiều ngôn ngữ: Nó có hỗ trợ API cho các ngôn ngữ khác nhau như Python, R, Scala, Java, giúp mọi người có nền tảng lập trình khác nhau dễ sử dụng hơn.
## 4.2 Tạo DataFrame từ Pandas
<p>Trong phần này, chúng ta xem cách thực hiện việc này bằng cách sử dụng hàm tạo DataFrame cùng với: </p>

<ul>
  <li>Python dictionaries</li>
  <li>Python lists</li>
  <li>Mảng 2 chiều NumPy</li>
  <li>Files</li>
</ul>
Bạn có thể bắt đầu bằng cách import Pandas cùng với NumPy, mà bạn sẽ sử dụng trong ví dụ sau:

import numpy as np

import pandas as pd


1. Tạo Pandas DataFrame với Dictionaries:
<p align="center">
<img src="1.png" width="500" height="200">
</p>

2.	Tạo Pandas DataFrame với Lists:
<p align="center">
<img src="2.png" width="500" height="200">
</p>

3.	Tạo Pandas DataFrame với NumPy Arrays
<p align="center">
<img src="3.png" width="500" height="230">
</p>

4.	Tạo Pandas DataFrame với từ Files
<p align="center">
<img src="4.png" width="550" height="200">
</p>

## 4.2 Tạo DataFrame từ Pandas PySpark

1.	Tạo DataFrame từ RDD

Một cách dễ dàng để tạo PySpark DataFrame theo cách thủ công là từ RDD hiện có. Đầu tiên, chúng ta hãy tạo một Spark RDD.
```Javascript
   spark=SparkSession.builder.appName('Noname').getOrCreate()
   rdd= spark.SparkContext.parallelize(data)
```
Sử dụng hàm toDF():
```Python
  dfFromRDD1 = rdd.toDF()
```
Sử dụng createDataFrame() từ SparkSession:
```Python
  dfFromRDD2 = spark.createDataFrame(rdd).toDF(*columns)
```
2.	Tạo DataFrame từ List Collection

Thay vì sử dụng Rdd ta sẽ sử dụng list (data)
```Python
  dfFromRDD = spark.createDataFrame(data).toDF(*columns)
```
3.	Tạo DataFrame từ datasource ( file)
```Python
df = spark.read.csv("/src/resources/file.csv")
df = spark.read.text("/src/resources/file.text")
df = spark.read.json("/src/resources/file.json")

```
# 5. Machine Learning
## 5.1 Spark MLlib
MLlib là thư viện học máy có thể mở rộng của Spark bao gồm các thuật toán và tiện ích học tập phổ biến, bao gồm phân loại, hồi quy, phân cụm, lọc cộng tác, giảm kích thước, cũng như các nguyên tắc tối ưu hóa cơ bản, như được nêu bên dưới:
### 5.1.1 Data Types
<ul>
  <li>Local vector</li>
  <li>Labeled point</li>
  <li>Local matrix</li>
  <li>Distributed matrix</li>
 </ul>
  Xem thêm: https://spark.apache.org/docs/1.1.0/mllib-data-types.html
  
### 5.1.2 Basic Statistics
<ul>
  <li>summary statistics</li>
  <li>correlations</li>
  <li>stratified sampling</li>
  <li>hypothesis testing</li>
  <li>random data generation</li>
 </ul>
  Xem thêm: https://spark.apache.org/docs/1.1.0/mllib-statistics.html
  
### 5.1.3 Classification and regression
<ul>
  <li>Linear models</li>
  <li>Decision tree</li>
  <li>Naive Bayes</li>
 </ul>
  Xem thêm: https://spark.apache.org/docs/1.1.0/mllib-classification-regression.html 
  
### 5.1.4 Clustering
<ul>
  <li>K-means</li>

 </ul>
  Xem thêm: https://spark.apache.org/docs/1.1.0/mllib-clustering.html
    
## 5.2 Example

### Ví dụ về Machine Learning áp dụng thuật toán Naive Bayes cho  dataset Irris (thực hiện trên Google Colab)

Tiền xử lý 

<p align="center">
<img src="5.PNG" width="max" height="320">
</p>

Xử lý dữ liệu

<p align="center">
<img src="6.PNG" width="max" height="420">
</p>

Tạo model và predict

<p align="center">
<img src="7.PNG" width="max" height="420">
</p>

Đánh giá

<p align="center">
<img src="8.PNG" width="max" height="180">
</p>




 

