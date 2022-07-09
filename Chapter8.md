## Chương 8: CQRS

### Vấn đề phát sinh với các xử lí truy vấn dữ liệu

Ngoài việc lưu trữ dữ liệu với đơn vị là các kết tập thông qua các repositories, thì trong thực tế ta cũng cần các xử lí liên quan đến truy vấn dữ liệu, ví dụ như màn hình hiển thị danh sách các users hoặc tasks, ...

Với các màn hình như vậy thì trong trường hợp chúng hiển thị dữ liệu liên quan đến nhiều kết tập khác nhau ví dụ như sau:

![File_000](https://user-images.githubusercontent.com/15076665/177430834-2eb57045-8ede-4b1c-97e5-4cba8f928f07.png)

Lúc này sẽ phát sinh một vài vấn đề như sau:
- Ta sẽ trả về những thuộc tính không cần thiết, từ đó sẽ làm giảm hiệu năng
- Việc tham chiếu giữa nhiều kết tập sẽ dẫn tới việc không thể tiến hành phân trang cho kết quả trả về

### Cách giải quyết

Rất đơn giản, đó là sử dụng CQRS. Về cơ bản CQRS là:

> Kiến trúc phần ra model dùng riêng cho tham chiếu và model dùng riêng cho thay đổi, cập nhật dữ liệu

Ở trong ngữ cảnh của DDD thì nó là model class. Nói đơn giản đó là tạo ra các classes dùng riêng cho tham chiếu cũng như cập nhật dữ liệu

![File_000 (1)](https://user-images.githubusercontent.com/15076665/177655223-a2df7239-b4ae-40b8-a1da-3749a08ee016.png)

Write-Model sẽ sử dụng `domain object` (`entity`, `value-object`)
Read-model sẽ định nghĩa một kiểu model dùng riêng cho use-case liên quan đến nghiệp vụ truy vấn dữ liệu. Ngoài ra cũng cần định nghĩa một service sử dụng model đó

VD:

```TS
class TaskDTO {
  private name: string;
  private deadline: Date;
}
```

Service sử dụng

```TS
interface TaskQueryService {
  public fetchTask(): TaskDTO[];
}
```

Cuốn sách này sẽ sử dụng `DTO` cho tên của model trả về, còn service sử dụng `DTO class` sẽ có tên là `XXXQueryService`

### Layer định nghĩa model dùng cho nghiệp vụ truy vấn

QueryService interface và model dùng cho nghiệp vụ truy vấn sẽ được định nghĩa ở tầng usecase. Còn class implement QueryService interface sẽ nằm ở tầng infra

QueryService ở tầng use-case sẽ `nhận điều kiện A` và `trả về dữ liệu` - mang tính trừu tượng cao (What).

Còn class implement ở tầng infra sẽ cho thấy `cách lấy dữ liệu cụ thể là như thế nào` - How.

![File_000](https://user-images.githubusercontent.com/15076665/178090177-6489e9b4-fb49-42ca-85b4-3da7af8330fd.png)

Với RDB, ở tầng infra khi tiến hành thực hiện việc lấy dữ liệu ra từ DB ta có thể tiến hành `JOIN` các bảng lại với nhau, chỉ `SELECT` những cột cần thiết → tối ưu hoá về mặt hiệu năng

Hơn thế nữa bằng cách thực thi câu query như trên, ta cũng có thể sử dụng các thư viện `query builder` để truyền câu query dưới dạng string vào.

### Ưu, nhược điểm

Đầu tiên là về ưu điểm:
- Trong trường hợp cần truy vấn dữ liệu liên quan đến nhiều kết tập, code của ta sẽ trở nên đơn giản hơn, tính bảo trì cũng cao hơn
- Dễ dàng cải thiện hiệu năng, tuning cho việc lấy dữ liệu
- Có thể tiến hành paging dữ liệu trả về

Tuy nhiên cũng có nhưng nhược điểm:
- Khó truy vết được rằng có tham chiếu đến dữ liệu của domain object hay không
- Kiến trúc sẽ trở nên phức tạp hơn, sẽ mất nhiều thời gian hơn để đọc hiểu

Về nhược điểm đầu tiên, nếu chỉ sử dụng domain object ta có thể sử dụng thông qua getter. Bằng cách này ta có thể nắm được mọi chỗ tham chiếu đến domain object. 
Còn với CQRS thì ta sẽ tạo ra một object tham chiếu riêng, nên việc nắm bắt toàn bộ việc tham chiếu này là khá vất vả.

### Những chú ý khi triển khai

#### Lí do của việc định nghĩa read model ở tầng usecase

Ta lấy ví dụ về ứng dụng quản lí task. Ta thấy rằng ngoài màn hình list toàn bộ tasks ra, ta cũng cần màn hình hiển thị task của từng cá nhân cũng như những task đã hoàn thành gần đây của từng người một.

Rõ ràng là yêu cầu của mỗi màn hình là khác nhau nên kiểu dữ liệu lấy về cũng sẽ khác nhau. Do đó việc định nghĩa ở tầng use-case sẽ có một ích lợi lớn đó là: **phân chia rõ được nhiệm vụ của từng class theo từng use-case cụ thể**

Từ đó giúp tăng khả năng bảo trì cho hệ thống

#### Bảo đảm tính toàn vẹn dữ liệu giữa write model và read model

Đó chính là viết test. Việc viết test cho `read model` là điều bắt buộc, thế nhưng với những xử lí quan trọng có liên quan đến `write model` ta cũng cần viết test cho cả xử lí update.

VD:
Sau khi xác nhận xong thì kết quả phải được phản ánh ở trên màn hình. Với trường hợp này là thấy rằng có 2 xử lí chính:
- Xử lí xác nhận (write)
- Phản ánh lên màn hình (read)

Thì sau khi viết test cho `xử lí write` xong, ta cũng cần phải tiếp tục viết test cho `xử lí read`.

> Dù cho có phân chia model nhưng việc viết test cũng sẽ giúp hệ thống tránh được nhiều bug tiềm tàng

### Những sai lầm, ngộ nhận thường thấy

Đầu tiên đó là **Phân chia data source**

![File_000 (1)](https://user-images.githubusercontent.com/15076665/178091715-4bfc4421-5aa6-4348-a36b-5debdcc0db39.png)

Nhiều người nghĩ rằng `CQRS đồng nghĩa với việc phân chia data source` - đây là một suy nghĩ hoàn toàn sai lầm. Việc phân chia data source chỉ là bước tiếp theo sau khi tiến hành phân chia model mà thôi.

Mục đích chính của việc phân chia data source như trên đó là để cải thiện hiệu năng của hệ thống. Cụ thể như sau:
- Nếu tiến hành tạo `read model` với tư cách như một `replica của write model` sau đo tiến hành `tuning read model` để cải thiện hiệu năng
- Nếu không tạo replica thì ta cũng có thể tạo `material view` dùng cho việc query dữ liệu.

Để cải thiện hiệu năng của `write model` ta cũng có thể ghi dữ liệu vào NoSQL, và khi tiến hành query ta chỉ việc lấy kết quả thống kê từ NoSQL ra là được.

Hiểu lầm tiếp theo đó là **Event Sourcing**

> CQRS = Event sourcing

Đây cũng là một hiểu nhầm khá phổ biến. Đầu tiên chúng ta cần phải hiểu `event sourcing` là gì, `event sourcing` là kiến trúc mà ở đó thay vì lưu trữ nguyên xi toàn bộ dữ liệu có trong domain object vào data store thì ta sẽ **lưu trữ các events** (ví dụ như: `user đã đăng kí thành công` hoặc `task đã được hoàn thành`).

Để có thể truy vấn dữ liệu ngay tức thì, ta cần phải tách xử lí thống kê dữ liệu ra và từ đó dẫn đến việc phân chia `write model` và `read model`.

Do đó

> Event sourcing là kết hợp giữa việc triển khai CQRS và phân chia data source

Event sourcing ngoài việc cải thiện hiệu năng còn giúp:
- Tránh các bug khi tiến hành cập nhận dữ liệu
- Lưu vết những lần lưu trữ dữ liệu

### QA
