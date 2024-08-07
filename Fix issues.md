##Fix_issue_1 :

The include file 'package:flutter_lints/flutter.yaml' in 'C:\Users\anass\Desktop\New folder\rayonnage_fil\analysis_options.yaml' can't be found when analyzing 'C:\Users\anass\Desktop\New folder\rayonnage_fil'.

Explain : 
الرسالة تقول أن الملف الذي يتضمنه analysis_options.yaml من حزمة flutter_lints في المسار C:\Users\anass\Desktop\New folder\rayonnage_fil\analysis_options.yaml لا يمكن العثور عليه عند تحليل المشروع في المسار C:\Users\anass\Desktop\New folder\rayonnage_fil.
هذا الخطأ يحدث عادةً لأن الملف flutter.yaml الذي يحتاجه التحليل غير موجود في المسار المحدد. لتحليل المشروع بنجاح، تحتاج إلى التأكد من وجود الملف وتوجيه المسار بشكل صحيح.

Solution :
flutter pub add dev:flutter_lints
