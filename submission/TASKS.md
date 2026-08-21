# Nhiệm vụ Lab 21 — Trần Việt Trường (2A202601467)

## Đã hoàn thành trên CPU

- [x] Tạo branch sinh viên `Day21_2A202601467_Tran-Viet-Truong`.
- [x] Chạy toàn bộ unit test: 115 passed, 3 skipped.
- [x] Chạy NB1 với Qwen/Qwen3.5-0.8B.
- [x] Chứng minh mask `assistant-only`: câu trả lời nằm trong loss, câu hỏi bị mask.
- [x] Kiểm tra chat template giữ khối `<think>`.
- [x] Đo độ dài token và split dữ liệu seed 42: train 225, val 25.
- [x] Điền thông tin sinh viên và số liệu NB1 vào `submission/REPORT.md`.

## Cần chạy tiếp trên Colab T4

- [ ] NB2: đóng băng tập eval, đo base + naive prompt và base + optimized prompt trước khi train.
- [ ] NB3: train cấu hình LoRA đúng, lưu `adapters/correct/` và dòng `correct` trong `results/runs.csv`.
- [ ] NB4: chạy `attn_only`, `wrong_lr`, `qlora` cùng số step và khớp ngân sách tham số.
- [ ] NB5: đánh giá target, regression, format, latency; tạo verdict, autopsy và ít nhất 5 ví dụ định tính (có ít nhất 2 ca fine-tune thua).
- [ ] Hoàn thiện các mục 3–7 trong report bằng số liệu thật.
- [ ] Chạy `make verify` trước khi nộp.

## Cách chạy phần còn lại

Mở `colab/Lab21_RUN_ALL.ipynb` trong Colab, chọn GPU T4 và chạy lần lượt các ô. Giữ cấu hình mặc định khi nộp; chỉ dùng `EPOCHS=1` hoặc `EVAL_LIMIT=8` cho lượt thử nhanh.
