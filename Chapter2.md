## Chương 2: Từ modeling đến implementation

### Sơ đồ usecase

Là sơ đồ mô tả các hành động của hệ thống nhằm đáp ứng yêu cầu từ phía người dùng.
Lấy ví dụ với ứng dụng quản lí task, ta sẽ có sơ đồ usecase như sau:

![File_000](https://user-images.githubusercontent.com/15076665/173172319-c5ecf2c2-a0f4-4295-bc88-869b8436e6fd.png)

`Sơ đồ use-case` sẽ được tạo ra trước `sơ đồ domain model`. Vậy tại sao ta lại cần sơ đồ use-case. Sau đây là lí do:
- Nếu ta không rõ được các use-cases thì việc thiết kế model sẽ trở nên khó khăn. Ở ví dụ trên nếu đứng ở góc độ của người làm việc thì quá trình quản lí task sẽ khác so với góc độ quản lí task của người quản lí (manager)

Ngoài ra ta cũng cần phải quyết định scope của `sơ đồ domain model`, lí dó là vì nếu không quyết định scope của domain model thì có thể dẫn tới tình trạng phạm vi của vấn đề sẽ trở nên quá rộng và quá trình thiết kế domain model sẽ không đem lại hiệu quả gì cả.

Khi bắt đầu ta có thể bắt đầu domain model với scope nhỏ, sau đó ta sẽ dần dần scale scope của nó lên cũng được. Khi đó ta cũng cần phải scale scope của sơ đồ use-case lên để tạo sự đồng bộ.

### Sơ đồ domain model

Về bản chất đây là sơ đồ được đơn giản hoá từ sơ đồ class. Nó sẽ chứa các yếu tố sau:
- Các properties tiêu biểu của object, không nhất thiết phải viết ra các methods
- Ghi rõ các rules cũng như business logic
- Biểu thị tính liên quan giữa các objects
- Định nghĩa tính đa dạng
- Định nghĩa phạm vi của Aggregate
- Ghi ví dụ cụ thể để dễ hình dung

Sau khi thiết kế Aggregate xong ta có thể quyết định cách thiết kế cũng như triển khai các Repositories.

> Bất kể với sơ đồ use-case hay sơ đồ domain model thì khi tiến hành trao đổi ý kiến với các thành viên khác trong team, ta đều nên viết ra white board để mọi người có thể cùng nhau trao đổi ý kiến và hoàn thành các sơ đồ trên.

Với sơ đồ use-case về ứng dụng quản lí task, ta có thể đưa ra sơ đồ domain model như sau:
![File_000 (1)](https://user-images.githubusercontent.com/15076665/173173287-824ffb66-6d00-4e00-9feb-29410ec72fa2.png)

Ta có thể thấy rằng 1 user có thể có 0..n task và cũng có thể thấy được các rules khi khởi tạo hoặc thay đổi user, task.

### Cách ghi chép các rules / business logic

Với các rules hay business logic thì khi ta có ý tưởng hay một sáng kiến nào đó về chúng, hãy viết ra dưới dạng các gạch đầu dòng.

Tiêu biểu ở đây ta có thể viết dưới dạng `sơ đồ chuyển trạng thái`. Trình bày dưới dạng sơ đồ sẽ dễ hiểu hơn so với viết chữ rất nhiều. Với những trường hợp như vậy ta có thể kết hợp giữa `sơ đồ chuyển trạng thái` với `sơ đồ domain model`.

### Cách ghi chép Aggregate

Khi ghi chép để biểu thị các Aggregate (kết tập), ta cần thể hiện được mối liên hệ giữa các objects, do đó việc phân biệt các object ở trong / ngoài kết tập là rất quan trọng do chúng sẽ có cách biểu thị khác nhau.
- Trong cùng một kết tập thì ta sẽ dùng kí hiệu composition để trỏ đến object được tham chiếu
- Khác kết tập thì ta sẽ dùng kí hiệu `association` để trỏ đến object được tham chiếu

**Lưu ý**: 2 kí hiệu `association`, `composition` đều là kí hiệu dùng trong UML. (Ref: https://github.com/tuananhhedspibk/TeckStack/issues/95)
