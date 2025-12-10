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
 
# 2. Hướng dẫn chạy code
- Chạy code trong file để cho ra kết quả
```
Lab_07\lab7_dependency_parsing.ipynb
```

# 3. Phân tích kết quả
## Phân tích câu và Trực quan hóa
- "jumps" được xác định đúng là ROOT, làm động từ trung tâm của câu.
- "fox" là nsubj (chủ ngữ) của "jumps", và các từ "The", "quick", "brown" được gắn đúng vào "fox" với quan hệ det, amod.
- Cụm giới từ "over the lazy dog" được phân tích chuẩn:
  - "over" là prep của "jumps"
  - "dog" là pobj của "over"
  - "the" và "lazy" bổ nghĩa cho "dog" với det, amod
 
## Truy cập các thành phần trong cây phụ thuộc
- ROOT của câu là "looking" -> toàn bộ câu xoay quanh hành động đang xem xét.
- Cấu trúc chính: Apple (nsubj) + is (aux) + looking (ROOT) + at (prep) + buying (pcomp) -> thể hiện mô hình "Apple đang xem xét việc mua…".
- Cụm "buying startup" là bổ sung cho giới từ "at", trong đó:
  - "startup" là dobj của "buying".
  - "U.K." là compound bổ nghĩa cho "startup".
- Giá trị tiền được phân tích rất chi tiết:
  - "for" (prep) -> "billion" (pobj), với "$" và "1" bổ nghĩa cho "billion".

## Duyệt cây phụ thuộc để trích xuất thông tin
- Tìm chủ ngữ và tân ngữ của một động từ:
  - (cat, chased, mouse): diễn đạt chính xác cấu trúc SVO cơ bản.
  - (dog, watched, them): nhận dạng đúng đại từ làm tân ngữ.
 
- Tìm các tính từ bổ nghĩa cho một danh từ:
  - cat -> big, fluffy, white: đầy đủ các tính từ mô tả.
  - mat -> warm: đúng một tính từ bổ nghĩa.

## Bài tập tự luyện
- Tìm động từ chính của câu:
  - ROOT = looking -> xác định đúng động từ trung tâm của câu phức.
    
- Trích xuất các cụm danh từ (Noun Chunks):
  - Nhóm thực thể: ['Apple', 'U.K.', 'U.K. startup'] -> nhận diện entity và kết hợp hợp lý (Apple, quốc gia U.K., và cụm danh từ "U.K. startup").

- Tìm đường đi ngắn nhất trong cây:
  - Chuỗi phụ thuộc đi lên: [startup, buying, at, looking] phản ánh đúng đường dependency từ danh từ đến ROOT, cho thấy việc lần theo token.head lặp lại hoạt động chính xác.
