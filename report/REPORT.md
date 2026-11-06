# Báo cáo Thực hành Ngày 1: Đọc và phân tích kết quả dự đoán YOLO11

Họ và tên học viên: Nguyễn Văn A
Mã học viên: Student_01
Lớp: AI-ML-DL

---

## 1. Phân loại ảnh (Image Classification) với `yolo11n-cls.pt`
- **Ảnh đầu vào**: `traffic.jpg` (COCO Image ID: 210273)
- **Lớp dự đoán có độ tin cậy cao nhất (Rank 1)**:
  - Class ID: `468`
  - Tên lớp (Class Name): `cab`
  - Điểm số tin cậy (Model Score): `0.510915`
- **Nhận xét**: 
  - Mô hình phân loại toàn bộ bức ảnh này thuộc lớp `cab` (xe taxi/cab) với độ tin cậy khoảng 51.1%. Trong ảnh thực tế có rất nhiều xe buýt, xe con và người đi đường, nhưng do nhãn ImageNet-1K tập trung phân loại toàn bộ khung cảnh hoặc vật thể chủ đạo, mô hình chọn ra nhãn đại diện gần nhất là xe taxi.

## 2. Phát hiện vật thể (Object Detection) với `yolo11n.pt`
- **Ảnh đầu vào**: `kitchen.jpg` (COCO Image ID: 397133)
- **Số lượng vật thể phát hiện với các ngưỡng (Threshold) khác nhau**:
  - Ngưỡng `0.20`: 17 vật thể (Bao gồm nhiều muỗng/spoon và chai lọ nhỏ được phát hiện thêm)
  - Ngưỡng `0.35`: 11 vật thể (Lọc bớt các dự đoán có độ tin cậy thấp)
  - Ngưỡng `0.60`: 6 vật thể (Chỉ giữ lại các đối tượng rõ ràng nhất như người và bát lớn)
- **Bảng danh sách vật thể tiêu biểu ở ngưỡng 0.35**:
  - Phát hiện `11` vật thể bao gồm các lớp: cup, bowl, person, oven.
  - Ví dụ tọa độ một Bounding Box của lớp `person`: `[385.33, 69.24, 498.92, 348.92]` với độ rộng `113.58`px và độ cao `279.68`px.
- **Nhận xét về lỗi và trường hợp khó quyết định nhãn**:
  - Có những chiếc thìa treo trên giá bị mô hình bỏ sót hoặc nhận diện sai ở ngưỡng cao.
  - Một số chiếc bát chồng lên nhau gây khó khăn cho việc xác định biên giới chính xác của từng hộp (Bounding Box overlap).

## 3. Phân đoạn đối tượng (Instance Segmentation) với `yolo11n-seg.pt`
- **Ảnh đầu vào**: `kitchen.jpg`
- **Phân tích kết quả**:
  - Tổng số đối tượng phân đoạn được ở ngưỡng `0.35` trong ảnh kitchen là `11` đối tượng.
  - Sự khác biệt giữa Bounding Box và Polygon (Đa giác): Bounding Box chỉ vẽ một hình chữ nhật bao quanh vật thể (chứa nhiều pixel nền thừa), trong khi Polygon (`polygon_xy`) bám sát đường biên thực tế của vật thể, giúp cô lập chính xác hình dáng vật lý của đối tượng.
- **Ứng dụng thực tế**: Định dạng Polygon cực kỳ quan trọng cho các bài toán cần độ chính xác cao về mặt cơ học hoặc không gian như robot gắp vật thể, xe tự lái (tránh chướng ngại vật sát biên) hoặc phân tích y tế (phân đoạn khối u).

## 4. Thiết lập quy trình dán nhãn dữ liệu (Data Annotation Workflow)
Để cải thiện chất lượng nhãn cho các phiên bản sau, chúng tôi đề xuất quy trình phối hợp giữa Annotator và Reviewer:

| Vai trò | Nhiệm vụ chính | Công cụ và Quy tắc |
| --- | --- | --- |
| **Annotator** | Tiến hành vẽ Box/Polygon bám sát đối tượng dựa theo Guideline | Sử dụng CVAT để gán nhãn, báo cáo các ca khó (Escalation) cho Team Lead |
| **Reviewer** | Kiểm tra chất lượng (QA/QC), phê duyệt hoặc từ chối nhãn sai lệch | Xem xét lỗi chồng lấn, thiếu nhãn, gán sai lớp để yêu cầu Rework |

## 5. Quy tắc an toàn dữ liệu và Bảo mật thông tin
- Chỉ sử dụng các tập dữ liệu công khai hoặc dữ liệu đã được cấp phép hợp pháp.
- Tuyệt đối không upload dữ liệu nội bộ của dự án, thông tin cá nhân (PII) nhạy cảm lên các môi trường chia sẻ công cộng.
- Tuân thủ các quy định về an toàn thông tin của tổ chức và bảo vệ quyền sở hữu trí tuệ.
