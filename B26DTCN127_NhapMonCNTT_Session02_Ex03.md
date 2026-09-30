# B26DTCN127 - Nhập Môn Công Nghệ Thông Tin - Session 02 - Bài 3

## Tối ưu hiệu năng hệ thống

### 1. Giải thích tình trạng máy tính

30 tab Chrome có thể tiêu thụ nhiều RAM vì mỗi tab phải giữ trong bộ nhớ các thành phần như DOM, JavaScript, hình ảnh, bộ nhớ đệm và trạng thái phiên. Khi RAM đạt 98%, hệ điều hành không còn nhiều vùng nhớ trống để cấp phát nhanh cho chương trình mới.

CPU phải xử lý việc chạy JavaScript, dựng giao diện và quản lý các tiến trình. Khi thiếu RAM, hệ điều hành phải chuyển một phần dữ liệu ít được dùng sang Page File/Swap trên Storage. Việc này làm phát sinh I/O liên tục, khiến máy giật lag và chuột phản hồi chậm dù %CPU có thể không đạt 100%.

### 2. Sơ đồ IPO của một tab Chrome

```text
INPUT (Nhập)
Người dùng nhập URL / click / gõ dữ liệu
        │
        ├── File Chrome, cache, lịch sử, Page File  ── Storage
        │
        ▼
PROCESS (Xử lý)
CPU đọc lệnh và dữ liệu cần thiết vào RAM
RAM giữ DOM, JavaScript, hình ảnh, trạng thái tab đang hoạt động
        │
        ├── Nếu RAM thiếu: dữ liệu ít dùng bị Swap/Page Out ── Storage
        │
        ▼
OUTPUT (Xuất)
Khung hình trên màn hình, âm thanh, kết quả tìm kiếm, phản hồi mạng
```

Storage lưu dữ liệu lâu dài và dữ liệu hoán trang; RAM lưu dữ liệu đang được CPU sử dụng để truy cập nhanh. CPU không chạy trực tiếp trên dữ liệu đang nằm ở Storage mà phải đọc dữ liệu đó vào RAM trước.

### 3. Ba hành động giải phóng RAM ngay lập tức

1. Đóng các tab Chrome không còn dùng; lưu lại trang cần thiết vào Bookmark hoặc dùng tính năng ngủ/giải phóng tab.
2. Mở Task Manager, sắp xếp theo cột **Memory**, đóng ứng dụng không cần thiết và tắt extension Chrome ngốn nhiều RAM. Chỉ kết thúc tiến trình do người dùng nhận diện được, không tắt tiến trình hệ thống tùy ý.
3. Lưu công việc rồi khởi động lại Chrome hoặc máy tính. Sau đó tắt các ứng dụng nền và chương trình khởi động cùng hệ điều hành nếu không cần.

### 4. Swap/Page File và tác động hiệu năng

Swap (Linux/macOS) hoặc Page File (Windows) là vùng trên Storage được hệ điều hành dùng để tạm lưu những trang bộ nhớ chưa cần dùng ngay. Khi RAM gần đầy, hệ điều hành thực hiện **page out** dữ liệu từ RAM xuống Storage; khi chương trình cần lại, dữ liệu được **page in** trở lại RAM.

Kỹ thuật này giúp hệ thống tránh treo ngay lập tức và cho phép mở nhiều chương trình hơn dung lượng RAM vật lý. Tuy nhiên, Storage chậm hơn RAM rất nhiều. Nếu phải swap liên tục, hệ thống xảy ra hiện tượng **thrashing**: CPU chờ I/O, ứng dụng phản hồi chậm, đèn ổ đĩa hoạt động nhiều và chuột có thể bị lag.

### 5. So sánh HDD và SSD NVMe

| Tiêu chí | HDD truyền thống | SSD NVMe |
|---|---|---|
| Truy cập ngẫu nhiên | Chậm do đầu đọc và đĩa quay cơ học | Nhanh hơn nhiều do bộ nhớ flash và giao tiếp NVMe |
| Khi Swap/Page File hoạt động | Lag rõ, thời gian phản hồi dài, dễ nghe tiếng ổ đĩa | Vẫn chậm hơn RAM nhưng ít lag hơn HDD |
| Kết luận | 30 tab và RAM 98% dễ gây cảm giác treo máy | Cải thiện đáng kể độ phản hồi, nhưng không thay thế việc nâng RAM/giảm tải |

NVMe chỉ làm giảm thời gian đọc/ghi Swap. Nó không biến Storage thành RAM, vì độ trễ và băng thông của RAM vẫn tốt hơn rất nhiều.

### 6. Báo cáo chẩn đoán từ số liệu thực tế

Số liệu được đo trên máy đang chạy báo cáo:

| Thông số | Giá trị đo được | Nhận định |
|---|---:|---|
| CPU | 18,32% sử dụng (81,68% nhàn rỗi) | CPU chưa quá tải tại thời điểm đo |
| RAM | khoảng 96% sử dụng | Áp lực bộ nhớ cao, cần giảm số ứng dụng/tab hoặc nâng RAM |
| Disk | khoảng 22,87 MB/s I/O trong mẫu đo | Có hoạt động I/O; macOS không cung cấp trực tiếp `%Disk` như Windows Task Manager |

Kết luận: nút thắt chính là **RAM**, không phải CPU. Nếu đúng kịch bản RAM 98% và máy dùng HDD, các lần Swap/Page In/Page Out sẽ làm độ trễ tăng mạnh, tạo cảm giác giật lag. Với SSD NVMe, tình trạng sẽ đỡ nghiêm trọng hơn nhưng giải pháp bền vững vẫn là giảm số tab/ứng dụng nền hoặc nâng cấp RAM.

### 7. Chạy chương trình chẩn đoán

```bash
python3 system_diagnosis.py
```

Chương trình in ra các chỉ số đo được, mức cảnh báo và kết luận nút thắt tài nguyên.

