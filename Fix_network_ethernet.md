#  الاتصال عبر Ethernet موجود ولكن النظام كيعرف الشبكة كـ "Unidentified network"، وهذا غالبًا كيعني أن الجهاز ما قدرش يحصل على عنوان IP صالح من الشبكة.


C:\Windows\System32>ipconfig /release

Windows IP Configuration

No operation can be performed on Bluetooth Network Connection while it has its media disconnected.

Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::c225:8725:c26a:755e%22
   Autoconfiguration IPv4 Address. . : 169.254.179.65
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Default Gateway . . . . . . . . . :

Ethernet adapter Bluetooth Network Connection:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

C:\Windows\System32>ipconfig /renew

Windows IP Configuration

No operation can be performed on Bluetooth Network Connection while it has its media disconnected.

Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::c225:8725:c26a:755e%22
   Default Gateway . . . . . . . . . : 192.168.1.254

Ethernet adapter Bluetooth Network Connection:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

C:\Windows\System32>netsh int ip reset
Resetting Compartment Forwarding, OK!
Resetting Compartment, OK!
Resetting Control Protocol, OK!
Resetting Echo Sequence Request, OK!
Resetting Global, OK!
Resetting Interface, OK!
Resetting Anycast Address, OK!
Resetting Multicast Address, OK!
Resetting Unicast Address, OK!
Resetting Neighbor, OK!
Resetting Path, OK!
Resetting Potential, OK!
Resetting Prefix Policy, OK!
Resetting Proxy Neighbor, OK!
Resetting Route, OK!
Resetting Site Prefix, OK!
Resetting Subinterface, OK!
Resetting Wakeup Pattern, OK!
Resetting Resolve Neighbor, OK!
Resetting , OK!
Resetting , OK!
Resetting , OK!
Resetting , OK!
Resetting , failed.
Access is denied.

Resetting , OK!
Resetting , OK!
Resetting , OK!
Resetting , OK!
Resetting , OK!
Resetting , OK!
Resetting , OK!
Resetting , OK!
Resetting , OK!
Resetting , OK!
Restart the computer to complete this action.


C:\Windows\System32>netsh winsock reset

Sucessfully reset the Winsock Catalog.
You must restart the computer in order to complete the reset.


C:\Windows\System32>ipconfig /flushdns

Windows IP Configuration

Successfully flushed the DNS Resolver Cache.

C:\Windows\System32>

### 🧠 خلاصة الوضع الحالي:

* كارت Ethernet شاد الشبكة ولكن ما خذاش IP من DHCP.
* النظام عطاه IP تلقائي من نوع: `169.254.x.x` ← هذا معناه أن DHCP **فشل**.
* الـ Gateway ظهر بعد محاولة `renew` (`192.168.1.254`), ولكن مازال الاتصال غير محدد أو محدود.

---

### ✅ الخطوات القادمة لحل المشكل:

#### 1. **تعيين IP يدويًا (Static IP):**

نجرب نعطي IP يدويًا ونشوف واش كيخدم:

##### 🔧 الطريقة:

1. افتح "Control Panel" > "Network and Sharing Center".
2. اختر "Change adapter settings".
3. كليك يمين على "Ethernet" > "Properties".
4. اختر "Internet Protocol Version 4 (TCP/IPv4)" > "Properties".
5. حدد الخيار: **Use the following IP address** وأدخل المعلومات التالية:

| الحقل           | القيمة          |
| --------------- | --------------- |
| IP address      | `192.168.1.100` |
| Subnet mask     | `255.255.255.0` |
| Default gateway | `192.168.1.254` |
| Preferred DNS   | `8.8.8.8`       |
| Alternate DNS   | `8.8.4.4`       |

ثم اضغط OK، OK، وأعد المحاولة.

---

#### 2. **التأكد من DHCP في الراوتر:**

إذا التعيين اليدوي خدم، فالمشكل غالبًا في إعدادات DHCP في الراوتر:

* دخل للراوتر من المتصفح: [http://192.168.1.254](http://192.168.1.254)
* تحقق أن **DHCP مفعّل**
* تأكد أن المدى (range) فيه عناوين كافية (مثلاً من 192.168.1.100 إلى 192.168.1.200)

---

#### 3. **اختبار الشبكة:**

بعد التعيين اليدوي:

* جرب تفتح المتصفح تدخل [http://192.168.1.254](http://192.168.1.254)
* جرب تعمل `ping` من CMD:

  ```bash
  ping 8.8.8.8
  ping google.com
  ```

---

إذا بقى المشكل، جاوبني على هاد الأسئلة باش نكمل معاك:

1. واش أجهزة أخرى فهاد الشبكة خدامة مزيان؟
2. واش كتستعمل سويتش أو مباشرة فالراوتر؟
3. واش كاين جدار حماية (Firewall) أو برنامج حماية مخصص؟


