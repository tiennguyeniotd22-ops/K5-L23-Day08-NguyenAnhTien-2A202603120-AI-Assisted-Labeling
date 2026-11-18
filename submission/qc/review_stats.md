# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 237; đối tượng sau duyệt: 369; ngưỡng accepted: IoU >= 0.9
- Đã **loại 15 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 206 | 206/237 = 86.9% | 237 box máy |
| edited (sửa) | 16 | 16/237 = 6.8% | 237 box máy |
| deleted (xoá) | 15 | 15/237 = 6.3% | 237 box máy |
| added (vẽ thêm) | 146 | 146/369 = 39.6% | 369 đối tượng sau duyệt |

Trong số deleted + added, **5 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 141.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 102 | 6 | 3 | 57 |
| bicycle | 3 | 1 | 1 | 3 |
| motorbike | 57 | 7 | 4 | 53 |
| car | 25 | 2 | 0 | 24 |
| bus | 1 | 0 | 1 | 2 |
| truck | 1 | 0 | 1 | 2 |
| three_wheeler | 17 | 0 | 5 | 5 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 45 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 21 | 12 | 24 | 63.6% (KTC 95%: 47–78%) | 46.7% (KTC 95%: 33–61%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 9/11 = 81.8% (KTC 95%: 52–95%) (ngưỡng đạt: 80%). Chỉ 11 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - missing: 2/3
  - extra: 3/3
  - wrong_class: 1/2
  - loose: 3/3
  - lọt: img_009.jpg (wrong_class), img_024.jpg (missing)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
