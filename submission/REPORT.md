# Lab 21 — Evaluation Report

**Họ tên**: Trần Việt Trường  **MSSV**: 2A202601467  **Ngày**: 2026-08-21
**Tier**: `CPU`  **Base model**: `Qwen/Qwen3.5-0.8B`  **GPU thực tế**: `Không có (giai đoạn NB1)`

> Mọi con số dưới đây phải khớp với file trong `results/`. Grader kiểm tra chéo.

---

## 1. Setup

| | |
|---|---|
| Dataset | Corpus mặc định: 250 ticket CSKH → JSON triage |
| Train / val | `225` / `25` (seed 42) |
| `max_length` | `512` — p95 đo được là `98`, gợi ý làm tròn `256`; giữ `512` theo cấu hình tier CPU để nhất quán pipeline *(results/token_stats.json)* |
| `MASK_MODE` | `assistant-only` |
| Epochs / max_steps | Chưa chạy — cần hoàn tất NB3 trên T4 |

**Template có giữ khối `<think>` không?** Có — cả thẻ mở và nội dung reasoning đều còn nguyên sau khi render *(results/template_check.json)*.

---

## 2. Mask proof (NB1)

| | |
|---|---|
| `supervised_fraction` | `0.3936` |
| Câu trả lời nằm trong loss | `true` |
| Câu hỏi KHÔNG nằm trong loss | `true` |

Dán 3–5 dòng đầu của đoạn được tính loss:

```
{"intent": "doi_tra", "urgency": "trung_binh", "product": "balo laptop", "sentiment": "trung_tinh"}<|im_end|>
```

---

## 3. Ba baseline (NB2 — đo TRƯỚC khi train)

| Run | target | regression | format | latency (ms) |
|---|---|---|---|---|
| (a) base + naive prompt | Chưa đo | Chưa đo | Chưa đo | Chưa đo |
| (b) base + optimized prompt | Chưa đo | Chưa đo | Chưa đo | Chưa đo |
| (c) LoRA fine-tune | Chưa train | Chưa train | Chưa train | Chưa train |

**(b) có thật sự mạnh hơn (a) không?** Chưa thể kết luận vì NB2 cần GPU và chưa được chạy.
Tôi chưa sửa `OPTIMIZED_PROMPT`. Prompt tối ưu phải được giữ nguyên trước khi train để
không làm yếu baseline (b) và không tạo lợi thế giả cho mô hình fine-tune. Bảng này chỉ
được cập nhật sau khi có `results/baselines_frozen.json`.

---

## 4. Giải phẫu cấu hình sai (NB4)

| Run | vị trí | r | trainable | LR | train loss (NB4) | **target (NB5 §4)** | s | VRAM GB |
|---|---|---|---|---|---|---|---|---|
| `correct` | text-linear | 16 | Chưa đo | Chưa đo | Chưa đo | Chưa đo | Chưa đo | Chưa đo |
| `attn_only` | q,v | matched (do hàm `matched_rank()` tính) | Chưa đo | Chưa đo | Chưa đo | Chưa đo | Chưa đo | Chưa đo |
| `wrong_lr` | text-linear | 16 | Chưa đo | Chưa đo | Chưa đo | Chưa đo | Chưa đo | Chưa đo |
| `qlora` | text-linear | 16 | Chưa đo | Chưa đo | Chưa đo | Chưa đo | Chưa đo | Chưa đo |

> Xếp hạng bằng cột **target**, không bằng cột train loss — chấm bằng chỉ số thay thế
> chính là Lỗi #3. Nếu hai cột cho hai thứ tự khác nhau, nói thẳng điều đó ở 4.1: đó là
> kết quả đáng giá nhất bạn đo được trong lab này.

Trả lời ba câu (mỗi câu ≥3 câu văn):

**4.1 — `attn_only` có cùng số tham số huấn luyện với `correct`. Trên tập target nó
thắng, thua, hay hoà? Thứ tự đó có giống thứ tự theo train loss không? Điều đó nói gì về
*rank* so với *vị trí gắn adapter*?**

Chưa thể xếp hạng hai run vì NB4 và phần chấm target ở NB5 chưa chạy. Thiết kế đúng phải
dùng `matched_rank()` để sai lệch ngân sách tham số dưới 5%, sau đó so điểm target chứ
không so riêng train loss. Chỉ khi ngân sách đã khớp, chênh lệch còn lại mới cung cấp bằng
chứng hợp lý về tác động của vị trí gắn adapter so với rank.

**4.2 — `wrong_lr` chỉ khác đúng một con số. Đường loss khác nhau ra sao? Nếu chỉ nhìn
loss mà không biết LR, bạn sẽ kết luận sai điều gì?**

Chưa có đường loss để mô tả định lượng. Run này phải giữ nguyên dữ liệu, seed, số step,
batch hiệu dụng, rank và vị trí adapter; biến duy nhất được đổi là learning rate. Nếu chỉ
nhìn final loss, tôi có thể kết luận nhầm rằng kiến trúc LoRA hoặc dữ liệu kém, trong khi
nguyên nhân thật có thể chỉ là learning rate đang ở thang của full fine-tuning.

**4.3 — `qlora` tiết kiệm bao nhiêu VRAM, trả giá bằng gì? Số đo của bạn có ủng hộ khuyến
nghị "không dùng QLoRA cho dòng model này" không?**

Chưa có số VRAM, thời gian và target score nên chưa thể khẳng định. Phép so sánh cần báo
cả lượng VRAM tiết kiệm lẫn chi phí về thời gian, độ ổn định và chất lượng target. Tôi sẽ
chỉ ủng hộ hoặc bác bỏ khuyến nghị sau khi `runs.csv` và `autopsy.json` có đủ số liệu của
hai cấu hình ở cùng số step.

---

## 5. Phán quyết (NB5)

**Kết quả cổng hồi quy**: `CHƯA ĐO`
`target Δ = chưa đo` · `regression Δ = chưa đo` · `valid_trace_rate = chưa đo`

Chưa thể đưa ra verdict PASS hoặc FAIL vì mô hình chưa được train và chưa có
`results/verdict.json`. Cổng đánh giá phải đồng thời xem target, regression, format và
latency. Việc target tăng một mình chưa đủ để deploy nếu regression giảm quá ngưỡng hoặc
đầu ra không còn là JSON hợp lệ. Ngược lại, train loss giảm cũng không chứng minh mô hình
tốt hơn baseline (b), vì loss đo mức khớp dữ liệu huấn luyện chứ không đo trực tiếp chất
lượng trên tập target đóng băng. Vì vậy báo cáo này chủ động giữ trạng thái “chưa đo” thay
vì suy diễn một verdict. Sau khi chạy NB2–NB5 trên cùng seed và cùng bộ eval, tôi sẽ đọc
`verdict.json`, kiểm tra chéo các delta với bảng baseline, rồi mới quyết định có nên dùng
adapter hay tiếp tục dùng base model với optimized prompt.

---

## 6. Định tính — bắt buộc có cả ca THUA

| # | Ticket (rút gọn) | Nhãn đúng | (b) prompt | (c) fine-tune | Nhận xét |
|---|---|---|---|---|---|
| 1 | Chưa sinh | Chưa đo | Chưa đo | Chưa đo | Chờ NB5 |
| 2 | Chưa sinh | Chưa đo | Chưa đo | Chưa đo | Chờ NB5 |
| 3 | Chưa sinh | Chưa đo | Chưa đo | Chưa đo | Chờ NB5 |
| 4 | Chưa sinh | Chưa đo | Chưa đo | Chưa đo | Chờ NB5 |
| 5 | Chưa sinh | Chưa đo | Chưa đo | Chưa đo | Chờ NB5 |

Có mẫu chung nào ở các ca FT thua không? Chưa thể phân tích trước khi có
`results/qualitative.json`; khi chọn mẫu phải giữ ít nhất hai ca fine-tune thua để tránh
cherry-pick.

---

## 7. Kết luận & điều tôi học được

**Kết luận (≥150 từ).** Ở trạng thái hiện tại, tôi không nên deploy vì mới chứng minh được
tính đúng đắn của bước chuẩn bị dữ liệu, chưa chứng minh được hiệu quả của adapter. NB1 cho
thấy mask `assistant-only` chỉ đưa 39,36% token vào loss; phần câu trả lời được giám sát và
phần câu hỏi bị loại đúng như mục tiêu. Chat template của Qwen3.5-0.8B giữ nguyên khối
`<think>`, nên reasoning trace không bị mất âm thầm trước khi tới hàm loss. Corpus có 250
mẫu, được chia cố định thành 225 train và 25 validation bằng seed 42. p95 độ dài là 98
token và giá trị gợi ý là 256; cấu hình 512 hiện rộng hơn cần thiết nhưng được giữ theo tier
CPU để pipeline nhất quán. Những bằng chứng này cho thấy mask và template là hai đòn bẩy
nền tảng: nếu chúng sai thì mọi thử nghiệm LoRA sau đó đều mất ý nghĩa. Tuy nhiên, chưa có
bằng chứng rằng fine-tune thắng base model với optimized prompt, chưa biết regression có
tụt hay không, và chưa có so sánh công bằng giữa vị trí adapter, learning rate và QLoRA.
Do đó kết luận đúng về mặt kỹ thuật là chưa đủ dữ liệu để deploy. Bước tiếp theo bắt buộc
là chạy NB2 trước khi train, rồi NB3–NB5 cùng seed và cùng số step, cuối cùng chỉ chấp nhận
adapter nếu vượt qua cổng target, regression, format và latency.

**Ba điều tôi học được** (cụ thể, không generic):
1. Tôi phải giải mã ngược token được giám sát để chứng minh mask đúng; nhìn loss giảm không thể phát hiện prompt đang bị tính loss.
2. Với corpus này, p95 chỉ 98 token nên `max_length` phải xuất phát từ số đo thay vì chọn theo cảm giác.
3. Baseline optimized prompt phải được đóng băng trước khi train; nếu làm yếu baseline sau khi thấy kết quả thì phép so sánh không còn công bằng.

**Nếu có thêm 2 giờ nữa, tôi sẽ thử:** chạy core NB2–NB5 trên Colab T4 với cấu hình mặc
định, ưu tiên hoàn thành cổng đánh giá trước khi làm NB6 hoặc các mục thưởng.

---

## Phụ lục — thưởng đã làm

- [ ] B1 NB6 merge + hot-swap
- [ ] B2 dataset miền riêng (`data/CUSTOM_DATASET.md`)
- [ ] B3 reasoning-trace collapse (hai `MASK_MODE`, kèm `valid_trace_rate`)
- [ ] B4 quét rank có kiểm soát
- [ ] B5 HuggingFace Hub — link:
