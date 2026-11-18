# Thông tin học viên

- Họ và tên: Nguyễn Anh Tiến
- MSHV: 2A202603120
- Lớp (L2 / L3 / khác): L2
- Tài khoản GitHub: tiennguyeniotd22-ops
- Repo bài nộp (URL): https://github.com/tiennguyeniotd22-ops/K5-L23-Day08-NguyenAnhTien-2A202603120-AI-Assisted-Labeling
- Commit chốt bài (hash): `b7b83b4` (commit dữ liệu bài nộp; commit hiện tại hoàn tất phần tóm tắt học viên).
- Máy: Windows 10 Pro; RAM 16 GB; NVIDIA RTX 3050 Laptop GPU 4 GB; driver 532.10; PyTorch cu118
- CVAT (Docker): v2.74.1; stack hiện đang hoạt động

## Kết quả tóm tắt (điền cuối bài; số lấy từ `submission/qc/*`, kèm mẫu số)

- Máy: accepted / edited / deleted: 206/237 (86.9%) / 16/237 (6.8%) / 15/237 (6.3%); added: 146/369 (39.6%).
- So với gold: precision / recall của **máy** 21/33 = 63.6% / 21/45 = 46.7%.
- Bắt bẫy: 9/11 = 81.8%.
- Thời gian: gán tay 7.5 phút/ảnh (30 phút/4 ảnh); duyệt có pre-label 1.9 phút/ảnh (45 phút/24 ảnh).
- Lỗi QC vòng hai: 0% theo ảnh (0/6), 0% theo đối tượng (0/79); cận trên Wilson 95% theo ảnh là 39%.
- **Quyết định:** Release — vòng hai không ghi nhận lỗi và bắt bẫy vượt ngưỡng 80%; mẫu 6 ảnh còn nhỏ để chứng minh tỷ lệ lỗi thật dưới 2%.
