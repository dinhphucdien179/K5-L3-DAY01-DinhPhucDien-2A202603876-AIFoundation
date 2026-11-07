# Báo cáo cá nhân — Day 01: Lọc tin nhắn rác bằng ML, DL, Embedding và LLM

- **MSSV:** 2A202603876
- **Họ và tên:** Đinh Phúc Diên
- **SEED:** 8678

> **Phạm vi số liệu:** Notebook hiện có output cho dữ liệu, ML, DL và Embedding. Phần LLM dừng ở lỗi `NameError: name 'model' is not defined`; các ô tổng hợp, stress test 50 case và thử thách cá nhân chưa chạy. Vì vậy, các mục chưa có kết quả được ghi rõ thay vì suy đoán.

## 1. Bằng chứng

| Tệp | Trạng thái |
| --- | --- |
| [`src/day01_lab.ipynb`](../../src/day01_lab.ipynb) — notebook có output | Có, nhưng mới chạy một phần; Phần 4 gặp lỗi và các phần sau chưa có output |
| `report/individual/lab1_report.json` — báo cáo cuối | Chưa có |
| [`ce6697d67403f45dad12.jpg`](../../ce6697d67403f45dad12.jpg) — ảnh tính tay | Có ở thư mục gốc, hiện thấy phép tính baseline; chưa đủ các phép tính yêu cầu |

Sau khi chạy hoàn tất notebook, cần lưu notebook và `lab1_report.json` vào `report/individual/`, đồng thời hoàn thành ảnh tính tay theo yêu cầu nộp bài.

## 2. Kết quả chính

Sau khi bỏ trùng, dữ liệu có **5.169 tin** (653 spam, 4.516 ham); chia thành train 3.619, validation 775, test 775 và SUB 40. Bảng dưới đây là kết quả test đã in trong notebook. Cột SUB 40 và chi phí không có kết quả được lưu trong notebook hiện tại.

| Phương pháp | F1 test | F1 SUB 40 | p95 (ms/tin) | USD / 1k tin |
| --- | ---: | ---: | ---: | ---: |
| BoW + Logistic Regression | 0.885 | Chưa có | 0.07 | Không dùng API tính phí; chưa tính hạ tầng |
| BoW + MLP | 0.927 | Chưa có | 0.22 | Không dùng API tính phí; chưa tính hạ tầng |
| MiniLM + kNN | 0.863 | Chưa có | 17.83 | Không dùng API tính phí; chưa tính hạ tầng |
| MiniLM + Logistic Regression | **0.942** | Chưa có | 10.13 | Không dùng API tính phí; chưa tính hạ tầng |
| MiniLM + MLP head | 0.931 | Chưa có | 10.48 | Không dùng API tính phí; chưa tính hạ tầng |

Trong các mô hình đã đo, MiniLM + Logistic Regression có F1 test cao nhất. Cả năm phương pháp đều có p95 dưới 50 ms/tin trong phép đo notebook; các phép đo này chưa bao gồm benchmark triển khai thực tế.

## 3. Dự đoán và thực tế

Notebook chưa có `lab1_report.json` và chưa chạy hết các thí nghiệm để xếp hạng chính xác ba dự đoán lệch nhất. Trong các dự đoán có thể đối chiếu với output hiện có:

1. **`ml_f1`:** Tôi dự đoán F1 khoảng 0.85 và precision cao hơn recall. Thực tế F1 test là **0.885**, precision **1.000**, recall **0.793**. Hướng dự đoán đúng, nhưng F1 thấp hơn kết quả 0.035.
2. **`dl_curve`:** Tôi dự đoán validation loss sẽ giảm rồi tăng và F1 MLP xấp xỉ hoặc nhỉnh hơn Logistic Regression. Validation loss thấp nhất ở **epoch 9**; F1 MLP là **0.927**, cao hơn Logistic Regression (**0.885**) 0.042.
3. **`emb_tricky`:** Tôi dự đoán Encoder + Logistic Regression sẽ làm tốt hơn BoW trên các câu ngụy trang. Trong sáu câu được in, BoW đúng **5/6**, Encoder + Logistic Regression đúng **6/6**. Đây là ví dụ 6 câu của thí nghiệm, không phải kết quả stress test 50 case.

## 4. Phân tích 50 case khó

Ô chạy stress test 50 case chưa có output, nên chưa thể kết luận model nào tụt F1 nhiều nhất hoặc nhóm case nào khó nhất. Hai lỗi spam bị phân loại thành ham được in ở ô đọc lỗi ML cho thấy những dạng tin có ngôn ngữ tự nhiên hoặc nội dung thông báo chung có thể thiếu dấu hiệu từ vựng mạnh:

- Tin mời tham gia giải đua xe kèm khuyến mãi Lucozade: có dạng lời rủ rê tự nhiên, không giống mẫu spam quảng cáo giải thưởng điển hình.
- “You have an important customer service announcement from PREMIER.”: thông báo chung, không có từ khóa giải thưởng hay yêu cầu trả phí nổi bật.

Đây là ví dụ lỗi trên tập đánh giá thường trong notebook, **không** phải kết quả của bộ 50 case viết tay. Cần chạy các ô stress test để phân tích theo nhóm và trích đúng hai case từ bộ đó.

## 5. Thử thách cá nhân

Chưa có output cho ô sinh thử thách theo SEED hoặc phần code/kết quả của thử thách. Mục này cần được hoàn thành sau khi chạy notebook; đề bài yêu cầu tự làm, không dùng AI, nên tôi không tự chọn hay giải thay.

## 6. Phương án cho sản phẩm thật

Với yêu cầu p95 < 50 ms/tin và 10 triệu SMS/ngày (trung bình khoảng 116 tin/giây), tôi chọn **MiniLM + Logistic Regression** trong số các phương án đã đo: F1 test **0.942**, p95 **10.13 ms/tin**, throughput **1.334 tin/giây** trong phép đo notebook. Kết quả này đáp ứng ngưỡng độ trễ và throughput trung bình đã nêu; tuy nhiên, cần đo lại tải đỉnh, tài nguyên máy và hiệu năng khi triển khai trước khi cam kết SLA.

Đánh đổi là phải chạy encoder MiniLM trước bộ phân loại, làm hệ thống nặng và phức tạp hơn BoW. Nếu ưu tiên đơn giản và độ trễ thấp hơn, BoW + MLP cũng đạt p95 **0.22 ms/tin** với F1 **0.927**. Cả hai số liệu đều là benchmark notebook, không thay thế kiểm thử production. Các mô hình cục bộ không phát sinh phí API theo tin nhắn; chi phí máy chủ/hạ tầng chưa được tính. Chưa thể ước tính chi phí LLM từ notebook hiện tại vì phần LLM và báo cáo cuối chưa chạy.

## 7. AI log và điều đã học

| Phần | Bạn hỏi AI gì (prompt) | AI trả lời/viết gì | Bạn đã sửa / kiểm chứng gì |
| --- | --- | --- | --- |
| Hoàn thiện báo cáo | Đối chiếu output notebook và các tệp liên quan để điền báo cáo cá nhân. | AI tổng hợp các metric ML/DL/Embedding và soạn phần so sánh, nhận xét; đồng thời chỉ ra các ô chưa chạy. | Các metric được đối chiếu với output trong notebook. Các giá trị SUB 40, LLM, stress test và thử thách chưa có nên được để rõ là chưa có, không tự tạo số liệu. |

Tôi hiểu rõ hơn rằng accuracy/F1 trên tập test cùng phân phối chưa đủ để kết luận mô hình xử lý tốt các tin nhắn ngụy trang; cần kiểm tra lỗi và stress test riêng. Tôi cũng thấy biểu diễn embedding có thể cải thiện F1 trên dữ liệu này, nhưng đi kèm chi phí suy luận cao hơn BoW. Chỉ so sánh các con số có cùng tập đánh giá và điều kiện đo mới có ý nghĩa.
