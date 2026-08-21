# Reflection — Lab 21

*Ngắn gọn, thành thật. Phần này chấm theo độ cụ thể, không theo độ dài.*

**1. Điều gì làm bạn ngạc nhiên nhất?**

Phần câu trả lời chỉ chiếm 39,36% token được render. Nếu dùng chế độ `everything`, toàn bộ
prompt cũng đi vào loss và model có thể học cách lặp lại câu hỏi dù quá trình train vẫn
chạy bình thường.

**2. Bạn mất nhiều thời gian nhất ở đâu? Nó có phải chỗ bạn dự đoán không?**

Tôi mất nhiều thời gian nhất ở việc chuẩn hóa môi trường Windows, cache tokenizer và xác
thực GitHub để lưu đúng artefact. Tôi từng nghĩ phần khó nhất sẽ là code mask, nhưng kiểm
soát môi trường tái lập và quyền truy cập mới là phần gây nhiều gián đoạn hơn.

**3. Trước lab này bạn tin điều gì về fine-tuning mà giờ bạn không còn tin?**

Tôi từng cho rằng train loss giảm là dấu hiệu đủ mạnh để đánh giá fine-tuning thành công.
Sau NB1, tôi không còn tin điều đó: loss vẫn có thể giảm khi mask sai, và chỉ phép so sánh
với baseline được đóng băng trên nhiều nhóm chỉ số mới trả lời được câu hỏi deploy.

**4. Bạn dùng công cụ hỗ trợ vào việc gì trong lab? Chỗ nào cần kiểm tra lại?**

Tôi dùng công cụ hỗ trợ để đọc rubric, thiết lập môi trường CPU, chạy test, thực thi NB1,
đối chiếu artefact và chuẩn bị commit. Quy trình GitHub ban đầu mất nhiều thời gian và
chưa nhấn mạnh đủ sớm rằng NB2–NB5 bắt buộc cần GPU; vì vậy tôi kiểm tra lại bằng
`scripts/verify.py` và chỉ giữ các kết luận có file kết quả hỗ trợ.

**5. Nếu ngày mai phải fine-tune cho một khách hàng thật, bước đầu tiên bạn làm là gì?**

Tôi sẽ định nghĩa tập đánh giá đóng băng và tiêu chí deploy trước khi train: chất lượng
target, regression, định dạng đầu ra và latency. Sau đó tôi mới kiểm tra template/mask,
đo baseline và bắt đầu thử adapter.
