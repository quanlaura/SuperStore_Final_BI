# Super Store Dataset

Bộ dữ liệu bán lẻ kinh điển

Link dowload dataset: [Superstore Dataset](https://www.kaggle.com/datasets/vivek468/superstore-dataset-final/data)

## Tổng quan về dataset

Bộ dữ liệu Superstore phản ánh chi tiết lịch sử giao dịch của một chuỗi bán lẻ tại Mỹ trong giai đoạn 2014 - 2017, với quy mô khoảng 10.000 dòng và 21 cột thông tin. Nguồn dữ liệu này cung cấp bức tranh toàn cảnh về hoạt động kinh doanh để phục vụ các bài toán tối ưu hóa lợi nhuận và quản lý vận hành.

- **Bối cảnh kinh doanh**: Doanh nghiệp đang đối mặt với áp lực cạnh tranh, đòi hỏi phải bóc tách dữ liệu để xác định chính xác các nhóm sản phẩm, phân khúc khách hàng, và khu vực địa lý tiềm năng cần tập trung khai thác, cũng như khoanh vùng các mảng đang gây thất thoát.

- **Cấu trúc nghiệp vụ**: Ta có thể chia 21 trường dữ liệu cơ bản được chia thành 4 nhóm trọng tâm.

## Các nhóm chính được chia

### Nhóm 1: Order Information - Thông tin đơn hàng

- Row ID: Unique ID for each row
- Order ID: Unique ID for each order
- Order Date: Date when the order was placed
- Ship Date: Date when the order was shipped
- Ship mode: Shipping method selected by the customer

Trọng tâm phân tích: Số lượng đơn hàng, xu hướng đặt hàng theo thời gian, thời gian giao hàng và phương thức vận chuyển

### Nhóm 2: Customer Information - Thông tin khách hàng

- Customer ID: Unique ID to identify each customer
- Customer Name: Name of the customer
- Segment: Customer Segment

Trọng tâm phân tích: Số lượng khách hàng, doanh thu và lợi nhuận theo từng phân khúc khách hàng.

### Nhóm 3: Geographic Information - Thông tin địa lý

- Country: Country where the customer resides
- City: City where the customer resides
- State: State where the customer resides
- Postal Code: Postal code of the customer
- Region: Geographic region where the customer resides

Trọng tâm phân tích: Doanh thu, lợi nhuận và hiệu quả kinh doanh theo khu vực địa lý

### Nhóm 4: Product Information - Thông tin sản phẩm

- Product ID: Unique ID of the product
- Category: Category of the product ordered
- Sub-Category: Sub-category of the product ordered
- Product Name: Name of the product.

Trọng tâm phân tích: Danh mục sản phẩm bán chạy, doanh thu và lợi nhuận theo danh mục, nhóm con và từng sản phẩm

### Nhóm 5: Sales and Profitability - Doanh số và lợi nhuận

- Sales: Revenue generated from product sales
- Quantity: Quantity of products sold
- Discount: Discount applied to the product
- Profit: Profit or loss generated from the sale.

Trọng tâm phân tích: Tổng doanh thu, tổng lợi nhuận, số lượng bán ra, tác động của chiết khấu và các sản phẩm hoặc đơn hàng gây thua lỗ.

## EDA của bài toán

### TIỀN XỬ LÝ VÀ LÀM SẠCH DỮ LIỆU (DATA CLEANING & PREPROCESSING)

Trước khi tiến hành phân tích sâu hoặc huấn luyện mô hình, dữ liệu cần được chuẩn hóa để đảm bảo độ chính xác.

- **Định dạng kiểu dữ liệu (Data Type Conversion)**: Chuyển đổi các cột Order Date và Ship Date từ định dạng chuỗi (String/Object) sang định dạng Datetime. Việc này cho phép trích xuất các đặc trưng thời gian như Năm, Tháng, Quý, Thứ trong tuần và đánh dấu ngày cuối tuần (IsWeekend).

- **Xử lý dữ liệu khuyết thiếu (Missing Values)**: Điền các giá trị thiếu trong cột Postal Code dựa trên tra cứu thành phố (ví dụ: gán mã 05401 cho Burlington, Vermont). Nếu tỷ lệ thiếu quá thấp và không thể tra cứu, tiến hành loại bỏ (drop) để không làm nhiễu phân tích không gian.

- **Kiểm định logic nghiệp vụ (Logic Validation)**: Thiết lập biến Lead Time (Thời gian xử lý đơn) = Ship Date - Order Date. Lọc và kiểm tra các dòng có Lead Time < 0 (giao hàng trước khi đặt) để loại bỏ các lỗi nhập liệu.

- **Xử lý giá trị ngoại lai (Outliers)**: Sử dụng phương pháp IQR (Interquartile Range) hoặc Z-score để phát hiện các giao dịch có Quantity hoặc Sales cao bất thường, hoặc Discount chạm mức tối đa đi kèm khoản lỗ lớn. Gắn nhãn Anomaly thay vì xóa bỏ, vì đây có thể là các đợt xả kho hoặc lỗi thiết lập giá trị hệ thống.

- **Trích xuất đặc trưng (Feature Engineering)**: Tạo thêm cột Price per Unit (Giá bán lẻ trên mỗi đơn vị) = Sales / Quantity để phục vụ phân tích độ co giãn của giá trước khi áp dụng chiết khấu.

### PHÂN TÍCH KHÁM PHÁ DỮ LIỆU (EXPLORATORY DATA ANALYSIS - EDA)

#### Phân tích Hiệu suất Kinh doanh (Sales & Profit Analysis)

- **Phân tích chuỗi thời gian (Time-Series)**: Trực quan hóa biến động doanh thu và lợi nhuận theo năm, quý, tháng. Xác định các "điểm neo" mùa vụ (Seasonality), điển hình là sự bùng nổ doanh số vào quý 4 (tháng 11, tháng 12) do ảnh hưởng của kỳ nghỉ lễ.

    Hiệu suất theo Danh mục (Category & Sub-Category Performance):

+ **Bóc tách đóng góp của 3 danh mục chính (Technology, Furniture, Office Supplies)**. Đánh giá sự đối lập giữa Doanh thu và Lợi nhuận: Danh mục Technology thường dẫn đầu về cả hai, trong khi Furniture (đặc biệt là Sub-Category Tables và Bookcases) có doanh thu cao nhưng lợi nhuận thường xuyên âm.

+ Áp dụng nguyên tắc Pareto (80/20) để chỉ ra rằng một nhóm nhỏ các sản phẩm hoặc giao dịch lỗi đang gây ra phần lớn các khoản thâm hụt tài chính.

+ **Độ nhạy của Giá và Chiết khấu (Discount vs. Profitability)**: Sử dụng biểu đồ phân tán (Scatter Plot) để đo lường mức độ tương quan nghịch giữa Discount và Profit Margin. Xác định "điểm gãy" chiết khấu (Break-even Discount) – mức giảm giá tối đa mà tại đó đơn hàng bắt đầu chuyển từ lãi sang lỗ.

#### Phân tích Không gian - Địa lý (Geospatial Analysis)

- **Đóng góp theo Khu vực (Regional & State Impact)**: Phân tích hiệu suất theo 4 vùng (West, East, South, Central). Đi sâu vào cấp độ Bang (State) để xác định các thị trường trọng điểm mang lại doanh thu lớn nhất (thường là California, New York, Texas, Washington).

- **Nhận diện Vùng rủi ro (Loss Hotspots)**: Khoanh vùng các bang hoặc thành phố có tổng lợi nhuận âm liên tục (ví dụ: Texas, Pennsylvania, Illinois hoặc các thành phố cụ thể như Philadelphia). Phân tích nguyên nhân cốt lõi (thường do chính sách lạm dụng mã giảm giá tại các khu vực này).

#### Phân tích Khách hàng & Chuỗi cung ứng (Customer & Shipping Analytics)

- **Giá trị theo Phân khúc (Customer Segment Value)**: So sánh 3 nhóm khách hàng (Consumer, Corporate, Home Office). Dù Consumer chiếm tỷ trọng lớn nhất về số lượng, cần đánh giá xem nhóm Corporate hay Home Office có Giá trị đơn hàng trung bình (Average Order Value - AOV) cao hơn hay không.

- **Phân tích RFM (Recency, Frequency, Monetary)**: Đánh giá khách hàng dựa trên độ mới của đơn hàng, tần suất mua và tổng tiền chi tiêu. Từ đó, nhận diện các khách hàng VIP và khách hàng có nguy cơ rời bỏ (Churn Risk).

- **Đánh giá Vận chuyển (Shipping Efficiency)**: Đánh giá tần suất sử dụng các phương thức vận chuyển (Same Day, First Class, Second Class, Standard Class). Phân tích sự ảnh hưởng của Ship Mode đến biên lợi nhuận, đặc biệt đối với các đơn hàng có giá trị thấp nhưng chi phí giao nhận hỏa tốc cao.

#### MÔ HÌNH HÓA VÀ HỌC MÁY (PREDICTIVE MODELING)

Bộ dữ liệu cung cấp nền tảng vững chắc để xây dựng các 파pipeline học máy giải quyết các bài toán hồi quy, phân loại và gom cụm:

Phân loại (Classification) - Dự báo rủi ro thua lỗ:

- Mục tiêu: Thiết lập biến nhị phân is_loss = 1 (nếu Profit < 0) và 0 (nếu ngược lại).

- Phương pháp: Sử dụng các thuật toán như Random Forest hoặc Gradient Boosting (XGBoost).

- Tiêu chí đánh giá: Tập trung tối ưu hóa chỉ số Recall để hạn chế tối đa việc bỏ sót các đơn hàng có rủi ro gây lỗ (False Negatives). Các đặc trưng đầu vào quan trọng bao gồm: Discount, Category, Region, và Ship Mode.

Hồi quy (Regression) - Dự báo Doanh thu và Nhu cầu (Forecasting):

- Mục tiêu: Dự đoán giá trị Sales hoặc Quantity cho từng đơn hàng hoặc tổng mức tiêu thụ trong các khoảng thời gian tới.

- Phương pháp: Áp dụng các mô hình học sâu (như Multi-Layer Perceptron với PyTorch/TensorFlow) hoặc chuỗi thời gian (ARIMA, Prophet) để dự báo nhu cầu hàng tồn kho nhằm chuẩn bị nguồn lực cho các tháng cao điểm cuối năm.

Học không giám sát (Unsupervised Learning) - Phân cụm khách hàng:

- Phương pháp: Đưa các chỉ số RFM (Recency, Frequency, Monetary) vào mô hình K-Means Clustering.

- Ứng dụng: Phân chia tệp dữ liệu thành các nhóm tự động như "Khách hàng Giá trị cao" (cần chăm sóc đặc biệt), "Khách hàng Ngủ đông" (cần chiến dịch tái kích hoạt), và "Khách hàng Nhạy cảm về giá" (chỉ phản ứng với các mức chiết khấu sâu).