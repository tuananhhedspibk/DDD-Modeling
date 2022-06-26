## Chương 5: Kiến trúc

### Kiến trúc 3 tầng

Dưới đây là kiến trúc được nhiều hệ thống web sử dụng

![File_000](https://user-images.githubusercontent.com/15076665/174479794-17b8bdde-5db4-4a3b-ad4b-c98b7f0f586f.png)

Có một vấn đề đối với kiến trúc này đó là tính liên kết thấp ở tầng `business logic`. Sẽ xuất hiện các classes kiểu như `XXXService` hoặc `XXXLogic` với hàng trăm, hàng ngàn dòng từ đó làm tăng chi phí bảo trì cũng như làm giảm đi tính dễ đọc của code.

Tại sao lại nói tính liên kết thấp, chúng ta hãy xét ví dụ về ứng dụng quản lí task với các chức năng như sau:
- User không thể tăng đăng kí nhiều mail cùng 1 lúc
- User khi mới tạo sẽ có trạng thái là `USING` nhưng cũng có thể chuyển đổi thành `SUSPENDING`
- Task chỉ có thể được gán cho các user có trạng thái là `USING`
- Task chỉ có 2 trạng thái là `COMPLETE` hoặc `DOING`

Ta có sơ đồ use-case như sau:

![File_000 (1)](https://user-images.githubusercontent.com/15076665/174480224-e0b30063-1e91-40a7-92a9-00a83b37e45f.png)

Về domain knowledge thì ta có thể chia thành hai nhóm:
① User
- Không thể tăng đăng kí nhiều mail cùng 1 lúc
- Khi mới tạo sẽ có trạng thái là `USING` nhưng cũng có thể chuyển đổi thành `SUSPENDING`

② Task
- Chỉ có thể được gán cho các user có trạng thái là `USING`
- Chỉ có 2 trạng thái là `COMPLETE` hoặc `DOING`

Về mặt logic thì hai domain knowledge này là khác nhau nên việc viết chúng vào chung một `business logic layer` là một sai lầm dẫn tới tầng này bị quá tải → giảm tính liên kết.

Hơn nữa domain knowledge có 2 phần là: `model` và `table` nên domain knowledge còn bị phân tán xuống dưới tầng `data access` qua đó làm tăng tính ràng buộc giữa các tầng.

### Layerd Architecture

![File_000](https://user-images.githubusercontent.com/15076665/174481079-c053b2dd-a946-477e-b666-3359a17a5d2e.png)

Ở kiến trúc này ta tách `business logic layer` thành 2 layers khác là:
- `Application layer`: thực thi usecase
- `Domain layer`: thực thi domain knowledge

Lúc này tính liên kết giữa các layer đã cao hơn tuy nhiên có một vấn đề đó là tầng domain sẽ phụ thuộc vào tầng infra (phụ thuộc vào việc sử dụng DB hay OR Mapper) → đây là điều cần tránh.

### Onion Architecture

Tương tự như `Layered Architecture` nhưng mối quan hệ phụ thuộc giữa `Domain Layer` và `Infra Layer` là ngược lại.

Chú ý: các mũi tên trắng ở hình dưới thể hiện việc implement các interface.

![File_000 (3)](https://user-images.githubusercontent.com/15076665/174481441-e75fbcb5-99d3-4b61-a42c-e9809b350044.png)

Do việc thực thi các `repository interface` sẽ diễn ra ở `infra layer` nên lúc này `Infra layer` sẽ phụ thuộc vào `Domain layer`.

Với từng tầng ta cần chú ý các điều như sau:

**Domain Layer**:
- Tầng này cần độc lập và không phụ thuộc vào các tầng khác
- Các classes: `Domain Entity`, `Value-Object`, `Domain Event`
- `Repository Interfaces`, `Domain Service`, `Factory` sử dụng các classes nói trên

**Usecase Layer**:
- Sử dụng các public methods của `Domain Entity`, ...
- Không phụ thuộc vào client

**Presentation Layer**:
- Tương tác với client
- Các classes: **Controller**, **External-Controller**

![File_000](https://user-images.githubusercontent.com/15076665/174482099-e8407758-e317-480b-8123-584ffd7c0fc8.png)

Req từ client sẽ tới Adapter (là controller). Tương tự, việc tương tác với DB cũng thông qua Adapter (implementation class của tầng infra).

### Hexagonal Architecture (Port And Adapter Architecture)

Tư tưởng chính ở đây đó là Application sẽ tương tác với thế giới bên ngoài thông qua:
- Adapter
- Port chuyên dụng

![Screen Shot 2022-06-19 at 22 06 20](https://user-images.githubusercontent.com/15076665/174482537-dc017d93-6ff8-4baa-b14a-f9b086bd75a2.png)

Về tư tưởng thì `Hexagonal Architecture` và `Layerd Architecture` hoàn toàn giống nhau do layerd architecture lấy cảm hứng từ hexagonal, nhưng có sự phân hoá tầng rõ rệt ở bên trong Application.

### Clean Architecture

Kiến trúc này là sự tổng hợp và kế thừa từ `Onion Architecture` và `Hexagonal Architecture`.

![1_0u-ekVHFu7Om7Z-VTwFHvg](https://user-images.githubusercontent.com/15076665/175429364-68f2d02f-2956-4278-8ea6-c84ae3377139.png)

Cách đặt tên từng tầng có thể có đôi chút khác biệt:
- Usecase Layer → Application layer
- Adapter Layer → Interface Adapter Layer

Nhưng về cơ bản nó vẫn kế thừa mọi yếu tố từ 2 kiến trúc `Onion Architecture` và `Hexagonal Architecture`.

### So sánh giữa Onion Architecture và Clean Architecture

Về cơ bản hai kiến trúc này đều kế thừa những tư tưởng của `Hexagonal Architecture` tuy nhiên có 2 điểm khác biệt như dưới đây khi ta áp dụng DDD với chúng

**1. Sự đơn giản**

Về cơ bản thì `Onion Architecture` không có nhiều yếu tố bên trong như `Clean Architecture`. Đối với một ứng dụng web thông thường thì ta cũng không cần quá nhiều yếu tố bên trong đến vậy

**2. Tư tưởng về nghiệp vụ của layer là khác nhau**

Xét entity layer của `Clean architecture`. Ở đây có ghi là `Enterprise wide business rules` - tức là nghiệp vụ ở tầng này sẽ được sử dụng cho toàn bộ hệ thống / doanh nghiệp. Nhưng trong DDD ta cần phân chia rõ từng context trong một hệ thống / doanh nghiệp nên việc áp dụng quy tắc trên cho DDD là không hợp lí.

### Thực thi việc phân chia từng context

