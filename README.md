# 1.Spark
# 2.Spark Properties
  Spark Properties kiểm soát hầu hết các tham số ứng dụng và có thể được thiết lập bằng cách sử dụng đối tượng `SparkConf` hoặc thông qua các thuộc tính hệ thống Java.
  
  Spark Properties kiểm soát hầu hết các cài đặt ứng dụng và được cấu hình riêng cho từng ứng dụng. Các thuộc tính này có thể được cài đặt trực tiếp trên `SparkConf` được chuyển tới `SparkContext`. `SparkConf` cho phép bạn định cấu hình một số thuộc tính phổ biến (ví dụ: URL chính và tên ứng dụng), cũng như các cặp khóa-giá trị tùy ý thông qua phương thức `set()` . Ví dụ: 
    - Có thể khởi tạo một ứng dụng với hai luồng như sau: Lưu ý rằng chúng ta chạy với local[2], nghĩa là hai luồng - đại diện cho sự song song “tối thiểu”, có thể giúp phát hiện các lỗi chỉ tồn tại khi chạy trong ngữ cảnh phân tán.   
 
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
### Sử dụng parallelize()

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

### Sử dụng textFile()

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
  <li>Map(func)	trả về 1 RDD mới bằng cách truyền mỗi phần tử đầu vào (nguồn) qua hàm funcr</li>
  <li>spark.serializer.objectStreamReset</li>
  <li>spark.kryoserializer.buffer</li>
  <li>spark.kryo.registrator</li>
  <li>spark.kryo.referenceTracking, ...</li>
</ul>

a


# 4.Spark DataFraem
