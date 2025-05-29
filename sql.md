ممتاز جدا! فكرة رائعة أنك توثق هاد الرحلة التعليمية. غادي نجمع ليك كلشي فملف Markdown مرتب ومنظم، تقدر تنسخو مباشرة وتحطو فـ GitHub.

غادي يكون فيه:

1.  مقدمة بسيطة.
2.  المفاهيم الأساسية (Database, Table, Row, Column).
3.  الأوامر الأساسية (CREATE, USE, INSERT, SELECT).
4.  المشروع الأول: مكتبة `my_library` مع علاقة Many-to-Many.
5.  التعديل، الحذف، والبحث (UPDATE, DELETE, WHERE, LIKE, ORDER BY).
6.  المشروع الثاني: منصة كورسات أونلاين (Constraints, Joins, Group By).
7.  الـ Joins المتقدمة (LEFT, RIGHT) و GROUP BY مع HAVING.
8.  حماية البيانات (DEFAULT, ON DELETE) والعلاقات المتقدمة (1-to-1, Self-Join).
9.  خاتمة.

يلاه نبداو:

```markdown
# رحلتي في تعلم MySQL بالدارجة المغربية 🚀🇲🇦

هذا المستودع كيوثق الرحلة ديالي فتعلم أساسيات ومفاهيم متقدمة فقواعد البيانات MySQL، الشرح كان بالدارجة المغربية باش الفهم يكون ساهل وممتع.

## 📚 جدول المحتويات

1.  [مقدمة: شنو هي MySQL؟](#1-مقدمة-شنو-هي-mysql)
2.  [المفاهيم الأساسية](#2-المفاهيم-الأساسية)
    *   [قاعدة البيانات (Database)](#قاعدة-البيانات-database)
    *   [الجدول (Table)](#الجدول-table)
    *   [الصف (Row)](#الصف-row)
    *   [العمود (Column)](#العمود-column)
3.  [البداية: إنشاء قاعدة بيانات وجداول](#3-البداية-إنشاء-قاعدة-بيانات-وجداول)
    *   [إنشاء قاعدة بيانات](#إنشاء-قاعدة-بيانات)
    *   [استخدام قاعدة بيانات](#استخدام-قاعدة-بيانات)
    *   [إنشاء جدول](#إنشاء-جدول)
    *   [إدخال بيانات](#إدخال-بيانات)
    *   [عرض البيانات](#عرض-البيانات)
4.  [المشروع الأول: مكتبة `my_library` (علاقة Many-to-Many)](#4-المشروع-الأول-مكتبة-my_library-علاقة-many-to-many)
    *   [إنشاء قاعدة البيانات والجداول](#إنشاء-قاعدة-البيانات-والجداول-my_library)
    *   [إدخال البيانات](#إدخال-البيانات-في-my_library)
    *   [عرض بيانات الاستعارات](#عرض-بيانات-الاستعارات-مع-join)
5.  [التعديل، الحذف، والبحث المتقدم](#5-التعديل-الحذف-والبحث-المتقدم)
    *   [تحديث البيانات (UPDATE)](#تحديث-البيانات-update)
    *   [حذف البيانات (DELETE)](#حذف-البيانات-delete)
    *   [البحث بشروط (WHERE)](#البحث-بشروط-where)
    *   [البحث بالكلمات (LIKE)](#البحث-بالكلمات-like)
    *   [ترتيب النتائج (ORDER BY)](#ترتيب-النتائج-order-by)
6.  [المشروع الثاني: منصة كورسات أونلاين](#6-المشروع-الثاني-منصة-كورسات-أونلاين)
    *   [تصميم قاعدة البيانات مع Normalization و Constraints](#تصميم-قاعدة-البيانات-مع-normalization-و-constraints)
    *   [إدخال بيانات واقعية](#إدخال-بيانات-واقعية-لمنصة-الكورسات)
    *   [استعلامات عملية (JOIN, AVG, COUNT, GROUP BY)](#استعلامات-عملية-لمنصة-الكورسات)
7.  [الـ Joins المتقدمة وشروط التجميع](#7-الـ-joins-المتقدمة-وشروط-التجميع)
    *   [LEFT JOIN و RIGHT JOIN](#left-join-و-right-join)
    *   [GROUP BY مع HAVING](#group-by-مع-having)
8.  [حماية البيانات والعلاقات المتقدمة](#8-حماية-البيانات-والعلاقات-المتقدمة)
    *   [Constraints إضافية (DEFAULT, ON DELETE)](#constraints-إضافية-default-on-delete)
    *   [علاقة 1-to-1 (جدول `user_profiles`)](#علاقة-1-to-1-جدول-user_profiles)
    *   [علاقة Self-Join (جدول `employees`)](#علاقة-self-join-جدول-employees)
9.  [خاتمة](#9-خاتمة)

---

## 1. مقدمة: شنو هي MySQL؟

MySQL (كتنطق: ماي-إس-كيو-إل) هي نظام لإدارة قواعد البيانات من نوع **علاقاتية (Relational Database Management System - RDBMS)**.
كتسمح ليك تخزن كمّ كبير من البيانات بطريقة منظمة داخل جداول، وكتخليك تقدر تبحث، تضيف، تحدث، أو تحذف البيانات بسهولة باستخدام لغة **SQL (Structured Query Language)**.

**علاش سميتها Relational Database؟**
لأنها كتخزن البيانات فجداول (Tables)، وكتسمح ليك تربط هاد الجداول ببعضياتها من خلال علاقات (Relations).

**علاش كنستخدموها؟**
*   تخزين البيانات بشكل منظم (مستخدمين، منتجات، طلبات...).
*   البحث عن بيانات معينة بسرعة.
*   كتخدم بزاف مع المواقع والتطبيقات (خصوصا مع PHP, Node.js, Python, Java...).

**المميزات:**
*   مجانية ومفتوحة المصدر.
*   سريعة وفعالة.
*   قوية وآمنة.
*   سهلة التعلم (لغة SQL بسيطة).

---

## 2. المفاهيم الأساسية

قبل ما نبداو، خاص نفهمو هاد المصطلحات:

### قاعدة البيانات (Database)
هي الصندوق الكبير اللي كيتخزن فيه كل البيانات ديالك. ممكن تكون فيها جداول متعددة. بحال مكتبة كبيرة فيها رفوف.

### الجدول (Table)
هو مكان داخل قاعدة البيانات فيه معلومات عن شيء معين (مثلاً: جدول للمستخدمين، جدول للمنتجات). الجدول كيتكون من صفوف وأعمدة. بحال رف فالكتبة.

### الصف (Row)
هو سطر واحد فالجدول، وكيمثل سجل واحد أو معلومة كاملة عن كيان واحد (مثلاً: مستخدم واحد، منتج واحد). بحال كتاب واحد فالرف.

### العمود (Column)
هو خاصية أو نوع بيانات معين داخل الجدول (مثلاً: عمود للاسم، عمود للعمر، عمود للبريد الإلكتروني). بحال عنوان الكتاب، اسم المؤلف، تاريخ النشر.

---

## 3. البداية: إنشاء قاعدة بيانات وجداول

باش نخدمو مع MySQL، نقدروا نستعملو سطر الأوامر (MySQL Command Line) أو برنامج رسومي بحال phpMyAdmin أو MySQL Workbench.

### إنشاء قاعدة بيانات
```sql
CREATE DATABASE my_first_db;
```
هنا `my_first_db` هو اسم قاعدة البيانات.

### استخدام قاعدة بيانات
باش نخدمو فواحد قاعدة بيانات معينة:
```sql
USE my_first_db;
```

### إنشاء جدول
مثلاً، جدول بسيط للمستخدمين:
```sql
CREATE TABLE users_simple (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);
```
*   `id INT AUTO_INCREMENT PRIMARY KEY`: عمود للأرقام التعريفية، كيتزاد بوحدو (`AUTO_INCREMENT`) وهو المفتاح الرئيسي (`PRIMARY KEY`) يعني ما كيتعاودش وفريد.
*   `name VARCHAR(100)`: عمود للاسم، نص فيه حتى لـ 100 حرف.
*   `email VARCHAR(100)`: عمود للبريد الإلكتروني.

### إدخال بيانات
باش نزيدو معلومات فالجدول:
```sql
INSERT INTO users_simple (name, email)
VALUES ('أحمد', 'ahmed@example.com');
```

### عرض البيانات
باش نشوفو شنو كاين فالجدول:
```sql
SELECT * FROM users_simple;
```
`*` كتعني "جميع الأعمدة".

---

## 4. المشروع الأول: مكتبة `my_library` (علاقة Many-to-Many)

هنا غادي نبنيو قاعدة بيانات لمكتبة فيها مستخدمين وكتب، وكل مستخدم يقدر يستاعر عدة كتب، والكتاب الواحد يقدر يستاعروه عدة مستخدمين (علاقة Many-to-Many).

### إنشاء قاعدة البيانات والجداول (`my_library`)

1.  **إنشاء قاعدة البيانات:**
    ```sql
    CREATE DATABASE my_library;
    USE my_library;
    ```

2.  **جدول المستخدمين (`users`):**
    ```sql
    CREATE TABLE users (
      id INT AUTO_INCREMENT PRIMARY KEY,
      name VARCHAR(100),
      email VARCHAR(100) UNIQUE, -- UNIQUE كتعني أن الإيميل ما خصوش يتعاود
      registered_at DATE
    );
    ```

3.  **جدول الكتب (`books`):**
    ```sql
    CREATE TABLE books (
      id INT AUTO_INCREMENT PRIMARY KEY,
      title VARCHAR(100),
      author VARCHAR(100),
      year INT
    );
    ```

4.  **جدول الاستعارات (`borrowings`) - جدول وسيط للعلاقة:**
    ```sql
    CREATE TABLE borrowings (
      id INT AUTO_INCREMENT PRIMARY KEY,
      user_id INT,
      book_id INT,
      borrowed_at DATE,
      returned_at DATE,
      FOREIGN KEY (user_id) REFERENCES users(id), -- ربط مع جدول users
      FOREIGN KEY (book_id) REFERENCES books(id)  -- ربط مع جدول books
    );
    ```
    *   `FOREIGN KEY`: هو مفتاح أجنبي كيشير للمفتاح الرئيسي فجدول آخر، هو اللي كيدير العلاقة بين الجداول.

### إدخال البيانات في `my_library`

```sql
-- إضافة مستخدمين
INSERT INTO users (name, email, registered_at) VALUES
('أمين', 'amine@example.com', '2023-01-10'),
('فاطمة', 'fatima@example.com', '2023-02-15');

-- إضافة كتب
INSERT INTO books (title, author, year) VALUES
('الخبز الحافي', 'محمد شكري', 1972),
('مدن الملح', 'عبد الرحمن منيف', 1984);

-- إضافة استعارة
INSERT INTO borrowings (user_id, book_id, borrowed_at)
VALUES (1, 1, '2023-03-01'); -- أمين (id=1) استعار الخبز الحافي (id=1)
```

### عرض بيانات الاستعارات (مع `JOIN`)
باش نشوفو شكون سلف شنو:
```sql
SELECT
    users.name AS user_name,
    books.title AS book_title,
    borrowings.borrowed_at
FROM
    borrowings
JOIN
    users ON borrowings.user_id = users.id
JOIN
    books ON borrowings.book_id = books.id;
```
*   `JOIN`: كتجمع البيانات من جداول مختلفة بناءً على العلاقة بيناتهم.
*   `AS`: كتعطي اسم مستعار للعمود أو الجدول فالنتيجة.

---

## 5. التعديل، الحذف، والبحث المتقدم

### تحديث البيانات (UPDATE)
مثلاً، مستخدم بدّل الإيميل ديالو:
```sql
UPDATE users
SET email = 'amine.new@example.com'
WHERE id = 1;
```
**مهم:** ديما استعمل `WHERE` فـ `UPDATE` باش تحدد السطر اللي بغيتي تعدل، وإلا غادي تعدل جميع السطور!

### حذف البيانات (DELETE)
مثلاً، حذف كتاب:
```sql
DELETE FROM books
WHERE id = 2;
```
**مهم:** ديما استعمل `WHERE` فـ `DELETE` باش تحدد السطر اللي بغيتي تمسح، وإلا غادي تمسح كلشي!

### البحث بشروط (WHERE)
مثلاً، جلب الكتب اللي تنشرات قبل سنة 2000:
```sql
SELECT * FROM books
WHERE year < 2000;
```

### البحث بالكلمات (LIKE)
مثلاً، جلب المستخدمين اللي الاسم ديالهم كيبدا بـ "أ":
```sql
SELECT * FROM users
WHERE name LIKE 'أ%';
```
*   `%`: كتعني "أي سلسلة من الحروف" (wildcard).
*   `'أ%'`: كيبدا بـ "أ".
*   `'%أ'`: كيسالي بـ "أ".
*   `'%أ%'`: فيه "أ" فشي بلاصة.

### ترتيب النتائج (ORDER BY)
مثلاً، جلب الكتب مرتبة حسب سنة النشر من الأقدم للأحدث:
```sql
SELECT * FROM books
ORDER BY year ASC; -- ASC: تصاعدي (من الصغير للكبير)
```
للترتيب التنازلي (من الأحدث للأقدم):
```sql
SELECT * FROM books
ORDER BY year DESC; -- DESC: تنازلي (من الكبير للصغير)
```

---

## 6. المشروع الثاني: منصة كورسات أونلاين

هنا غادي نطبقو مفاهيم متقدمة بحال القيود (Constraints)، وتصميم علاقات أكثر تعقيدًا.

### تصميم قاعدة البيانات مع Normalization و Constraints

**فكرة Normalization (ببساطة):** هي عملية تنظيم البيانات فالجداول باش نقللو من تكرار البيانات ونتفاداو المشاكل فالتحديث. الهدف هو أن كل معلومة تكون فبلاصتها المناسبة.

**الكيانات الأساسية:**
*   `users`: المستخدمين
*   `courses`: الكورسات
*   `enrollments`: التسجيلات (علاقة Many-to-Many بين `users` و `courses`)
*   `ratings`: التقييمات (علاقة Many-to-Many بين `users` و `courses`)

1.  **إنشاء قاعدة البيانات:**
    ```sql
    CREATE DATABASE online_courses_platform;
    USE online_courses_platform;
    ```

2.  **جدول المستخدمين (`users`):**
    ```sql
    CREATE TABLE users (
      id INT AUTO_INCREMENT PRIMARY KEY,
      name VARCHAR(100) NOT NULL, -- NOT NULL: ضروري هاد الحقل يتعمر
      email VARCHAR(100) UNIQUE NOT NULL,
      created_at DATE DEFAULT CURRENT_DATE -- DEFAULT: قيمة افتراضية إذا ما دخلناش قيمة
    );
    ```

3.  **جدول الكورسات (`courses`):**
    ```sql
    CREATE TABLE courses (
      id INT AUTO_INCREMENT PRIMARY KEY,
      title VARCHAR(150) NOT NULL,
      description TEXT,
      instructor VARCHAR(100),
      created_at DATE DEFAULT CURRENT_DATE
    );
    ```

4.  **جدول التسجيلات (`enrollments`):**
    ```sql
    CREATE TABLE enrollments (
      id INT AUTO_INCREMENT PRIMARY KEY,
      user_id INT NOT NULL,
      course_id INT NOT NULL,
      enrolled_at DATE DEFAULT CURRENT_DATE,
      FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE, -- ON DELETE CASCADE: إذا تمسح المستخدم، يتمسحو تسجيلاتو
      FOREIGN KEY (course_id) REFERENCES courses(id) ON DELETE CASCADE,
      UNIQUE (user_id, course_id) -- باش نضمنو أن المستخدم ما يتسجلش فالكورس جوج مرات
    );
    ```

5.  **جدول التقييمات (`ratings`):**
    ```sql
    CREATE TABLE ratings (
      id INT AUTO_INCREMENT PRIMARY KEY,
      user_id INT NOT NULL,
      course_id INT NOT NULL,
      rating INT CHECK (rating >= 1 AND rating <= 5), -- CHECK: القيمة خاص تكون بين 1 و 5
      comment TEXT,
      rated_at DATE DEFAULT CURRENT_DATE,
      FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
      FOREIGN KEY (course_id) REFERENCES courses(id) ON DELETE CASCADE,
      UNIQUE (user_id, course_id) -- باش نضمنو أن المستخدم يقيم الكورس مرة وحدة
    );
    ```

### إدخال بيانات واقعية لمنصة الكورسات

```sql
-- مستخدمين
INSERT INTO users (name, email) VALUES
('أمين الزروالي', 'amine@gmail.com'),
('سلمى البقالي', 'salma@gmail.com'),
('يونس البقيوي', 'younes@gmail.com');

-- كورسات
INSERT INTO courses (title, description, instructor) VALUES
('مقدمة في البرمجة بلغة Python', 'تعلم أساسيات البرمجة خطوة بخطوة.', 'أستاذ طارق'),
('تصميم واجهات UI/UX باستخدام Figma', 'من الصفر للاحتراف في تصميم الواجهات.', 'أستاذة ليلى');

-- تسجيلات
INSERT INTO enrollments (user_id, course_id) VALUES
(1, 1), -- أمين تسجل فكورس Python
(2, 1), -- سلمى تسجلات فكورس Python
(2, 2), -- سلمى تسجلات فكورس Figma
(3, 2); -- يونس تسجل فكورس Figma

-- تقييمات
INSERT INTO ratings (user_id, course_id, rating, comment) VALUES
(1, 1, 5, 'دورة ممتازة وشرح واضح!'),
(2, 1, 4, 'محتوى جيد، شكراً.'),
(2, 2, 5, 'أفضل دورة تصميم حضرتها!');
```

### استعلامات عملية لمنصة الكورسات

1.  **عرض المسجلين في كل كورس:**
    ```sql
    SELECT
        c.title AS course_title,
        u.name AS student_name
    FROM
        enrollments e
    JOIN
        users u ON e.user_id = u.id
    JOIN
        courses c ON e.course_id = c.id
    ORDER BY
        c.title, u.name;
    ```

2.  **عرض الكورس الأعلى تقييمًا (متوسط التقييمات):**
    ```sql
    SELECT
        c.title AS course_title,
        AVG(r.rating) AS average_rating
    FROM
        ratings r
    JOIN
        courses c ON r.course_id = c.id
    GROUP BY
        c.title
    ORDER BY
        average_rating DESC
    LIMIT 1;
    ```
    *   `AVG()`: كتحسب المتوسط.
    *   `GROUP BY`: كتجمع السطور اللي عندها نفس القيمة فعمود معين (هنا `c.title`).
    *   `LIMIT 1`: كترجع غير السطر الأول من النتيجة.

3.  **عدد المسجلين في كل كورس:**
    ```sql
    SELECT
        c.title AS course_title,
        COUNT(e.user_id) AS number_of_students
    FROM
        courses c
    LEFT JOIN -- كنستعملو LEFT JOIN باش يبانو حتى الكورسات اللي ما تسجل فيهم حتى واحد
        enrollments e ON c.id = e.course_id
    GROUP BY
        c.title
    ORDER BY
        number_of_students DESC;
    ```
    *   `COUNT()`: كتحسب عدد السطور.

---

## 7. الـ Joins المتقدمة وشروط التجميع

### LEFT JOIN و RIGHT JOIN

*   `INNER JOIN` (أو `JOIN` بوحدها): كترجع فقط السطور اللي عندها تطابق فالجداول بجوج.
*   `LEFT JOIN`: كترجع جميع السطور من الجدول اللي على **اليسار**، والسطور المتطابقة من الجدول اللي على اليمين. إذا ماكانش تطابق، كترجع `NULL` لقيم الجدول اللي على اليمين.
*   `RIGHT JOIN`: كترجع جميع السطور من الجدول اللي على **اليمين**، والسطور المتطابقة من الجدول اللي على اليسار. إذا ماكانش تطابق، كترجع `NULL` لقيم الجدول اللي على اليسار.

**مثال `LEFT JOIN`: عرض جميع الكورسات، وحتى اللي ما تسجل فيها حتى واحد:**
```sql
SELECT
    c.title AS course_title,
    u.name AS student_name
FROM
    courses c
LEFT JOIN
    enrollments e ON c.id = e.course_id
LEFT JOIN
    users u ON e.user_id = u.id
ORDER BY
    c.title;
```

### GROUP BY مع HAVING

`HAVING` كتستعمل باش تفيلتري النتائج **بعد** ما تطبق `GROUP BY`. كتخدم بحال `WHERE` ولكن على المجموعات.

**مثال: عرض الكورسات اللي عندها أكثر من تقييم واحد ومتوسط تقييمها أكبر من 4:**
```sql
SELECT
    c.title AS course_title,
    COUNT(r.id) AS total_ratings,
    AVG(r.rating) AS average_rating
FROM
    ratings r
JOIN
    courses c ON r.course_id = c.id
GROUP BY
    c.title
HAVING
    COUNT(r.id) > 1 AND AVG(r.rating) > 4
ORDER BY
    average_rating DESC;
```

---

## 8. حماية البيانات والعلاقات المتقدمة

### Constraints إضافية (DEFAULT, ON DELETE)

*   `DEFAULT value`: كتعطي قيمة افتراضية للعمود إذا ما دخلتيش ليه قيمة عند `INSERT`. (شفناها فجدول `users` و `courses`).
*   `ON DELETE action`: كتحدد شنو يوقع فالجدول الحالي إذا تمسح سجل مرتبط بيه فجدول آخر (عن طريق `FOREIGN KEY`).
    *   `CASCADE`: إذا تمسح السجل الأب، يتمسحو حتى السجلات الأبناء المرتبطة به. (شفناها فجدول `enrollments` و `ratings`).
    *   `SET NULL`: إذا تمسح السجل الأب، قيم المفتاح الأجنبي فالسجلات الأبناء كتولي `NULL`. (خاص العمود يسمح بـ `NULL`).
    *   `RESTRICT` أو `NO ACTION`: كيمنع حذف السجل الأب إذا كان عندو أبناء مرتبطين. (هادي هي الحالة الافتراضية غالبا).

### علاقة 1-to-1 (جدول `user_profiles`)
كل مستخدم (`users`) عندو بروفايل واحد فقط (`user_profiles`).

1.  **إنشاء جدول `user_profiles`:**
    ```sql
    CREATE TABLE user_profiles (
      user_id INT PRIMARY KEY, -- المفتاح الرئيسي هنا هو نفسه المفتاح الأجنبي
      bio TEXT,
      profile_picture VARCHAR(255),
      website VARCHAR(255),
      FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
    );
    ```

2.  **إدخال بيانات:**
    ```sql
    INSERT INTO user_profiles (user_id, bio, website) VALUES
    (1, 'مطور ويب شغوف بالتقنيات الجديدة.', 'amine.dev'),
    (2, 'مصممة جرافيك ومحبة للفنون البصرية.', 'salma.art');
    ```

3.  **استعلام لجلب المستخدم مع بروفايلو:**
    ```sql
    SELECT
        u.name, u.email, up.bio, up.website
    FROM
        users u
    JOIN -- أو INNER JOIN
        user_profiles up ON u.id = up.user_id
    WHERE u.id = 1;
    ```

### علاقة Self-Join (جدول `employees`)
الجدول كيرتبط براسو. مثلاً، موظف عندو مدير، والمدير هو موظف آخر فنفس الجدول.

1.  **إنشاء جدول `employees`:**
    ```sql
    CREATE TABLE employees (
      id INT AUTO_INCREMENT PRIMARY KEY,
      name VARCHAR(100) NOT NULL,
      position VARCHAR(100),
      manager_id INT, -- هذا العمود كيشير لـ id ديال موظف آخر (المدير)
      FOREIGN KEY (manager_id) REFERENCES employees(id) ON DELETE SET NULL
      -- ON DELETE SET NULL: إذا تمسح المدير، الموظفين اللي كانو تحتو manager_id ديالهم يولي NULL
    );
    ```

2.  **إدخال بيانات:**
    ```sql
    INSERT INTO employees (name, position, manager_id) VALUES
    ('كريم العلوي', 'المدير العام', NULL),          -- id=1, ما عندوش مدير
    ('ليلى بناني', 'مديرة التسويق', 1),           -- id=2, المدير ديالها هو كريم
    ('سمير حمدي', 'مسؤول مبيعات', 2),           -- id=3, المدير ديالو هي ليلى
    ('هدى الشرقاوي', 'مطورة ويب أولى', 1);         -- id=4, المدير ديالها هو كريم
    ```

3.  **استعلام لجلب الموظف واسم المدير ديالو:**
    ```sql
    SELECT
        e.name AS employee_name,
        e.position AS employee_position,
        m.name AS manager_name
    FROM
        employees e
    LEFT JOIN -- كنستعملو LEFT JOIN باش نجيبو حتى الموظفين اللي ما عندهمش مدير (بحال المدير العام manager_id = NULL)
        employees m ON e.manager_id = m.id;
    ```

---

## 9. خاتمة

هاد الرحلة كانت غنية بالمعلومات والتطبيقات العملية. من الأساسيات بحال إنشاء الجداول وإدخال البيانات، حتى المفاهيم المتقدمة بحال أنواع الـ Joins المختلفة، القيود، والعلاقات المعقدة.
دابا ولات عندي نظرة شاملة على كيفاش نصمم ونتعامل مع قواعد البيانات MySQL بفعالية.

الخطوة الجاية هي ربط هادشي بواجهة تطبيق أو موقع ويب! 💪

شكراً بزاف على هاد المرافقة التعليمية الممتعة والمفيدة!
```

هذا الملف Markdown شامل ومرتب. تقدر تنسخو كامل وتحطو فملف `README.md` أو أي ملف آخر فالـ GitHub ديالك.
إذا بغيتي أي تعديل أو إضافة، قول ليا! برافو عليك مرة أخرى على هاد الإنجاز! 🎉
