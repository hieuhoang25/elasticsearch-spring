# Elasticsearch Spring
## 1. Elasticsearch (ES) là gì?
là một công cụ tìm kiếm dựa trên nền tảng Apache Lucene. Nó cung cấp một máy tìm kiếm dạng phân tán, có đâỳ đủ công cụ với một gioa diện web HTTP hỗ trợ dữ liệu JSON
## 2. Chi tiết về Elasticsearch 
- `Elasticsearch` là một search engine
- `Elasticsearch` được kế thừa từ Lucene Apache
- `Elasticsearch` thực chất hoạt đông như 1 webserver, có khả năng tìm kiếm nhanh chóng (near realtime) thông qua giao thức RESTful
- `Elasticsearch` có khả năng phân tích và thống kê dữ liệu
- `Elasticsearch` chạy trên server riêng và đồng thờ giao tiếp thông qua Restful do vậy nên nó không phụ thuộc vào client viết bằng gì hay hệ thông hiện tại của ban viết bằng gif. Nên việc tích hợp nó vào hệ thông là dễ dàng, bạn chỉ gửi request http lên là nó trả về kết quả
- `Elasticsearch` là 1 hệ thống phân tán và có khả năng mở rộng tuyệt vời (horizontal scalability). Lắp thêm node cho nó là nó tự động auto mở rộng
- `Elasticsearch` là 1 open source phát triển bằng Java
## 3. Elasticsearch hoạt động như thế nào?
![Alt text](https://topdev.vn/blog/wp-content/uploads/2020/05/elasticsearch-la-gi.png)
Hoạt động của Elasticsearch đó là 1 server riêng biệt để phục vụ việc tìm kiếm dữ liệu. ES sẽ chạy một cổng (dưới local default 9200). Người ta cũng có thể dùng ES là DB chính nhưng thươngf ko ai làm vì ccái gì cũng có nhiêmj vụ riêng biệt của nó
ES không manhj trong thao tác CRUD, nên thường sẽ dùng song song với 1 DB chính (SQL, NOSQL)
## 4. Tại sao nên dùng Elasticsearch
Tại sao phải dùng ES trong khi tìm kiếm văn bản có thể sử dụng câu lệnh LIKE SQL cũng được?
<br>
Nếu search bằng truy vấn LIKE "%one%" thì kết quả sẽ chỉ cần chứa one là ra: Ví dụ: "phone", "zone", "money", "alone", sẽ ra 1 list kêts quả không mong muốn
<br>
Còn search bằng ES thì gõ  "one" sẽ chỉ có "one" trả về mà thôi. Truy vấn LIKE không truy vấn từ có dấu. Ví dụ: từ khoá có dấu là "có", nếu truy vấn LIKE chỉ gõ "co" thì sẽ không trả về được chính xác kết quả về performance thì ES sẽ là tốt hơn, truy vấn LIKE sẽ tìm kiếm đơn thuần toàn văn bản không sử dụng index, nghĩa là tập trung dữ kiệu càng lơn thì tìm kiếm càng laua, trong khi ES lại "đánh index" cho các trường được chọn để tìm kiếm.
## 5. Các khài niệm cần biết
1. Document trong Elasticsearch
Document là một JSON object với một số dữ liệu. Đây là basic information unit trong ES.
2. Index
[Ref1](https://www.elastic.co/guide/en/elasticsearch/guide/current/inverted-index.html)
[Ref1](https://www.elastic.co/guide/en/elasticsearch/guide/current/relevance-intro.html#relevance-intro)
Index có lẽ là một khái niệm quá quen thuộc đối với các anh em dùng Mysql, Tuy nhiên trong ES hoàn toàn khác trong Mysql.
Trong Elasticsearch, sưr dụng một cấu trúc gọi là inverted index. Nó được thiết kế để cho phép tìm kiếm full-text search. Cách thức của nó khá đơn giảnm, cac văn bản được phân tách thành từng từ có nghĩa sau đó sẽ được mao xme thuoc văn bản nào. Khi search tuỳ thược vào loại search sẽ đưa ra kết quả cụ thể.
Ví dụ: Chúng ta có 2 văn bản cụ thể sau:
```java
1,The quick brown fox jumped over the lazy dog
2,Quick brown foxes leap over lazy dogs in summer
```
Để tạo ra một inverted index, trước hết dhungd ta sẽ phân chia noi dung của từng tài liệu thành các từ riêng biêt(chúng tôi gọi terms) tạo một danh sách được sắp xeoes của tất cả terms duy nhât, sau đó liệt kê tài liệu nào mà mỗi thuật ngữ xuất hiện. Kết quả như sau:

Term      Doc_1  Doc_2
-------------------------
Quick   |       |  X
The     |   X   |
brown   |   X   |  X
dog     |   X   |
dogs    |       |  X
fox     |   X   |
foxes   |       |  X
in      |       |  X
jumped  |   X   |
lazy    |   X   |  X
leap    |       |  X
over    |   X   |  X
quick   |   X   |
summer  |       |  X
the     |   X   |
------------------------

Bây giờ chúng ta muốn tìm kiếm màu quick brown, chúng tả chỉ cần tìm trong các tài liệu trong đó mõi thuật ngữ có xuất hiện hay không kêt quả nh sau:

Term      Doc_1  Doc_2
-------------------------
brown   |   X   |  X
quick   |   X   |
------------------------
Total   |   2   |  1

![Alt text](https://topdev.vn/blog/wp-content/uploads/2020/05/elasticsearch-la-gi1.png)
3. Shard (Mãnh vỡ)
- Shard là đối tượng của Lucene, là tập con các documents của 1 Index. một index có thể chia thành nhiều shard
- Mỗi node bao gồm nhiều shard, chính vì thế Shard mà là đối tượng nhỏ nhât, hoạt động ở mức thấp nhất, đóng vai trò lưu trữ dữ liệu
- CHúng ta gần như không bao giờ làm việc truc tiếp với Shard vì Elasticsearch đã support toàn bộ việc giao tiep cung như tự động thay đổi các Shard khi cần thiết.
- Có 2 laoij Shard là primary shard, và replica shard
3.1 Primary Shard
- Primary Shard là sẽ lưu trữ dữ liệu và đánh index. Sau khi đánh xong dữ liệu sẽ được vận chuyễn tới các Replica Shard
- Mặc định của Elasticsearch là mỗi index sẽ có 5 primary shard vơi mỗi Primary shard thì sẽ đi kèm với 1 Replica Shard
3.2 Replica Shard
-  Replica Shard đúng như cái tên của nó, nó là nơi lưu trưx dữ liệu nhân bản của Primary Shard
-  Replica Shard có vai trò đảm bảo tính toàn vẹn dữ liệu khi Primary Shard xaỷ ra vấn đề.
-  Ngoài ra Repilca Shard có thể giúp tăng cường toc độ tìm kiếm vì chúng ta có thế setup lượng Replica Shard nhiêu hơn mặc định ES
4. Node
- Là trung tâm hoạt động của Elasticsearch. Là nơi lữu trữ dữ liệu, tham gia thực hiện đánh index của cluster cũng như thực hiện các thao tác tìm kiếm
- Mỗi node được định dang bằng 1 unique name
5. Cluster
- Tập hợp các nodes hoạt động cùng với nhau, chia sẽ cùng thuộc tính cluster.name. Chính vì thế Cluster sẽ được xác định bằng 1 'unique name'. Việc định danh các cluster trùng tên sẽ gây nên lỗi cho các node vì vậy khi setup các hết sức chú ý điểm nayf
- Mỗi cluster có một node chính (master), được lựa chọn một cách tự động và có thể thay thế nếu sự cố xẩy ra, Một cluster có thể gồm 1 hoặc nhiều nodes. Các nodes có thể hoạt động cùng trên 1 server.
- Tuy nhiên trong thực tế, một cluster sẽ gồm nhiều nodes hoạt động trên các server khác nhau để đảm bảo nếu có 1 server gặp sự cố thì server khác (node khác) có thể hoạt động đây đủ chức năng so với có khi có 2 servers. Các node có thể tùm thay nhau để hoạt động trên cùng 1 cluster trong giao thức unicast.
***HỨC NĂNG CHÍNH CỦA CLUSTER ĐÓ CHÍNH LÀ QUYẾT ĐỊNH XEM SHARDS NÀO ĐƯỢC PHÂN BỔ CHO NODE NÀO VÀ KHI NÀO THÌ DI CHUYỂN CÁC CLUSTER ĐỂ CÂN BẰNG LẠI CLUSTER***
## 5. Ưu nhược điểm của ES
Ưu điểm:
- Tìm kiếm dữ liệu rất nhanh chóng, mạnh mẽ dựa trên Apache Lucên
- Có khả năng phân tích dữ liệu (Analysis data)
- Khả năng mở rộng theo chiều nganh tuyệt với
- Hỗ trợ tìm kiếm mờ, tưc là từ khoá tìm kiếm có thể bị sai lỗi chính tả hay không đúng cú pháp thì vẫn có khả năng elasticsearch trả về kết quả tốt
- Hỗ trợ Structured Query DSL (Domain-Specific Language), cung cấp việc đăcj tả những câu truy vấn phức tạp một cách cụ thể và rõ ràng bằng JSON
Nhược điêm:
- Elasticsearch được thiết kế cho mục đích search do vậy với những nhiệm vụ khác ngàoi search như CRUD thì elastic kém thế hon so với những đatabase khác như
- Trong elasticsearch không có khái niệm database transaction , tức là nó sẽ không đảm bảo được toàn vẹn dữ liệu trong các hoạt độngInsert, Update, Delete.Tức khi chúng ta thực hiện thay đổi nhiều bản ghi nếu xảy ra lỗi thì sẽ làm cho logic của mình bị sai hay dẫn tới mất mát dữ liệu. Đây cũng là 1 phần khiến elasticsearch không nên là database chính.
- Không thích hợp với những hệ thống thường xuyên cập nhật dữ liệu. Sẽ rất tốn kém cho việc đánh index dữ liệu.
