+++
title = "Java 8+ Features: Làm chủ Stream API và Lambda Expression"
date = 2025-12-26T21:15:00+07:00
draft = false
featured = true
toc = true
weight = 50
description = "Khám phá kỷ nguyên lập trình hàm trong Java: Cách sử dụng Stream API, Lambda và Parallel Streams để tối ưu hóa mã nguồn."

# --- PHẦN TAGS VÀ TOPICS ---
tags = ["Java", "Stream API", "Lambda", "Functional Programming", "Backend"]
topics = ["Java Core", "Modern Java"]

[images]
  featured_image = "/img/blog/java-8-streams.png"
+++
![Java 8 Streams](/img/blog/java-8-streams.png)
## 1. Kỷ nguyên lập trình hàm (Functional Programming)

Java 8 đánh dấu một bước ngoặt lớn khi giới thiệu các khái niệm **Functional Programming** như Lambda Expression, Functional Interface, và Stream API. Lập trình hàm giúp mã nguồn ngắn gọn, giảm bớt các trạng thái (state) dư thừa và dễ bảo trì hơn so với mô hình OOP thuần túy trước đây.

### 1.1 Lambda Expression
Lambda Expression cho phép bạn truyền **hành vi (behavior)** như một tham số, giúp loại bỏ các lớp ẩn danh (anonymous classes) dài dòng.

```java
// Trước Java 8: Dùng Anonymous Class
Collections.sort(list, new Comparator<Integer>() {
    public int compare(Integer a, Integer b) {
        return a - b;
    }
});
// Java 8+: Dùng Lambda Expression
Collections.sort(list, (a, b) -> a - b);
```
### 1.2 Functional Interface
Là interface chỉ có duy nhất một phương thức trừu tượng. Một số Functional Interface phổ biến sẵn có trong gói java.util.function:Predicate<T>: Kiểm tra điều kiện (trả về boolean).Function<T, R>: Biến đổi dữ liệu từ kiểu T sang R.Consumer<T>: Thực thi hành động trên dữ liệu mà không trả về kết quả.
## 2. Thao tác dữ liệu với Stream API
Stream API cho phép xử lý tập hợp dữ liệu theo kiểu pipeline (đường ống), tương tự như cách truy vấn trong SQL nhưng được thực hiện trực tiếp trong bộ nhớ.
### 2.1 Cấu trúc của Stream Pipeline
Một Stream pipeline chuẩn gồm 3 phần:
* **Source:** Nguồn dữ liệu (như List, Set, Array).
* **Intermediate Operations:** Các thao tác trung gian (filter, map, sorted). Đặc điểm của chúng là Lazy Evaluation - chỉ thực thi khi có Terminal Operation.
* **Terminal Operation:** Thao tác kết thúc để trả về kết quả (collect, forEach, count).
```java
JavaList<String> result = users.stream()
    .filter(u -> u.getAge() >= 18)    // Lọc người trưởng thành
    .map(User::getName)              // Lấy tên
    .collect(Collectors.toList());   // Gom vào danh sách mới
```
### 2.2 Các toán tử Stream phổ biến
* **Filter**: Lọc dữ liệu theo điều kiện.
* **Map**: Biến đổi dữ liệu (Ví dụ: Chuyển chuỗi thành chữ hoa).
* **Sorted**: Sắp xếp các phần tử.
* **Distinct**: Loại bỏ các phần tử trùng lặp.
## 3. Lợi ích của Parallel Streams
Parallel Stream tự động chia nhỏ dữ liệu để xử lý song song trên nhiều nhân CPU bằng cách sử dụng `ForkJoinPool`.
```java
Javalist.parallelStream()
    .filter(x -> x > 10)
    .forEach(System.out::println);
```
## 3.1 Khi nào nên dùng?
* **Dữ liệu lớn**: Tập dữ liệu đủ lớn để việc chia nhỏ và gộp lại không tốn thời gian hơn xử lý tuần tự.
* **Nhiều core CPU**: Tận dụng được phần cứng hiện đại.
* **Tránh dùng**: Khi các tác vụ có liên quan đến I/O (Database, File) hoặc dữ liệu nhỏ.
## 4. So sánh Stream vs Vòng lặp truyền thống
So sánh Stream API và Vòng lặp (Loop) truyền thống

| Tiêu chí | Stream API | For-loop truyền thống |
| :--- | :--- | :--- |
| **Độ ngắn gọn** | ⭐⭐⭐⭐⭐ | ⭐⭐ |
| **Dễ đọc** | (Declarative) | ⭐⭐⭐ (Imperative) |
| **Hiệu năng** | Tốt (Có Parallel Stream) | Rất cao (Xử lý trực tiếp) |
| **Khả năng Debug** |  Khó hơn (Lambda/Pipeline) | Dễ dàng (Step-by-step) |
## 5. Best Practice khi dùng Stream
Không thay đổi trạng thái bên ngoài: Tránh các hiệu ứng lề (side-effects) bên trong Stream để đảm bảo an toàn luồng.Dùng Method Reference: Ưu tiên User::getName thay vì u -> u.getName() để code sạch hơn.Đóng Resource: Nếu dùng Stream với I/O (như Files.lines), hãy dùng try-with-resources.
## 6. Kết luận
Stream API và Lambda không chỉ là những cú pháp mới, chúng thay đổi tư duy lập trình của Java Developer theo hướng hiện đại, an toàn và hiệu quả hơn. Đây là nền tảng quan trọng để tiếp cận các công nghệ như Spring Boot hay Reactive Programming.