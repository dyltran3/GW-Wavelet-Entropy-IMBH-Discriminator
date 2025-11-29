# Giải Mã Vùng Cấm: Phân Biệt Tín Hiệu Lỗ Đen Trung Gian (IMBH) và Nhiễu Xung (Glitches) Bằng Phân Tích Wavelet-Entropy

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Python](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Status](https://img.shields.io/badge/status-In%20Development-yellow.svg)]()

## 📋 Tổng Quan

Dự án nghiên cứu này phát triển một phương pháp định lượng mới dựa trên **Wavelet-Entropy** để giải quyết bài toán phân biệt tín hiệu sóng hấp dẫn từ Lỗ đen Trung gian (IMBH) với nhiễu xung của máy dò LIGO (đặc biệt là Blip Glitches).

### 🎯 Mục Tiêu Chính

- Phát triển chỉ số **Quadratic Entropy ($H_Q$)** để định lượng sự khác biệt về cấu trúc tín hiệu
- Giảm thiểu tỷ lệ báo động giả (False Positive Rate) hiệu quả hơn phương pháp SNR truyền thống
- Cung cấp công cụ kiểm chứng độc lập cho các ứng viên IMBH trong dữ liệu LIGO O4 Run

### 🔬 Lĩnh Vực

- Vật lý Hiện đại
- Thiên văn học Sóng hấp dẫn
- Xử lý Tín hiệu Số

---

## 🌟 Điểm Nổi Bật

### Vấn Đề Nghiên Cứu

Lỗ đen trung gian (IMBH) với khối lượng trong "Vùng cấm" (65-120 $M_\odot$) tạo ra tín hiệu sóng hấp dẫn cực ngắn, rất khó phân biệt với nhiễu Blip Glitches của máy dò LIGO trên biểu đồ phổ truyền thống.

### Giải Pháp Đề Xuất

Chuyển từ phân tích dựa trên **năng lượng** (SNR) sang phân tích dựa trên **cấu trúc và trật tự** của tín hiệu trong không gian thời gian-tần số bằng:

1. **Continuous Wavelet Transform (CWT)** - Tối ưu độ phân giải thời gian-tần số
2. **Quadratic Entropy ($H_Q$)** - Định lượng mức độ tập trung năng lượng

---

## 🔧 Phương Pháp Luận

### 1. Biến Đổi Wavelet Liên Tục (CWT)

```
W(a, b) = (1/√a) ∫ x(t) ψ*((t-b)/a) dt
```

- Sử dụng hàm Morlet Wavelet
- Khắc phục hạn chế của STFT/Q-transform
- Quan sát chi tiết sự thay đổi tần số nhanh

### 2. Quadratic Entropy ($H_Q$)

```
H_Q = 1 - Σ p_i²
```

- $p_i$: Xác suất năng lượng chuẩn hóa tại điểm $i$
- Tín hiệu có trật tự → Entropy thấp
- Nhiễu hỗn loạn → Entropy cao

### 3. Tối Ưu Hóa Ngưỡng

- Sử dụng đường cong ROC (Receiver Operating Characteristic)
- Áp dụng Youden's J Statistic: $J = TPR - FPR$
- Xác định ngưỡng $H_{Q, \text{ngưỡng}}$ tối ưu tại $J_{\text{max}}$

---

## 📊 Dữ Liệu

### Dữ Liệu Nhiễu (Thực Tế)
- **200+** mẫu Blip Glitches từ GWOSC O3 Run
- Dữ liệu Alert O4a Run mới nhất
- Phân tích tính ổn định thống kê qua các chu kỳ quan sát

### Dữ Liệu Tín Hiệu (Giả Lập)
- **200+** Templates IMBH (ví dụ: $m_1=70 M_\odot, m_2=70 M_\odot$)
- Tạo bằng PyCBC dựa trên Thuyết Tương đối Hậu Newton
- Inject vào nền nhiễu thật (noise floor)

---

## 🚀 Cài Đặt

### Yêu Cầu Hệ Thống

```bash
Python >= 3.8
NumPy >= 1.20
SciPy >= 1.7
PyCBC >= 2.0
Matplotlib >= 3.3
```

### Cài Đặt Packages

```bash
# Clone repository
git clone https://github.com/your-username/imbh-glitch-detection.git
cd imbh-glitch-detection

# Tạo virtual environment
python -m venv venv
source venv/bin/activate  # Linux/Mac
# hoặc
venv\Scripts\activate  # Windows

# Cài đặt dependencies
pip install -r requirements.txt
```

---

## 💻 Sử Dụng

### 1. Tải và Chuẩn Bị Dữ Liệu

```python
from data_loader import load_glitches, generate_imbh_templates

# Tải Blip Glitches từ GWOSC
glitches = load_glitches(run='O3', glitch_type='Blip', n_samples=200)

# Tạo IMBH templates
imbh_signals = generate_imbh_templates(
    m1=70, m2=70, 
    n_samples=200,
    noise_injection=True
)
```

### 2. Tính Toán Wavelet-Entropy

```python
from wavelet_entropy import compute_cwt, calculate_entropy

# Tính CWT và Entropy cho một tín hiệu
cwt_coeffs = compute_cwt(signal, wavelet='morlet')
entropy = calculate_entropy(cwt_coeffs, method='quadratic')
```

### 3. Phân Tích và Đánh Giá

```python
from analysis import compute_roc_curve, find_optimal_threshold

# Tính toán đường cong ROC
fpr, tpr, thresholds = compute_roc_curve(
    imbh_entropies, 
    glitch_entropies
)

# Tìm ngưỡng tối ưu
optimal_threshold = find_optimal_threshold(
    fpr, tpr, thresholds, 
    method='youden'
)
```

### 4. Kiểm Chứng Ứng Viên O4

```python
from validation import validate_candidates

# Áp dụng mô hình lên ứng viên O4
results = validate_candidates(
    o4_candidates,
    threshold=optimal_threshold
)
```

---

## 📈 Kết Quả Dự Kiến

### Proof of Concept

1. **Phân biệt rõ ràng**: Phân bố $H_{Q, \text{IMBH}}$ tách biệt với $H_{Q, \text{Glitch}}$ trên histogram
2. **Ưu thế hiệu suất**: Đường cong ROC của Wavelet-Entropy cao hơn đáng kể so với SNR/Matched Filtering

### Validation

- Áp dụng $H_{Q, \text{ngưỡng}}$ lên ứng viên O4 Run
- Ưu tiên các sự kiện có $H_Q$ thấp hơn ngưỡng
- Cung cấp đánh giá độc lập về ứng viên IMBH mới nhất

---

## 📁 Cấu Trúc Thư Mục

```
imbh-glitch-detection/
│
├── data/                      # Dữ liệu thô và đã xử lý
│   ├── glitches/
│   └── templates/
│
├── notebooks/                 # Jupyter notebooks phân tích
│   ├── 01_data_exploration.ipynb
│   ├── 02_wavelet_analysis.ipynb
│   └── 03_entropy_optimization.ipynb
│
├── src/                       # Source code chính
│   ├── data_loader.py
│   ├── wavelet_entropy.py
│   ├── analysis.py
│   └── validation.py
│
├── results/                   # Kết quả và visualizations
│   ├── figures/
│   └── reports/
│
├── tests/                     # Unit tests
│
├── requirements.txt           # Dependencies
├── README.md                  # File này
└── LICENSE                    # Giấy phép
```

---

## 🔮 Hướng Phát Triển

### Ngắn Hạn

- [ ] Hoàn thiện engine CWT và tính toán $H_Q$
- [ ] Phân tích 200+ mẫu Glitches và IMBH templates
- [ ] Xác định ngưỡng $H_{Q, \text{ngưỡng}}$ tối ưu
- [ ] Kiểm chứng trên ứng viên O4 Run

### Dài Hạn

- [ ] Mở rộng phân loại cho các loại Glitch khác (Whistle, Scratchy)
- [ ] Tích hợp Machine Learning (Random Forest, SVM)
- [ ] Sử dụng $H_Q$ như feature đầu vào cho mô hình học máy
- [ ] Xây dựng pipeline tự động cho real-time detection

---

## 📚 Tài Liệu Tham Khảo

### Datasets
- [GWOSC - Gravitational Wave Open Science Center](https://www.gw-openscience.org/)
- [LIGO O3 Glitch Catalogue](https://www.gw-openscience.org/o3/)

### Tools & Libraries
- [PyCBC](https://pycbc.org/) - Gravitational Wave Analysis
- [GWpy](https://gwpy.github.io/) - Gravitational Wave Python Package
- [PyWavelets](https://pywavelets.readthedocs.io/) - Wavelet Transforms in Python

---

## 👥 Đóng Góp

Chúng tôi hoan nghênh mọi đóng góp! Vui lòng:

1. Fork repository
2. Tạo branch mới (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Mở Pull Request

---

## 📝 License

Dự án được phân phối dưới giấy phép MIT. Xem file `LICENSE` để biết thêm chi tiết.

---

## 📧 Liên Hệ

**Thời gian thực hiện**: 30/11/2025 – 31/12/2025

Để biết thêm thông tin hoặc báo cáo lỗi, vui lòng mở Issue trên GitHub repository.

---

## 🙏 Acknowledgments

- LIGO Scientific Collaboration
- Gravitational Wave Open Science Center (GWOSC)
- PyCBC Development Team

---

*Dự án nghiên cứu khoa học - Vật lý Thiên văn Sóng hấp dẫn*
