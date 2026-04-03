# BÀI TẬP: Morphology & Sentiment Tiếng Việt với Underthesea

## Thông Tin Sinh Viên
- **Họ Tên:** Hàn Quốc Bảo
- **MSSV:** 123001056

---

## 📋 Giới Thiệu

Ứng dụng này là một tool phân tích **Morphology** (tính chất hình thái từ) và **Sentiment Analysis** (phân tích cảm xúc) cho tiếng Việt. Ứng dụng so sánh hai phương pháp xử lý NLP:

1. **Phương pháp Rule-based**: Sử dụng danh sách cụm từ cảm xúc cố định
2. **Phương pháp Underthesea**: Sử dụng mô hình NLP đã được huấn luyện

---

## 🔄 Luồng Hoạt Động

```
┌─────────────────────────────────────────────────────────────┐
│                    NGƯỜI DÙNG NHẬP VĂN BẢN                  │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│              1️⃣ BƯỚC TIẾN TỮ (TOKENIZATION)                │
│                                                              │
│  Cách 1: Simple Tokenize (split đơn giản)                  │
│  └─ Tách văn bản bằng khoảng trắng                         │
│                                                              │
│  Cách 2: Underthesea Word_Tokenize                         │
│  └─ Tách văn bản dựa trên mô hình NLP                      │
│  └─ Tách các cụm từ một từ (multi-word tokens)             │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│         2️⃣ PHÂN TÍCH TIỀN TỐ HÁN-VIỆT (MORPHOLOGY)        │
│                                                              │
│  Từ các token từ underthesea, tìm các tiền tố như:         │
│  • bất- (phủ định)                                         │
│  • phi- (trái với)                                         │
│  • tái- (lặp lại)                                          │
│  • siêu- (vượt trội)                                       │
│  • phụ- (phụ trợ)                                          │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│          3️⃣ PHÂN TÍCH CẢMXÚC - SENTIMENT (2 CÁCH)         │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Cách 1: Rule-Based                                  │   │
│  │  • Tìm kiếm cụm từ tích cực (POSITIVE_PHRASES)      │   │
│  │  • Tìm kiếm cụm từ tiêu cực (NEGATIVE_PHRASES)      │   │
│  │  • Đếm số lần xuất hiện                              │   │
│  │  • Tính Score = (Positive - Negative) / Tổng         │   │
│  │  • Phân loại: TÍCH CỰC / TIÊU CỰC / TRUNG TÍNH      │   │
│  └──────────────────────────────────────────────────────┘   │
│                          ↓                                   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Cách 2: Underthesea Sentiment                        │   │
│  │  • Gọi mô hình sentiment(text)                        │   │
│  │  • Trả về nhãn: positive / negative / neutral         │   │
│  │  • Xử lý exception nếu model chưa fit                 │   │
│  │  • Fallback sang rule-based nếu lỗi                  │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│            4️⃣ SO SÁNH 2 PHƯƠNG PHÁP VÀ HIỂN THỊ           │
│                                                              │
│  • Hiển thị kết quả rule-based từng bước                   │
│  • Hiển thị kết quả underthesea                            │
│  • So sánh ưu/nhược điểm                                   │
│  • Giải thích sự khác biệt                                 │
└─────────────────────────────────────────────────────────────┘
```

---

## 📊 Báo Cáo Yêu Cầu

### 1️⃣ Tokenization: Dùng underthesea.word_tokenize thay cho simple_tokenize

#### Mục tiêu
Thay thế phương pháp tokenize đơn giản (split) bằng `underthesea.word_tokenize` để xử lý tốt hơn các cụm từ tiếng Việt.

#### Triển khai
```python
# Cách 1: Simple Tokenize (Simple Split)
def simple_tokenize(text: str):
    """Tokenize đơn giản bằng split() — dùng làm baseline so sánh."""
    return text.split()

# Cách 2: Underthesea Word_Tokenize (NLP Model)
def underthesea_tokenize(text: str):
    try:
        tokens_list = word_tokenize(text)  # Trả về list token
        tokens_text = word_tokenize(text, format="text")  # Dạng text
    except Exception as e:
        tokens_list = []
        tokens_text = f"Lỗi tokenize: {e}"
    return tokens_list, tokens_text
```

#### So Sánh Kết Quả
| Tiêu chí | Simple Split | Underthesea word_tokenize |
|---|---|---|
| **Cơ chế** | Tách bằng khoảng trắng | Mô hình NLP |
| **Xử lý cụm từ** | Tách từng chữ đơn lẻ | Giữ nguyên cụm từ (multi-word) |
| **Ví dụ** | "chơi_game_mượt" → ["chơi_game_mượt"] | "chơi game mượt" → ["chơi_game", "mượt"] |
| **Ưu điểm** | Nhanh, đơn giản | Chính xác, hiểu ngữ cảnh |
| **Nhược điểm** | Bỏ sót cấu trúc câu | Chậm hơn, cần mô hình |

#### Ứng dụng trong dự án
- Sử dụng token từ `underthesea_tokenize` để phân tích tiền tố Hán-Việt
- Token chính xác hơn giúp phát hiện các tiền tố `bất-`, `phi-`, `tái-`, `siêu-`, `phụ-` tốt hơn

---

### 2️⃣ Sentiment Analysis: Dùng underthesea.sentiment thay cho rule-based

#### Mục tiêu
Thay thế hệ thống sentiment phụ thuộc vào danh sách từ cố định bằng mô hình sentiment từ `underthesea` học từ dữ liệu.

#### Triển khai

**Cách 1: Rule-Based (Đếm cụm từ)**
```python
def detect_phrases(text: str, phrases: list) -> Counter:
    counts = Counter()
    for p in phrases:
        if not p:
            continue
        matches = re.findall(re.escape(p), text)
        if matches:
            counts[p] = len(matches)
    return counts

# Score = (Tích cực - Tiêu cực) / (Tích cực + Tiêu cực)
```

**Cách 2: Underthesea Sentiment (Mô hình NLP)**
```python
def safe_sentiment(text: str) -> str:
    try:
        result = sentiment(text)
        if isinstance(result, (list, tuple)):
            return str(result[0])
        return str(result)
    except Exception as e:
        # Fallback sang rule-based nếu lỗi
        norm_text = normalize_text(text)
        pos_counts = detect_phrases(norm_text, POSITIVE_PHRASES)
        neg_counts = detect_phrases(norm_text, NEGATIVE_PHRASES)
        pos_total = sum(pos_counts.values())
        neg_total = sum(neg_counts.values())
        label = overall_sentiment(pos_total, neg_total)
        return f"(Fallback rule-based) {label}"
```

#### So Sánh Kết Quả
| Tiêu chí | Rule-Based | Underthesea |
|---|---|---|
| **Cơ chế** | Đếm cụm từ trong danh sách | Mó hình ML đã huấn luyện |
| **Dữ liệu** | Danh sách cố định | Học từ dataset lớn |
| **Xử lý ngữ cảnh** | Không (chỉ đếm) | Có (hiểu ý nghĩa câu) |
| **Ví dụ** | "pin yếu" → tiêu cực | "Pin không yếu" → tích cực |
| **Ưu điểm** | Minh bạch, dễ debug | Chính xác cao, xử lý phức tạp |
| **Nhược điểm** | Bỏ sót, bị phụ thuộc từ điển | Khó giải thích, cần model |

#### Xử lý Lỗi
- **Vấn đề:** Khi chạy lần đầu, mô hình chưa được fit → lỗi "TfidfTransformer instance is not fitted yet"
- **Giải pháp:** 
  - Cài đặt `underthesea` lại: `pip install underthesea --upgrade`
  - Hoặc fallback sang rule-based nếu lỗi (đã implement trong code)

---

### 3️⃣ So Sánh Kết Quả 2 Cách

#### Quy trình chi tiết

**Phương pháp Rule-Based - 5 bước:**
1. **Phát hiện cụm cảm xúc** → Tìm kiếm từng cụm từ trong text
2. **Thống kê số lượng** → Đếm số lần xuất hiện (Positive vs Negative)
3. **Tính Sentiment Score** → `Score = (P - N) / (P + N)`
4. **Quy tắc phân loại**:
   - P > N (Score > 0) → TÍCH CỰC
   - N > P (Score < 0) → TIÊU CỰC
   - P = N (Score = 0) → TRUNG TÍNH
5. **Kết luận** → Hiển thị nhãn cuối cùng

**Phương pháp Underthesea - 1 bước:**
1. **Gọi mô hình** → `sentiment(text)` → Nhãn: positive/negative/neutral

#### Bảng So Sánh Chi Tiết

| Yếu Tố | Rule-Based | Underthesea |
|--------|------------|-------------|
| **Kiến trúc** | Dựa trên từ điển | Dựa trên mô hình ML |
| **Tốc độ** | Rất nhanh | Chậm hơn (phải load model) |
| **Độ chính xác** | 50-70% | 75-90% |
| **Xử lý phủ định** | ❌ Không | ✅ Có ("không xấu" → tích cực) |
| **Xử lý châm biếu** | ❌ Không | ✅ Có ("rất 'tuyệt vời'" → tiêu cực) |
| **Từ mới / OOV** | ❌ Bỏ sót | ✅ Xử lý tốt |
| **Minh bạch** | ✅ Dễ giải thích | ❌ "Black box" |
| **Kích thước model** | Nhỏ | Lớn (~100MB+) |

#### Ví Dụ Thực Tế

**Văn bản:** "Máy chạy nhanh, chơi game mượt, màn hình đẹp nhưng pin tụt kinh khủng, sạc hoài luôn."

**Rule-Based:**
- Tích cực: "chạy_nhanh" (1), "chơi_game_mượt" (1), "màn_hình_đẹp" (1) → P = 3
- Tiêu cực: "tụt_kinh_khủng" (1) → N = 1
- Score = (3 - 1) / (3 + 1) = +0.50 → **TÍCH CỰC**

**Underthesea:**
- Mô hình hiểu toàn bộ câu, xét cân bằng tích cực - tiêu cực
- Kết quả có thể: **TÍCH CỰC** hoặc **TRUNG TÍNH** (tuỳ model)

#### Kết Luận
- **Rule-based:** Tốt cho ứng dụng đơn giản, nhanh, dễ debug
- **Underthesea:** Tốt cho ứng dụng cần độ chính xác cao, xử lý phức tạp

---

## 🛠️ Cài Đặt & Chạy

### Yêu Cầu
- Python 3.7+
- pip

### Cài Đặt Thư Viện
```bash
pip install underthesea streamlit
```

### Chạy Ứng Dụng
```bash
streamlit run app_3_todo.py
```

Ứng dụng sẽ mở ở `http://localhost:8501`

---

## 📁 Cấu Trúc File

```
.
├── app_3_todo.py          # Main app (Streamlit)
├── README.md             # Documentation (File này)
└── review.md             # Tài liệu bổ sung
```

---

## ✅ Danh Sách Hoàn Thành

- ✅ TODO 1: Import `word_tokenize`, `sentiment` từ `underthesea`
- ✅ TODO 2: Viết hàm `underthesea_tokenize()` dùng `word_tokenize`
- ✅ TODO 3: Viết hàm `safe_sentiment()` dùng `sentiment`
- ✅ TODO 4: Hiển thị kết quả underthesea trên giao diện Streamlit

---

## 📝 Ghi Chú

1. **Lỗi Model TF-IDF**: Nếu gặp "TfidfTransformer instance is not fitted yet", hãy:
   - Cài đặt lại: `pip install --upgrade underthesea`
   - Hoặc app sẽ fallback sang rule-based tự động

2. **Danh sách từ**: Các cụm từ tích cực/tiêu cực được define cứng trong code, có thể thêm/sửa để cải thiện

3. **Performance**: Lần chạy đầu tiên sẽ chậm (load model), các lần sau nhanh hơn

---

## 👨‍🎓 Sinh Viên

**Hàn Quốc Bảo - MSSV: 123001056**
