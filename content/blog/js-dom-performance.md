+++
title = "Tối ưu hiệu năng Frontend – DOM, Virtual DOM và Event Delegation"
date = 2025-12-26T23:45:00+07:00
draft = false
featured = true
toc = true
weight = 80
description = "Tìm hiểu cách trình duyệt render giao diện, sự khác biệt giữa Reflow/Repaint và cách Virtual DOM cùng Event Delegation giúp website chạy mượt mà hơn."

# --- PHẦN TAGS VÀ TOPICS ---
tags = ["JavaScript", "DOM", "Virtual DOM", "Web Performance", "Frontend"]
topics = ["Frontend Optimization", "Web Development"]

+++

## 1. DOM là gì và tại sao nó chậm?

**DOM (Document Object Model)** là cấu trúc dạng cây đại diện cho toàn bộ HTML của trang web. Trình duyệt sử dụng DOM để render giao diện, gắn CSS và lắng nghe sự kiện.

Rất nhiều website chậm không phải vì logic phức tạp, mà vì **thao tác với DOM sai cách**. Mỗi khi DOM thay đổi, trình duyệt phải thực hiện chuỗi tác vụ tốn kém:
1.  **Reflow (Layout):** Tính toán lại vị trí, kích thước các phần tử.
2.  **Repaint (Paint):** Vẽ lại các pixel lên màn hình.
3.  **Composite:** Ghép các lớp (layer) lại với nhau.



### Ví dụ thao tác DOM gây chậm:
```javascript
const list = document.getElementById("list");
for (let i = 0; i < 1000; i++) {
  const li = document.createElement("li");
  li.textContent = i;
  list.appendChild(li); // Mỗi lần gọi appendChild có thể gây một đợt Reflow
}
```
Cách tối ưu cơ bản với `DocumentFragment`:
```JavaScript
const fragment = document.createDocumentFragment();
for (let i = 0; i < 1000; i++) {
  const li = document.createElement("li");
  li.textContent = i;
  fragment.appendChild(li);
}
list.appendChild(fragment); // Chỉ gây ra 1 lần Reflow duy nhất
```
## 2. Reflow vs Repaint – Hiểu đúng để tránh sai lầm
### So sánh Reflow và Repaint

| Khái niệm | Xảy ra khi nào? | Đặc điểm |
| :--- | :--- | :--- |
| **Reflow** (Layout) | Thay đổi kích thước (`width`, `height`), vị trí (`top`, `left`), thay đổi font-size, hoặc thêm/xóa các node DOM. | **Cực kỳ tốn tài nguyên** vì trình duyệt phải tính toán lại hình học (vị trí/kích thước) của toàn bộ hoặc một phần trang web. |
| **Repaint** (Paint) | Thay đổi các thuộc tính chỉ liên quan đến diện mạo như: `color`, `background-color`, `visibility`, `outline`. | **Nhẹ hơn Reflow** vì cấu trúc layout không đổi, trình duyệt chỉ cần "vẽ" lại các pixel lên màn hình. |Lưu ý: Reflow luôn kéo theo Repaint, nhưng Repaint không nhất thiết gây ra Reflow.
## 3. Virtual DOM – Giải pháp của Framework hiện đại
Virtual DOM là một bản sao nhẹ (in-memory) của DOM thật. Thay vì cập nhật DOM trực tiếp mỗi khi state thay đổi, các Framework như React sẽ:
1. Cập nhật Virtual DOM mới.
2. So sánh (Diffing) với Virtual DOM cũ.
3. Tìm ra các thay đổi tối thiểu (Patching).
4. Cập nhật DOM thật một lần duy nhất.
**Vì sao Virtual DOM nhanh?**
Thực tế Virtual DOM không nhanh hơn DOM thật về mặt lý thuyết, nhưng nó nhanh hơn ở chỗ nó tối ưu số lần thao tác. Nó giúp gom nhiều thay đổi vào một đợt cập nhật (Batching), tránh các đợt Reflow thừa thãi.
## 4. Event Delegation – Tối ưu bộ nhớ
Khi bạn có một danh sách hàng trăm phần tử, việc gán addEventListener cho từng phần tử sẽ gây tốn bộ nhớ nghiêm trọng.
Cách làm cũ:
```JavaScript
const items = document.querySelectorAll(".item");
items.forEach(item => {
  item.addEventListener("click", handleClick);
});
```
Giải pháp: **Event Delegation** (Ủy quyền sự kiện)Tận dụng cơ chế **Event Bubbling** (Sự kiện nổi bọt từ con lên cha), ta chỉ cần gán một listener duy nhất cho phần tử cha.
```JavaScript
document.getElementById("list").addEventListener("click", (e) => {
  if (e.target.matches(".item")) {
    console.log("Item clicked:", e.target.textContent);
  }
});
```
## 5. Lợi ích của Event Delegation
* **Giảm Memory Footprint:** Chỉ dùng 1 listener thay vì hàng trăm.
* **Tự động xử lý:** Các phần tử được thêm mới vào danh sách sau này vẫn sẽ tự động nhận sự kiện mà không cần gán lại.
* **Code sạch hơn:** Dễ quản lý và bảo trì.
## 6. Kết luận
1. Hạn chế thao tác trực tiếp vào DOM thật.
2. Hiểu về Reflow/Repaint để viết CSS/JS tối ưu.
3. Sử dụng Virtual DOM (qua framework) hoặc Event Delegation (JS thuần) để bảo vệ bộ nhớ và CPU.