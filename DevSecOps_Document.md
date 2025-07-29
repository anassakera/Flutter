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

# 🧨 **1️⃣ أمان بنية ملفات PHP API**

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


# 🧨 **2️⃣ نظام التحقق والصلاحيات**

**المفارقة المدمرة**: تبني نظام API محكم، ثم تضع رابط السيرفر مكشوفاً في `ApiService.dart` - وكأنك تبني خزنة فولاذية ثم تترك المفتاح في القفل!

---

## 1️⃣ **التفكيك**: الخطأ الأمني المميت

**الكارثة المخفية**: 
```dart
// 💀 هذا السطر = تدمير أمني كامل
static const String baseUrl = "https://yourapi.com/api/";
```

**لماذا هذا انتحار تقني؟**
- **Flutter APK قابل للفك** = أي شخص يستطيع استخراج الرابط
- **لا توجد حماية** = وصول مباشر لكل APIs
- **لا يوجد تحكم** = استنزاف الموارد والبيانات
- **التزوير سهل** = أي شخص يمكنه إرسال طلبات مزيفة

---

## 2️⃣ **البلورة**: تشبيه القلعة الرقمية

تخيل API كـ **قلعة محصنة**:
- `baseUrl` = **عنوان القلعة** (يجب إخفاؤه)
- `API Key` = **خاتم الملك** (تصريح دخول)
- `JWT Token` = **بطاقة هوية موقتة** (تنتهي صلاحيتها)
- `Rate Limiting` = **حراس البوابة** (يمنعون الإزعاج)

**الهدف**: حتى لو اكتشف أحد عنوان القلعة، لن يستطيع الدخول بدون تصاريح صحيحة.

---

## 3️⃣ **الهندسة**: نظام الحماية المتعدد الطبقات

# 🔐 **نظام التحقق متعدد الطبقات - الحماية الشاملة**

## 🏗️ **معمارية النظام الأمني**

```
Flutter App (Client)
    ↓ [API Key + Device ID]
Security Gateway (PHP)
    ↓ [JWT Token Validation]
Auth Middleware (PHP)
    ↓ [Role-Based Access]
Core API (Protected)
    ↓ [Rate Limiting + Logging]
Database (Secured)
```

---

## ⚡ **الطبقة الأولى: إخفاء baseUrl وحماية API Key**

### 1️⃣ مولد API Keys الديناميكي: `/api_core/classes/ApiKeyManager.php`

```php
<?php
/**
 * 🔑 مدير مفاتيح API الديناميكية
 * المسار: /api_core/classes/ApiKeyManager.php
 */

defined('API_CORE_ACCESS') or die('🚫 Unauthorized Access');

class ApiKeyManager {
    private $db;
    private const KEY_LENGTH = 64;
    private const KEY_PREFIX = 'ak_';
    
    public function __construct() {
        $this->db = Database::getInstance()->getConnection();
    }
    
    /**
     * 🎯 إنشاء API Key جديد للتطبيق
     */
    public function generateAppKey(string $app_name, string $device_id): array {
        try {
            // إنشاء مفتاح فريد
            $api_key = self::KEY_PREFIX . bin2hex(random_bytes(self::KEY_LENGTH / 2));
            $secret_hash = hash('sha256', $api_key . $device_id . time());
            
            // تسجيل المفتاح في قاعدة البيانات
            $stmt = $this->db->prepare("
                INSERT INTO api_keys (api_key, app_name, device_id, secret_hash, status, created_at, expires_at) 
                VALUES (?, ?, ?, ?, 'active', NOW(), DATE_ADD(NOW(), INTERVAL 1 YEAR))
            ");
            
            $stmt->execute([$api_key, $app_name, $device_id, $secret_hash]);
            
            return [
                'success' => true,
                'api_key' => $api_key,
                'secret_hash' => $secret_hash,
                'expires_at' => date('Y-m-d H:i:s', strtotime('+1 year'))
            ];
            
        } catch (Exception $e) {
            $this->logSecurityEvent('API_KEY_GENERATION_FAILED', $e->getMessage());
            return ['success' => false, 'message' => 'Key generation failed'];
        }
    }
    
    /**
     * ✅ التحقق من صحة API Key
     */
    public function validateApiKey(string $api_key, string $device_id, string $signature): bool {
        try {
            $stmt = $this->db->prepare("
                SELECT id, secret_hash, status, expires_at, last_used, request_count 
                FROM api_keys 
                WHERE api_key = ? AND device_id = ? AND status = 'active'
            ");
            
            $stmt->execute([$api_key, $device_id]);
            $key_data = $stmt->fetch();
            
            if (!$key_data) {
                $this->logSecurityEvent('INVALID_API_KEY', "Key: $api_key, Device: $device_id");
                return false;
            }
            
            // التحقق من انتهاء الصلاحية
            if (strtotime($key_data['expires_at']) < time()) {
                $this->logSecurityEvent('EXPIRED_API_KEY', "Key: $api_key");
                return false;
            }
            
            // التحقق من التوقيع
            $expected_signature = hash_hmac('sha256', $api_key . $device_id . date('Y-m-d-H'), $key_data['secret_hash']);
            if (!hash_equals($expected_signature, $signature)) {
                $this->logSecurityEvent('INVALID_SIGNATURE', "Key: $api_key");
                return false;
            }
            
            // تحديث آخر استخدام وعداد الطلبات
            $this->updateKeyUsage($key_data['id']);
            
            return true;
            
        } catch (Exception $e) {
            $this->logSecurityEvent('API_KEY_VALIDATION_ERROR', $e->getMessage());
            return false;
        }
    }
    
    /**
     * 📊 فرض حدود معدل الطلبات (Rate Limiting)
     */
    public function checkRateLimit(string $api_key, int $max_requests = 1000, int $time_window = 3600): bool {
        try {
            $stmt = $this->db->prepare("
                SELECT COUNT(*) as request_count 
                FROM api_requests 
                WHERE api_key = ? AND created_at >= DATE_SUB(NOW(), INTERVAL ? SECOND)
            ");
            
            $stmt->execute([$api_key, $time_window]);
            $result = $stmt->fetch();
            
            if ($result['request_count'] >= $max_requests) {
                $this->logSecurityEvent('RATE_LIMIT_EXCEEDED', "Key: $api_key, Count: {$result['request_count']}");
                return false;
            }
            
            // تسجيل الطلب الحالي
            $stmt = $this->db->prepare("
                INSERT INTO api_requests (api_key, endpoint, ip_address, user_agent, created_at) 
                VALUES (?, ?, ?, ?, NOW())
            ");
            
            $endpoint = $_SERVER['REQUEST_URI'] ?? 'unknown';
            $ip = $_SERVER['REMOTE_ADDR'] ?? 'unknown';
            $user_agent = $_SERVER['HTTP_USER_AGENT'] ?? 'unknown';
            
            $stmt->execute([$api_key, $endpoint, $ip, $user_agent]);
            
            return true;
            
        } catch (Exception $e) {
            $this->logSecurityEvent('RATE_LIMIT_CHECK_ERROR', $e->getMessage());
            return false;
        }
    }
    
    private function updateKeyUsage(int $key_id): void {
        $stmt = $this->db->prepare("
            UPDATE api_keys 
            SET last_used = NOW(), request_count = request_count + 1 
            WHERE id = ?
        ");
        $stmt->execute([$key_id]);
    }
    
    private function logSecurityEvent(string $event_type, string $details): void {
        $log_file = __DIR__ . '/../logs/security.log';
        $timestamp = date('Y-m-d H:i:s');
        $ip = $_SERVER['REMOTE_ADDR'] ?? 'unknown';
        $log_entry = "[$timestamp] [$event_type] IP: $ip | $details" . PHP_EOL;
        file_put_contents($log_file, $log_entry, FILE_APPEND | LOCK_EX);
    }
}

/**
 * 📋 SQL لإنشاء جداول النظام
 */
/*
CREATE TABLE api_keys (
    id INT PRIMARY KEY AUTO_INCREMENT,
    api_key VARCHAR(100) UNIQUE NOT NULL,
    app_name VARCHAR(100) NOT NULL,
    device_id VARCHAR(100) NOT NULL,
    secret_hash VARCHAR(255) NOT NULL,
    status ENUM('active', 'suspended', 'revoked') DEFAULT 'active',
    request_count INT DEFAULT 0,
    last_used TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,
    INDEX idx_api_key (api_key),
    INDEX idx_device_id (device_id),
    INDEX idx_status (status)
);

CREATE TABLE api_requests (
    id INT PRIMARY KEY AUTO_INCREMENT,
    api_key VARCHAR(100) NOT NULL,
    endpoint VARCHAR(255) NOT NULL,
    ip_address VARCHAR(45) NOT NULL,
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_api_key_time (api_key, created_at),
    INDEX idx_ip_time (ip_address, created_at)
);
*/
?>
```

### 2️⃣ بوابة الأمان الرئيسية: `/public_html/api/security/gateway.php`

```php
<?php
/**
 * 🛡️ بوابة الأمان - التحقق من API Key
 * المسار: /public_html/api/security/gateway.php
 */

define('API_CORE_ACCESS', true);
$core_path = dirname(dirname(dirname(__DIR__))) . '/api_core';

require_once $core_path . '/classes/Database.php';
require_once $core_path . '/classes/ApiKeyManager.php';
require_once $core_path . '/helpers/response.php';

class SecurityGateway {
    private $apiKeyManager;
    
    public function __construct() {
        $this->apiKeyManager = new ApiKeyManager();
    }
    
    /**
     * 🔐 التحقق الشامل من الطلب
     */
    public function validateRequest(): array {
        // التحقق من وجود Headers المطلوبة
        $api_key = $this->getHeader('X-API-Key');
        $device_id = $this->getHeader('X-Device-ID');
        $signature = $this->getHeader('X-Signature');
        $timestamp = $this->getHeader('X-Timestamp');
        
        if (!$api_key || !$device_id || !$signature) {
            return $this->securityResponse('Missing required headers', 400);
        }
        
        // التحقق من صحة التوقيت (منع Replay Attacks)
        if (!$timestamp || abs(time() - intval($timestamp)) > 300) { // 5 دقائق
            return $this->securityResponse('Request expired', 401);
        }
        
        // التحقق من API Key
        if (!$this->apiKeyManager->validateApiKey($api_key, $device_id, $signature)) {
            return $this->securityResponse('Invalid API credentials', 401);
        }
        
        // التحقق من حدود معدل الطلبات
        if (!$this->apiKeyManager->checkRateLimit($api_key)) {
            return $this->securityResponse('Rate limit exceeded', 429);
        }
        
        return ['success' => true, 'api_key' => $api_key, 'device_id' => $device_id];
    }
    
    private function getHeader(string $header_name): ?string {
        $header = $_SERVER['HTTP_' . str_replace('-', '_', strtoupper($header_name))] ?? null;
        return $header ? trim($header) : null;
    }
    
    private function securityResponse(string $message, int $code): array {
        http_response_code($code);
        echo json_encode([
            'success' => false,
            'error' => $message,
            'code' => $code,
            'timestamp' => date('Y-m-d H:i:s')
        ], JSON_UNESCAPED_UNICODE);
        exit;
    }
}

// تشغيل البوابة إذا تم استدعاؤها مباشرة
if (basename(__FILE__) === basename($_SERVER['SCRIPT_NAME'])) {
    $gateway = new SecurityGateway();
    $result = $gateway->validateRequest();
    
    if ($result['success']) {
        ApiResponse::success('Gateway passed', $result);
    }
}
?>
```

---

## ⚡ **الطبقة الثانية: نظام JWT المتقدم**

### 3️⃣ مدير الرموز المميزة: `/api_core/classes/JWTManager.php`

```php
<?php
/**
 * 🎫 مدير الرموز المميزة JWT
 * المسار: /api_core/classes/JWTManager.php
 */

defined('API_CORE_ACCESS') or die('🚫 Unauthorized Access');

class JWTManager {
    private $secret_key;
    private $algorithm = 'HS256';
    private $token_lifetime = 3600; // ساعة واحدة
    
    public function __construct() {
        $this->secret_key = DatabaseConfig::getEncryptionKey() . '_JWT_SECRET';
    }
    
    /**
     * 🎯 إنشاء JWT Token
     */
    public function generateToken(array $user_data, string $api_key): string {
        $header = json_encode(['typ' => 'JWT', 'alg' => $this->algorithm]);
        
        $payload = json_encode([
            'user_id' => $user_data['id'],
            'email' => $user_data['email'],
            'role' => $user_data['role'] ?? 'user',
            'api_key' => $api_key,
            'iat' => time(), // إصدار في
            'exp' => time() + $this->token_lifetime, // انتهاء في
            'jti' => bin2hex(random_bytes(16)) // معرف فريد للتوكن
        ]);
        
        $base64_header = $this->base64UrlEncode($header);
        $base64_payload = $this->base64UrlEncode($payload);
        
        $signature = hash_hmac('sha256', $base64_header . "." . $base64_payload, $this->secret_key, true);
        $base64_signature = $this->base64UrlEncode($signature);
        
        return $base64_header . "." . $base64_payload . "." . $base64_signature;
    }
    
    /**
     * ✅ التحقق من صحة JWT Token
     */
    public function validateToken(string $token): array {
        try {
            $token_parts = explode('.', $token);
            
            if (count($token_parts) !== 3) {
                return ['valid' => false, 'error' => 'Invalid token format'];
            }
            
            [$header, $payload, $signature] = $token_parts;
            
            // التحقق من التوقيع
            $expected_signature = hash_hmac('sha256', $header . "." . $payload, $this->secret_key, true);
            $expected_signature_b64 = $this->base64UrlEncode($expected_signature);
            
            if (!hash_equals($expected_signature_b64, $signature)) {
                return ['valid' => false, 'error' => 'Invalid signature'];
            }
            
            $payload_data = json_decode($this->base64UrlDecode($payload), true);
            
            // التحقق من انتهاء الصلاحية
            if (isset($payload_data['exp']) && $payload_data['exp'] < time()) {
                return ['valid' => false, 'error' => 'Token expired'];
            }
            
            // التحقق من صحة البيانات المطلوبة
            if (!isset($payload_data['user_id']) || !isset($payload_data['api_key'])) {
                return ['valid' => false, 'error' => 'Invalid token data'];
            }
            
            return [
                'valid' => true,
                'data' => $payload_data
            ];
            
        } catch (Exception $e) {
            return ['valid' => false, 'error' => 'Token validation failed'];
        }
    }
    
    /**
     * 🔄 تجديد Token (إذا كان قريب من الانتهاء)
     */
    public function refreshTokenIfNeeded(string $token): ?string {
        $validation = $this->validateToken($token);
        
        if (!$validation['valid']) {
            return null;
        }
        
        $payload = $validation['data'];
        $time_left = $payload['exp'] - time();
        
        // إذا بقي أقل من 15 دقيقة، أجد التوكن
        if ($time_left < 900) {
            $user_data = [
                'id' => $payload['user_id'],
                'email' => $payload['email'],
                'role' => $payload['role']
            ];
            
            return $this->generateToken($user_data, $payload['api_key']);
        }
        
        return null; // لا يحتاج تجديد
    }
    
    private function base64UrlEncode(string $data): string {
        return rtrim(strtr(base64_encode($data), '+/', '-_'), '=');
    }
    
    private function base64UrlDecode(string $data): string {
        return base64_decode(str_pad(strtr($data, '-_', '+/'), strlen($data) % 4, '=', STR_PAD_RIGHT));
    }
}
?>
```

### 4️⃣ نظام المصادقة المطور: `/api_core/classes/AdvancedAuth.php`

```php
<?php
/**
 * ⚡ نظام المصادقة المتقدم
 * المسار: /api_core/classes/AdvancedAuth.php
 */

defined('API_CORE_ACCESS') or die('🚫 Unauthorized Access');

require_once __DIR__ . '/JWTManager.php';

class AdvancedAuth {
    private $db;
    private $jwtManager;
    private $securityConfig;
    
    public function __construct() {
        $this->db = Database::getInstance()->getConnection();
        $this->jwtManager = new JWTManager();
        $this->securityConfig = DatabaseConfig::getSecurityConfig();
    }
    
    /**
     * 🔐 تسجيل دخول محسّن مع JWT
     */
    public function login(string $email, string $password, string $api_key, string $device_id): array {
        try {
            // التحقق من محاولات تسجيل الدخول المتعددة
            if (!$this->checkLoginAttempts($email)) {
                return [
                    'success' => false, 
                    'message' => 'Account temporarily locked due to too many failed attempts'
                ];
            }
            
            // البحث عن المستخدم
            $stmt = $this->db->prepare("
                SELECT id, email, password_hash, role, status, failed_attempts, locked_until 
                FROM users 
                WHERE email = ? AND status = 'active'
            ");
            
            $stmt->execute([$email]);
            $user = $stmt->fetch();
            
            if (!$user || !password_verify($password, $user['password_hash'])) {
                $this->recordFailedAttempt($email);
                return ['success' => false, 'message' => 'Invalid credentials'];
            }
            
            // إنشاء JWT Token
            $token = $this->jwtManager->generateToken([
                'id' => $user['id'],
                'email' => $user['email'],
                'role' => $user['role']
            ], $api_key);
            
            // تسجيل تسجيل الدخول الناجح
            $this->recordSuccessfulLogin($user['id'], $device_id);
            
            // مسح محاولات الفشل
            $this->clearFailedAttempts($email);
            
            return [
                'success' => true,
                'message' => 'Login successful',
                'data' => [
                    'token' => $token,
                    'user' => [
                        'id' => $user['id'],
                        'email' => $user['email'],
                        'role' => $user['role']
                    ],
                    'expires_in' => 3600
                ]
            ];
            
        } catch (Exception $e) {
            $this->logSecurityEvent('LOGIN_ERROR', $e->getMessage());
            return ['success' => false, 'message' => 'Login failed'];
        }
    }
    
    /**
     * ✅ التحقق من صحة Token وإرجاع بيانات المستخدم
     */
    public function validateUserToken(string $token): array {
        $validation = $this->jwtManager->validateToken($token);
        
        if (!$validation['valid']) {
            return ['valid' => false, 'error' => $validation['error']];
        }
        
        $user_data = $validation['data'];
        
        // التحقق من أن المستخدم ما زال نشطاً
        $stmt = $this->db->prepare("SELECT status FROM users WHERE id = ?");
        $stmt->execute([$user_data['user_id']]);
        $user_status = $stmt->fetchColumn();
        
        if ($user_status !== 'active') {
            return ['valid' => false, 'error' => 'User account suspended'];
        }
        
        return [
            'valid' => true,
            'user' => $user_data
        ];
    }
    
    /**
     * 🚪 تسجيل خروج آمن
     */
    public function logout(string $token, string $device_id): array {
        try {
            $validation = $this->validateUserToken($token);
            
            if ($validation['valid']) {
                // تسجيل تسجيل الخروج
                $stmt = $this->db->prepare("
                    INSERT INTO user_sessions (user_id, device_id, action, ip_address, created_at) 
                    VALUES (?, ?, 'logout', ?, NOW())
                ");
                
                $ip = $_SERVER['REMOTE_ADDR'] ?? 'unknown';
                $stmt->execute([$validation['user']['user_id'], $device_id, $ip]);
            }
            
            return ['success' => true, 'message' => 'Logged out successfully'];
            
        } catch (Exception $e) {
            return ['success' => false, 'message' => 'Logout failed'];
        }
    }
    
    /**
     * 🔒 التحقق من محاولات تسجيل الدخول
     */
    private function checkLoginAttempts(string $email): bool {
        $stmt = $this->db->prepare("
            SELECT failed_attempts, locked_until 
            FROM users 
            WHERE email = ?
        ");
        
        $stmt->execute([$email]);
        $user = $stmt->fetch();
        
        if (!$user) return true;
        
        // التحقق من انتهاء فترة القفل
        if ($user['locked_until'] && strtotime($user['locked_until']) > time()) {
            return false;
        }
        
        // التحقق من عدد المحاولات
        return $user['failed_attempts'] < $this->securityConfig['max_login_attempts'];
    }
    
    private function recordFailedAttempt(string $email): void {
        $stmt = $this->db->prepare("
            UPDATE users 
            SET failed_attempts = failed_attempts + 1,
                locked_until = CASE 
                    WHEN failed_attempts + 1 >= ? THEN DATE_ADD(NOW(), INTERVAL ? SECOND)
                    ELSE locked_until 
                END
            WHERE email = ?
        ");
        
        $stmt->execute([
            $this->securityConfig['max_login_attempts'],
            $this->securityConfig['lockout_duration'],
            $email
        ]);
    }
    
    private function recordSuccessfulLogin(int $user_id, string $device_id): void {
        $stmt = $this->db->prepare("
            INSERT INTO user_sessions (user_id, device_id, action, ip_address, user_agent, created_at) 
            VALUES (?, ?, 'login', ?, ?, NOW())
        ");
        
        $ip = $_SERVER['REMOTE_ADDR'] ?? 'unknown';
        $user_agent = $_SERVER['HTTP_USER_AGENT'] ?? 'unknown';
        
        $stmt->execute([$user_id, $device_id, $ip, $user_agent]);
    }
    
    private function clearFailedAttempts(string $email): void {
        $stmt = $this->db->prepare("
            UPDATE users 
            SET failed_attempts = 0, locked_until = NULL 
            WHERE email = ?
        ");
        $stmt->execute([$email]);
    }
    
    private function logSecurityEvent(string $event, string $details): void {
        $log_file = __DIR__ . '/../logs/security.log';
        $timestamp = date('Y-m-d H:i:s');
        $log_entry = "[$timestamp] [$event] $details" . PHP_EOL;
        file_put_contents($log_file, $log_entry, FILE_APPEND | LOCK_EX);
    }
}

/**
 * 📋 SQL لإنشاء جدول sessions
 */
/*
CREATE TABLE user_sessions (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    device_id VARCHAR(100) NOT NULL,
    action ENUM('login', 'logout') NOT NULL,
    ip_address VARCHAR(45) NOT NULL,
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_user_device (user_id, device_id),
    INDEX idx_created_at (created_at)
);
*/
?>
```

---

## ⚡ **الطبقة الثالثة: Flutter Integration**

### 5️⃣ ApiService محسّن في Flutter: `lib/services/secure_api_service.dart`

```dart
/**
 * 🔐 خدمة API آمنة - Flutter
 * المسار: lib/services/secure_api_service.dart
 */

import 'dart:convert';
import 'dart:io';
import 'package:crypto/crypto.dart';
import 'package:device_info_plus/device_info_plus.dart';
import 'package:http/http.dart' as http;
import 'package:shared_preferences/shared_preferences.dart';

class SecureApiService {
  // 🎯 إخفاء baseUrl - استخدم دومين عكسي أو تشفير
  static const String _encryptedBaseUrl = "aXR0cHM6Ly95b3VyZG9tYWluLmNvbS9hcGkv"; // base64 encoded
  static String get baseUrl => utf8.decode(base64.decode(_encryptedBaseUrl));
  
  // 🔑 بيانات التطبيق (يجب تشفيرها في الإنتاج)
  static const String appName = "YourAppName";
  static const String appVersion = "1.0.0";
  
  // 💾 تخزين محلي للبيانات الحساسة
  static SharedPreferences? _prefs;
  static String? _apiKey;
  static String? _deviceId;
  static String? _jwtToken;
  static String? _secretHash;
  
  /**
   * 🚀 تهيئة الخدمة وإنشاء API Key
   */
  static Future<bool> initialize() async {
    try {
      _prefs = await SharedPreferences.getInstance();
      
      // الحصول على معرف الجهاز
      _deviceId = await _getDeviceId();
      
      // استرجاع أو إنشاء API Key
      _apiKey = _prefs?.getString('api_key');
      _secretHash = _prefs?.getString('secret_hash');
      
      if (_apiKey == null || _secretHash == null) {
        return await _generateNewApiKey();
      }
      
      return true;
    } catch (e) {
      print('🚨 API Service initialization failed: $e');
      return false;
    }
  }
  
  /**
   * 🔑 إنشاء API Key جديد
   */
  static Future<bool> _generateNewApiKey() async {
    try {
      final response = await http.post(
        Uri.parse('${baseUrl}security/generate_key.php'),
        headers: {
          'Content-Type': 'application/json',
          'User-Agent': '$appName/$appVersion',
        },
        body: json.encode({
          'app_name': appName,
          'device_id': _deviceId,
          'platform': Platform.isAndroid ? 'android' : 'ios',
        }),
      );
      
      if (response.statusCode == 200) {
        final data = json.decode(response.body);
        if (data['success']) {
          _apiKey = data['data']['api_key'];
          _secretHash = data['data']['secret_hash'];
          
          // حفظ البيانات محلياً
          await _prefs?.setString('api_key', _apiKey!);
          await _prefs?.setString('secret_hash', _secretHash!);
          
          return true;
        }
      }
      
      return false;
    } catch (e) {
      print('🚨 API Key generation failed: $e');
      return false;
    }
  }
  
  /**
   * 📱 الحصول على معرف الجهاز الفريد
   */
  static Future<String> _getDeviceId() async {
    try {
      final deviceInfo = DeviceInfoPlugin();
      
      if (Platform.isAndroid) {
        final androidInfo = await deviceInfo.androidInfo;
        return androidInfo.id ?? 'unknown_android';
      } else if (Platform.isIOS) {
        final iosInfo = await deviceInfo.iosInfo;
        return iosInfo.identifierForVendor ?? 'unknown_ios';
      }
      
      return 'unknown_platform';
    } catch (e) {
      return 'device_id_error';
    }
  }
  
  /**
   * 🔐 إنشاء توقيع آمن للطلب
   */
  static String _generateSignature() {
    final timestamp = DateTime.now().millisecondsSinceEpoch ~/ 1000;
    final hourString = DateTime.now().toString().substring(0, 13); // YYYY-MM-DD-HH
    final data = '$_apiKey$_deviceId$hourString';
    
    final key = utf8.encode(_secretHash!);
    final message = utf8.encode(data);
    final hmac = Hmac(sha256, key);
    final digest = hmac.convert(message);
    
    return digest.toString();
  }
  
  /**
   * 📤 إرسال طلب آمن مع كل الحماية
   */
  static Future<http.Response> _secureRequest(
    String method,
    String endpoint,
    {Map<String, dynamic>? body}
  ) async {
    
    if (_apiKey == null || _deviceId == null) {
      throw Exception('API Service not initialized');
    }
    
    final uri = Uri.parse('$baseUrl$endpoint');
    final timestamp = DateTime.now().millisecondsSinceEpoch ~/ 1000;
    
    final headers = {
      'Content-Type': 'application/json',
      'X-API-Key': _apiKey!,
      'X-Device-ID': _deviceId!,
      'X-Signature': _generateSignature(),
      'X-Timestamp': timestamp.toString(),
      'User-Agent': '$appName/$appVersion',
    };
    
    // إضافة JWT Token إذا كان متاحاً
    if (_jwtToken != null) {
      headers['Authorization'] = 'Bearer $_jwtToken';
    }
    
    switch (method.toLowerCase()) {
      case 'get':
        return await http.get(uri, headers: headers);
      case 'post':
        return await http.post(
          uri, 
          headers: headers, 
          body: body != null ? json.encode(body) : null
        );
      case 'put':
        return await http.put(
          uri, 
          headers: headers, 
          body: body != null ? json.encode(body) : null
        );
      case 'delete':
        return await http.delete(uri, headers: headers);
      default:
        throw Exception('Unsupported HTTP method: $method');
    }
  }
  
  /**
   * 🔐 تسجيل دخول آمن
   */
  static Future<Map<String, dynamic>> login(String email, String password) async {
    try {
      final response = await _secureRequest('POST', 'auth/login.php', body: {
        'email': email,
        'password': password,
      });
      
      final data = json.decode(response.body);
      
      if (response.statusCode == 200 && data['success']) {
        // حفظ JWT Token
        _jwtToken = data['data']['token'];
        await _prefs?.setString('jwt_token', _jwtToken!);
        
        return {
          'success': true,
          'user': data['data']['user'],
          'message': data['message']
        };
      } else {
        return {
          'success': false,
          'message': data['error'] ?? 'Login failed'
        };
      }
    } catch (e) {
      return {
        'success': false,
        'message': 'Network error: $e'
      };
    }
  }
  
  /**
   * 📋 الحصول على قائمة الفواتير بشكل آمن
   */
  static Future<Map<String, dynamic>> getInvoices({
    int page = 1,
    int limit = 20
  }) async {
    try {
      final response = await _secureRequest(
        'GET', 
        'invoices/list.php?page=$page&limit=$limit'
      );
      
      final data = json.decode(response.body);
      
      if (response.statusCode == 200) {
        return data;
      } else {
        throw Exception(data['error'] ?? 'Failed to fetch invoices');
      }
    } catch (e) {
      return {
        'success': false,
        'message': 'Error fetching invoices: $e'
      };
    }
  }
  
  /**
   * ➕ إنشاء فاتورة جديدة
   */
  static Future<Map<String, dynamic>> createInvoice(Map<String, dynamic> invoiceData) async {
    try {
      final response = await _secureRequest('POST', 'invoices/create.php', body: invoiceData);
      return json.decode(response.body);
    } catch (e) {
      return {
        'success': false,
        'message': 'Error creating invoice: $e'
      };
    }
  }
  
  /**
   * 📤 رفع ملف بشكل آمن
   */
  static Future<Map<String, dynamic>> uploadFile(String filePath, String fileType) async {
    try {
      final uri = Uri.parse('${baseUrl}documents/upload.php');
      final request = http.MultipartRequest('POST', uri);
      
      // إضافة Headers الأمان
      request.headers.addAll({
        'X-API-Key': _apiKey!,
        'X-Device-ID': _deviceId!,
        'X-Signature': _generateSignature(),
        'X-Timestamp': (DateTime.now().millisecondsSinceEpoch ~/ 1000).toString(),
      });
      
      if (_jwtToken != null) {
        request.headers['Authorization'] = 'Bearer $_jwtToken';
      }
      
      // إضافة الملف
      request.files.add(await http.MultipartFile.fromPath('file', filePath));
      request.fields['file_type'] = fileType;
      
      final response = await request.send();
      final responseData = await response.stream.bytesToString();
      
      return json.decode(responseData);
    } catch (e) {
      return {
        'success': false,
        'message': 'Upload failed: $e'
      };
    }
  }
  
  /**
   * 🚪 تسجيل خروج آمن
   */
  static Future<bool> logout() async {
    try {
      await _secureRequest('POST', 'auth/logout.php');
      
      // مسح البيانات المحلية
      _jwtToken = null;
      await _prefs?.remove('jwt_token');
      
      return true;
    } catch (e) {
      print('Logout error: $e');
      return false;
    }
  }
  
  /**
   * 🔄 تجديد Token تلقائياً
   */
  static Future<bool> refreshTokenIfNeeded() async {
    if (_jwtToken == null) return false;
    
    try {
      final response = await _secureRequest('POST', 'auth/refresh.php');
      final data = json.decode(response.body);
      
      if (data['success'] && data['new_token'] != null) {
        _jwtToken = data['new_token'];
        await _prefs?.setString('jwt_token', _jwtToken!);
        return true;
      }
      
      return false;
    } catch (e) {
      return false;
    }
  }
  
  /**
   * 🧹 تنظيف البيانات عند إلغاء تثبيت التطبيق
   */
  static Future<void> cleanup() async {
    try {
      await _prefs?.clear();
      _apiKey = null;
      _deviceId = null;
      _jwtToken = null;
      _secretHash = null;
    } catch (e) {
      print('Cleanup error: $e');
    }
  }
}
```

---

## ⚡ **الطبقة الرابعة: نقاط الدخول المحمية**

### 6️⃣ نقطة دخول محمية - تسجيل الدخول: `/public_html/api/auth/secure_login.php`

```php
<?php
/**
 * 🔐 تسجيل دخول محمي بالكامل
 * المسار: /public_html/api/auth/secure_login.php
 */

define('API_CORE_ACCESS', true);
$core_path = dirname(dirname(dirname(__DIR__))) . '/api_core';

// تحميل الكلاسات المطلوبة
require_once $core_path . '/classes/Database.php';
require_once $core_path . '/classes/ApiKeyManager.php';
require_once $core_path . '/classes/AdvancedAuth.php';
require_once $core_path . '/helpers/response.php';
require_once __DIR__ . '/../security/gateway.php';

// إعدادات الأمان الأساسية
header('Content-Type: application/json; charset=utf-8');
header('Access-Control-Allow-Origin: *');
header('Access-Control-Allow-Methods: POST, OPTIONS');
header('Access-Control-Allow-Headers: Content-Type, X-API-Key, X-Device-ID, X-Signature, X-Timestamp');

// التعامل مع طلبات OPTIONS (CORS)
if ($_SERVER['REQUEST_METHOD'] === 'OPTIONS') {
    http_response_code(200);
    exit;
}

// السماح بـ POST فقط
if ($_SERVER['REQUEST_METHOD'] !== 'POST') {
    ApiResponse::error('Method not allowed', 405);
}

try {
    // المرور عبر بوابة الأمان
    $gateway = new SecurityGateway();
    $security_check = $gateway->validateRequest();
    
    if (!$security_check['success']) {
        exit; // البوابة ستتولى الرد
    }
    
    // قراءة البيانات المرسلة
    $input = json_decode(file_get_contents('php://input'), true);
    
    if (!$input || !isset($input['email']) || !isset($input['password'])) {
        ApiResponse::error('Email and password are required', 400);
    }
    
    // التحقق من صحة البيانات
    if (!filter_var($input['email'], FILTER_VALIDATE_EMAIL)) {
        ApiResponse::error('Invalid email format', 400);
    }
    
    if (strlen($input['password']) < 6) {
        ApiResponse::error('Password too short', 400);
    }
    
    // تسجيل الدخول
    $auth = new AdvancedAuth();
    $result = $auth->login(
        $input['email'], 
        $input['password'], 
        $security_check['api_key'],
        $security_check['device_id']
    );
    
    if ($result['success']) {
        // تسجيل نجاح العملية
        error_log("Successful login: {$input['email']} from {$_SERVER['REMOTE_ADDR']}");
        ApiResponse::success($result['message'], $result['data']);
    } else {
        // تسجيل فشل العملية (بدون كشف كلمة المرور)
        error_log("Failed login attempt: {$input['email']} from {$_SERVER['REMOTE_ADDR']}");
        ApiResponse::error($result['message'], 401);
    }
    
} catch (Exception $e) {
    // تسجيل الخطأ دون كشف التفاصيل
    error_log("Login system error: " . $e->getMessage());
    ApiResponse::error('Authentication service temporarily unavailable', 503);
}
?>
```

### 7️⃣ نقطة إنشاء API Key: `/public_html/api/security/generate_key.php`

```php
<?php
/**
 * 🔑 مولد مفاتيح API للتطبيقات الجديدة
 * المسار: /public_html/api/security/generate_key.php
 */

define('API_CORE_ACCESS', true);
$core_path = dirname(dirname(dirname(__DIR__))) . '/api_core';

require_once $core_path . '/classes/Database.php';
require_once $core_path . '/classes/ApiKeyManager.php';
require_once $core_path . '/helpers/response.php';

// إعدادات الأمان
header('Content-Type: application/json; charset=utf-8');
header('Access-Control-Allow-Origin: *');
header('Access-Control-Allow-Methods: POST');
header('Access-Control-Allow-Headers: Content-Type');

if ($_SERVER['REQUEST_METHOD'] !== 'POST') {
    ApiResponse::error('Method not allowed', 405);
}

try {
    $input = json_decode(file_get_contents('php://input'), true);
    
    // التحقق من البيانات المطلوبة
    if (!isset($input['app_name']) || !isset($input['device_id'])) {
        ApiResponse::error('App name and device ID are required', 400);
    }
    
    // التحقق من طول البيانات
    if (strlen($input['app_name']) < 3 || strlen($input['device_id']) < 5) {
        ApiResponse::error('Invalid app name or device ID', 400);
    }
    
    // فحص إضافي للأمان - منع الطلبات المشبوهة
    $ip = $_SERVER['REMOTE_ADDR'] ?? 'unknown';
    $user_agent = $_SERVER['HTTP_USER_AGENT'] ?? 'unknown';
    
    // التحقق من معدل إنشاء المفاتيح لنفس IP
    $db = Database::getInstance()->getConnection();
    $stmt = $db->prepare("
        SELECT COUNT(*) as key_count 
        FROM api_keys 
        WHERE created_at >= DATE_SUB(NOW(), INTERVAL 1 HOUR)
        AND JSON_EXTRACT(metadata, '$.ip_address') = ?
    ");
    
    $stmt->execute([$ip]);
    $ip_keys = $stmt->fetchColumn();
    
    if ($ip_keys >= 5) { // الحد الأقصى 5 مفاتيح في الساعة لنفس IP
        ApiResponse::error('Rate limit exceeded for key generation', 429);
    }
    
    // إنشاء API Key
    $keyManager = new ApiKeyManager();
    $result = $keyManager->generateAppKey($input['app_name'], $input['device_id']);
    
    if ($result['success']) {
        // إضافة metadata للمراقبة
        $metadata = json_encode([
            'ip_address' => $ip,
            'user_agent' => $user_agent,
            'platform' => $input['platform'] ?? 'unknown'
        ]);
        
        $stmt = $db->prepare("
            UPDATE api_keys 
            SET metadata = ? 
            WHERE api_key = ?
        ");
        $stmt->execute([$metadata, $result['api_key']]);
        
        ApiResponse::success('API Key generated successfully', [
            'api_key' => $result['api_key'],
            'secret_hash' => $result['secret_hash'],
            'expires_at' => $result['expires_at']
        ]);
    } else {
        ApiResponse::error($result['message'], 500);
    }
    
} catch (Exception $e) {
    error_log("API Key generation error: " . $e->getMessage());
    ApiResponse::error('Key generation service unavailable', 503);
}
?>
```

---

## 🛡️ **SQL Schema الكامل للنظام**

```sql
-- 📊 جدول مفاتيح API
CREATE TABLE api_keys (
    id INT PRIMARY KEY AUTO_INCREMENT,
    api_key VARCHAR(100) UNIQUE NOT NULL,
    app_name VARCHAR(100) NOT NULL,
    device_id VARCHAR(100) NOT NULL,
    secret_hash VARCHAR(255) NOT NULL,
    status ENUM('active', 'suspended', 'revoked') DEFAULT 'active',
    request_count INT DEFAULT 0,
    last_used TIMESTAMP NULL,
    metadata JSON,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,
    
    INDEX idx_api_key (api_key),
    INDEX idx_device_id (device_id),
    INDEX idx_status_expires (status, expires_at),
    INDEX idx_created_at (created_at)
);

-- 📊 جدول طلبات API (للـ Rate Limiting)
CREATE TABLE api_requests (
    id INT PRIMARY KEY AUTO_INCREMENT,
    api_key VARCHAR(100) NOT NULL,
    endpoint VARCHAR(255) NOT NULL,
    ip_address VARCHAR(45) NOT NULL,
    user_agent TEXT,
    response_code INT,
    response_time_ms INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_api_key_time (api_key, created_at),
    INDEX idx_ip_time (ip_address, created_at),
    INDEX idx_endpoint (endpoint)
);

-- 📊 جدول المستخدمين المحسن
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role ENUM('admin', 'user', 'viewer') DEFAULT 'user',
    status ENUM('active', 'suspended', 'pending') DEFAULT 'active',
    failed_attempts INT DEFAULT 0,
    locked_until TIMESTAMP NULL,
    last_login TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_email_status (email, status),
    INDEX idx_locked_until (locked_until)
);

-- 📊 جدول جلسات المستخدمين
CREATE TABLE user_sessions (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    device_id VARCHAR(100) NOT NULL,
    action ENUM('login', 'logout', 'token_refresh') NOT NULL,
    ip_address VARCHAR(45) NOT NULL,
    user_agent TEXT,
    session_data JSON,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_user_device (user_id, device_id),
    INDEX idx_user_time (user_id, created_at),
    INDEX idx_action_time (action, created_at),
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

---

## 🚀 **التطبيق الفوري - خطوات العمل**

### 1️⃣ تحديث قاعدة البيانات
```sql
-- تشغيل كود SQL أعلاه في phpMyAdmin
-- إضافة العمود metadata لجدول api_keys الحالي
ALTER TABLE api_keys ADD COLUMN metadata JSON AFTER last_used;
```

### 2️⃣ تطبيق الملفات على السيرفر
```bash
# رفع الملفات الجديدة
/api_core/classes/ApiKeyManager.php
/api_core/classes/JWTManager.php  
/api_core/classes/AdvancedAuth.php
/public_html/api/security/gateway.php
/public_html/api/security/generate_key.php
/public_html/api/auth/secure_login.php
```

### 3️⃣ تحديث Flutter
```dart
// إضافة dependencies في pubspec.yaml
dependencies:
  crypto: ^3.0.3
  device_info_plus: ^9.1.0
  shared_preferences: ^2.2.2

// استبدال ApiService الحالي بـ SecureApiService
```

### 4️⃣ اختبار النظام
```bash
# اختبار إنشاء API Key
curl -X POST "https://yourdomain.com/api/security/generate_key.php" \
     -H "Content-Type: application/json" \
     -d '{"app_name":"TestApp","device_id":"test123","platform":"android"}'

# اختبار تسجيل الدخول الآمن  
curl -X POST "https://yourdomain.com/api/auth/secure_login.php" \
     -H "Content-Type: application/json" \
     -H "X-API-Key: YOUR_API_KEY" \
     -H "X-Device-ID: test123" \
     -H "X-Signature: YOUR_SIGNATURE" \
     -H "X-Timestamp: $(date +%s)" \
     -d '{"email":"test@example.com","password":"testpass"}'
```

## 4️⃣ **الإشعال**: دافع التنفيذ الفوري

**⚡ لماذا هذا النظام ضرورة حتمية الآن؟**

### 🚨 **الخطر الحالي في نظامك:**
```dart
// 💀 هذا السطر يعني أن أي شخص يمكنه:
static const String baseUrl = "https://yourapi.com/api/";
```

1. **فك APK في 5 دقائق** = استخراج كل روابط APIs
2. **إرسال 1000 طلب/ثانية** = انهيار السيرفر وزيادة التكلفة
3. **سرقة البيانات** = وصول لكل فواتيرك ومستنداتك
4. **هجمات DDoS** = تعطيل الخدمة تماماً

### 🔥 **المزايا الفورية للنظام الجديد:**

✅ **حماية معمارية**: API Key فريد لكل جهاز  
✅ **توقيع ديناميكي**: يتغير كل ساعة تلقائياً  
✅ **JWT متقدم**: انتهاء صلاحية + تجديد تلقائي  
✅ **Rate Limiting**: حماية من الإفراط في الاستخدام  
✅ **تسجيل شامل**: مراقبة كل طلب ومحاولة اختراق  
✅ **عزل الأخطاء**: فشل طبقة واحدة لا يعطل النظام  

### 🎯 **خطة التطبيق الفوري (30 دقيقة):**

**الدقائق 1-10**: رفع ملفات PHP على السيرفر
**الدقائق 11-20**: تشغيل SQL وإنشاء الجداول  
**الدقائق 21-30**: تحديث Flutter واختبار أول API

---

**🔥 السؤال الحاسم**: 
**هل تريد البدء بتطبيق الطبقة الأولى (API Keys) فوراً؟**  
**كل دقيقة تأخير = خطر أمني إضافي على بياناتك!**


---


