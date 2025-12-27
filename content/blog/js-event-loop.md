+++
title = "JavaScript Event Loop – Hiểu đúng để làm chủ bất đồng bộ"
date = 2025-12-26T22:30:00+07:00
draft = false
featured = true
toc = true
weight = 60
description = "Khám phá cơ chế vận hành của Event Loop, sự khác biệt giữa Microtasks và Macrotasks để viết code JavaScript hiệu quả, không gây nghẽn ứng dụng."

# --- PHẦN TAGS VÀ TOPICS ---
tags = ["JavaScript", "Event Loop", "Asynchronous", "Frontend", "NodeJS"]
topics = ["Modern JavaScript", "Web Performance"]

[images]
  featured_image = "/img/blog/js-event-loop.png"
+++
![Sự kiện vòng lặp trong Javascript](/img/blog/js-event-loop.png)
## 1. JavaScript là Single-threaded, nhưng không hề đơn giản

JavaScript thường bị hiểu lầm là “chậm” vì chỉ chạy trên một luồng duy nhất. Thực tế, chính mô hình **single-threaded** kết hợp với **non-blocking I/O** đã giúp JavaScript trở thành nền tảng cốt lõi của web hiện đại.

JavaScript chỉ có một **Call Stack**, đồng nghĩa với việc tại một thời điểm, chỉ có một đoạn code được thực thi. Tuy nhiên, các tác vụ nặng như `setTimeout`, HTTP request, hay File I/O sẽ được ủy quyền cho môi trường thực thi (Browser hoặc Node.js) xử lý.

```javascript
console.log("Start");

setTimeout(() => {
  console.log("Timeout");
}, 1000);

console.log("End");
```
Kết quả:StartEndTimeout (sau 1 giây)Mặc dù setTimeout mất 1 giây, JavaScript không chờ mà tiếp tục thực thi code còn lại. Đây chính là nền tảng của Non-blocking I/O.
## 2. Event Loop là gì?
Event Loop là cơ chế liên tục theo dõi trạng thái của Call Stack và các Task Queue, nhằm quyết định khi nào một task sẽ được đưa vào thực thi.
### Các thành phần chính:
* **Call Stack:** Nơi thực thi code synchronous (LIFO).
* **Web APIs / Node APIs:** Xử lý tác vụ bất đồng bộ.
* **Task Queues:** Bao gồm Microtask Queue và Macrotask Queue (Callback Queue).
## 3. Chu trình hoạt động của Event Loop
Event Loop hoạt động theo vòng lặp không ngừng:
1. Thực thi toàn bộ code synchronous trong Call Stack.
2. Khi Call Stack trống: Thực thi toàn bộ Microtasks hiện có.
3. Sau đó lấy duy nhất một Macrotask để chạy.
4. Lặp lại chu trình.
👉 Quy tắc vàng: Microtasks luôn được ưu tiên xử lý trước Macrotasks sau khi code chính chạy xong.
## 4. Microtasks vs Macrotasks
Sự khác biệt về độ ưu tiên giữa hai loại hàng đợi này là chìa khóa để debug các lỗi bất đồng bộ phức tạp.
### So sánh Microtasks và Macrotasks trong JavaScript

| Đặc điểm | Microtasks | Macrotasks (Callback Queue) |
| :--- | :--- | :--- |
| **Độ ưu tiên** | ⭐⭐⭐⭐⭐ (Cao nhất) | ⭐⭐⭐ (Thấp hơn) |
| **Ví dụ tiêu biểu** | `Promise.then/catch/finally`, `queueMicrotask`, `MutationObserver` | `setTimeout`, `setInterval`, `DOM events`, `I/O callbacks`, `setImmediate` (Node.js) |
| **Thời điểm thực thi** | Ngay sau khi Call Stack trống và trước khi render lại giao diện. | Sau khi toàn bộ Microtask Queue đã được xử lý xong. |
| **Tần suất xử lý** | Thực thi **toàn bộ** hàng đợi trong một vòng lặp (loop). | Thực thi **từng cái một** trong mỗi vòng lặp. |
Ví dụ minh họa:
```java
JavaScriptconsole.log("A");

setTimeout(() => {
  console.log("B");
}, 0);

Promise.resolve().then(() => {
  console.log("C");
});

console.log("D");
```
Kết quả: A -> D -> C -> B.
* **"A" và "D"** chạy ngay vì là synchronous.
* **"C"** (Microtask) chạy trước khi Event Loop lấy "B" (Macrotask) ra khỏi queue.
## 5. Cảnh báo: Microtask có thể block ứng dụng
Vì Microtask luôn được ưu tiên, nếu bạn liên tục tạo ra các Microtask mới, trình duyệt sẽ không bao giờ có cơ hội xử lý Macrotask hay vẽ lại giao diện 
```java
(Render).JavaScriptfunction infiniteMicrotask() {
  Promise.resolve().then(infiniteMicrotask);
}
// infiniteMicrotask(); // CẨN THẬN: Đoạn code này sẽ làm treo trình duyệt/UI
```
## 6. Khi nào nên dùng Microtask hay Macrotask?
Trường hợpGiải phápXử lý logic phụ thuộc kết quả asyncPromise / async-await (Microtask)Delay UI, tạo hiệu ứng chuyển cảnhsetTimeout / requestAnimationFrame (Macrotask)Xử lý chuỗi logic nhỏ, cần chạy ngayqueueMicrotask
## 7. Kết luận
JavaScript là single-threaded nhưng cực kỳ mạnh mẽ nhờ Event Loop.Hiểu đúng sự ưu tiên của Microtask giúp bạn tránh được các lỗi logic "ma" khi làm việc với API hay state management.Luôn nhớ: Sync code > Microtasks > 1 Macrotask > Render.