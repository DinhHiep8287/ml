# Tổng Quan Cuộc Thi

## **1. Mô Tả Cuộc Thi**
Cuộc thi nhằm dự đoán mức độ sử dụng Internet một cách vấn đề ở trẻ em và thanh thiếu niên, đánh giá qua thang điểm **Severity Impairment Index (sii)**. Đây là một vấn đề quan trọng liên quan đến sức khỏe tâm thần, đặc biệt trong bối cảnh việc tiếp cận các phương pháp đo lường truyền thống còn nhiều hạn chế.

- **Mục tiêu**: Xây dựng mô hình dự đoán dựa trên dữ liệu về hoạt động thể chất, sức khỏe và thói quen Internet.
- **Lợi ích**: Góp phần thúc đẩy các thói quen kỹ thuật số lành mạnh thông qua phát hiện sớm các hành vi sử dụng Internet vấn đề.

---

## **2. Giới Thiệu về Cuộc Thi**
Cuộc thi **“Child Mind Institute Problematic Internet Use”** sử dụng dữ liệu từ dự án **Healthy Brain Network (HBN)** với hơn 5,000 trẻ em và thanh thiếu niên (từ 5–22 tuổi).

### **Dữ liệu cung cấp**:
- **Dữ liệu bảng (CSV)**: Bao gồm các chỉ số như tuổi, giới tính, BMI, huyết áp, hoạt động thể chất, và khảo sát liên quan đến Internet.
- **Dữ liệu chuỗi thời gian (parquet)**: Chuyển động cổ tay được ghi nhận bằng thiết bị đeo tay trong nhiều ngày.

### **Thách thức chính**:
- **Thiếu dữ liệu**: Nhiều giá trị bị thiếu ở các biến sức khỏe và hành vi.
- **Kích thước lớn**: Dữ liệu chuỗi thời gian đòi hỏi xử lý đặc thù.
- **Đa dạng loại dữ liệu**: Kết hợp biến phân loại và biến liên tục.
- **Mối quan hệ phức tạp**: Các chỉ số sức khỏe và hành vi có sự tương tác chặt chẽ.

---

## **3. Tiếp Cận Bằng Ensemble Boosting và Voting Regressor**

### **3.1. Ensemble Learning**
Phương pháp học Ensemble kết hợp nhiều mô hình để tăng độ chính xác và tính ổn định. Mỗi mô hình cơ bản sẽ đóng góp vào kết quả dự đoán tổng thể.

### **3.2. Boosting**
Các thuật toán Boosting được sử dụng:
- **LightGBM**: Tối ưu tốc độ huấn luyện, hiệu quả với dữ liệu lớn.
- **XGBoost**: Hiệu suất cao, giảm overfitting.
- **CatBoost**: Tốt cho dữ liệu phân loại, ít cần tiền xử lý.

### **3.3. Voting Regressor**
Voting Regressor kết hợp các dự đoán từ nhiều mô hình bằng cách tính trung bình, giúp giảm sai số và tăng tính ổn định.

### **Lợi ích chính**:
- **Tăng độ chính xác**: Kết hợp điểm mạnh của các mô hình cơ bản.
- **Ổn định hơn**: Giảm sự dao động trong dự đoán.
- **Dễ mở rộng**: Thêm hoặc thay thế mô hình dễ dàng.

---

## **4. Kết Hợp Các Phương Pháp**
Để xử lý dữ liệu phức tạp, chúng tôi sử dụng:
- **Ensemble Boosting (LightGBM, XGBoost, CatBoost)**.
- **Voting Regressor** để kết hợp các mô hình mạnh mẽ nhất, đảm bảo độ chính xác và ổn định trong dự đoán.

# Mô Tả Dataset

## **4.1. Tổng Quan về Dataset**
- **Healthy Brain Network (HBN)**: Một nghiên cứu lâm sàng với ~5,000 trẻ em và thanh thiếu niên (5–22 tuổi), tập trung vào việc tìm kiếm các dấu hiệu sinh học để cải thiện chẩn đoán và điều trị rối loạn sức khỏe tâm thần.
- **Mục tiêu cuộc thi**: Dự đoán **Severity Impairment Index (sii)** dựa trên dữ liệu thể chất, hành vi, và chuỗi thời gian.

---

## **4.2. Cấu Trúc Dataset**

### **Dữ liệu Bảng (Tabular Data)**
- **Định dạng**: `.csv`
- **Nội dung**: Tuổi, giới tính, BMI, huyết áp, hoạt động thể chất, thời gian sử dụng Internet.
- **Kích thước**: ~5,000 bản ghi (train.csv), ~3,800 bản ghi (test.csv).

### **Dữ liệu Chuỗi Thời Gian (Time-Series Data)**
- **Định dạng**: `.parquet`
- **Nội dung**: Chuyển động cổ tay (X, Y, Z), ENMO, góc, ánh sáng, và các chỉ số khác.
- **Kích thước**: Tương ứng với từng **id** trong dữ liệu bảng.

---

## **4.3. Các Trường Dữ Liệu Chính**
### **Dữ liệu Bảng**
- **Nhân khẩu học**: Tuổi, giới tính.
- **Thói quen Internet**: Số giờ sử dụng máy tính/internet mỗi ngày.
- **Thể chất và sức khỏe**: BMI, huyết áp, nhịp tim, cân nặng, chiều cao.
- **Đánh giá hành vi**: Children’s Global Assessment Scale (CGAS), PCIAT-PCIAT_Total (thang đo nghiện Internet).

### **Dữ liệu Chuỗi Thời Gian**
- **Chỉ số vận động**: X, Y, Z (gia tốc), ENMO (mức độ chuyển động).
- **Thông số bổ sung**: Cường độ ánh sáng (lux), thời gian trong ngày, trạng thái thiết bị đeo.

---

## **4.4. Phân Bố và Mối Tương Quan**
### **Mục tiêu: Severity Impairment Index (sii)**

[![0a9b91b8-d8f9-4329-9760-99cebaac7950.jpg](https://i.postimg.cc/prVm5sCp/0a9b91b8-d8f9-4329-9760-99cebaac7950.jpg)](https://postimg.cc/jw9xGQGb)

- Phân loại theo PCIAT-PCIAT_Total:
  - **0 (None)**: 0–30.
  - **1 (Mild)**: 31–49.
  - **2 (Moderate)**: 50–79.
  - **3 (Severe)**: ≥80.
- **Phân bố**: ~40% không bị ảnh hưởng, ~10% ở mức độ nghiêm trọng (moderate/severe).

### **Mối Tương Quan Chính**
[![431e1730-48ca-43c2-bf4f-2766d01544b3.jpg](https://i.postimg.cc/kMw6PdM8/431e1730-48ca-43c2-bf4f-2766d01544b3.jpg)](https://postimg.cc/z3bXT6gX)
- **Mạnh**: 
  - **sii và PCIAT-PCIAT_Total**: Hệ số tương quan r = 0.899681.
  - **Tuổi (Age)**: r = 0.409559.
  - **Thời gian sử dụng Internet (Hours/Day)**: r = 0.374124.
- **Yếu hoặc không đáng kể**: Một số biến như BIA-BIA_ECW, BIA-BIA_LDM có hệ số tương quan rất thấp (< 0.1).
- **Tiêu cực nhẹ**: 
  - **Giới tính (Sex)**: r = -0.093648.
  - **Một số biến thể chất**: r ~ -0.1.

---
## **4.5. Nhận Xét**
- **Biến mục tiêu (sii)** phụ thuộc chặt chẽ vào **PCIAT-PCIAT_Total**, nhưng các câu trả lời chủ quan từ phụ huynh có thể làm giảm tính chính xác.
- **Dữ liệu đa dạng** với nhiều loại chỉ số (bảng, chuỗi thời gian) đòi hỏi kỹ thuật xử lý và mô hình hóa phù hợp.
- **Thách thức chính**: Giá trị thiếu nhiều, kích thước dữ liệu lớn, mối quan hệ phức tạp giữa các chỉ số sức khỏe và hành vi.
# **Thực Nghiệm và Kết Quả**

## **1. Quy Trình Thực Nghiệm**
### **1.1. Quy trình tổng quát:**
1. **Tải và Tiền Xử Lý Dữ Liệu**:
   - Tải dữ liệu huấn luyện, kiểm tra và chuỗi thời gian.
   - Xử lý chuỗi thời gian bằng AutoEncoder.
   - Gộp dữ liệu chuỗi thời gian đã mã hóa với dữ liệu chính.
   - Xử lý giá trị thiếu bằng **KNNImputer**.

2. **Chọn Đặc Trưng và Chuẩn Bị Dữ Liệu**:
   - Lựa chọn các cột đặc trưng quan trọng.
   - Loại bỏ các mẫu có giá trị thiếu trong biến mục tiêu `sii`.

3. **Xây Dựng Mô Hình Ensemble**:
   - Kết hợp các mô hình như **LightGBM**, **XGBoost**, **CatBoost** qua **VotingRegressor**.

4. **Tối Ưu Hóa và Kết Hợp Dự Đoán**:
   - Tối ưu hóa ngưỡng dự đoán để cải thiện **QWK**.
   - Sử dụng phương pháp **Majority Voting** để kết hợp dự đoán từ các mô hình.

---
# **Các Kỹ Thuật Tối Ưu**

## **1. Cross-Validation với StratifiedKFold**
- **Cross-Validation**: 
  - Chia dữ liệu thành nhiều phần (folds) để huấn luyện và kiểm tra.
  - Tăng độ ổn định và sử dụng hiệu quả dữ liệu.
  - Phát hiện overfitting/underfitting.
  
- **StratifiedKFold**: 
  - Đảm bảo tỷ lệ phân bố lớp giống nhau trong mỗi fold.
  - Giảm bias và tăng khả năng tổng quát hóa.

---

## **2. Tối Ưu Hóa Ngưỡng**
- **Mục tiêu**: 
  - Chuyển dự đoán liên tục thành các lớp phân loại (0, 1, 2, 3).
  - Tối đa hóa chỉ số đánh giá **QWK** (Quadratic Weighted Kappa).
- **Lợi ích**: 
  - Cải thiện hiệu suất dự đoán, giảm sai số giữa dự đoán và giá trị thực.

---

## **3. Quadratic Weighted Kappa (QWK)**
- **Ý nghĩa**:
  - Đo lường mức độ phù hợp giữa dự đoán và giá trị thực.
  - Phù hợp cho các bài toán phân loại có thứ tự (ordinal).

---

## **4. Xử Lý Dữ Liệu Thiếu với KNNImputer**
- **Phương pháp**:
  - Điền giá trị thiếu bằng trung bình của k-láng giềng gần nhất.
  - Phù hợp cho cả biến liên tục và phân loại.
- **Lợi ích**:
  - Bảo toàn tính toàn vẹn dữ liệu.
  - Giảm ảnh hưởng của giá trị thiếu lên mô hình.

---

## **5. Feature Engineering và Feature Encoding**
- **Feature Engineering**:
  - Tạo đặc trưng mới như BMI_Age, Internet_Hours_Age.
  - Tăng cường khả năng mô hình học hỏi.
- **Feature Encoding**:
  - Chuyển đổi biến phân loại thành dạng số.

## **2. Các Phiên Bản Mô Hình**
### **2.1. Version 1**
- **Đặc điểm chính**: 
  - Sử dụng **SimpleImputer** và VotingRegressor (LightGBM, XGBoost, CatBoost).
- **Kết quả**:
  - **Mean Train QWK**: 0.9144 (Rất cao, dễ overfit).
  - **Mean Validation QWK**: 0.3904 (Thấp, mô hình kém tổng quát hóa).
  - **Optimized QWK SCORE**: 0.455 (Cải thiện nhẹ sau tối ưu hóa).
- **Nhận xét**:
  - Điểm số huấn luyện cao nhưng điểm kiểm tra thấp → hiện tượng **overfitting**.
  - Tối ưu hóa ngưỡng chỉ cải thiện điểm số QWK một cách hạn chế.

---

### **2.2. Version 2**
- **Đặc điểm chính**:
  - Tối ưu hóa CatBoost, thêm RandomForest và GradientBoosting.
- **Kết quả**:
  - **Mean Train QWK**: 0.6682 (Thấp hơn Version 1, giảm overfitting).
  - **Mean Validation QWK**: 0.4730 (Cải thiện đáng kể).
  - **Optimized QWK SCORE**: 0.527 (Tăng đáng kể sau tối ưu hóa).
- **Nhận xét**:
  - Sự cải thiện về khả năng tổng quát hóa nhờ bổ sung các mô hình RandomForest và GradientBoosting.
  - Điểm kiểm tra tốt hơn, chứng tỏ hiệu quả giảm overfitting.

---

### **2.3. Version 3**
- **Đặc điểm chính**:
  - Tích hợp **AutoEncoder**, **KNNImputer**, và kỹ thuật **Feature Engineering**.
- **Kết quả**:
  - **Mean Train QWK**: 0.7032 (Hợp lý, không quá cao).
  - **Mean Validation QWK**: 0.4956 (Tốt hơn Version 1 và 2).
  - **Optimized QWK SCORE**: 0.540 (Điểm cao nhất, cải thiện rõ rệt).
- **Nhận xét**:
  - Việc sử dụng AutoEncoder và Feature Engineering đã tăng khả năng mô hình hóa dữ liệu.
  - Model 3 đạt **hiệu suất tốt nhất** cả về Mean Validation QWK và Optimized QWK SCORE.

---

## **3. Kết Quả Cuộc Thi**
### **Điểm Số Cuối Cùng:**
- **Public Score**: 0.447 (Ổn định, thể hiện khả năng tổng quát hóa tốt).
- **Private Score**: 0.384 (Chứng minh mô hình không bị overfit quá mức).

---

## **4. Nhận Xét Chung**
1. **Version 1**: Overfitting, điểm kiểm tra thấp.
2. **Version 2**: Tối ưu hóa hiệu quả hơn với CatBoost, giảm overfitting.
3. **Version 3**: Tích hợp AutoEncoder và kỹ thuật đặc trưng cho hiệu suất tốt nhất.

**Tổng Kết**: 
- **Version 3** là phiên bản tối ưu nhất, thể hiện khả năng tổng quát hóa cao và đạt điểm QWK tốt nhất trên tập kiểm tra. 

