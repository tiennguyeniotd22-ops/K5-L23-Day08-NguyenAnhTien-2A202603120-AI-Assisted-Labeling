# Báo cáo Day 08 — 2A202603120 - Nguyễn Anh Tiến

> Một file duy nhất cho cả bài (đã gộp recipe, QC và ghi chú cá nhân). Mọi con số kèm **mẫu số** và nguồn (file nào). Viết bằng lời của bạn.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao (dẫn `run_id` trong `model_log.csv`) |
|---|---|---|---|
| person / bicycle / motorbike / car / bus / truck | YOLO26-n | `conf=0.30` mặc định | r02/r03: 0.25 cho nhiều box sát/chồng; 0.5 làm số box giảm 120 xuống 54 trên pilot 8 ảnh và bỏ sót thêm. Sweep trên 4 ảnh gold không đạt recall 90% cho các lớp này; bicycle, bus, truck còn quá ít mẫu để đặt ngưỡng riêng. |
| three_wheeler | YOLOE-s, lớp/prompt `three_wheeler` | `conf=0.30` | r04 và threshold sweep: recall 5/5 = 100%, precision 62% trên gold; dùng làm detector bổ sung. |

- Lệnh gộp (tái dựng từ metadata của `work/prelabel/merged_review/pre.coco.json`; dòng lệnh gốc không được lưu): `python scripts/merge_prelabels.py work/prelabel/yoloe_s_all/pre.coco.json work/prelabel/y26n_all/pre.coco.json --conf 0.3 --thr "three_wheeler=0.3" --out work/prelabel/merged_review/pre.coco.json`. YOLOE được ưu tiên trước YOLO26 khi box chồng lấn.
- Đã thử prompt YOLOE offline: r08 dùng danh sách `tuk-tuk`, `cyclo`, `motorized tricycle` và các biến thể (`146` box/24 ảnh); r09 prompt chung (`118` box/24 ảnh); r10 `cargo tricycle` (`56` box/24 ảnh). Đây là so sánh prompt, không phải file merge cuối.
- **Phiên bản SAM bạn chọn**: SAM 2.1 base (`sam2.1_b.pt`), trọng số có sẵn trên máy và nhận prompt điểm/box cho ca khó.
- Lỗi không sửa được chỉ bằng đổi ngưỡng: phân lớp sai dù box đúng vị trí. Ví dụ `img_013`, model gọi một xe buýt là `three_wheeler` ở conf 0.66; `img_034`, xe tải thành `three_wheeler` ở conf 0.57. Sửa lớp theo cấu tạo/số bánh và kiểm tra bằng mắt; threshold chỉ lọc box, không đổi nhãn.
- Ví dụ điểm tương đối cao nhưng sai: box `img_013`, conf 0.66, đổi từ `three_wheeler` sang `bus` sau review.
- Giới hạn: sweep dùng chính 4 ảnh gold; precision/recall trên gold là in-sample và lạc quan. Bus/truck không có vật gold; bicycle chỉ có 2 vật nên chưa đủ căn cứ chọn threshold riêng.

## 2. AI error profile (từ `qc/review_stats.md`)

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---:|---:|---|
| accepted | 206 | 206/237 = 86.9% | 237 box máy |
| edited | 16 | 16/237 = 6.8% | 237 box máy |
| deleted | 15 | 15/237 = 6.3% | 237 box máy |
| added | 146 | 146/369 = 39.6% | 369 đối tượng sau duyệt |

- Theo số box phải thêm, `person` nhiều nhất (57), sát sau là `motorbike` (53). Trên gold, `car` có recall tối đa thấp nhất trong nhóm đủ mẫu: 50% trên 12 vật. Ảnh đông và vật bị che làm máy bỏ sót; ngưỡng cao hơn sẽ làm recall giảm thêm.
- So với gold 4 ảnh/45 đối tượng: precision 21/33 = 63.6%; recall 21/45 = 46.7%. Khoảng tin cậy 95% lần lượt 47–78% và 33–61% theo `review_stats.md`.

## 3. Thiên kiến tự động hóa và ảnh bẫy

- Bắt được 9/11 = 81.8%; lọt `wrong_class` ở `img_009` và `missing` ở `img_024`. Tỉ lệ theo loại: missing 2/3, extra 3/3, wrong_class 1/2, loose 3/3. Log không ghi checklist step đã bỏ; suy luận từ dạng lỗi là ca `img_009` liên quan bước kiểm lớp, còn `img_024` liên quan quét ảnh tìm vật chưa có box.
- Accepted 206/237 = 86.9% nhưng bắt bẫy chỉ 9/11 và batch còn 146/369 đối tượng added (gồm 5 đổi lớp, khoảng 141 vật bị bỏ sót thật). Giữ nguyên nhiều box máy không chứng minh ảnh đã đủ nhãn; phải quét ảnh trước và sau khi xem box.

## 4. Máy có đáng dùng không?

- Thời gian: gán tay 4 ảnh trong 30 phút = 7.5 phút/ảnh theo người dùng; CVAT ghi 36 phút = 9 phút/ảnh. Review 24 ảnh trong 45 phút = 1.9 phút/ảnh. So theo thời gian người dùng báo, review nhanh hơn khoảng 75%/ảnh, nhưng bộ gold và batch khác độ khó nên đây không phải so sánh hoàn toàn ngang nhau.
- Kết luận: dùng AI làm bản nháp có người kiểm, không tự động chấp nhận. 206/237 box máy được giữ nguyên và tốc độ review cao hơn, nhưng máy chỉ đạt precision 63.6%, recall 46.7% trên gold và người duyệt phải thêm 146/369 đối tượng.
- **Ba quy tắc giảm thiên kiến cho lần sau**:
  1. Ẩn box và quét ảnh ít nhất 15 giây trước khi hiện pre-label; nhẩm số người/xe theo lớp cho từng ảnh.
  2. Kiểm từng box theo thứ tự bỏ sót → thừa → sai lớp → độ ôm sát; không bỏ qua box vì conf cao.
  3. Sau khoảng 10 ảnh nghỉ 3–5 phút; cuối lô kiểm ngẫu nhiên ít nhất 5 ảnh và ghi lỗi theo ảnh/đối tượng.

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM (phiên bản) | Số click | Mask gồm người lái / hàng hoá? | Lỗi quan sát | File trong `masks/` |
|---|---|---|---|---|---|
| img_013 | SAM 2.1 base | 4 | Người lái phần lớn ngoài mask; không có hàng hóa | Rò nhẹ ở chân/ranh giới người và thân xe | `tuktuk_img013.jpg` |
| img_010 | SAM 2.1 base | 13 | Người đạp/khách phần lớn ngoài mask; không có hàng hóa | Điểm âm cải thiện rõ; còn rò nhẹ ở chân và nơi khung xe bị che | `cyclo_img010.jpg` |
| img_023 | SAM 2.1 base | 4 | Rò một phần vùng người cạnh xe; không có hàng hóa | Biên xe và người bị che vẫn lẫn | `occluded_tuk_tuk_img023.jpg` |

- Đối chiếu guideline mục 4.2: bám phần biên nhìn thấy và tách `person` khỏi xe giúp đánh giá ranh giới; guideline chưa chỉ rõ cách xử lý vùng người/xe chồng lấn. Ở img_010, tăng negative clicks từ 6 lên 9 và thêm điểm dương trên khung giúp tách người tốt hơn; vẫn còn rò nhẹ ở vùng chân/khung bị che.
- *(Tuỳ chọn)* Interactor SAM trong CVAT: chưa thử; phần bắt buộc được làm offline bằng `sam2_points.py`.

## 6. Calibration (Part 6)

- Số quyết định: 8 (`calibration_decisions.csv`); quan trọng nhất: `CAL-01`/`CAL-08` — mô hình gán xe tải bốn bánh ở img_034 thành `three_wheeler` dù conf 0.57.
- Quy tắc mới đã vào `guideline_v1.1_calibrated.md`: chốt 7 câu hỏi mở về xe điện, cụm đông, hàng rời, người bị che, kính, box chồng lấn và vai trò của conf; ghi rõ khi cần `can_review`.
- Nguyên nhân gốc: có cả lỗi model (nhầm lớp) lẫn các quy tắc biên còn cần cách áp dụng cụ thể hơn.

## 7. QC và quyết định (Part 6)

- Mẫu vòng cuối: 6 ảnh (1 ảnh `can_review` + 5 ảnh ngẫu nhiên, seed 42), 79 đối tượng; người kiểm: Nguyễn Anh Tiến (**hạn chế**: cùng người đã duyệt batch).
- Lỗi vòng đầu: 2/80 đối tượng = 2.5% · 2/6 ảnh = 33.3%. Sau khi sửa và kiểm lại: 0/79 đối tượng = 0% · 0/6 ảnh = 0% (ngưỡng Release ≤ 2%).
- Lỗi vòng đầu: `thua` — ID 16 ở `img_003`; `sai_vi_tri_kich_thuoc` — ID 105 ở `img_014`. Đã xóa box thừa, thu gọn box người bán hàng trong CVAT, lưu `labels/qc_fixed.json` và build lại release. Vòng hai không ghi nhận lỗi.
- **Giới hạn mẫu nhỏ**: cận trên 95% Wilson là 39% theo `qc_report_auto.md`; 0/6 không chứng minh được tỷ lệ lỗi thật ≤ 2%.
- Script kiểm được lớp/ID/phạm vi box và tính tỉ lệ; không tự kiểm được box ôm sát, vật cùng bị người và máy bỏ sót, hay việc làm theo checklist. Người gán và người QC là một nên vẫn có thiên lệch.

**Quyết định: Release**, ngày 2026-11-18. Vòng hai có 0/6 ảnh lỗi; bắt bẫy 9/11 = 81.8% (trên ngưỡng 80%). Release cuối gồm 24 ảnh, 384 đối tượng (`release_manifest.json`). Kết luận chỉ áp dụng cho nhãn đã kiểm; mẫu QC nhỏ chưa xác nhận được ngưỡng lỗi thật 2%.
Nếu Rework / Hold, việc cần làm tiếp: không còn việc theo vòng QC hiện tại; lưu ý giới hạn mẫu nhỏ khi dùng release.

## 8. Ghi chú cá nhân

- **Dùng AI để làm bài**: YOLO26-n tạo box cho 6 lớp COCO; YOLOE-s gợi ý `three_wheeler`; SAM 2.1 tạo mask thử nghiệm; Codex hỗ trợ giải thích label, chạy script, kiểm tra overlay và cập nhật báo cáo. Nhãn cuối được review trong CVAT; vòng QC kiểm thủ công 6 ảnh và phát hiện/sửa 2 lỗi.
- Gold có nhiễm nhẹ từ AI: trước khi gán 4 ảnh gold đã hỏi AI định nghĩa các label; theo trình tự trao đổi, chưa xem pre-label trước khi gán xong gold và chỉ import pre-label sau đó.
- Máy/CVAT: Windows, chạy model trên CUDA device 0; tên GPU không được ghi trong run log. CVAT cục bộ, Task #6 `batch-review`.
- Không dùng cache cho pre-label cuối: run metadata ghi YOLO26-n và YOLOE-s chạy trên CUDA device 0; cache có sẵn là các file model khác.
- Không ghi nhận lỗi script. Hạn chế gặp phải là SAM còn rò mask quanh người/xe chồng lấn ở img_010 và img_023; thêm negative clicks cải thiện nhưng không tách hoàn toàn.
- Lần sau sẽ hoàn thành gold mù trước khi hỏi AI về nhãn, ghi prompt/command ngay lúc chạy, và nhờ người thứ hai kiểm QC để giảm thiên lệch cùng người gán-review.
