# [Vận dụng chuyên sâu] Thiết kế giải pháp Prompting ứng dụng thực tế cho Kỹ thuật Prompting Nâng cao (Logic & Chuỗi)
## 1. Ý tưởng bài toán
Tên giải pháp: Tự động xử lý đơn hàng cho cửa hàng bán lẻ.

Cửa hàng thường nhận đơn hàng qua tin nhắn của khách. Việc nhập thông tin thủ công dễ xảy ra nhầm lẫn. Vì vậy, em sử dụng AI để đọc tin nhắn, kiểm tra sản phẩm và tính tổng tiền đơn hàng.
## 2. Thiết kế Input / Output
**Input:**
- Tin nhắn đặt hàng của khách.
- Danh sách sản phẩm, giá bán và số lượng tồn kho.

**Output:**
- Thông tin khách hàng.
- Danh sách sản phẩm và số lượng.
- Đơn giá và tổng tiền.
- Trạng thái đơn hàng.
## 3. Quy trình xử lý (Prompt Chaining)
Chia quy trình thành 3 bước:
- **Bước 1 – Trích xuất:** Đọc tin nhắn và lấy tên, số điện thoại, địa chỉ, sản phẩm và số lượng.
- **Bước 2 – Kiểm tra:** Đối chiếu sản phẩm, giá bán và tồn kho.
- **Bước 3 – Tính tiền:** Tính tổng tiền và xuất kết quả JSON.

Nếu một bước có lỗi thì dừng lại để kiểm tra, không chuyển dữ liệu sai sang bước tiếp theo.
## 4. Prompt thực thi
```text
Bạn là nhân viên xử lý đơn hàng của một cửa hàng bán lẻ.

Nhiệm vụ:
1. Đọc tin nhắn và trích xuất thông tin khách hàng.
2. Kiểm tra sản phẩm, giá bán và tồn kho theo danh sách được cung cấp.
3. Tính thành tiền từng sản phẩm và tổng tiền đơn hàng.

Yêu cầu:
- Chỉ sử dụng thông tin được cung cấp.
- Không tự ý tạo thêm sản phẩm hoặc giá bán.
- Nếu thiếu thông tin, trả về null.
- Nếu sản phẩm không tồn tại, báo PRODUCT_NOT_FOUND.
- Nếu số lượng vượt tồn kho, báo OUT_OF_STOCK.
- Nếu dữ liệu không hợp lệ, phải dừng xử lý.
- Kiểm tra lại phép tính trước khi trả kết quả.

Áp dụng tư duy logic theo từng bước và sử dụng Prompt Chaining để tách quá trình xử lý.

Chỉ trả về kết quả theo JSON:
{
  "customer": {
    "name": "",
    "phone": "",
    "address": ""
  },
  "items": [],
  "subtotal": 0,
  "status": ""
}

Dữ liệu đơn hàng:
"Chào shop, mình tên Minh, số điện thoại
0912345678, địa chỉ 12 Nguyễn Trãi, Quận 1.
Mình muốn mua 2 áo thun basic và 1 bình nước inox."

Danh sách sản phẩm:
- Áo thun basic: 150000 đồng, tồn kho 5.
- Bình nước inox: 90000 đồng, tồn kho 10.
```
## 5. Kết quả chạy thử
```JSON
{
  "customer": {
    "name": "Minh",
    "phone": "0912345678",
    "address": "12 Nguyễn Trãi, Quận 1"
  },
  "items": [
    {
      "product_name": "Áo thun basic",
      "quantity": 2,
      "unit_price": 150000,
      "total": 300000
    },
    {
      "product_name": "Bình nước inox",
      "quantity": 1,
      "unit_price": 90000,
      "total": 90000
    }
  ],
  "subtotal": 390000,
  "status": "READY_FOR_CONFIRMATION"
}
```
