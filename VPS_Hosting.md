---

# 🚀 **دليل شامل بالدارجة المغربية: كيفاش ترفع مشروع MERN Stack على VPS ديالك (Hostinger)**

---

## 🔑 **معلوماتك الخاصة (باش الخدمة تكون واضحة وسهلة):**

- **IP ديال VPS:** `191.96.1.148`
- **الدخول للسيرفر عبر SSH:**  
  SSH هو واحد البروتوكول كيمكنك تدخل للسيرفر ديالك عن بعد وتتحكم فيه بحال إلى كنت جالس قدامو.
  ```bash
  ssh root@191.96.1.148
  ```
  - `root` هو المستخدم الرئيسي (عندو جميع الصلاحيات).
  - أول مرة تدخل غادي يطلب منك تأكد الهوية (ممكن يطلب منك تكتب yes أو تدخل الباسورد).

- **الدومينات لي عندك:**  
  هادو هما الأسامي لي غادي يستعملوهم الناس باش يوصلو للموقع ديالك:
  ```
  amrtechso.com
  www.amrtechso.com
  ```

- **رابط GitHub ديال المشروع:**  
  هنا فين مخزن الكود ديالك كامل:
  ```
  https://github.com/anassakera/amr-landing-nexus.git
  ```

---

## ✅ **1. تجهيز السيرفر VPS**

### **علاش هاد الخطوة؟**
باش تقدر تخدم على السيرفر خاصك تجهزو وتثبت فيه الأدوات لي غادي تحتاجهم.

### **الخطوات:**

#### 1.1 الدخول للسيرفر:
```bash
ssh root@191.96.1.148
```
- إذا كنت على ويندوز، استعمل [PuTTY](https://www.putty.org/) أو CMD.
- على Mac أو Linux، استعمل Terminal مباشرة.

#### 1.2 تحديث النظام:
```bash
sudo apt update
sudo apt upgrade -y
```
- `sudo apt update`: كيجبد آخر المعلومات على البرامج لي ممكن تثبتهم.
- `sudo apt upgrade -y`: كيدير تحديث للبرامج لي عندك دابا.

#### 1.3 تثبيت Node.js و npm:
Node.js هو لي كيشغل الكود ديال الباك إند (Node/Express)، وnpm هو مدير الحزم (باش تثبت المكتبات).
```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo bash -
sudo apt-get install -y nodejs
```
- `curl ... | sudo bash -`: كيجبد السكريبت لي كيدير إعدادات Node.js.
- `sudo apt-get install -y nodejs`: كيثبت Node.js و npm.

#### 1.4 تثبيت Git:
Git هو لي غادي تستعمل باش تجبد الكود من GitHub.
```bash
sudo apt install -y git
```

---

## ✅ **2. تثبيت MongoDB**

### **علاش؟**
MongoDB هي قاعدة البيانات لي غادي تخزن فيها المعلومات ديال الموقع (بحال المستخدمين، المنتجات...).

### **كيفاش؟**
- ممكن تثبتها مباشرة على السيرفر أو تستعمل خدمة خارجية (MongoDB Atlas).
- باش تثبتها على السيرفر، تابع هاد الشرح المفصل:  
  [شرح تثبيت MongoDB على VPS](https://github.com/GreatStackDev/notes/blob/main/MongoDB_Setup_on_VPS.md)

**ملاحظة:**  
من بعد التثبيت، تأكد أن MongoDB خدامة:
```bash
sudo systemctl status mongod
```
إذا لقيتي "active (running)" راها خدامة مزيان.

---

## ✅ **3. رفع الباك إند (Express و Node.js)**

### **علاش؟**
الباك إند هو لي كيتكلف بالمنطق ديال الموقع (API، التعامل مع قاعدة البيانات...).

### **الخطوات:**

#### 3.1 إنشاء مجلد المشاريع:
```bash
mkdir -p /var/www
cd /var/www
```
- `/var/www` هو المكان الكلاسيكي لي كيتخزن فيه الكود ديال المواقع على السيرفرات.

#### 3.2 جلب الكود من GitHub:
```bash
git clone https://github.com/anassakera/amr-landing-nexus.git
cd amr-landing-nexus/backend
```
- `git clone ...`: كينسخ المشروع كامل من GitHub للسيرفر ديالك.
- `cd .../backend`: كيدخلك لمجلد الباك إند.

#### 3.3 تثبيت المكتبات:
```bash
npm install
```
- هاد الأمر كيقرا ملف `package.json` ويثبت جميع المكتبات لي محتاجهم المشروع.

#### 3.4 إعداد ملف البيئة (.env):
```bash
nano .env
```
- `.env` فيه معلومات سرية (بحال رابط MongoDB، مفاتيح API...).
- مثال:
  ```
  MONGO_URI=mongodb://localhost:27017/amrdb
  JWT_SECRET=supersecretkey
  PORT=4000
  ```
- من بعد ما تكتب المعلومات، ضغط `Ctrl + X`، ثم `Y`، ثم `Enter` باش تسجل وتخرج.

#### 3.5 تشغيل الباك إند باستعمال pm2:
pm2 هو برنامج كيساعدك تخلي السيرفر ديالك خدام حتى إلا طاح أو تسد التيرمينال.
```bash
npm install -g pm2
pm2 start server.js --name amr-backend
```
- `server.js` هو الملف الرئيسي لي كيشغل الباك إند (تأكد من الإسم في مشروعك).
- `--name amr-backend`: كيعطي اسم للعملية باش تعرفها بسهولة.

#### 3.6 باش pm2 يبقى خدام حتى من بعد restart:
```bash
pm2 startup
pm2 save
```
- `pm2 startup`: كينشئ سكريبت باش pm2 يبدا مع كل إعادة تشغيل.
- `pm2 save`: كيسجل جميع العمليات لي خدامة دابا.

#### 3.7 فتح البورت في الجدار الناري (firewall):
الجدار الناري كيحمي السيرفر ديالك من الهجمات، ولكن خاصك تفتح البورتات لي غادي تحتاجهم.
```bash
sudo ufw enable
sudo ufw allow 'OpenSSH'
sudo ufw allow 4000
```
- `OpenSSH`: باش تبقى تقدر تدخل عبر SSH.
- `4000`: هو البورت لي خدام عليه الباك إند (تأكد من الرقم في .env).

---

## ✅ **4. رفع الفرونت إند (React)**

### **علاش؟**
الفرونت إند هو لي كيشوفو الزوار (واجهة الموقع).

### **الخطوات:**

#### 4.1 الدخول لمجلد الفرونت إند:
```bash
cd /var/www/amr-landing-nexus/frontend
```

#### 4.2 تثبيت المكتبات:
```bash
npm install
```
- بحال الباك إند، كيقرا `package.json` ويثبت كلشي.

#### 4.3 إعداد ملف البيئة (.env) إذا كان ضروري:
```bash
nano .env
```
- مثلا إذا عندك API URL أو مفاتيح خاصة بالفرونت إند.

#### 4.4 بناء المشروع:
```bash
npm run build
```
- هاد الأمر كينشئ مجلد `dist` أو `build` فيه ملفات HTML, CSS, JS لي غادي يتخدمو مباشرة على السيرفر.

#### 4.5 تثبيت Nginx:
Nginx هو سيرفر ويب سريع بزاف، كيتستعمل باش يخدم ملفات الفرونت إند ويوجه الطلبات.
```bash
sudo apt install -y nginx
sudo ufw allow 'Nginx Full'
```
- `Nginx Full`: كيسمح للويب سيرفر يدوز من الجدار الناري.

#### 4.6 إعداد Nginx للفرونت إند:
```bash
nano /etc/nginx/sites-available/amrtechso.com.conf
```
- هاد الملف فيه الإعدادات لي غادي تقول لـ Nginx فين يلقى ملفات الموقع.

ضيف هاد الكود:
```nginx
server {
    listen 80;
    server_name amrtechso.com www.amrtechso.com;

    location / {
        root /var/www/amr-landing-nexus/frontend/dist;
        try_files $uri /index.html;
    }
}
```
- `listen 80;`: البورت 80 هو البورت الافتراضي للويب (HTTP).
- `server_name ...`: الدومينات لي غادي يخدم عليهم هاد السيرفر.
- `root ...`: المسار فين كاينين ملفات الفرونت إند.
- `try_files $uri /index.html;`: أي طلب ما لقاوش كيرجع للصفحة الرئيسية (مهم فـ SPA بحال React).

#### 4.7 تفعيل الموقع:
```bash
ln -s /etc/nginx/sites-available/amrtechso.com.conf /etc/nginx/sites-enabled/
```
- هاد الأمر كينشئ رابط رمزي باش Nginx يعرف هاد الإعدادات.

#### 4.8 التأكد من الإعدادات وإعادة تشغيل Nginx:
```bash
nginx -t
systemctl restart nginx
```
- `nginx -t`: كيتأكد من أن الإعدادات صحيحة.
- `systemctl restart nginx`: كيعيد تشغيل Nginx باش يطبق التغييرات.

---

## ✅ **5. إعداد Nginx كـ Reverse Proxy للباك إند (admin.amrtechso.com)**

### **علاش؟**
Reverse Proxy كيسمح لك تخبي الباك إند وراء Nginx، وتخلي الطلبات يدوزو عبر Nginx (أكثر أمان وتحكم).

### **الخطوات:**

#### 5.1 إعداد ملف جديد للباك إند:
```bash
nano /etc/nginx/sites-available/admin.amrtechso.com.conf
```

ضيف هاد الكود:
```nginx
server {
    listen 80;
    server_name admin.amrtechso.com;

    location / {
        proxy_pass http://localhost:4000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
- `proxy_pass http://localhost:4000;`: أي طلب جاي لـ admin.amrtechso.com غادي يمشي للباك إند لي خدام على البورت 4000.
- باقي السطور كيزيدو معلومات على الطلب (IP ديال الزبون، البروتوكول...).

#### 5.2 تفعيل الموقع:
```bash
ln -s /etc/nginx/sites-available/admin.amrtechso.com.conf /etc/nginx/sites-enabled/
systemctl restart nginx
```

---

## ✅ **ربط الدومينات مع السيرفر (DNS)**

### **علاش؟**
باش أي واحد يكتب الدومين ديالك في المتصفح، يوصل للسيرفر ديالك.

### **كيفاش؟**
- دخل للوحة التحكم ديال الدومين (GoDaddy, Namecheap...).
- قلب على DNS Management أو Domain Records.
- ضيف سجلات من نوع A:

| Type | Host/Name | IP Address      |
|------|-----------|-----------------|
| A    | @         | 191.96.1.148    | ← amrtechso.com
| A    | www       | 191.96.1.148    | ← www.amrtechso.com
| A    | admin     | 191.96.1.148    | ← admin.amrtechso.com

- ممكن ياخد شوية الوقت (من دقائق حتى ساعات) باش يتحدث الـ DNS.

---

## ✅ **6. تثبيت شهادة SSL (HTTPS)**

### **علاش؟**
SSL كتحمي البيانات بين الزبون والسيرفر (HTTPS)، وكتعطي ثقة للموقع ديالك.

### **الخطوات:**

#### 6.1 تثبيت Certbot:
```bash
sudo apt install -y certbot python3-certbot-nginx
```
- Certbot هو برنامج كيساعدك تثبت شهادات SSL مجانا من Let's Encrypt.

#### 6.2 طلب الشهادة:
```bash
certbot --nginx -d amrtechso.com -d www.amrtechso.com -d admin.amrtechso.com
```
- هاد الأمر غادي يضبط SSL تلقائيا فـ Nginx ويعطيك HTTPS.

#### 6.3 التأكد من التجديد التلقائي:
```bash
certbot renew --dry-run
```
- باش تتأكد أن الشهادة غادي تتجدد بوحدها قبل ما تسالي الصلاحية.

---

## 🛠️ **نصائح تقنية إضافية:**

- **الأمان:**  
  - متخليش البورتات مفتوحة بلا سبب.
  - استعمل كلمات سر قوية.
  - دير نسخ احتياطية من قاعدة البيانات والكود ديالك.

- **المراقبة:**  
  - استعمل أوامر بحال `htop` أو `top` باش تراقب الموارد ديال السيرفر.
  - pm2 فيه لوحة تحكم (`pm2 monit`).

- **التحديثات:**  
  - ديما دير تحديثات للنظام والبرامج باش تبقى آمن.

---

## 🎉 **مبروك عليك!**

دابا الموقع ديالك MERN Stack خدام على VPS ديالك بطريقة احترافية وآمنة، وعندك تحكم كامل فكلشي.

