# LongEmbed – Demo Retrieval với Long Document Embeddings

💡 **Mục tiêu demo:** thử nghiệm khả năng mở rộng embedding cho mô hình E5 trong các tác vụ truy xuất thông tin (retrieval) trên văn bản dài, không cần huấn luyện lại mô hình – chỉ thay đổi positional embedding.

---

## 🚀 1. Giới thiệu
Trong NLP, nhiều tác vụ yêu cầu mô hình đọc và tìm kiếm thông tin từ văn bản rất dài. Tuy nhiên, đa số embedding models chỉ hỗ trợ ~512 token, gây giới hạn khả năng truy xuất.

**Long Embedding (LongEmbed)** giúp mở rộng độ dài ngữ cảnh lên đến 4k–32k tokens, từ đó cải thiện khả năng tìm kiếm đoạn văn liên quan.

Demo này tập trung thử nghiệm mô hình **E5-Base-4k**, so sánh kết quả retrieval trước và sau khi mở rộng positional embedding.

---

## 📦 2. Bộ dữ liệu sử dụng
Demo sử dụng 2 synthetic datasets từ benchmark LongEmbed:

| Dataset | Mô tả | Thành phần |
|---------|-------|------------|
| **LEMBNeedleRetrieval** | Document chứa thông tin nhỏ/hiếm ("needle") | Corpus / Queries / Qrels |
| **LEMBPasskeyRetrieval** | Document chứa passkey cần truy xuất chính xác | Corpus / Queries / Qrels |

📌 Synthetic dataset giúp mô phỏng môi trường văn bản dài với khả năng điều chỉnh độ dài document.

---

## 🧠 3. Mô hình sử dụng
| Model | Mô tả |
|-------|------|
| **E5-Base-4k** | Mở rộng embedding lên 4.096 position IDs |
| **E5-RoPE-Base** | Không chạy trong demo do giới hạn tài nguyên |

---

## 🧪 4. Cách cài đặt & chạy demo

### Cài đặt môi trường
```bash
git clone https://github.com/dwzhu-pku/LongEmbed.git
cd LongEmbed
pip install -r requirements.txt
```

### Tải dữ liệu
```python
from datasets import load_dataset
needle = load_dataset("dwzhu/LongEmbed", name="needle", split="corpus")
passkey = load_dataset("dwzhu/LongEmbed", name="passkey", split="corpus")
```

### Chạy đánh giá
```python
from mteb import MTEB
eval = MTEB(tasks=["LEMBNeedleRetrieval","LEMBPasskeyRetrieval"])
eval.run(model)
```

---

## 📊 5. Kết quả Demo
📌 Đánh giá dựa trên NDCG, MAP, Recall, MRR, Precision

### 🔹 Needle Retrieval – 1024 token
| Metric | @1 | @5 | @10 | @100 | @1000 |
|--------|----|----|-----|------|-------|
| Chạy bình thường | 0.66 | 0.80 | 0.82 | 0.83 | 0.83 |
| Mở rộng embedding | **0.70** | **0.82** | **0.84** | **0.85** | **0.85** |

### 🔹 Passkey Retrieval – 512 token
| Metric | @1 | @5 | @10 | @100 | @1000 |
|--------|----|----|-----|------|-------|
| Chạy bình thường | 0.70 | 0.85 | 0.86 | 0.86 | 0.86 |
| Mở rộng embedding | **1.00** | **1.00** | **1.00** | **1.00** | **1.00** |

📌 Nhận xét nhanh:  
- Long embedding cải thiện hiệu quả retrieval đáng kể  
- Với Passkey Retrieval, kết quả đạt **100%** toàn bộ top-k  

---

## 🏁 6. Kết luận
- Long embedding giúp mô hình duy trì khả năng biểu diễn trên văn bản dài
- Không thay đổi kiến trúc mô hình – chỉ cải thiện positional encoding
- Hiệu quả đặc biệt mạnh đối với bài toán chứa thông tin "ẩn sâu" (passkey)

---

## 📚 Tham khảo
LongEmbed – Extending Embedding Models for Long Context Retrieval – arXiv:2404.12096
