أكيد! هذا ملف بصيغة `README.md` منسّق بشكل احترافي، ومرتب لتشاركه على GitHub كمشروع تعليمي لتعلم **MySQL** خطوة بخطوة من البداية حتى المستوى المتقدم:

---

### 📁 `README.md`

````markdown
# 📘 مشروع تعليمي: تعلم MySQL خطوة بخطوة 💻🔥

هذا المشروع هو دليل شامل لتعلم قواعد البيانات باستخدام MySQL بطريقة تطبيقية وعملية من الصفر حتى المستوى المتقدم.

---

## 🧠 المحتويات

1. [مقدمة عن MySQL](#مقدمة-عن-mysql)
2. [الفرق بين Database و Table و Row و Column](#الفرق-بين-database--table--row--column)
3. [إنشاء قاعدة بيانات وجداول](#إنشاء-قاعدة-بيانات-وجداول)
4. [مشروع مصغّر: مكتبة إلكترونية](#مشروع-مصغّر-مكتبة-إلكترونية)
5. [عمليات التعديل (UPDATE) والحذف (DELETE)](#عمليات-التعديل-update-والحذف-delete)
6. [استعلامات احترافية: WHERE, LIKE, ORDER BY](#استعلامات-احترافية-where-like-order-by)
7. [مشروع متقدم: موقع كورسات أونلاين](#مشروع-متقدم-موقع-كورسات-أونلاين)
8. [Joins المتقدمة](#joins-المتقدمة)
9. [GROUP BY مع HAVING](#group-by-مع-having)
10. [قيود البيانات (Constraints)](#قيود-البيانات-constraints)
11. [علاقات متقدمة (1-to-1 و Self-Join)](#علاقات-متقدمة-1-to-1-و-self-join)

---

## مقدمة عن MySQL

- **MySQL** هو نظام إدارة قواعد بيانات علائقية (Relational Database).
- يستعمل لتخزين وتنظيم واسترجاع البيانات بطريقة منظمة وسريعة.
- مجاني ومفتوح المصدر، ويُستخدم في ملايين التطبيقات والمواقع.

---

## الفرق بين Database / Table / Row / Column

| المصطلح | المعنى | مثال |
|--------|--------|-------|
| Database | قاعدة بيانات | `library_db` |
| Table | جدول داخل القاعدة | `books`, `users` |
| Row | صف واحد داخل جدول | كتاب واحد أو مستخدم |
| Column | عمود يمثل نوع بيانات | `title`, `author`, `email` |

---

## إنشاء قاعدة بيانات وجداول

```sql
CREATE DATABASE library_db;
USE library_db;

CREATE TABLE books (
  id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(255),
  author VARCHAR(100),
  published_year INT
);

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100),
  created_at DATE
);
````

---

## مشروع مصغّر: مكتبة إلكترونية

### 🔗 ربط المستخدمين بالكتب (استعارة)

```sql
CREATE TABLE borrowings (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT,
  book_id INT,
  borrowed_at DATE,
  FOREIGN KEY (user_id) REFERENCES users(id),
  FOREIGN KEY (book_id) REFERENCES books(id)
);
```

---

## عمليات التعديل (UPDATE) والحذف (DELETE)

```sql
-- تعديل بريد إلكتروني
UPDATE users SET email = 'new@example.com' WHERE id = 1;

-- حذف مستخدم
DELETE FROM users WHERE id = 2;

-- حذف استعارة
DELETE FROM borrowings WHERE user_id = 1 AND book_id = 3;
```

---

## استعلامات احترافية: WHERE, LIKE, ORDER BY

```sql
-- بحث بالاسم
SELECT * FROM users WHERE name LIKE '%أمين%';

-- ترتيب حسب تاريخ التسجيل
SELECT * FROM users ORDER BY created_at DESC;

-- فلترة سنة نشر الكتب
SELECT * FROM books WHERE published_year >= 2020;
```

---

## مشروع متقدم: موقع كورسات أونلاين

### 📋 الجداول:

* `users`: معلومات المستخدمين
* `courses`: تفاصيل الكورسات
* `enrollments`: تسجيلات المستخدمين في الكورسات
* `ratings`: تقييمات الكورسات

### 🛠️ إنشاء الجداول:

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100),
  created_at DATE
);

CREATE TABLE courses (
  id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(255),
  description TEXT,
  instructor VARCHAR(100),
  created_at DATE
);

CREATE TABLE enrollments (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT,
  course_id INT,
  enrolled_at DATE,
  FOREIGN KEY (user_id) REFERENCES users(id),
  FOREIGN KEY (course_id) REFERENCES courses(id)
);

CREATE TABLE ratings (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT,
  course_id INT,
  rating INT,
  comment TEXT,
  rated_at DATE,
  FOREIGN KEY (user_id) REFERENCES users(id),
  FOREIGN KEY (course_id) REFERENCES courses(id)
);
```

---

## إدخال بيانات واقعية

```sql
INSERT INTO users (name, email, created_at) VALUES
('أمين الزروالي', 'amine@gmail.com', '2025-01-05');

INSERT INTO courses (title, description, instructor, created_at) VALUES
('مقدمة في البرمجة', 'تعلم Python', 'أستاذ طارق', '2025-01-01');

INSERT INTO enrollments (user_id, course_id, enrolled_at) VALUES
(1, 1, '2025-01-10');

INSERT INTO ratings (user_id, course_id, rating, comment, rated_at) VALUES
(1, 1, 4, 'مفيدة جدًا', '2025-01-20');
```

---

## Joins المتقدمة

```sql
-- LEFT JOIN: كل الكورسات حتى اللي ما تسجّل فيها حتى واحد
SELECT courses.title, users.name
FROM courses
LEFT JOIN enrollments ON courses.id = enrollments.course_id
LEFT JOIN users ON enrollments.user_id = users.id;

-- RIGHT JOIN: كل المستخدمين حتى اللي ما تسجلو
SELECT users.name, courses.title
FROM enrollments
RIGHT JOIN users ON enrollments.user_id = users.id
LEFT JOIN courses ON enrollments.course_id = courses.id;
```

---

## GROUP BY مع HAVING

```sql
-- كورسات عندها أكثر من تقييمين
SELECT courses.title, COUNT(*) AS total
FROM ratings
JOIN courses ON ratings.course_id = courses.id
GROUP BY courses.title
HAVING total > 2;
```

---

## قيود البيانات (Constraints)

```sql
CREATE TABLE enrollments (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT NOT NULL,
  course_id INT NOT NULL,
  enrolled_at DATE DEFAULT CURRENT_DATE,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
  FOREIGN KEY (course_id) REFERENCES courses(id) ON DELETE CASCADE
);
```

---

## علاقات متقدمة (1-to-1 و Self-Join)

```sql
-- علاقة 1 إلى 1: ملف تعريفي
CREATE TABLE user_profiles (
  user_id INT PRIMARY KEY,
  bio TEXT,
  profile_picture VARCHAR(255),
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

-- Self Join: مدير المستخدم
CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  manager_id INT,
  FOREIGN KEY (manager_id) REFERENCES employees(id)
);
```

---

## ✨ القادم...

🚀 ربط قاعدة البيانات مع الواجهة:

* باستخدام **Flutter** أو **PHP**
* بناء لوحة تحكم، تسجيل دخول، عرض الكورسات، والتقييمات

---

## 📌 ملاحظات

* هذا المشروع تم تطويره بأسلوب تدريجي، مناسب للتعلم الذاتي.
* يمكن التوسيع عليه ليصبح نظام إدارة كورسات حقيقي متكامل.

---

## 🧑‍💻 كاتب المشروع

**المبرمج المتعلّم 🔥**

> تعلم وعلّم غيرك، خطوة بخطوة 🚀

```

---

لو بغيتي نضيف لوجو، صور، أو روابط، نقدر نحسن الملف أكثر.

واش تبغي نجهز ليك نسخة `.zip` أو نكتب ليك أوامر `git init` و `push` باش تنشرها بسرعة؟ 😄
```
