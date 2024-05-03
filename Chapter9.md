## Chương 9: Thực thi tầng presentation

### Xử lí của tầng Presentation

Đây sẽ là tầng tương tác ra / vào trực tiếp với client. Do đó ở giữa tầng này và tầng use-case cần phải thực hiện việc convert data

![Screenshot 2024-05-03 at 10 53 43](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/db2a9156-9e78-4ab9-b49b-52e15f4c820d)

Trong ứng dụng sẽ có:
- JSON controller nhận và trả về kết quả dưới format json
- HTML controller nhận dữ liệu submit từ HTML form thông qua format `application/x-www-form-urlencoded`

Tuy nhiên các req đến hoặc response trả về từ tầng use-case không được phép phụ thuộc vào yêu cầu từ phía client mà use-case sẽ chỉ trả về pure object thuần tuý.

Nếu làm như vậy thì tầng presentation sẽ phụ thuộc vào tầng use-case, đảm bảo về tính gắn kết cũng như tính dễ đọc và dễ bảo trì của hệ thống.

### Files và classes liên quan đến tầng prensetation

Thông thường các classes sẽ được định nghĩa dưới dạng `XXXController`. Ngoài ra thì tuỳ theo framework mà routing file cũng sẽ được đặt ở tầng này.

> Tóm lại, mọi sự tương tác vào / ra với client đều phải thông qua tầng presentation

### Định nghĩa cấu trúc của request

Về cấu trúc của req, nó sẽ bao gồm những phần sau:
- Protocol
- Endpoint
- Parameter

Về cơ bản thì cấu trúc của controller phụ thuộc khá nhiều vào framework sử dụng, cho nên nếu hạn chế được sự phụ thuộc này vào các tầng bên trong như `use-case` hay `domain` thì hoàn toàn không vấn đề gì.

### Định nghĩa cấu trúc response

Format của respsone trả về sẽ là mối quan tâm chính của tầng này. Ta lấy ví dụ như sau:

![Screenshot 2024-05-03 at 10 55 40](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/a03481ff-0884-41af-86c4-b9a4eb3f22dd)

Phía client yêu cầu hiển thị `1,000` thay vì `1000` thì nhiệm vụ convert format cho dữ liệu sẽ là nhiệm vụ của tầng presentation.

> Tính kết hợp ở đây chỉ việc nếu không "kết hợp" nhiều modules lại với nhau thì sẽ không thể hoàn thiện được một chức năng

### Có nên sử dụng method tạo value-object ở tầng prensetation hay không ?

Về cơ bản thì domain object chỉ public những generation method đảm bảo được tính ràng buộc về mặt dữ liệu mà thôi. Hơn thế nữa việc tạo domain object (entity, value-object), kết hợp chúng lại để hoàn thiện một usecase của hệ thống là trách nhiệm của tầng usecase. Do đó việc tạo value-object chỉ nên được tiến hành ở tầng usecase.

Tuy nhiên với những trường hợp mà logic sinh một value-object không quá phức tạp ví dụ như tạo một ID, sử dụng ID đó làm tham số của use-case thì nếu như chỉ rõ ràng rằng ID đó là của object nào thì hoàn toàn chấp nhận được.

Nhưng với những trường hợp ngoại lệ như vậy thì cần phải ghi chú rõ ràng, cẩn thận về cách triển khai.
