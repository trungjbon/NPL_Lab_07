# 1. Mô tả code
## Phân tích câu và Trực quan hóa
- nlp = spacy.load("en_core_web_md") -> load mô hình spaCy (tokenization, POS, dependency, NER, ...).
- Cho từng text gọi doc = nlp(text) để có đối tượng Doc chứa token-level annotations.
- displacy.serve(doc, style="dep") -> khởi server nhỏ (localhost) để trực quan hóa cây phụ thuộc (dependency tree).

## Truy cập các thành phần trong cây phụ thuộc
- Lặp qua for token in doc: và in token.text, token.dep_, token.head.text, token.head.pos_, list(token.children).

## Duyệt cây phụ thuộc để trích xuất thông tin
- Tìm chủ ngữ và tân ngữ của một động từ
  - Duyệt tokens, chọn token có token.pos_ == "VERB".
  - Với mỗi child của verb, kiểm tra child.dep_ == "nsubj" để lấy subject, child.dep_ == "dobj" để lấy object.
  - Nếu tìm được cả subject và object thì in triplet (subject, verb, object).
- Tìm các tính từ bổ nghĩa cho một danh từ
  - Duyệt tokens có token.pos_ == "NOUN", lấy các child có child.dep_ == "amod" -> thu danh sách tính từ bổ nghĩa cho danh từ đó.
 
## Bài tập tự luyện
- Tìm động từ chính của câu
  - Tìm token có dep_ == "ROOT" và trả về -> đây thường là động từ chính của câu. 
- Trích xuất các cụm danh từ (Noun Chunks)
  - Cho tập modifiers = {"det","amod","compound","nummod"}.
  - Với mỗi token là NOUN hoặc PROPN, lấy modifiers ở bên trái (index < token.i), ghép với token và join thành cụm.
- Tìm đường đi ngắn nhất trong cây
  - Tìm token có token.text == target.
  - Leo lần lượt từ token lên token.head cho đến khi tmp == tmp.head (ROOT).
  - Trả về list token trên đường đi.
 
# Hướng dẫn chạy code
- Chạy code trong file để cho ra kết quả
```
Lab_07\lab7_dependency_parsing.ipynb
```
