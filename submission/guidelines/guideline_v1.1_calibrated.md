# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603120 - Nguyễn Anh Tiến

> Chỉ ghi phần làm rõ/bổ sung so với v1.0. Các quyết định có ví dụ và mã tương ứng trong `qc/calibration_decisions.csv`.

## 1. Câu hỏi mở đã chốt (từ mục 6 của v1.0)

| # | Câu hỏi | Quyết định | Ảnh ví dụ | `case_id` |
|---|---|---|---|---|
| 1 | Xe đạp trợ lực điện hay xe máy điện nhỏ? | Bicycle nếu vẫn có bàn đạp và cách vận hành chính là đạp; motorbike nếu là xe điện kiểu moped/scooter chạy bằng ga. Nếu không nhìn rõ cấu tạo thì `can_review`. Bộ ảnh không có ví dụ xe điện nhìn đủ rõ; ảnh dẫn chiếu là xe đạp và xe máy thông thường để đối chiếu lớp. | `img_031`, `img_027` | CAL-02 |
| 2 | Cụm xe máy đỗ dày đặc gần camera | Gán từng xe còn nhận ra được thân/đầu xe riêng; dùng `ignore_region` chỉ cho phần không thể tách cá thể, không chỉ vì ảnh đông. | `img_006` | CAL-03 |
| 3 | Hàng rời nhô khỏi xe | Box/mask theo kết cấu xe và bộ phận gắn cố định. Không gán hàng rời thành xe; loại phần hàng rời nhô quá khoảng 20% chiều dài xe khỏi biên xe. Ca không rõ ranh giới thì `can_review`. | `img_003` | CAL-04 |
| 4 | Người ngồi trong xe ba bánh có mui che | Chỉ gán `person` khi phần nhìn thấy đủ nhận dạng và xấp xỉ từ 20% trở lên; không suy diễn phần khuất. Ca sát ngưỡng thì `can_review`. Mask xe không phủ phần người nhìn thấy. | `img_013` | CAL-05 |
| 5 | Kính chắn gió/cửa kính trong mask xe | Kính và khung kính nhìn thấy thuộc xe; người nhìn xuyên qua vẫn là `person` riêng và không tô mask xe lên phần cơ thể người. | `img_013` | CAL-06 |
| 6 | Box chồng lấn trong ảnh đêm | Không gộp các xe thật thành một box. Gán từng xe khi nhận diện được; chỉ dùng `ignore_region` nếu không thể tách cá thể. | `img_024` | CAL-07 |
| 7 | Conf tối thiểu để chấp nhận box máy | Không có ngưỡng conf để bỏ qua kiểm tra. Conf chỉ ưu tiên thứ tự xem; mọi box còn lại vẫn phải kiểm theo checklist. | `img_034` | CAL-08 |

## 2. Ca khó mới (từ lỗi của máy)

| Ảnh | Tình huống | Máy làm gì (model nào) | Quyết định | Quy tắc mới |
|---|---|---|---|---|
| `img_034` | Xe tải bốn bánh, box conf 0.57 | Mô hình dự đoán `three_wheeler`; nhãn đã duyệt là `truck` | Giữ `truck` theo cấu tạo và số bánh nhìn thấy; conf không đổi nhãn | Mục 4.3 v1.0 đã có quy tắc theo số bánh; ghi nhận đây là lỗi model, không thay định nghĩa taxonomy (CAL-01, CAL-08). |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do (ca nào, `case_id`) |
|---|---|---|
| v1.0 | Bản giảng viên | — |
| v1.1 | Làm rõ xe điện, cụm xe đông, hàng rời, người bị che, kính trong suốt, box chồng lấn ban đêm và vai trò của conf. | Các câu hỏi mở CAL-02–CAL-08; lỗi model CAL-01. |
