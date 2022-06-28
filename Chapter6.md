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
