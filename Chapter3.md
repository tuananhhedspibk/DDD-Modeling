## Chương 3: Phương pháp mô hình hoá DDD

### Aggregate (Kết tập) là gì ?

Kết tập là một tập hợp các object có liên quan đến nhau chặt chẽ về mặt dữ liệu mà ta bắt buộc phải đảm bảo điều đó.

### Các quy tắc khi thiết kế và thực thi

Có 2 quy tắc chính ở đây:

① Việc đảm bảo tính thống nhất về mặt dữ liệu phải được đưa vào một kết tập duy nhất

② Bắt buộc chỉ có duy nhất 1 transaction

Với quy tắc ① ta xét ví dụ sau: các CLB ở trường học sẽ có các thành viên tham gia, khi số lượng thành viên đạt tới 5 thì trạng thái của CLB sẽ trở thành "full người" còn nhỏ hơn hoặc 4 sẽ là "còn chỗ".

![File_000](https://user-images.githubusercontent.com/15076665/173837434-e33c0a12-7953-4045-a86a-b17777f2a87e.png)

Lúc này trạng thái của CLB và số lượng thành viên trong CLB sẽ có mối liên hệ chặt chẽ.

Vậy nên `Club` và `Member` sẽ được cho vào cùng một "kết tập" còn `Student` sẽ thuộc về một kết tập khác.

Trong một kết tập, Object cha sẽ được gọi là `Root Aggregate` hoặc `Kết tập nguồn`.

![File_000](https://user-images.githubusercontent.com/15076665/173838944-d55ed06b-84e4-4e50-a894-32fd335d7aed.png)

Với quy tắc thứ ② **ta phải lấy từ repository bằng một đơn vị kết tập và truyền vào repository cũng bằng một đơn vị kết tập**. Mục đích chính ở đây là để đảm bảo toàn vẹn về mặt dữ liệu của kết tập.

Như ở ví dụ trên nếu ta cho phép xoá từng thành viên trong CLB thì trạng thái của CLB sẽ trở thành `NOT_FULL`, điều này làm ảnh hưởng đến toàn vẹn dữ liệu.

Vậy nên mọi thao tác với `Member` đều phải thông qua kết tập `Club`.

### Cách xác định giới hạn của kết tập

Không có một công thức chuẩn nào cho việc xác định giới hạn của kết tập cả.

> Hãy cân nhắc giữa nhiều quan điểm và tìm ra giải pháp cân bằng nhất có thể

Về cơ bản của 2 quan điểm như sau:

**Nhấn mạnh vào tính toàn vẹn của dữ liệu**

Nếu nhấn mạnh vào tính toàn vẹn của dữ liệu thì ta có thể cân nhắc về tính toàn vẹn giữa các kết tập với nhau. Tuy nhiên việc triển khai điều này lại rất khó và tốn kém. Vậy nên việc triển khai tính toàn vẹn **trong một kết tập** hoặc **giữa nhiều kết tập** là phụ thuộc vào người thiết kế

**Phạm vi hợp lí của transaction**

Như đã nói ở trên, ta sẽ lấy về kết tập, update nó chỉ trong phạm vi 1 transaction. Vậy nên nếu phạm vi transaction quá lớn sẽ dẫn đến thời gian DB bị lock sẽ kéo dài.

Với ví dụ về CLB nói trên, nếu `Club` là con của object `School` thì khi ta update một `Club` thì `School` và các `Club` khác sẽ bị lock và không thể update được.

![File_000 (1)](https://user-images.githubusercontent.com/15076665/173846343-2b9a9b50-4cff-48b8-99ba-625a199664eb.png)

### Giới hạn ngữ cảnh

Giới hạn ngữ cảnh có thể hiểu như sau:

> Là việc làm rõ giới hạn mà ta sẽ áp dụng cho một model cụ thể

2 ví dụ tiêu biểu ở đây đó là team, sub-system

Cùng lấy một ví dụ cụ thể hơn đó là việc **Chia sẻ Model**
Giả sử bạn đang build một hệ thống EC với sự tham gia của các bên:
- Dev
- Team bán hàng
- Team vận chuyển
- Team kế toán

Bạn đang thiết kế model `Product`, thế nhưng có một vấn đề đó là mỗi team sẽ có cách hiểu về `Product` khác nhau

![File_000 (2)](https://user-images.githubusercontent.com/15076665/173850043-6c28cb7e-c14a-4e5a-a89b-b7142db1346a.png)

> Do đó khi quy mô của project ngày một lớn thì sẽ rất khó để tạo được một model thống nhất cho toàn bộ hệ thống

Như cuốn sách nổi tiếng của Evans về DDD có viết:

> Việc tạo dựng một model thống nhất cho toàn bộ hệ thống là việc bất khả thi cũng như gây tốn kém nhiều chi phí.

Do đó với DDD hãy **định nghĩa rõ ràng phạm vi áp dụng của model và trong từng phạm vi hãy dùng một model và một ngôn ngữ thống nhất**

Phạm vi được định nghĩa một cách rõ ràng này được gọi là `Giới hạn ngữ cảnh`.

Do đó với ví dụ về hệ thống EC phía trên ta sẽ chia thành 2 ngữ cảnh:
- Ngữ cảnh bán hàng
- Ngữ cảnh vận chuyển

![File_000](https://user-images.githubusercontent.com/15076665/174430292-468518c7-389f-445d-b57c-29df0814108c.png)

Với từng ngữ cảnh riêng ta sẽ sử dụng duy nhất `một model` và `một ngôn ngữ` trong ngữ cảnh đó mà thôi

### Suy nghĩ về DB

Khi thiết kế sơ đồ domain model, ta sẽ dễ dàng bị ảnh hưởng bởi những liên tưởng tới cấu trúc của DB. Để tránh điều này hãy nghĩ rằng bạn đang sử dụng NoSQL và mọi thứ trong hệ thống của bạn là object.

### Kết tập và Transaction

Khi tiến hành thiết kế sơ đồ domain model, hãy tránh những yếu tố kĩ thuật và chỉ tập trung vào việc thiết kế một sơ đồ mang tính trừu tượng cao. Nhưng khi thiết kế kết tập, hãy chú ý tới transaction.
