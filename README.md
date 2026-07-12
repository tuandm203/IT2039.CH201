# IT2039.CH201

 "HyperFusion" – Mạng Siêu liên kết (Hypernetworks) điều hướng bằng Lâm sàng Thay vì để 2 nhánh chạy độc lập rồi mới gộp, hãy để dữ liệu máu "dạy" mô hình cách nhìn hình ảnh.Vấn đề: Bác sĩ ngoài đời không nhìn ảnh siêu âm một cách "mù mịt". Nếu họ biết bệnh nhân có nồng độ $Lp(a)$ và $LDL\text{-}C$ cao đột biến, họ sẽ lập tức săm soi rất kỹ vào các vùng dễ có mảng bám hồi âm cao (high echogenicity) trên siêu âm.Giải pháp (Đề tài của bạn): Sử dụng Hypernetworks hoặc Cross-Modal Attention.Bạn dùng dữ liệu dạng bảng (Tabular data) đưa qua một mạng MLP nhỏ. Đầu ra của mạng MLP này không phải là dự đoán rủi ro, mà nó sinh ra các trọng số (weights) để tinh chỉnh (modulate) trực tiếp vào các lớp tích chập (Convolutional layers) của nhánh CNN đang xử lý ảnh siêu âm.Tên đề tài gợi ý: Mô hình Học sâu Đa phương thức dựa trên Kiến trúc Hypernetwork: Điều hướng Đặc trưng Hình ảnh Siêu âm bằng Thông số Lipid trong Đánh giá Xơ vữa Động mạch.


 Cách 1: Tách thành nhiều dòng (Data Augmentation bằng cách lấy mẫu)
Bạn có thể tách mỗi bệnh nhân có nhiều ảnh thành nhiều dòng dữ liệu riêng biệt.

Cách làm: Nếu bệnh nhân P003 có 5 ảnh, bạn tạo 5 dòng dữ liệu. Mỗi dòng sẽ có Patient_ID, các chỉ số lâm sàng giống nhau, nhưng Associated_Image là một ảnh khác nhau.

Ưu điểm: Tăng đáng kể số lượng mẫu cho mô hình huấn luyện, giúp mô hình thấy được nhiều góc độ của mảng bám.

Nhược điểm: Khi đánh giá (Test), bạn cần một chiến lược "bỏ phiếu" (voting). Ví dụ: Nếu 5 ảnh của bệnh nhân P003 mà có 3 ảnh được AI đoán là "Có mảng bám", thì bạn kết luận bệnh nhân đó "Có mảng bám".

Khi nào dùng: Nếu dữ liệu của bạn quá ít và mô hình đang bị Underfitting.

Cách 2: Trích xuất đặc trưng gộp (Feature Fusion - Tối ưu nhất)
Thay vì chỉ lấy ảnh đầu tiên, bạn giữ nguyên cấu trúc mỗi bệnh nhân là 1 dòng, nhưng thay đổi cách mô hình đọc dữ liệu.

Cách làm:

Trong DataLoader, thay vì return images[0], bạn cho nó trả về một danh sách các ảnh (ví dụ: tối đa 5 ảnh).

Đi qua một lớp CNN (Encoder) giống nhau cho tất cả các ảnh để lấy ra các vector đặc trưng (feature vectors).

Sử dụng Pooling (Max Pooling hoặc Average Pooling) trên các vector đặc trưng của 5 ảnh này để gộp lại thành 1 vector duy nhất.

Đưa vector gộp này vào nhánh FiLM của bạn.

Ưu điểm: Đây là cách chuẩn trong học đa phương thức, giúp mô hình tận dụng được toàn bộ thông tin hình ảnh của bệnh nhân mà không làm sai lệch cấu trúc dữ liệu.

Khi nào dùng: Khi bạn muốn mô hình chuyên nghiệp và tận dụng tối đa thông tin từ các ảnh CCA và IMT.