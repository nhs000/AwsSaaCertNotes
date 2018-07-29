## IAM - Identiy and Access Management
* Setup áp dụng cho toàn bộ region
* root account = accout khi đăng nhập bằng email.
  - Có mọi quyền thực thi mọi thứ trên aws
  - Administration Access
* Power user có mọi quyền truy cập vào các resource của aws nhưng ko thể thay
  đổi user và group trong IAM.
  - Power user permission
* Default secret key only showed once when finishing creating a new user.
  - Can create new access-secret key.
* Có thể thêm permission cho user bằng cách add user vào 1 group hoặc thêm trực
  tiếp permission mà ko cần thông qua group.

### Roles
```
IAM roles are a secure way to grant permissions to entities that you trust. Examples of entities include the following:

IAM user in another account
Application code running on an EC2 instance that needs to perform actions on AWS resources
An AWS service that needs to act on resources in your account to provide its features
Users from a corporate directory who use identity federation with SAMLflymd
```
* Ví dụ tạo role cho EC2 có thể call các service khác như Lambda, S3
  - Roles -> Create role -> EC2 -> Select use case -> Choose permission policy
* Khi gán role cho ec2 instance thì instance đấy có thể kết nối đến aws mà không cần phải configure credential. 


## S3 
* Object storage vs block storage (EC2)
  - Key - value storage
    - Key ( object name)
    - Value (sequence of bytes)
    - Version ID (Important for versioning)
    - Metadata (created date, updated date ...)
* File is 0 - 5TB and storaged in bucket (aka folder)
* Unlimited storage
* When write object then you can read it immediately but when update or delete
  object it takes time to update.

### S3 classes
> Setting trong phần properties của object
1. S3 - IA: For data that is accessed less frequently but requires rapid access
   when needed.
2. RRS - Reduced Redundancy Storage: dùng để lưu trữ các data có thể generate ra
   được ? ( Ví dụ thumbnail của ảnh ...)
3. Glacier: Dùng để lưu archieved datas, mấy vài tiếng đến vài ngày để lấy được
   data.

### Charge 
* Storage
* Number of requests
* Storage management pricing ( phí quản lý các loại data theo tag ...)
* Data transfer pricing
#### S3 transfer acceleration 
* Sử dụng các server cloudfront phân tán để tăng tốc tộ upload file của người
  dùng.



### Version controlling 
* Store all versions of objects
* Can't be disabled, only suspended
* Can use MFA mutil-factor authentication when deleting.


### Replication
* Version control must be enabled in both buckets
* Only new or changed files is copied.
* Permission not copied

### Security and encryption
#### Bucket policy
Policy cho toàn bộ object trong bucket

#### Access Control List
Policy cho từng object riêng biệt trong bucket

#### Encryption
1. In transit
  * Mã hoá trong khi truyền dữ liệu sử dụng ssl/tls
2. At rest
  1. Server side
    * SSE-S3
    * SSE-KMS
    * SSE-C
  2. Client side You encrypt your files and upload to S3.


## Cloudfront - CDN

Cache dữ liệu tại 50 edge location trên thế giới giúp giảm độ trễ.

## Storage gateway

## EC2

### Options 
* On Demand: trả tiền theo từng giờ ( windows instance) hoặc từng giây ( linux
  instance)
  - Tự scale vào 1 khoảng thời gian nhất định 
* Reserverd: Đặt trước với capacity reservation, bằng cách ký hợp đồng 1 hoặc 3
  năm.
  - Giảm giá cho từng giờ sử dụng.
* Spot: Cho phép chọn giá mà bạn muốn cho instance capacity -> giúp tiết kiệm
  - Ví dụ một bệnh viện có nhiều dữ liệu nhưng muốn giảm cost và cho thống kê chạy trong khoảng 0am-4am => Đấu giá 
  - Nếu tự terminate EC2 thì sẽ phải trả luôn cả giờ làm tròn (2h30 thì coi như đến 3h)
  - Nhưng nếu EC2 bị terminate do giá cao hơn giá bid thì sẽ chỉ phải trả 2h 
 o  - Spot nghĩa là tập trung 1 điểm nào đó 
  nếu như ứng dụng của bạn có flexible start and end times.
* Dedicated hosted: host được tạo ra để dùng riêng cho bạn.
  - Sử dụng các liencse có sẵn
  - Sử dụng các server có sắn

### Instance types 
|Family|Speciality|Usecase|
|------|----------|--------|
|D2|Dense Storage|FileServer, DataWareHouse, Hadoop|
|R4|Memory Optimized|Memory Extensive Apps|
|M4|General purpose|Application Server|
|C4|Computed Optimized|Cpu extensive app|
|G2|Graphics Intensive|Video encoding, 3d Application streaming|
|I2|High speed storage|NoSQL DB, Datawarehouse|
|F1|Field programable gate array|Hardware acceleration for your code|
|T2|Low cose, general purpose|Webserver, small DBs|
|P2|Graphic, general purpose GPUs|Machine learning, bitcoin mining|
|X1|Memory optimized|Apache SPARK...|
  
### EBS 
* GP2: General Purpose SSD
  - IOPS: 3 -> 10000, burst up to 3000
* Provisioned IOPS SSD
  - Sử dụng cho các ứng dụng sử dụng nhiều IO như large relational database or
    NoSQL database
  - IOPS: > 10000
* Throughtput Optimized HDD (ST1)
  - Big data, data warehouse, log volumn ...
  - Can't use as boot volumn
* Cold HDD
  - Sử dụng cho các workload thỉnh thoảng mới được sử dụng
    - File server
  - Can't use as boot volumn
* Magnetic
  - Volumn giá rẻ nhất mà có thể boot được
  - Sử dụng cho workload thỉnh thoảng được sử dụng
  - Một khi đã tạo thì không thay đổi được volumn
* Không thể encrypt boot volumn của các AMI default nhưng có thể encrypt cho các
  AMI mà mình tạo ra .
#### Thực hành 
* Muốn thay đổi AZ của volumn thì phải tạo snapshot -> tạo volumn mới từ
  snapshot và thay đổi AZ của volumn này.

#### Root volumne : EBS vs instance store
* Instance store - Ephemeral Storage : không thể bị stop. Một khi nó bị stop do
  underlying system (aws system) thì nó sẽ bị mất hết dữ liệu.
* EBS có thể stop mà không mất dữ liệu.
* Cả 2 đều có thể reboot mà ko mất dữ liệu.
* Thông thường cả 2 sẽ mất dữ liệu khi bị terminated nhưng EBS cho phép lựa chọn
  giữ lại dữ liệu trong root volume.
* EBS volume được tạo ra từ EBS snapshot
* Instance volume được tạo ra từ template stored trong S3

### Security groups 
* Có thể add security groups vào 1 ec2 instance.
* Stateful: Khi bạn add 1 inbound rule thì nó sẽ tự động add outbound rule tương
  ứng
* Có thể cài đặt các allow rule nhưng không thể tạo ra các deny rule -> Sử dụng
  network access list trong VPC

### Network and security
#### Key pairs
* Hiển thị các key pair ssh


### ELB - Elastic Load Balancer
* Phải để security group cho các node open thì load balancer mới checkhealth
  đượckj.

#### Application Load Balancer
* LB ở tầng thứ 7, Application Layer.
* Mới xuất hiện
* Hiệu quả tốt hơn Classic Load Balancer
* Có thể thêm target group để chỉ định protocol và port mà muốn tạo load balancing 

#### Classic load balancer 
* Hoặc động ở tầng thứ 4, TCP Layer
* Load balancer protocol: Là giao thức để chuyển balancing cho các node

### EC2 instance meta-data
* SSH vào instance và curl đến đia chỉ `http://196.254.196.254/latest/meta-data/` để lấy meta của instance .


### Bootstrap scrip
* Khi launch 1 instace 
  - Configure detail -> Advanced detail -> User data -> paste bootstrap scrip into here. 
  

### Auto-scaling
* Create auto-scaling group 
  - Tạo autoscaling group template: là template của các instance được tạo thêm để scaling 
    - Nhấn vào sẽ đi đến chỗ cấu hình 1 ec2 instance giống với khi launch 1 instance 
  - Group size: số instance được tạo ra ban đầu
  - Subnet: các AZ được sử dụng để tạo các instace khi auto-scaling 
  - Load balancer: Thêm các instace được tạo ra để scaling vào 1 nhóm load balancer
* Auto-scaling policy 
  - Khi nào thì thêm instance 

### Elastic File System EFS 
* Is a file storage service for EC2. 
* Tự tăng giảm kích thước khi thêm hoặc xoá files. 
* Data được lưu cross trong các AZ của 1 region.
* Block base storage ( S3: object base storage) 
``` 
Giúp việc lưu giữ file ( thuường là server file) được tập trung lại 1 nơi
```
* EFS : nhiều instances cso thể mount vào 1 EFS. EBS: chỉ 1 instance có thể mount vào 1 EBS 

### LAMBDA 
* Là nơi bạn upload code và AWS sẽ lo tất cả về phần server. Chỉ cần tạo event trigger.
  - Phù hợp cho các service event-driven 
  - Xử lí các response từ API gateway hoặc các api call từ AWS SDKs. 
* LAMBDA có thể trigger các lambda khác hoặc các service khác của AWS SDKs. 
* **Mỗi 1 xử lý cho 1 event là 1 instance của LAMDA. Các instances của lambda có thể ở các AZ hoàn toàn khác nhau.** 
* Tự scale -> không cần quan tâm về Elastic Load Balancer ... 
#### Thực hành
* From scratch:
  - Runtime: C#, Python, Java, Go, Nodejs 
 * Một function không thể có thời gian xử lý quá 5p. Nếu quá phải break ra thành nhiều function và cho chúng gọi lẫn nhau. 
 * Sử dụng AWS-XRay để debug Lambda 
 
## API gateway
* Enable CORS on method/resource ??
  - Mục đích là gì?
* API key and usage plans??
* Phải config endpoint cho phía client trùng với endpoint của API gateway

## Lamda
* Change lambda role to role containing "cdaLambdaRole"
  - Execution role?
* Ensure Lambda can post to DynamoDB
* Configure SQS for anyone to post to queue
