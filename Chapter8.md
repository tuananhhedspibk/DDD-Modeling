## Chương 8: CQRS

### Vấn đề phát sinh với các xử lí truy vấn dữ liệu

Ngoài việc lưu trữ dữ liệu với đơn vị là các kết tập thông qua các repositories, thì trong thực tế ta cũng cần các xử lí liên quan đến truy vấn dữ liệu, ví dụ như màn hình hiển thị danh sách các users hoặc tasks, ...

Với các màn hình như vậy thì trong trường hợp chúng hiển thị dữ liệu liên quan đến nhiều kết tập khác nhau ví dụ như sau:

