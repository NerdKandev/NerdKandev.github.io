+++
title = "Xử lý đa luồng và Concurrent Programming trong Java"
date = 2025-12-26T19:20:00+07:00
draft = false
featured = true
toc = true
weight = 40
description = "Làm chủ sức mạnh xử lý song song trong Java: Từ Thread cơ bản đến ExecutorService và các vấn đề về Concurrency."

# --- PHẦN TAGS VÀ TOPICS ---
tags = ["Java", "Multithreading", "Concurrency", "Backend", "Performance"]
topics = ["Java Core", "Advanced Programming"]

[images]
  featured_image = "/img/blog/java-threads.png"
+++
![Luồng trong java](/img/blog/java-threads.png)
## 1. Thread là gì?

**Thread (Luồng)** là đơn vị nhỏ nhất của quá trình thực thi trong một ứng dụng. Một chương trình Java có thể chứa nhiều thread chạy song song, chia sẻ cùng bộ nhớ Heap nhưng có Stack riêng biệt.

Đa luồng giúp ứng dụng phản hồi nhanh hơn, tận dụng tối đa CPU đa nhân và xử lý song song các tác vụ nặng như I/O hay Database.



📌 Trong Java, có 2 cách phổ biến để tạo Thread:
- Kế thừa lớp `Thread`
- Triển khai interface `Runnable` (Cách được khuyến nghị vì linh hoạt hơn)

```java
class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Thread is running: " + Thread.currentThread().getName());
    }
}

Thread t = new Thread(new MyTask());
t.start();
```
## 2. Các vấn đề trong lập trình đa luồng
Lập trình đa luồng rất mạnh mẽ nhưng cũng tiềm ẩn những lỗi logic cực kỳ khó debug.

### 2.1 Race Condition
Xảy ra khi nhiều luồng cùng truy cập và thay đổi một dữ liệu dùng chung tại cùng một thời điểm. Kết quả cuối cùng phụ thuộc vào thứ tự thực thi của các luồng.

```Java

class Counter {
    int count = 0;
    void increment() {
        count++; // Thao tác này không phải nguyên tử (atomic)
    }
}
```
📌 Nguyên nhân: count++ thực chất gồm 3 bước: đọc giá trị -> tăng 1 -> ghi lại. Nếu 2 thread cùng đọc giá trị 5, cả hai đều tăng lên 6 và ghi lại, kết quả là 6 thay vì 7.

### 2.2 Deadlock
Tình trạng hai hoặc nhiều luồng chờ đợi lẫn nhau giải phóng tài nguyên vô thời hạn. Ví dụ: Thread A giữ Khóa 1 chờ Khóa 2, trong khi Thread B giữ Khóa 2 lại đang chờ Khóa 1.

### 2.3 Visibility Problem
Xảy ra do cơ chế cache của CPU. Một thread thay đổi giá trị biến trên cache của nó nhưng thread khác vẫn đọc giá trị cũ từ RAM hoặc cache riêng của nó. ➡️ Giải pháp: Sử dụng từ khóa volatile để bắt buộc đọc/ghi trực tiếp từ bộ nhớ chính (RAM).

## 3. Giải pháp với synchronized và Lock API
### 3.1 Keyword synchronized
Đảm bảo tại một thời điểm chỉ có duy nhất một luồng được phép truy cập vào đoạn code quan trọng (Critical Section).

```Java

// Method-level synchronization
synchronized void increment() {
    count++;
}
```
### 3.2 Lock API (java.util.concurrent.locks)
Cung cấp khả năng kiểm soát linh hoạt hơn synchronized, cho phép thử lấy khóa (tryLock) hoặc đặt thời gian chờ (timeout).

```Java

Lock lock = new ReentrantLock();
lock.lock();
try {
    count++;
} finally {
    lock.unlock(); // Luôn giải phóng trong finally
}
```
## 4. Thư viện java.util.concurrent – Vũ khí tối thượng
### 4.1 ExecutorService – Quản lý Thread Pool
Thay vì tạo mới Thread thủ công (tốn tài nguyên), ta sử dụng Thread Pool để tái sử dụng các luồng có sẵn.

```Java

ExecutorService executor = Executors.newFixedThreadPool(4);
executor.submit(() -> System.out.println("Task is executing..."));
executor.shutdown();
```
### 4.2 Concurrent Collections
Các cấu trúc dữ liệu được tối ưu cho môi trường đa luồng mà không cần khóa thủ công:

ConcurrentHashMap: Hiệu năng cao hơn nhiều so với Hashtable.

CopyOnWriteArrayList: An toàn cho các danh sách ít ghi nhưng đọc nhiều.

### 4.3 Atomic Classes
Giải quyết Race Condition cho các biến đơn giản mà không cần dùng Lock:

```Java

AtomicInteger count = new AtomicInteger(0);
count.incrementAndGet();
``` 
## 5. Best Practice khi lập trình đa luồng
Ưu tiên dùng ExecutorService thay vì tự khởi tạo new Thread().

Hạn chế phạm vi của synchronized: Chỉ khóa những gì thực sự cần thiết để tránh làm chậm hệ thống.

Sử dụng Immutable Objects: Đối tượng không thể thay đổi thì luôn an toàn trong đa luồng.

Luôn giải phóng Lock: Sử dụng khối finally để đảm bảo unlock() luôn được gọi.

# 6. Kết luận
Đa luồng là "con dao hai lưỡi". Hiểu đúng bản chất về Thread, Lock và Concurrent API không chỉ giúp ứng dụng của bạn chạy nhanh hơn mà còn tránh được những thảm họa về sai lệch dữ liệu.

🎯 Đây là kỹ năng phân biệt giữa một lập trình viên Java cơ bản và một Senior Backend Developer.


---
