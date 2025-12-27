+++
title = "Closure & Prototype trong JavaScript – Nền tảng quyền lực nhưng dễ bị hiểu sai"
date = 2025-12-26T23:15:00+07:00
draft = false
featured = true
toc = true
weight = 70
description = "Đi sâu vào cơ chế Lexical Scope, Closure và hệ thống kế thừa Prototype để hiểu cách JavaScript quản lý bộ nhớ và đối tượng."

# --- PHẦN TAGS VÀ TOPICS ---
tags = ["JavaScript", "Closure", "Prototype", "OOP", "Frontend"]
topics = ["Modern JavaScript", "Programming Concepts"]

+++

## 1. Closure là gì?

**Closure** là khả năng của một hàm ghi nhớ và truy cập được các biến nằm ngoài phạm vi (scope) của nó, ngay cả khi hàm bên ngoài đã thực thi xong. Nói cách khác, một hàm luôn “đi kèm” với lexical scope nơi nó được khai báo.

### Ví dụ cơ bản:
```javascript
function outer() {
  let count = 0;

  return function inner() {
    count++;
    return count;
  };
}

const counter = outer();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```
Mặc dù outer() đã chạy xong, biến count vẫn tồn tại trong bộ nhớ vì inner() đang giữ tham chiếu đến nó. Đây chính là sức mạnh của Closure.

## 2. Vì sao Closure tồn tại?
Closure tồn tại nhờ Lexical Scope:

Scope được xác định tại thời điểm viết code (static).

Không phụ thuộc vào việc hàm được gọi ở đâu.

```JavaScript

let x = 10;
function foo() {
  console.log(x);
}

function bar() {
  let x = 20;
  foo();
}


bar(); // Kết quả là 10, không phải 20
```
👉 foo() vẫn truy cập x = 10 vì scope của nó được “đóng lại” ngay khi được khai báo trong môi trường chứa x = 10.

## 3. Kế thừa nguyên mẫu (Prototype) là gì?
Khác với Java hay C#, JavaScript không kế thừa dựa trên Class mà dựa trên Prototype. Mỗi object trong JavaScript đều có một thuộc tính ẩn gọi là [[Prototype]] (có thể truy cập qua __proto__).

```JavaScript

const animal = { eats: true };
const dog = { barks: true };

dog.__proto__ = animal; 
console.log(dog.eats); // true
```
👉 Khi không tìm thấy thuộc tính trong object hiện tại, JavaScript sẽ tìm ngược lên Prototype chain.

## 4. Class trong JS thực chất là gì?
Từ ES6, JavaScript có cú pháp class, nhưng đây thực chất chỉ là Syntactic Sugar (cú pháp làm đẹp) cho Prototype.

```JavaScript

class Person {
  constructor(name) {
    this.name = name;
  }
  sayHello() {
    console.log(`Hi, I'm ${this.name}`);
  }
}
```
Thực tế, phương thức sayHello nằm trong Person.prototype. Mọi thực thể (instance) tạo ra từ class này đều dùng chung một bản duy nhất trong bộ nhớ, giúp tối ưu hiệu năng.

## 5. Ứng dụng thực tế của Closure
### 5.1 Tạo biến "Private"
Trước khi có #private field, Closure là cách duy nhất để tạo tính đóng gói.

```JavaScript

function createUser(name) {
  let password = "secret_password";
  return {
    getName: () => name,
    checkPassword: (pwd) => pwd === password
  };
}
const user = createUser("John");
console.log(user.password); // undefined (không thể truy cập trực tiếp)
```
### 5.2 Closure trong vòng lặp (Bẫy kinh điển)
```JavaScript

for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1000);
}
// Kết quả: 3 3 3 (vì var có function scope)

// Giải pháp với ES6:
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1000);
}
// Kết quả: 0 1 2 (let tạo block scope cho mỗi lần lặp)
```
## 6. Khi nào nên cẩn thận với Closure?
Closure giữ tham chiếu đến biến, điều này có thể gây Memory Leak (rò rỉ bộ nhớ) nếu:

Giữ tham chiếu đến các DOM node lớn không cần thiết.

Không gỡ bỏ (cleanup) các event listener trong các ứng dụng Single Page (SPA).

```JavaScript

function attach() {
  const element = document.getElementById("huge-btn");
  element.addEventListener("click", () => {
    console.log(element.id); // Closure giữ lại toàn bộ element trong bộ nhớ
  });
}
```