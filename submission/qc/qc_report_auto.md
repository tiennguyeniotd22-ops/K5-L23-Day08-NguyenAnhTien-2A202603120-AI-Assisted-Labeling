# Báo cáo QC tự động (nháp)

> **Gợi ý** do script tính; bạn xác nhận. Script chỉ đếm theo phiếu kiểm bằng mắt người.

## Mẫu
- 6 ảnh (1 ca khó + 5 ngẫu nhiên), 79 đối tượng.
- taxonomy v1.0

## Số đo
| Chỉ số | Giá trị | Mẫu số |
|---|---|---|
| Lỗi **theo đối tượng** | 0/79 = 0.0% | 79 đối tượng trong mẫu |
| **Ảnh có lỗi** (dùng cho ngưỡng) | 0/6 = 0.0% | 6 ảnh trong mẫu |
| Máy: accepted / edited / deleted | 206/237 = 86.9% / 16/237 = 6.8% / 15/237 = 6.3% | 237 box máy |
| Máy: added | 146/369 = 39.6% | 369 đối tượng sau duyệt |
| Bắt lỗi bẫy | 9/11 = 81.8% | 11 bẫy |

## Top 3 loại lỗi
(không ghi nhận lỗi nào)

## Giới hạn thống kê
- 0 ảnh lỗi trong 6 ảnh mẫu: cận trên 95% (Wilson) của tỉ lệ ảnh lỗi thật là **39%**. Ngưỡng 2% không thể được **chứng minh** bằng mẫu nhỏ như vậy: hãy nói rõ giới hạn này trong báo cáo.

## Gợi ý quyết định
**RELEASE**

- ảnh lỗi 0.0% <= 2%, không lỗi hệ thống, bắt bẫy / nhất quán đạt (nếu đã đo)

Cây quyết định: Hold (guideline chưa rõ / dữ liệu hỏng) -> Rework (> ngưỡng, lỗi lặp, bắt bẫy / nhất quán thấp) -> Release.
