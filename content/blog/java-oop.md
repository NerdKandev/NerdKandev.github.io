+++
title = "Lập trình hướng đối tượng nâng cao: Từ Abstract Class đến Interface"
date = 2025-12-26T14:30:00+07:00
draft = false
featured = true
toc = true
weight = 20
description = "Tìm hiểu sâu về tính trừu tượng trong Java, cách phân biệt và sử dụng Abstract Class và Interface hiệu quả trong dự án thực tế."

# --- PHẦN TAGS VÀ TOPICS ---
tags = ["Java", "OOP", "Abstraction", "Interface", "Backend"]
topics = ["Java Core", "Object-Oriented Programming"]

[images]
  featured_image = "/img/blog/java-oop.png"
+++
![Sự khác biết giữa abstract và interface](/img/blog/java-oop.png)
## 1. Bản chất của tính Trừu tượng (Abstraction)
Tính trừu tượng (Abstraction) là một trong 4 trụ cột của lập trình hướng đối tượng (OOP), cho phép lập trình viên tập trung vào **cái gì** hệ thống làm được thay vì **nó làm như thế nào**.

Nói cách khác, trừu tượng giúp ẩn đi các chi tiết triển khai phức tạp và chỉ phơi bày những hành vi cần thiết cho bên sử dụng.

### Vì sao trừu tượng quan trọng?
Trong hệ thống thực tế:
- Người dùng không cần biết động cơ xe hoạt động thế nào để lái xe.
- Lập trình viên không cần biết chi tiết database để gọi API.

**👉 Trừu tượng giúp:**
- Giảm độ phức tạp của mã nguồn.
- Tăng khả năng bảo trì và mở rộng.
- Giảm sự phụ thuộc (coupling) giữa các module.

### Trừu tượng trong Java thể hiện qua đâu?
Trong Java, trừu tượng được thể hiện thông qua hai công cụ chính:
1. **Abstract Class (Lớp trừu tượng)**
2. **Interface (Giao diện)**

Cả hai đều giúp định nghĩa hành vi chung, nhưng mục đích sử dụng lại khác nhau hoàn toàn.



## 2. So sánh Abstract Class và Interface
Đây là phần rất hay xuất hiện trong phỏng vấn Java và cũng là chỗ nhiều người mới học dễ nhầm lẫn nhất.

### 2.1 Khi nào dùng Abstract Class?
Abstract Class phù hợp khi các lớp có mối quan hệ **IS-A** (là một) rõ ràng, cần chia sẻ logic chung hoặc trạng thái (state) dùng chung.

**Đặc điểm chính:**
- Có thể chứa cả phương thức trừu tượng và phương thức đã có nội dung (implementation).
- Có thể chứa các biến thực thể (instance variables).
- Một class chỉ có thể kế thừa duy nhất một abstract class.

**Ví dụ thực tế:**
```java
public abstract class Enemy {
    protected int health;

    public abstract void attack();

    public void takeDamage(int damage) {
        health -= damage;
    }
}
```
---
### 2.2 Sức mạnh của Interface
Interface đại diện cho một khả năng (capability), không phải bản chất của đối tượng. Nó thường được dùng để định nghĩa các "hợp đồng" mà các lớp khác phải tuân thủ.

Đặc điểm chính:

Không chứa trạng thái (trừ hằng số public static final).

Một class có thể triển khai (implement) nhiều Interface cùng lúc.

Từ Java 8, Interface có thể chứa default method và static method.

Ví dụ về khả năng bay:

```java
public interface Flyable {
    void fly();
}
```
👉 Bird (Con chim) và Airplane (Máy bay) không cùng bản chất, nhưng đều có cùng khả năng là Flyable.

[Image comparing Abstract Class vs Interface usage in software architecture]

# 3. Tổng kết: Chọn công cụ nào?
Chọn Abstract Class khi bạn muốn xây dựng một bộ khung chung cho các đối tượng có họ hàng gần gũi.

Chọn Interface khi bạn muốn định nghĩa một tính năng có thể áp dụng cho bất kỳ lớp nào, không quan trọng chúng thuộc cây gia phả nào.



