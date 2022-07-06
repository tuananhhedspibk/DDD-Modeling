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