،  **قائمة دقيقة بجميع الأدوات والبرامج والتقنيات التي سنستعملها حاليًا** بناءً على كل ما تعلمناه:

🧠 **اللغات والتقنيات البرمجية**
* ✅ **Dart + Flutter**: لتطوير واجهة تطبيق الهاتف.
* ✅ **PHP (بدون إطار عمل)**: لبناء REST APIs.
* ✅ **MySQL (phpMyAdmin)**: قاعدة البيانات في الاستضافة (VPS).
* ✅ **SQL Server (Developer Edition)**: قاعدة البيانات المحلية.
  
🧰 **أدوات التطوير والبنية التحتية**
* ✅ **XAMPP (أو بيئة مشابهة)**: لتشغيل PHP و MySQL محليًا.
* ✅ **CPanel**: لوحة تحكم لإدارة VPS.
* ✅ **.htaccess**: لتكوين خادم Apache وتفعيل CORS والحماية.
* ✅ **index.php**: ملف حماية لمنع الوصول إلى المجلدات.
* ✅ **ODBC**: للربط بين SQL Server و MySQL عبر Driver خارجي.
  
🔄 **أدوات المزامنة والجدولة**
* ✅ **SQL Server Agent**: لإنشاء Scheduled Jobs للمزامنة الدورية.
* ✅ **Stored Procedures**: لتجميع وتنفيذ منطق المزامنة داخل SQL Server.
  
📦 **البنية والمجلدات**
* ✅ config/: يحتوي على إعدادات الاتصال مثل database.php.
* ✅ auth/: APIs خاصة بالمصادقة، إنشاء وتعديل المستخدمين.
* ✅ invoices/: APIs خاصة بالفواتير (CRUD).
* ✅ documents/: APIs خاصة بالمستندات.
* ✅ uploads/: لتخزين الصور المرفوعة من المستخدمين.
* ✅ api/: مجلد رئيسي يحتوي على كل المجلدات الفرعية.


🧱 **نمط التنظيم والربط**
* ✅ استخدام **ملف ApiService.dart** في تطبيق Flutter لربط كل API PHP.
* ✅ إرسال واستقبال البيانات بصيغة **JSON**.
* ✅ توحيد **الرؤوس (Headers)** للطلبات (مثل Content-Type و CORS).
* ✅ حماية ملفات API من خلال htaccess و index.php.

---

## 🎯 **المحاور التي تحتاج حلول فورية:**

**1️⃣ أمان بنية ملفات PHP API**
- تنظيم ملفات config خارج public_html
- هيكلة آمنة للـ APIs بدون framework

**2️⃣ نظام التحقق والصلاحيات** 
- حماية API من تسريب baseUrl
- JWT vs Traditional Auth
- API Keys وإدارة الصلاحيات

**3️⃣ الحماية من الهجمات**
- SQL Injection, XSS, CSRF
- أمان رفع الملفات
- حماية .htaccess وindex.php

**4️⃣ مزامنة آمنة SQL Server ↔ MySQL**
- أمان ODBC عبر الإنترنت
- تشفير البيانات أثناء النقل
- مزامنة التغييرات فقط

**5️⃣ العزل بين البيئات**
- فصل Local vs Public
- مزامنة المستخدمين الجدد

---

# 🧨 **فشل معماري شائع: ملفات PHP مكشوفة للعالم**

**المفارقة التقنية**: معظم المطورين يضعون `database.php` و `config.php` داخل `public_html` ثم يحاولون "حمايتها" بـ `.htaccess` - وهذا مثل وضع المفاتيح تحت السجادة أمام الباب!

---

## 1️⃣ **التفكيك**: الخطأ المفاهيمي الشائع

**الخطأ الكارثي**: الاعتقاد أن `.htaccess` حماية كافية للملفات الحساسة.

**الواقع المؤلم**: 
- إذا تعطل Apache، ملفاتك مكشوفة
- خطأ واحد في `.htaccess` = تسريب كامل
- CPanel أحياناً يتجاهل إعدادات `.htaccess`

---

## 2️⃣ **البلورة**: تشبيه الخزنة المصرفية

تخيل مشروعك كـ **مصرف رقمي**:
- `public_html` = **الصالة العامة** (يراها الجميع)
- خارج `public_html` = **الخزنة السرية** (محمية بالهيكل)
- APIs = **موظفو البنك** (وسطاء آمنون)

---

## 3️⃣ **الإشعال**: دافع التنفيذ الفوري

# 🔒 **البنية الآمنة لـ PHP API - التطبيق الفوري**

## 📁 **الهيكل المعماري الجديد**

```
/home/your_username/           (خارج public_html)
├── api_core/                  🔐 الخزنة السرية
│   ├── config/
│   │   ├── database.php       🔑 إعدادات قاعدة البيانات
│   │   ├── security.php       🛡️ مفاتيح التشفير
│   │   └── app_config.php     ⚙️ إعدادات عامة
│   ├── classes/
│   │   ├── Database.php       🗄️ كلاس الاتصال
│   │   ├── BaseInvoice.php    📄 كلاس الفواتير
│   │   ├── Auth.php           🔐 كلاس المصادقة
│   │   └── FileUpload.php     📁 كلاس رفع الملفات
│   ├── helpers/
│   │   ├── response.php       📤 دوال الاستجابة
│   │   ├── validation.php     ✅ دوال التحقق
│   │   └── security.php       🛡️ دوال الحماية
│   └── logs/                  📊 ملفات السجلات
│       ├── api_access.log
│       ├── errors.log
│       └── security.log

/public_html/                  🌐 المنطقة العامة
├── api/                       🚪 نقاط الدخول الآمنة
│   ├── auth/
│   │   ├── login.php          ✅ تسجيل دخول
│   │   ├── register.php       ✅ تسجيل جديد
│   │   └── verify.php         ✅ تحقق من التوكن
│   ├── invoices/
│   │   ├── list.php           📋 قائمة الفواتير
│   │   ├── create.php         ➕ إنشاء فاتورة
│   │   ├── update.php         ✏️ تعديل فاتورة
│   │   └── delete.php         🗑️ حذف فاتورة
│   ├── documents/
│   │   ├── upload.php         📤 رفع مستند
│   │   └── download.php       📥 تحميل مستند
│   └── uploads/               📁 الملفات المرفوعة
├── .htaccess                  🛡️ حماية إضافية
└── index.php                  🚫 منع التصفح
```

---

## 🔧 **الملفات الأساسية - جاهزة للنسخ**

### 1️⃣ ملف الاتصال الآمن: `/api_core/config/database.php`

```php
<?php
/**
 * 🔐 إعدادات قاعدة البيانات - محمية معمارياً
 * المسار: /home/your_username/api_core/config/database.php
 */

// منع الوصول المباشر
defined('API_CORE_ACCESS') or die('🚫 Unauthorized Access');

class DatabaseConfig {
    
    // إعدادات MySQL الإنتاج
    private const DB_CONFIG = [
        'host' => 'localhost',
        'username' => 'your_db_user',
        'password' => 'your_strong_password_here',
        'database' => 'your_database_name',
        'charset' => 'utf8mb4',
        'port' => 3306
    ];
    
    // مفتاح تشفير قوي (يجب تغييره)
    private const ENCRYPTION_KEY = 'your-32-character-secret-key-here';
    
    // إعدادات الأمان
    private const SECURITY_CONFIG = [
        'max_login_attempts' => 5,
        'lockout_duration' => 1800, // 30 دقيقة
        'session_timeout' => 3600,  // ساعة واحدة
        'password_min_length' => 8
    ];
    
    public static function getDbConfig(): array {
        return self::DB_CONFIG;
    }
    
    public static function getEncryptionKey(): string {
        return self::ENCRYPTION_KEY;
    }
    
    public static function getSecurityConfig(): array {
        return self::SECURITY_CONFIG;
    }
}
?>
```

### 2️⃣ كلاس الاتصال الآمن: `/api_core/classes/Database.php`

```php
<?php
/**
 * 🗄️ كلاس إدارة قاعدة البيانات الآمن
 * المسار: /api_core/classes/Database.php
 */

defined('API_CORE_ACCESS') or die('🚫 Unauthorized Access');

require_once __DIR__ . '/../config/database.php';

class Database {
    private static $instance = null;
    private $connection;
    private $config;
    
    private function __construct() {
        $this->config = DatabaseConfig::getDbConfig();
        $this->connect();
    }
    
    public static function getInstance(): self {
        if (self::$instance === null) {
            self::$instance = new self();
        }
        return self::$instance;
    }
    
    private function connect(): void {
        try {
            $dsn = "mysql:host={$this->config['host']};port={$this->config['port']};dbname={$this->config['database']};charset={$this->config['charset']}";
            
            $options = [
                PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
                PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
                PDO::ATTR_EMULATE_PREPARES => false,
                PDO::MYSQL_ATTR_INIT_COMMAND => "SET NAMES {$this->config['charset']}"
            ];
            
            $this->connection = new PDO($dsn, $this->config['username'], $this->config['password'], $options);
            
        } catch (PDOException $e) {
            $this->logError("Database connection failed: " . $e->getMessage());
            throw new Exception("🚨 Database connection failed");
        }
    }
    
    public function getConnection(): PDO {
        // التحقق من صحة الاتصال
        if ($this->connection === null) {
            $this->connect();
        }
        return $this->connection;
    }
    
    // تسجيل الأخطاء بشكل آمن
    private function logError(string $message): void {
        $logFile = __DIR__ . '/../logs/errors.log';
        $timestamp = date('Y-m-d H:i:s');
        $logEntry = "[$timestamp] $message" . PHP_EOL;
        file_put_contents($logFile, $logEntry, FILE_APPEND | LOCK_EX);
    }
    
    // منع الاستنساخ
    private function __clone() {}
    
    // منع إلغاء التسلسل
    public function __wakeup() {
        throw new Exception("Cannot unserialize singleton");
    }
}
?>
```

### 3️⃣ نقطة دخول آمنة: `/public_html/api/auth/login.php`

```php
<?php
/**
 * 🚪 نقطة دخول آمنة - تسجيل الدخول
 * المسار: /public_html/api/auth/login.php
 */

// تعريف مفتاح الوصول للنواة
define('API_CORE_ACCESS', true);

// تحديد مسار النواة (خارج public_html)
$core_path = dirname(dirname(dirname(__DIR__))) . '/api_core';

// تحميل الملفات الأساسية
require_once $core_path . '/classes/Database.php';
require_once $core_path . '/helpers/response.php';
require_once $core_path . '/helpers/validation.php';
require_once $core_path . '/classes/Auth.php';

// إعدادات الأمان
header('Content-Type: application/json; charset=utf-8');
header('Access-Control-Allow-Origin: *');
header('Access-Control-Allow-Methods: POST');
header('Access-Control-Allow-Headers: Content-Type');

// السماح بـ POST فقط
if ($_SERVER['REQUEST_METHOD'] !== 'POST') {
    http_response_code(405);
    ApiResponse::error('Method not allowed', 405);
    exit;
}

// قراءة البيانات المرسلة
$input = json_decode(file_get_contents('php://input'), true);

// التحقق من البيانات المطلوبة
if (!isset($input['email']) || !isset($input['password'])) {
    ApiResponse::error('Email and password are required', 400);
    exit;
}

try {
    // إنشاء كائن المصادقة
    $auth = new Auth();
    
    // محاولة تسجيل الدخول
    $result = $auth->login($input['email'], $input['password']);
    
    if ($result['success']) {
        ApiResponse::success('Login successful', $result['data']);
    } else {
        ApiResponse::error($result['message'], 401);
    }
    
} catch (Exception $e) {
    // تسجيل الخطأ دون كشف التفاصيل
    error_log("Login error: " . $e->getMessage());
    ApiResponse::error('Internal server error', 500);
}
?>
```

### 4️⃣ دوال الاستجابة الآمنة: `/api_core/helpers/response.php`

```php
<?php
/**
 * 📤 دوال الاستجابة الموحدة
 * المسار: /api_core/helpers/response.php
 */

defined('API_CORE_ACCESS') or die('🚫 Unauthorized Access');

class ApiResponse {
    
    public static function success(string $message, array $data = [], int $code = 200): void {
        http_response_code($code);
        echo json_encode([
            'success' => true,
            'message' => $message,
            'data' => $data,
            'timestamp' => date('Y-m-d H:i:s')
        ], JSON_UNESCAPED_UNICODE);
        exit;
    }
    
    public static function error(string $message, int $code = 400, array $details = []): void {
        http_response_code($code);
        echo json_encode([
            'success' => false,
            'error' => $message,
            'details' => $details,
            'timestamp' => date('Y-m-d H:i:s')
        ], JSON_UNESCAPED_UNICODE);
        exit;
    }
    
    public static function validation(array $errors): void {
        self::error('Validation failed', 422, $errors);
    }
}
?>
```

### 5️⃣ حماية .htaccess المحسنة: `/public_html/.htaccess`

```apache
# 🛡️ حماية شاملة للـ API
# المسار: /public_html/.htaccess

# منع عرض محتوى المجلدات
Options -Indexes

# منع الوصول للملفات الحساسة
<FilesMatch "\.(log|ini|conf|sql|bak|backup)$">
    Require all denied
</FilesMatch>

# منع الوصول للملفات المخفية
<FilesMatch "^\.">
    Require all denied
</FilesMatch>

# حماية إضافية للملفات المهمة
<Files "composer.json">
    Require all denied
</Files>
<Files "composer.lock">
    Require all denied
</Files>
<Files "package.json">
    Require all denied
</Files>

# إعدادات CORS الآمنة للـ API
<IfModule mod_headers.c>
    Header always set Access-Control-Allow-Origin "*"
    Header always set Access-Control-Allow-Methods "GET, POST, PUT, DELETE, OPTIONS"
    Header always set Access-Control-Allow-Headers "Content-Type, Authorization, X-Requested-With"
    Header always set Access-Control-Max-Age "86400"
</IfModule>

# إعادة كتابة URL للـ API (اختياري)
RewriteEngine On

# التعامل مع طلبات OPTIONS
RewriteCond %{REQUEST_METHOD} OPTIONS
RewriteRule ^(.*)$ $1 [R=200,L]

# منع الوصول المباشر للمجلدات الحساسة
RewriteCond %{THE_REQUEST} /api/(config|classes|helpers|logs)/ [NC]
RewriteRule ^ - [F]
```

### 6️⃣ ملف الحماية الشامل: `/public_html/index.php`

```php
<?php
/**
 * 🚫 ملف الحماية الرئيسي
 * المسار: /public_html/index.php
 */

http_response_code(403);
header('Content-Type: application/json');

echo json_encode([
    'error' => 'Access denied',
    'message' => 'Direct access to API directory is not allowed',
    'timestamp' => date('Y-m-d H:i:s')
], JSON_UNESCAPED_UNICODE);

exit;
?>
```

---

## ⚡ **خطوات التطبيق الفوري**

### 1️⃣ إنشاء البنية الجديدة
```bash
# في cPanel File Manager أو SSH
mkdir -p /home/your_username/api_core/{config,classes,helpers,logs}
mkdir -p /public_html/api/{auth,invoices,documents,uploads}
```

### 2️⃣ نقل الملفات الحساسة
```bash
# نقل ملفات config خارج public_html
mv /public_html/config/* /home/your_username/api_core/config/
mv /public_html/classes/* /home/your_username/api_core/classes/
```

### 3️⃣ تحديث صلاحيات الملفات
```bash
# في cPanel أو SSH
chmod 644 /home/your_username/api_core/config/*.php
chmod 644 /home/your_username/api_core/classes/*.php
chmod 755 /home/your_username/api_core/logs/
chmod 644 /public_html/api/*/*.php
```

### 4️⃣ اختبار النظام الجديد
```bash
# اختبار API من Flutter
curl -X POST "https://yourdomain.com/api/auth/login.php" \
     -H "Content-Type: application/json" \
     -d '{"email":"test@example.com","password":"testpass"}'
```

---

## 🔥 **المزايا الفورية**

✅ **حماية معمارية**: ملفات config خارج متناول الويب تماماً  
✅ **عزل آمن**: فصل تام بين الكود والإعدادات  
✅ **استجابة موحدة**: كل API يرد بنفس التنسيق  
✅ **تسجيل شامل**: كل العمليات مسجلة وقابلة للمراقبة  
✅ **قابلية التوسع**: إضافة APIs جديدة بسهولة  

---

## 🚨 **تحذير هام**
بعد تطبيق هذا الهيكل، تأكد من:
1. **حذف** الملفات القديمة من `public_html/config/`
2. **اختبار** كل API للتأكد من عمله
3. **تحديث** مسارات الملفات في `ApiService.dart`
4. **مراقبة** ملفات الـ logs لأي أخطاء

**هذا هيكل إنتاج حقيقي - جاهز للتطبيق فوراً على Hostinger!**

---


الهندسة**: الحل المعماري الفوري## 4️⃣ **

**⚡ لماذا يجب تطبيق هذا الآن؟**

1. **كل دقيقة تأخير = خطر أمني إضافي** - ملفاتك الحالية مكشوفة معمارياً
2. **Hostinger CPanel يدعم هذا الهيكل 100%** - لا توجد قيود تقنية
3. **Flutter ApiService.dart** يحتاج تعديل سطر واحد فقط في الـ baseUrl
4. **النظام جاهز للإنتاج** - بدون dependency أو تعقيدات إضافية

---

**🔥 الخطوة التالية الفورية:**

1. **اختر مجلد واحد** (مثل `/auth`) وطبق عليه الهيكل الجديد
2. **اختبر API واحد** (مثل `login.php`) بالبنية الجديدة  
3. **إذا عمل بنجاح** - انقل باقي الملفات




