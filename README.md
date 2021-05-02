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
## 1.Giới thiệu
<p><b>Resilient Distributed Datasets</b> <i>(RDD)</i> là một cấu trúc dữ liệu cơ bản của Spark. Nó là một tập hợp bất biến phân tán của một đối tượng. Mỗi <i>dataset</i> trong <i>RDD</i> được chia ra thành nhiều phân vùng <i>logical</i>. Có thể được tính toán trên các node khác nhau của một cụm máy chủ <i>(cluster)</i>.</p>

<p><i>RDDs</i> có thể chứa bất kỳ kiểu dữ liệu nào của <i>Python, Java</i> hoặc đối tượng <i>Scala</i>, bao gồm các kiểu dữ liệu do người dùng định nghĩa. Thông thường, <i>RDD</i> chỉ cho phép đọc, phân mục tập hợp của các bản ghi. <i>RDDs</i> có thể được tạo ra qua điều khiển xác định trên dữ liệu trong bộ nhớ hoặc <i>RDDs</i>, <i>RDD</i> là một tập hợp có khả năng chịu lỗi mỗi thành phần có thể được tính toán song song.</p>

## 2.Khởi tạo
Để tạo RDD, trước tiên cần tạo **SparkSession**, đây là một điểm vào ứng dụng PySpark. SparkSession có thể được tạo bằng cách sử dụng một *builder()* hoặc *newSession()* là các phương thức của **SparkSession**.

**SparkSession** tạo ra một biến sparkContext. Có thể tạo nhiều đối tượng SparkSession nhưng chỉ một SparkContext cho mỗi JVM (Java virtual machine). Trong trường hợp nếu bạn muốn tạo một SparkContext mới khác, bạn nên dừng Sparkcontext hiện có (sử dụng  *stop()*) trước khi tạo một cái mới.


```python
spark = SparkSession.builder()
      .master("local[2]")
      .appName("Noname")
      .getOrCreate()
```
### 2.1 Sử dụng parallelize()

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

### 2.1 Sử dụng textFile()

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
## 3.Các Tranformation và Action với Rdd
### 3.1 Tranformation

<p>Qua 1 phương thức transformations thì sẽ cho phép tạo mới 1 RDD từ 1 RDD đã tồn tại. Tất cả các transformation đều là lazy, có nghĩa là các transformation này sẽ không thực hiện tính toán trong phương thức ngay mà chúng sẽ được lưu lại thành dữ liệu cơ bản( ví dụ như file) và chúng chỉ thực hiện tính toán khi 1 action được gọi để yêu cầu trả về kết quả cho driver program. Nhờ thiết kế này mà Spark chạy hiệu quả hơn.</p>

Ví dụ :

<ul>
  <li>Map(func)	trả về 1 RDD mới bằng cách truyền mỗi phần tử đầu vào (nguồn) qua hàm</li>
  <li>Filter(func) trả về 1 RDD mới bằng cách chọn những phần tử đầu vào)mà hàm trả về kết quả true.</li>
  <li>FlatMap(func) tương tự map nhưng khác map ở chỗ, mỗi phần tử đầu vào quaflatMap sẽ trả về 0 hoặc nhiều phần tử đầu ra(có thể hiểu qua map sẽ là 1-1)</li>
  <li>Union(otherDataset)	trả về 1 RDD mới là hợp của tập dữ liệu phần tử đầu vào(nguồn) vàcác phần tử của đối(otherDataset).</li>
  <li>Distinct([numTasks]))	Trả về 1 RDD mới chứa mỗi phần tử là duy nhất của tập dữ liệu nguồn(đầu vào).</li>  
</ul>

### 3.2 Action
<p>Qua 1 phương thức actions thì sẽ cho phép trả về 1 giá trị cho driver program sau khi chạy tính toán trên tập dữ liệu.<p>
 
Ví dụ :

<ul>
  <li>Reduce tổng hợp các phần tử của tập dữ liệu sử dụng hàm func(có 2 đối vàtrả về 1 kết quả)</li>
  <li>Count()	trả về số phần tử của tập dữ liệu</li>
  <li>First()	trả về phần tử đầu tiên của tập dữ liệu</li>
  <li>countByKey() chỉ cho RDD có kiểu (K,V). Trả về 1 Map (K,Int). Int là chỉ số key.</li>
  <li>Foreach()	Chạy hàm cho mỗi phần tử của tập dữ liệu..</li>  
</ul>


# 4.Spark DataFraem
