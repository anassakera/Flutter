

### **الحل الأمثل لمزامنة فورية ثنائية الاتجاه (محلي ↔ خادم):**

---

#### **المشكلة الرئيسية:**  
البيانات يجب أن تت synchronise **فوريًا** بين:  
- **الحواسيب المتصلة محليًا (SQL Server)**.  
- **الحواسيب المتصلة بالخادم (MySQL)**.  

---

### **الطريقة المقترحة (بدقة تقنية):**

---

#### **1. المزامنة من المحلي إلى الخادم (SQL Server → MySQL):**  
**السيناريو:**  
- عند إضافة فاتورة جديدة في SQL Server، تُرسل البيانات فورًا إلى MySQL.

**الخطوات:**  
1. **إنشاء Trigger في SQL Server:**  
   ```sql
   CREATE TRIGGER trg_Invoice_Insert
   ON Invoices
   AFTER INSERT
   AS
   BEGIN
       DECLARE @InvoiceData NVARCHAR(MAX);
       SELECT @InvoiceData = (SELECT * FROM INSERTED FOR JSON PATH);
       
       -- استدعاء Stored Procedure لإرسال البيانات
       EXEC sp_SendToRemote @InvoiceData;
   END;
   ```

2. **إنشاء Stored Procedure لإرسال البيانات عبر HTTP:**  
   ```sql
   CREATE PROCEDURE sp_SendToRemote (@Data NVARCHAR(MAX))
   AS
   BEGIN
       DECLARE @URL NVARCHAR(500) = 'https://your-vps.com/api/sync_invoice.php';
       DECLARE @Cmd NVARCHAR(MAX) = 
           'powershell -Command "Invoke-RestMethod -Uri ''' + @URL + ''' -Method Post -Body ''' + @Data + ''' -ContentType ''application/json''"""';
       
       EXEC xp_cmdshell @Cmd; -- يحتاج HTTP تمكين (قد تحتاج إلى تكوين)
   END;
   ```

3. **إنشاء API في الخادم (PHP) لاستقبال البيانات:**  
   ```php
   // sync_invoice.php
   <?php
   header("Content-Type: application/json");
   $json = file_get_contents('php://input');
   $data = json_decode($json, true);

   // إدخال البيانات في MySQL
   $pdo = new PDO("mysql:host=localhost;dbname=remote_accounting", "user", "pass");
   $stmt = $pdo->prepare("INSERT INTO Invoices (InvoiceID, CustomerName, ...) VALUES (?, ?, ...)");
   $stmt->execute([$data['InvoiceID'], $data['CustomerName'], ...]);
   echo json_encode(['status' => 'success']);
   ?>
   ```

---

#### **2. المزامنة من الخادم إلى المحلي (MySQL → SQL Server):**  
**السيناريو:**  
- عند إضافة فاتورة عبر الموقع (MySQL)، تُرسل البيانات فورًا إلى SQL Server.

**الخطوات:**  
1. **تعديل كود الموقع (PHP) لإرسال البيانات عند الإدخال:**  
   ```php
   // عند حفظ الفاتورة في MySQL:
   $pdo = new PDO("mysql:host=localhost;dbname=remote_accounting", "user", "pass");
   $stmt = $pdo->prepare("INSERT INTO Invoices ...");
   $stmt->execute([...]);

   // بعد النجاح، أرسل البيانات إلى المحلي
   $localApiUrl = 'http://local-server-ip:port/api/receive_invoice.php';
   $data = ['InvoiceID' => $newId, 'CustomerName' => $name, ...];
   $response = file_get_contents($localApiUrl . '?data=' . urlencode(json_encode($data)));
   ```

2. **إنشاء API في المحلي (PHP) لاستقبال البيانات:**  
   ```php
   // receive_invoice.php (على الجهاز المحلي)
   <?php
   header("Content-Type: application/json");
   $data = json_decode($_GET['data'], true);

   // إدخال البيانات في SQL Server
   $conn = new COM("ADODB.Connection");
   $conn->Open("Driver={SQL Server};Server=.;Database=AccountingDB;");
   $rs = $conn->Execute("INSERT INTO Invoices VALUES ($data[InvoiceID], '$data[CustomerName]', ...)");
   echo json_encode(['status' => 'success']);
   ?>
   ```

---

### **التحديات والحلول:**  
1. **المزامنة من الخادم إلى المحلي:**  
   - **المشكلة:** الجهاز المحلي قد يكون خلف جدار حماية (Firewall) ولا يمكن الوصول إليه عبر الإنترنت.  
   - **الحل:**  
     - **تكوين NAT/Port Forwarding** على الراوتر المحلي لفتح منفذ (مثال: 8080) للـ API.  
     - **استخدام HTTPS** لتشفير البيانات أثناء النقل.  

2. **التأكد من عدم تكرار البيانات:**  
   - أضف حقل `IsSynced` في الجداول لتتبع حالة المزامنة.  

3. **معالجة الأخطاء:**  
   - إذا فشل الإرسال، قم بتخزين البيانات في جدول `PendingSync` وأعد المحاولة تلقائيًا.  

---

### **أفضل الممارسات:**  
1. **استخدام WebSockets أو SignalR:**  
   - بدلًا من HTTP polling، استخدم تقنيات الوقت الفعلي (Real-Time) لإرسال الإشعارات عند تغير البيانات.  

2. **النسخ المتماثل (Replication):**  
   - **SQL Server → MySQL:** استخدم **SQL Server Replication** لتصدير البيانات تلقائيًا.  
   - **MySQL → SQL Server:** استخدم **MySQL Event Scheduler** لإرسال البيانات.  

3. **الاستفادة من خدمات السحابة:**  
   - **Azure SQL Data Sync** (إذا كان الخادم على Azure) لربط SQL Server و MySQL دون كتابة كود.  

---

### **ملخص:**  
- **المزامنة الفورية تتطلب:**  
  - **Triggers** لالتقاط التغييرات.  
  - **HTTP APIs** لنقل البيانات.  
  - **تكوين شبكي** (Port Forwarding) لو كان الخادم المحلي خلف جدار حماية.  
- **الأداة المثالية للمبتدئين:**  
  - **Firebase Realtime Database** كجسر بين SQL Server و MySQL (لا يحتاج إلى برمجة معقدة).  

هل تريد شرحًا تفصيليًا لأحد هذه الخطوات؟ 😊
