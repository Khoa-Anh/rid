# [AI] RID - Tài liệu hướng dẫn

## Giới thiệu

- Api tích hợp chức năng phân biệt đâu là hình chụp trực tiếp, và đâu là hình chụp qua màn hình LCD của thiết bị khác.

## Đặc trưng của hình chụp qua thiết bị khác

![Hình chụp trực tiếp](images/Untitled.png)

Hình chụp trực tiếp

![Hình chụp qua màn hình máy tính](images/Untitled%201.png)

Hình chụp qua màn hình máy tính

- Việc chụp lại hình ảnh từ màn hình LCD có thể tạo ra nhiều đặc trưng khác biệt so với việc chụp trực tiếp từ một thước ảnh.
    - Moiré: Moiré là hiện tượng xảy ra khi hình ảnh màn hình pixel (chẳng hạn như văn bản hoặc lưới trực tiếp) tương tác với lưới pixel trên máy ảnh. Điều này có thể tạo ra các mẫu không mong muốn và đặc trưng trong hình ảnh chụp lại.
    - Circular bokeh: các dấu chấm tròn nhỏ trên hình ảnh chụp lại, do cách mà máy ảnh tương tác với màn hình pixel. circular bokeh mắt cá thường không tồn tại trong hình ảnh gốc.
    - Sự mất màu hoặc sự biến đổi màu: Màn hình LCD có thể làm thay đổi màu sắc của hình ảnh ban đầu, đặc biệt là trong việc tái tạo màu sắc và độ tương phản. Điều này có thể dẫn đến sự biến đổi màu sắc trong hình ảnh chụp lại.
    - Highlights: Màn hình LCD có thể tạo ra những điểm highlights trong hình ảnh, những điểm sáng đặc biệt. Những điểm này có thể là một đặc điểm quan trọng cho việc phát hiện hình ảnh chụp lại.
    - Đặc điểm vật lý của màn hình: Một số đặc trưng vật lý của màn hình LCD, chẳng hạn như các viền màn hình, các điểm nổi bật hoặc vết xước có thể xuất hiện trong hình ảnh chụp lại.

---

## Hướng dẫn chạy chương trình trên local

**Cài Virtualenv**

```
pip install virtualenv

```

**Tạo môi trường ảo**

- Mở terminal và di chuyển đến thư mục dự án. Sau đó, sử dụng lệnh sau để tạo một môi trường ảo mới (thay `myenv` bằng tên muốn đặt cho môi trường ảo):

```
python -m venv myenv

```

**Kích hoạt môi trường ảo**

- Trên Linux/macOS:

```
source myenv/bin/activate

```

- Trên Windows (PowerShell)

```
.\\myenv\\Scripts\\Activate.ps1

```

- Trên Windows (Command Prompt):

```
.\\myenv\\Scripts\\activate

```

- **Cài các thư viện trong** requirements.txt

```
pip install -r requirements.txt

```

- Chạy chương trình:

```
python app.py

```

**Thoát khỏi môi trường ảo**

```
deactivate

```

---

## Các thuật toán và công nghệ được sử dụng

### 1. **Flask - Web Framework**

- **Flask** là một micro web framework bằng Python giúp triển khai ứng dụng web. Ứng dụng này tạo một API đơn giản để nhận ảnh qua yêu cầu POST từ người dùng và trả về kết quả dự đoán.
- Các thành phần chính của Flask được sử dụng bao gồm:
    - `@app.route('/')`: Định nghĩa route cho trang chủ.
    - `@app.route('/predict', methods=['POST'])`: Định nghĩa route cho việc nhận ảnh và xử lý dự đoán từ mô hình.

### 2. **TensorFlow - Thư viện Máy Học**

- **TensorFlow** là một thư viện máy học mạnh mẽ, phổ biến cho việc xây dựng và triển khai mô hình học sâu (Deep Learning). Trong chương trình này, TensorFlow được dùng để load mô hình đã huấn luyện và dự đoán ảnh.
- Một số điểm quan trọng liên quan đến TensorFlow:
    - `load_model(weights_path)`: Load mô hình từ file `weights.best.hdf5`, là file chứa các trọng số tốt nhất từ quá trình huấn luyện trước đó.
    - **Tiền xử lý ảnh**: Ảnh được tiền xử lý trước khi đưa vào mô hình để dự đoán. Các bước bao gồm resize ảnh về kích thước `(150, 150)`, chuyển đổi ảnh thành mảng NumPy, và chuẩn hóa giá trị pixel về khoảng `[0, 1]`.

### 3. **TensorFlow Addons - F1 Score Metric**

- **TensorFlow Addons** cung cấp các hàm bổ sung cho TensorFlow, bao gồm các phép đo đánh giá, metric bổ sung như **F1 Score**. Đây là một thước đo quan trọng trong các bài toán phân loại, giúp đánh giá cân bằng giữa độ chính xác và khả năng bao phủ (precision và recall).
- `F1Score` được thêm vào custom metric để mô hình có thể tối ưu hóa theo tiêu chí này khi huấn luyện.
- Mặc dù trong đoạn mã trên không trực tiếp tính toán F1 Score khi dự đoán, nhưng nó được sử dụng trong quá trình huấn luyện mô hình.

### 4. **Keras - Xử lý ảnh và Mô hình**

- **Keras** là API cấp cao của TensorFlow, giúp việc xây dựng và xử lý mô hình dễ dàng hơn. Trong đoạn mã này, các thư viện từ Keras như `image.load_img` và `image.img_to_array` được sử dụng để tiền xử lý ảnh trước khi đưa vào mô hình.
- Ảnh được resize và chuyển đổi thành mảng 3D để mô hình có thể đọc và xử lý.

### 5. **Luồng hoạt động của chương trình**

- Khi có yêu cầu POST đến endpoint `/predict`, ảnh sẽ được tải lên từ người dùng.
- Ảnh sau đó sẽ được lưu vào thư mục tạm và được xử lý thành mảng để đưa vào mô hình.
- Mô hình đã huấn luyện (được lưu dưới dạng file `.hdf5`) sẽ được load và sử dụng để dự đoán.
- Kết quả dự đoán sẽ là `Recaptured` hoặc `Original` dựa trên ngưỡng của giá trị dự đoán. Nếu giá trị dự đoán lớn hơn `0.5`, ảnh sẽ được coi là "Recaptured" (ảnh bị chụp lại), ngược lại là "Original" (ảnh gốc).

### 6. **Các thành phần bổ sung**

- **Werkzeug - Secure Filename**: `secure_filename(file.filename)` được dùng để bảo vệ tên file, đảm bảo rằng file sẽ được lưu với tên hợp lệ, tránh việc khai thác lỗi bảo mật qua tên file.
- **Temporary File Handling**: Ảnh sau khi được xử lý sẽ bị xóa khỏi hệ thống để tránh tiêu tốn bộ nhớ không cần thiết.

### 7. **Công nghệ và thuật toán tổng quát**

- **Mô hình phân loại nhị phân**: Đây là mô hình phân loại có hai kết quả, trong đó mô hình dự đoán xem một ảnh là "Recaptured" hay "Original".
- **Xử lý ảnh**: Các thuật toán cơ bản trong xử lý ảnh và học sâu được sử dụng để chuẩn hóa và phân loại ảnh.
- **API RESTful**: Flask cung cấp một API RESTful đơn giản để giao tiếp với ứng dụng dựa trên HTTP requests.

### Tóm tắt

- **Flask** cung cấp giao diện web để nhận và trả về kết quả dự đoán từ mô hình.
- **TensorFlow và Keras** được sử dụng để load mô hình và xử lý ảnh.
- **F1 Score** được tích hợp làm metric trong quá trình huấn luyện mô hình.
- Chương trình xử lý dự đoán bằng cách nhận ảnh, tiền xử lý, và trả kết quả qua API.
