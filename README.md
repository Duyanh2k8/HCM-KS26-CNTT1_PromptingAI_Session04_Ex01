# [Vận dụng chuyên sâu] Thiết kế giải pháp Prompting ứng dụng thực tế cho Kỹ thuật Prompting Nâng cao (Logic & Chuỗi)
## 1. Mô tả bài toán
**Tên giải pháp:** Tự động hóa quy trình kiểm tra và xử lý đơn hàng tại cửa hàng bán lẻ.

**Bối cảnh:**

Một cửa hàng mỗi ngày nhận được nhiều đơn hàng từ khách. Nhân viên phải kiểm tra thông tin khách hàng, số lượng sản phẩm, giá tiền và số lượng hàng tồn kho trước khi xác nhận đơn.

Nếu xử lý thủ công thì dễ xảy ra sai sót như nhập thiếu thông tin, tính sai tiền hoặc xác nhận đơn hàng khi sản phẩm không đủ số lượng.

Vì vậy, em đề xuất sử dụng AI kết hợp với Prompt Chaining để tự động kiểm tra và phân loại đơn hàng.
## 2. Bước 1: Thiết kế giải pháp (Input / Output)
### 2.1. Input – Dữ liệu đầu vào
Hệ thống cần cung cấp các thông tin sau:
|Thông tin|	Mô tả|
|---|---|
|Mã đơn hàng	|Mã nhận diện đơn hàng|
|Tên khách hàng	|Người đặt hàng|
|Số điện thoại|	Thông tin liên hệ|
|Sản phẩm|	Tên và mã sản phẩm|
|Số lượng đặt|	Số lượng khách muốn mua|
|Đơn giá|	Giá bán của sản phẩm|
|Tồn kho	|Số lượng sản phẩm hiện có|

### 2.2. Quy trình xử lý
Em chia công việc thành 4 giai đoạn để AI xử lý lần lượt:
Bước 1: Kiểm tra dữ liệu

Kiểm tra thông tin có bị thiếu hoặc sai định dạng không.


Bước 2: Kiểm tra hàng tồn kho

Đối chiếu số lượng khách đặt với số lượng hàng hiện có.


Bước 3: Tính toán đơn hàng

Tính tiền từng sản phẩm và tổng tiền đơn hàng.


Bước 4: Xuất kết quả

Phân loại đơn hàng và đề xuất hướng xử lý.
### 2.3. Output – Kết quả đầu ra
AI phải trả về những thông tin sau:
- Mã đơn hàng.
- Tình trạng thông tin đơn hàng.
- Kết quả kiểm tra tồn kho.
- Tổng tiền đơn hàng (nếu đủ điều kiện tính).
- Trạng thái đơn hàng.
- Lý do nếu đơn hàng không thể xử lý.
- Đề xuất hướng giải quyết.
Định dạng kết quả sử dụng Markdown để nhân viên dễ đọc.
### Áp dụng kiến thức Prompting nâng cao
|Kỹ thuật|	Cách áp dụng|
|---|---|
|Chain-of-Thought	|Chia bài toán thành các bước kiểm tra và xử lý logic, chỉ xuất kết quả và giải thích ngắn gọn thay vì yêu cầu AI tiết lộ suy nghĩ nội bộ.|
|Prompt Chaining|Chia quy trình thành nhiều giai đoạn, kết quả bước trước được dùng làm đầu vào cho bước sau.|
|Error Handling|	Nếu thiếu dữ liệu, sai định dạng hoặc không đủ hàng thì phải thông báo và dừng các bước phụ thuộc vào dữ liệu lỗi.|
## 3. Bước 2: Viết Prompt thực thi
Dưới đây là prompt hoàn chỉnh có thể copy trực tiếp vào ChatGPT để chạy thử.
```
# VAI TRÒ
Bạn là nhân viên quản lý đơn hàng tại một cửa hàng bán lẻ. Nhiệm vụ của bạn là kiểm tra thông tin đơn hàng, đối chiếu tồn kho và tính toán chi phí trước khi đề xuất xử lý.

# MỤC TIÊU
Xử lý đơn hàng theo từng giai đoạn bằng kỹ thuật Prompt Chaining. Không được bỏ qua bước kiểm tra dữ liệu đầu vào.

# QUY TRÌNH XỬ LÝ

**Bước 1: Kiểm tra dữ liệu đầu vào**
- Kiểm tra mã đơn hàng, tên khách hàng và số điện thoại.
- Kiểm tra tên sản phẩm, số lượng đặt, đơn giá và số lượng tồn kho.
- Nếu thiếu thông tin hoặc dữ liệu không hợp lệ, ghi rõ lỗi.
- Không tự ý đoán hoặc tạo thêm dữ liệu.

**Bước 2: Kiểm tra tồn kho**
- So sánh số lượng khách đặt với số lượng tồn kho.
- Nếu tồn kho lớn hơn hoặc bằng số lượng đặt thì sản phẩm đủ điều kiện.
- Nếu tồn kho ít hơn số lượng đặt thì thông báo không đủ hàng.
- Nếu không có dữ liệu tồn kho thì đánh dấu chưa xác định.

**Bước 3: Tính toán**
- Tính thành tiền từng sản phẩm = Số lượng đặt × Đơn giá.
- Tính tổng tiền đơn hàng bằng cách cộng thành tiền các sản phẩm.
- Chỉ xác nhận tổng tiền khi tất cả sản phẩm đều đủ điều kiện và dữ liệu hợp lệ.
- Không tự ý thay đổi giá bán hoặc số lượng.

**Bước 4: Đưa ra kết quả**
Phân loại đơn hàng theo 3 trạng thái:
- READY: Thông tin hợp lệ và tất cả sản phẩm đủ hàng.
- HOLD: Thiếu thông tin, dữ liệu không hợp lệ hoặc có sản phẩm chưa đủ hàng.
- REJECT: Đơn hàng có dữ liệu sai nghiêm trọng hoặc không thể xử lý theo quy định.

# QUY TẮC XỬ LÝ LỖI
1. Nếu thiếu số điện thoại hoặc tên khách hàng thì chuyển HOLD.
2. Nếu số lượng đặt nhỏ hơn hoặc bằng 0 thì báo lỗi và chuyển HOLD.
3. Nếu đơn giá hoặc tồn kho bị thiếu thì không được tự suy đoán.
4. Nếu có ít nhất một sản phẩm không đủ hàng thì không xác nhận toàn bộ đơn hàng.
5. Khi gặp lỗi ở bước nào, phải ghi nhận lỗi và không thực hiện những bước phụ thuộc vào dữ liệu chưa hợp lệ.
6. Không được tự ý xác nhận đơn hàng khi chưa kiểm tra xong.

# CÁCH TRẢ KẾT QUẢ
Trình bày kết quả theo cấu trúc:

## 1. Thông tin đơn hàng
## 2. Kết quả kiểm tra dữ liệu
## 3. Kết quả kiểm tra tồn kho
## 4. Kết quả tính toán
## 5. Trạng thái đơn hàng
## 6. Đề xuất xử lý

Mỗi lỗi cần ghi rõ nguyên nhân và cách khắc phục. Chỉ đưa ra phần giải thích ngắn gọn, không cần trình bày suy nghĩ nội bộ.

# DỮ LIỆU ĐẦU VÀO
[Nhập thông tin đơn hàng tại đây]
```
## 4. Chạy thử Prompt
Để kiểm tra khả năng xử lý lỗi, em sử dụng một đơn hàng giả lập có cả sản phẩm đủ hàng và sản phẩm không đủ hàng.
5.1. Dữ liệu đầu vào chạy thử
Đơn hàng DH001
- Khách hàng: Nguyễn Văn A
- Số điện thoại: 0901234567
Sản phẩm	SL đặt	Đơn giá	Tồn kho
Chuột Logitech	2	250.000đ	10
Bàn phím cơ	3	750.000đ	2

Đây là dữ liệu giả lập phục vụ bài tập.

5.2. Kết quả AI phản hồi
Dưới đây là kết quả mẫu khi đưa dữ liệu trên vào prompt:
1. Thông tin đơn hàng
- Mã đơn: DH001
- Khách hàng: Nguyễn Văn A
- Số điện thoại: 0901234567

2. Kết quả kiểm tra dữ liệu
Hợp lệ
Các thông tin cần thiết đã được cung cấp đầy đủ.

3. Kết quả kiểm tra tồn kho
Sản phẩm	Kết quả
Chuột Logitech	Đủ hàng
Bàn phím cơ	Thiếu 1 sản phẩm


4. Kết quả tính toán
- Chuột Logitech: 2 × 250.000 = 500.000đ
- Bàn phím cơ: 3 × 750.000 = 2.250.000đ
Tổng tiền dự kiến: 2.750.000đ

Đây là tổng tiền dự kiến nếu tất cả sản phẩm đều có đủ hàng, chưa phải giá trị đơn hàng được xác nhận.

5. Trạng thái đơn hàng
HOLD
Đơn hàng chưa thể xác nhận vì sản phẩm bàn phím cơ chỉ còn 2 sản phẩm trong kho, trong khi khách đặt 3 sản phẩm.

6. Đề xuất xử lý
- Kiểm tra lại số lượng bàn phím cơ trong kho.
- Liên hệ khách hàng để thông báo tình trạng sản phẩm.
- Chờ xác nhận trước khi tiếp tục xử lý đơn hàng.
