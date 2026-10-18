# Báo cáo Phân tích Hệ thống & Tài liệu Chuyển giao README.md

---

## PHẦN 1: PHÂN TÍCH HỆ THỐNG SMART-FARM

### 1. Xác định Input / Output (Mô hình IPO)

*   **Input (Đầu vào):** Dữ liệu môi trường thu thập liên tục từ mạng lưới cảm biến: nhiệt độ ($^\circ\text{C}$), độ ẩm đất ($\%$), độ ẩm không khí ($\%$), cường độ ánh sáng (Lux).
*   **Process (Xử lý - SoC/CPU):** Bộ vi điều khiển trung tâm (ESP32/Raspberry Pi SoC) tiếp nhận dữ liệu, giải mã câu lệnh, so sánh với ngưỡng thiết lập tối ưu cho hoa Lan và đưa ra quyết định đóng/ngắt thiết bị.
*   **Output (Đầu ra):** Tín hiệu điều khiển rơ-le (Relay) kích hoạt máy bơm phun sương, hệ thống quạt thông gió, mái che tự động và cập nhật trạng thái lên bảng điều khiển (Dashboard).

### 2. Sơ đồ phối hợp phần cứng (SoC Architecture)

$$\text{Cảm biến (DHT22/Capacitive)} \xrightarrow{\text{GPIO/I2C}} \text{SoC (CPU + RAM + Wi-Fi)} \xrightarrow{\text{Tín hiệu Relay}} \text{Máy bơm / Quạt}$$

---

## PHẦN 2: FILE README.MD CHUYÊN NGHIỆP

```markdown
# 🚀 Hệ thống Quản lý Trang trại Lan Thông minh (Smart-Farm IoT)

*Giải pháp tự động hóa quy trình chăm sóc hoa Lan toàn diện, ứng dụng công nghệ IoT và xử lý dữ liệu thông minh nhằm tối ưu năng suất và tiết kiệm tài nguyên.*

---

## 📌 1. Giới thiệu & Mô tả Hệ thống

Hệ thống **Smart-Farm IoT** được thiết kế riêng cho đặc thù sinh trưởng của vườn Lan. Nhờ tích hợp vi điều khiển dạng **SoC (System on Chip)**, hệ thống có khả năng vận hành độc lập, thu thập dữ liệu thời gian thực và tự động điều khiển tưới tiêu mà không cần sự can thiệp thủ công liên tục từ người nông dân.

![Sơ đồ kiến trúc hệ thống Smart Farm](https://images.unsplash.com/photo-1585320806297-9794b3e4eeae?w=800 "Sơ đồ tổng quan kiến trúc Smart Farm")

---

## 💻 2. Hướng dẫn Cài đặt & Khởi chạy

Chạy các lệnh sau trên Terminal của thiết bị trung tâm (Raspberry Pi hoặc Máy chủ điều khiển):

```bash
# 1. Cập nhật hệ thống và tải mã nguồn dự án
git clone https://github.com/greentech/smart-farm-orchid.git
cd smart-farm-orchid

# 2. Tạo môi trường ảo và cài đặt thư viện phụ thuộc
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# 3. Khởi chạy hệ thống giám sát
python3 main.py --config=config_lan_farm.json
```

---

## ✅ 3. Tính năng cốt lõi

- [x] Giám sát nhiệt độ và độ ẩm không khí thời gian thực $24/7$.
- [x] Tự động kích hoạt hệ thống phun sương khi độ ẩm xuống dưới $60\%$.
- [x] Đẩy cảnh báo qua SMS/Zalo khi nhiệt độ vượt ngưỡng $35^\circ\text{C}$.
- [ ] *Tính năng đang phát triển:* Dự đoán sâu bệnh bằng hình ảnh AI camera.

---

## 📊 4. Bảng Thông số Kỹ thuật & Thiết bị

| Tên thiết bị / Module | Loại linh kiện | Chức năng chính | Thông số vận hành |
| :--- | :--- | :--- | :--- |
| **ESP32 Microcontroller** | SoC Chip | Xử lý trung tâm & Wi-Fi | $3.3\text{V}$, Dual-Core $240\text{MHz}$ |
| **Capacitive v1.2** | Cảm biến độ ẩm đất | Thu thập dữ liệu độ ẩm | Chuẩn giao tiếp Analog |
| **DHT22** | Cảm biến Nhiệt/Ẩm | Thu thập dữ liệu môi trường | Khoảng đo $-40$ đến $80^\circ\text{C}$ ($\pm 0.5^\circ\text{C}$) |
| **Module Relay 4 Kênh** | Bộ chấp hành | Đóng/ngắt máy bơm 220V | Dòng tải tối đa $10\text{A}$ |

---

## 🧠 5. Cơ chế Xử lý Luồng dữ liệu (DIKW & Chu kỳ CPU)

### Mô hình Kim tự tháp DIKW trong Quản lý Tưới Lan
1. **Data (Dữ liệu thô):** Con số $38.5$ nhận từ cảm biến nhiệt độ.
2. **Information (Thông tin):** "Nhiệt độ hiện tại tại vườn Lan là $38.5^\circ\text{C}$ (Vượt ngưỡng an toàn $32^\circ\text{C}$)."
3. **Knowledge (Tri thức):** "Nếu nhiệt độ quá $35^\circ\text{C}$ kéo dài trên 15 phút, lá Lan sẽ bị cháy nắng và thối rễ."
4. **Wisdom (Trí tuệ):** Tự động bật hệ thống quạt thông gió và phun sương làm mát trong 10 phút, đồng thời khép mái che nắng.

### Chu kỳ Fetch–Decode–Execute của CPU khi nhận lệnh tưới
> **1. Fetch (Lấy lệnh):** CPU truy xuất lệnh `CHECK_PUMP_STATUS` từ bộ nhớ RAM.
> 
> **2. Decode (Giải mã):** Bộ giải mã chỉ thị phân tích lệnh, xác định tín hiệu kiểm tra độ ẩm đất đang là $45\%$ ($< 60\%$).
> 
> **3. Execute (Thực thi):** ALU gửi tín hiệu mức HIGH tới chân GPIO 23, kích hoạt Relay bật máy bơm nước tưới Lan.

```

---

## PHẦN 3: KIỂM THỬ HỆ THỐNG & CẬP NHẬT TRƯỜNG HỢP BIẾN CỐ

### Kịch bản Sự cố Kiểm thử (Edge Case)
**Sự cố:** Bộ nhớ RAM bị đầy do lưu trữ nhật ký (log) dữ liệu quá tải sau thời gian dài vận hành, dẫn đến nguy cơ treo hệ thống SoC điều khiển.

### Đoạn cập nhật mục "Lưu ý" trong README.md:

```markdown
## ⚠️ LƯU Ý VẬN HÀNH & XỬ LÝ SỰ CỐ AN TOÀN

### Quản lý Bộ nhớ RAM & Bộ nhớ đệm Log
Để tránh tình trạng hệ thống bị tràn bộ nhớ RAM (Out of Memory - OOM) do log tích tụ:

1. **Xoá bộ nhớ đệm tự động:** Hệ thống đã được cấu hình tự động ghi đè tệp log tròn (Circular Log Buffer) giới hạn ở mức **50MB**.
2. **Xử lý thủ công khi RAM quá tải ($>90\%$):**
   Mở Terminal và thực hiện giải phóng RAM an toàn mà không cần khởi động lại máy bơm:
   ```bash
   # Kiểm tra dung lượng RAM
   free -h
   
   # Truncate tệp log hệ thống về 0 bytes
   sudo cat /dev/null > /var/log/smartfarm_app.log
   
   # Giải phóng PageCache, Dentries và Inodes trong RAM
   sudo sync && echo 3 | sudo tee /proc/sys/vm/drop_caches
   ```
```
