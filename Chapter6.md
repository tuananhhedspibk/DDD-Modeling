## Chương 6: Thực thi tầng domain

Ở tầng domain ta sẽ thực thi pattern design "chiến thuật", cụ thể là tầng domain sẽ tiến hành biểu thị `domain model` và chia chúng thành 2 loại như sau:
- Domain Object: entity, value-object, domain-event
- Những thứ sử dụng domain object: repository, factory, domain service

> Entity, value-object sẽ biểu thị những "sự vật" của domain model
> Domain-event sẽ biểu thị những "sự việc" của donain model

Sự khác nhau giữa `entity` và `value-object` nằm ở chỗ
- `Entity` sẽ được phân biệt bởi các ID
- `Value-Object` là giá trị dùng để đảm bảo sự phân biệt

### Entity

Ta lấy ví dụ: với ứng dụng quản lí sinh viên, mỗi sinh viên sẽ có một mã số sinh viên (MSSV) riêng và nó tương đương với ID. Khi một sinh viên A được cập nhật thành tích học tập hay địa chỉ thì sih viên A vẫn là sinh viên A. Cũng là một sinh viên A khác (tuy cùng tên nhưng MSSV khác nhau), thì sinh viên này là một thực thể khác biệt so với sinh viên A ở trên.

### Value-object

Lấy ví dụ với 2 tờ tiền 10,000. Hai tờ này được in vào 2 năm là 2022 và 2021. Về mặt giá trị thì chúng là tương đương nhau. Tuy nhiên nếu đưa chúng vào một bộ sưu tập tiền thì do context thay đổi nên dựa vào năm in ta sẽ phân biệt chúng như 2 "thực thể" khác nhau.

Hơn nữa giá trị của 2 tờ tiền này luôn là 10,000 - KHÔNG BAO GIỜ THAY ĐỔI

Điều này đồng nghĩa với việc

> Entity thì có thể thay đổi còn Value-Object thì bất biến

### Domain Service

Dùng khi `việc biểu thị model bằng một object là không thể`. Thông thường sẽ thao tác với một tập các objects.

VD: một ví dụ tiêu biểu đó là việc check xem mail có bị trùng lặp hay không - nói cách khác mail đã được sử dụng cho user trong hệ thống hay chưa. Bản thân một user object có thể biết được mail của mình nhưng không thể biết được thông tin về mail của object khác nên việc tự nó kiểm tra là điều không thể.

Những trường hợp như trên thường sẽ được xử lí bởi `domain service`.

Thế nhưng:

> Hãy cố gắng sử dụng entity và value-object nhiều nhất có thể và hạn chế tối đa việc sử dụng domain-service

Lí do là bởi nếu vô tình viết nhiều `business logic` vào đây thì trong tương lai nó sẽ trở thành một `Fat class` một cách không mong muốn.

### Repository

Repository dùng để lưu thông dữ liệu của một `kết tập` vào DB.

Các dữ liệu của một `kết tập` thường sẽ có tính gắn kết cao.

Một repository sẽ gắn với một `kết tập`, tuy nhiên việc truyền kết tập vào repository hay trả về kết tập từ repository đều phải thông qua `kết tập gốc` - `root aggregate`. Việc tham chiếu đến các object con bên trong aggregate đều phải thông qua `root aggregate` này.

Tuy nhiên không được phép trả về trực tiếp object con từ repository hoặc tạo một repository dùng riêng cho object con

> Repository sẽ được sử dụng như một LIST

Cụ thể như sau: Repository sẽ không chứa LIST các method như `Register User` hay `Suspend User` mà thay vào đó nó sẽ chứa **LIST các users** với các trạng thái như `Registing` hay `Suspending`. Việc làm này giúp cho repository không phải chứa quá nhiều các method liên quan đến business logic.

Hơn nữa cách làm trên cũng giúp ta dễ dàng thực hiện `In memory mock` khi tiến hành test. Do vậy nếu repository có chứa `business logic` thì sẽ không đảm bảo được tính chính xác khi test

### Factory

Được sử dụng để tạo ra object mới khi logic tạo object phức tạp hoặc trong trường hợp cần có sự tham chiếu đến một kết tập khác.

Factory cũng có thể được coi như một loại `domain service`

Do nó biểu thị domain logic nên nó cũng có thể tham chiếu hoặc sử dụng `repository`.

### Các object khác ở tầng domain

Ngoài cách thiết kế `chiến thuật` như đã nói ở trên, nếu đảm bảo `tính liên kết cao` và `tính phụ thuộc thấp` thì các cách thiết kế khác cũng hoàn toàn có thể được sử dụng.

Ví dụ như: `First class collection` hoặc sử dụng `enum` để biểu thị business logic.

> Điều quan trọng nhất với tầng domain đó là: biểu thị được domai logic và viết các object biểu thị các model

Nếu đảm bảo được 2 yếu tố trên thì dù là cách thiết kế hay triển khai nào cũng đều OK cả.
