## Chương 10: Tổng quan về kiến trúc, library

### Xử lí ngoại lệ

Về cơ bản có thể phân chúng thành 2 nhóm như sau:
- Ngoại lệ không lường trước
- Ngoại lệ nằm trong dự tính

**Ngoại lệ không lường trước**

Thường phát sinh khi:
- Tính ràng buộc dữ liệu gặp sự cố (có dữ liệu A thì chắc chắn sẽ có dữ liệu B, nhưng thực tế lại không có)
- Truy cập vào mảng với index không hợp lệ
- ...

Đây là những ngoại lệ sẽ được trả về với mã lỗi `500`.

Những trường hợp này ta sẽ trả về response thể hiện lỗi chung chung. Cách thực thi sẽ là `throw Exception tại usecase` và `bắt nó tại presentation` dưới hình thức là một `ExceptionClass` chung. Tuỳ vào loại client mà cách trả về lỗi cũng khác nhau
- Có thể trả về `error page HTML`
- Có thể trả về `error data dưới format json`

**Ngoại lệ nằm trong dự tính**

Những ngoại lệ này thường do vấn đề từ phía client, những trường hợp này ta sẽ trả về mã lỗi `400`.

Phát sinh khi vi phạm `data validation`. Cần phải chú ý là ngoại lệ có thể phát sinh ở các tầng khác nhau (`DomainException` hoặc `UsecaseException`)

Với **tầng presentation** nếu request parameters vi phạm cấu trúc param đã được định nghĩa và public trước đó của API thì sẽ đưa ra `PresentationException`
Với **tầng domain** nếu vi phạm domain rule thì ta sẽ đưa ra `DomainException`, tuy nhiên nơi `catch exception` lại là tầng usecase nên việc:
- Tiếp tục xử lí
- Kết thúc xử lí và đưa ra `DomainException`
là tuỳ vào xử lí ở tầng usecase
Với **tầng usecase** với những trường hợp có sự khác thường so với **usecase senario** thì ta sẽ đưa ra `UsecaseException`, tuy nhiên do tầng use-case cũng sẽ `catch domain exception` nên việc đưa ra `DomainException` hay `UsecaseException` sẽ gây ra một sự bối rối nhất định. Do đó

> Nếu thấy ngoại lệ này không thuộc về DomainException hay PresentationException thì hãy đưa vào UsecaseException

### Validation bị trùng lặp

Với các tầng domain hay presentation thì đôi khi ta muốn thực hiện những validation giống nhau. Tuy nhiên nếu thực hiện ở tầng domain thì sẽ tránh được trường hợp `có chỗ validation có chỗ lại không được validation` khiến cho tính toàn vẹn của dữ liệu bị vi phạm. Hơn nữa domain logic là thứ cần phải được đảm bảo do đó việc tiến hành validation ở tầng domain nên được ưu tiên hơn cả.

Với trường hợp của presentation layer là khi ta muốn public API như một external API với document ghi đầy đủ cấu trúc và validation của API.
Nếu thực thi validation ở presentation layer thì trong trường hợp nghiệp vụ thay đổi thì có thể dẫn đến **rủi ro validation logic không được cập nhật toàn bộ**.

Tuy nhiên nếu muốn tăng UX cho sản phẩm, ta có thể thực hiện validate ngay tại phía front end bằng việc viết các logic validation ở front end, lúc này nếu validate failed thì sẽ không gửi req đến server nữa. Tuy nhiên cách làm này cũng tiềm ẩn nguy cơ validation logic không được cập nhật toàn bộ.

### Không biểu thị ngoại lệ dưới hình thức kết quả xử lí

Ta thấy rằng:
- Nếu không có ngoại lệ → Success
- Nếu có ngoại lệ       → Failed
lúc này ta có thể trả về kết quả Success hay Failed dưới dạng object.

### Package

Với các xử lí dùng chung thì tuỳ theo từng layer sẽ có sự khác biệt. Ví dụ như cùng là xử lí liên quan đến ngày, nếu là tầng domain thì không cần phải có format đặc trung như là đối với tầng presentation.

### Sự phụ thuộc vào Web Framework

Nếu hệ thống quá phụ thuộc vào framework thì khi tiến hành di chú hoặc làm mới lại hệ thống ta sẽ gặp phải nhiều điều khó khăn, tuy nhiên việc làm cho hệ thống không phụ thuộc hoàn toàn vào framework lại là điều không thể.

Với **tầng domain** ta luôn muốn tầng này không phụ thuộc vào framework. Tuy nhiên với trường hợp của `domain service`, khi tiến hành sử dụng repository nó cần thực hiện DI, nếu so sánh giữa việc tự mình triển khai và việc sử dụng tính năng DI sẵn có của framework thì việc tự mình triển khai sẽ tốn nhiều công sức hơn, nên trường hợp này ta có thể cân nhắc việc cho phép sử dụng DI của framework.

Với **tầng usecase** thì việc phụ thuộc một phần vào framework là điều cần thiết. Ngoài ví dụ về DI như trên, ta còn có thể kể thêm ra ví dụ về sử dụng transaction, mỗi một framework sẽ có cách viết và sử dụng transaction khác nhau, nên việc **làm cho use-case không phụ thuộc vào cách dùng transaction** là điều không thể.
Với logging thì ta có thể tự định nghĩa một `interface logger` và tiến hành implement nó ở tầng infra để tránh việc phụ thuộc vào một thư viện log bất kì.

Với **tầng presentation** thì như đã nói ở chương trước, việc tránh phụ thuộc vào framework là không thể. Trái lại hãy làm cho tầng này đáp ứng đúng những yêu cầu từ phía framework.

### OR mapper

Một ví dụ tiêu biểu là `ActiveRecord`. Đây là các class tương ứng với các bảng trong DB. Việc sử dụng các class này trong tầng domain là điều **không được phép**
