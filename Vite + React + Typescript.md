بما أنك لم تبرمج أي كود مسبقًا، غادي نبداو من الأساسيات باش تفهم المفاهيم قبل ما تدخل للكود.

---

### **1. مقدمة حول Vite و React و TypeScript**
- **Vite**: هو أداة حديثة لإنشاء المشاريع بسرعة، وكيتميز بسرعة التشغيل أثناء التطوير مقارنة بـ CRA (Create React App).
- **React**: مكتبة لبناء واجهات المستخدم التفاعلية باستخدام المكونات (Components).
- **TypeScript**: نسخة مطورة من JavaScript، تضيف التحقق من الأنواع (Types) مما يجعل الكود أكثر أمانًا وسهولة في الصيانة.

---

### **2. إعداد مشروع Vite مع React و TypeScript**
الخطوات اللي تبعتي صحيحة، وغادي نوضحها باش تفهمها مزيان:

1. **إنشاء المشروع**:
   ```sh
   npm create vite@latest kachi
   ```
   هذا الأمر كينشئ مشروع جديد باستخدام Vite.

2. **اختيار الإعدادات**:
   - اختر `React` كإطار عمل.
   - اختر `TypeScript` كمتغير.

3. **الدخول إلى مجلد المشروع**:
   ```sh
   cd kachi
   ```

4. **تثبيت الحزم**:
   ```sh
   npm install
   ```
   هذا الأمر كيحمّل جميع المكتبات اللي تحتاجها.

5. **تشغيل المشروع**:
   ```sh
   npm run dev
   ```
   غادي يظهر لك رابط `http://localhost:5173/` باش تشوف التطبيق في المتصفح.

---

### **3. هيكلة المشروع**
بعد إنشاء المشروع، غادي تلقى الملفات التالية:
- **`src/`**: يحتوي على الكود الرئيسي.
  - **`main.tsx`**: يدخل التطبيق داخل الـ DOM.
  - **`App.tsx`**: الملف الرئيسي للمكون الأساسي.
- **`public/`**: يحتوي على الأيقونات والملفات الثابتة.
- **`index.html`**: الصفحة الرئيسية.

---

### **4. تجربة تعديل التطبيق**
باش تتأكد أن الأمور شغالة، جرب تعديل الملف `App.tsx`:

افتح `src/App.tsx` وبدّل الكود بـ:
```tsx
function App() {
  return <h1>مرحبا بك في React مع TypeScript!</h1>;
}

export default App;
```
احفظ التعديلات، وغادي تشوف النتيجة مباشرة في المتصفح.

---

### **5. الخطوة القادمة**
بما أنك جديد على البرمجة، خاصنا ندوزو لمفاهيم أساسية:
- **JSX و TypeScript في React**.
- **المكونات (Components) والممتلكات (Props)**.
- **حالة المكون (State) والتفاعلات**.

### **6. شنو هو JSX؟**

JSX هو طريقة باش تكتب الكود اللي كيشبه HTML وسط JavaScript أو TypeScript. React كيعتمد عليه باش تبني الواجهات ديالك بطريقة سهلة وقابلة للفهم. مثلا، فاش كتكتب:

```tsx
function App() {
  return <h1>مرحبا بك في React مع TypeScript!</h1>;
}
```

هاد `<h1>...</h1>` راه JSX، وReact كيتكلف يحولو لكود JavaScript اللي كيرسم هاد العنصر فالمتصفح.

#### **ملاحظات على JSX:**
- خاصك دايما ترجع عنصر واحد من الدالة (مثلا عنصر `<div>` أو أي عنصر آخر).
- تقدر تستعمل المتغيرات وسط JSX باستعمال `{}`. مثلا:

```tsx
const name = "سعيد";
return <h1>مرحبا، {name}!</h1>;
```

---

### **7. المكونات (Components) في React**

المكونات هما قطع صغيرة من الكود اللي كتستعملهم باش تبني واجهة المستخدم. كل مكون هو دالة كترجع JSX.

مثال بسيط على مكون:

```tsx
function Welcome() {
  return <p>أهلا وسهلا!</p>;
}
```

وتقدر تستعمل هاد المكون وسط مكون آخر:

```tsx
function App() {
  return (
    <div>
      <h1>تطبيقي الأول</h1>
      <Welcome />
    </div>
  );
}
```

---

### **8. الممتلكات (Props) في React**

الممتلكات أو Props هما معلومات كتبعثهم من مكون الأب لمكون الإبن. مثلا، بغيت ترسل اسم لمكون باش يعرضو:

```tsx
function Welcome(props: { name: string }) {
  return <p>مرحبا، {props.name}!</p>;
}

function App() {
  return <Welcome name="سعيد" />;
}
```

هنا `name` هي Prop، وTypeScript كيعرف النوع ديالها (string).

---

### **9. الحالة (State) في React**

الحالة أو State هي معلومات كتتبدل مع الوقت داخل المكون. مثلا، بغيت تدير عداد كيزيد كل مرة كتضغط على زر:

```tsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>القيمة: {count}</p>
      <button onClick={() => setCount(count + 1)}>زيد 1</button>
    </div>
  );
}
```

- `useState` هي دالة من React كتخليك تدير State.
- `count` هو القيمة الحالية.
- `setCount` هي دالة كتبدل القيمة.

---

### **10. التفاعلات (Events) في React**

تقدر تدير تفاعل مع المستخدم باستعمال الأحداث (Events) بحال الضغط على زر، الكتابة في input، إلخ.

مثال:

```tsx
function App() {
  function handleClick() {
    alert("ضغطتي على الزر!");
  }

  return <button onClick={handleClick}>ضغط هنا</button>;
}
```

---

### **11. تنظيم المشروع وزيادة مكونات**

من بعد ما فهمتي الأساسيات، تقدر تبدا تزيد مكونات وتنظم المشروع ديالك. مثلا، تقدر تدير مجلد `components` وسط `src/` وتحط فيه جميع المكونات ديالك.

مثال:

- `src/components/Welcome.tsx`
- `src/components/Counter.tsx`

وتستوردهم في `App.tsx`:

```tsx
import Welcome from "./components/Welcome";
import Counter from "./components/Counter";

function App() {
  return (
    <div>
      <Welcome name="سعيد" />
      <Counter />
    </div>
  );
}
```

---

### **12. شنو من بعد؟**

دابا عندك فكرة على:
- كيفاش تنشئ مشروع React بVite وTypeScript.
- شنو هو JSX وكيفاش تستعملو.
- كيفاش تدير مكونات وتستعمل Props وState.
- كيفاش تدير تفاعلات مع المستخدم.

إذا بغيتي تزيد تتعمق، تقدر تشوف:
- كيفاش تدير Routing (تنقل بين الصفحات) باستعمال `react-router-dom`.
- كيفاش تدير استدعاء البيانات من API.
- كيفاش تستعمل CSS أو مكتبات التصميم بحال Tailwind أو Material UI.

### **13. التعمق في React و TypeScript**

دابا غادي ندخلو شوية فالتفاصيل باش تولي عندك فكرة أعمق على كيفاش React وTypeScript خدامين، وكيفاش تقدر تبني تطبيقات أكثر تنظيما واحترافية.

---

#### **13.1. أنواع المكونات: دوال (Function Components) وكلاسات (Class Components)**

حالياً، أغلب المطورين كيعتمدو على المكونات اللي هي دوال (Function Components) مع الـ Hooks، ولكن خاصك تعرف أن كاينين حتى المكونات اللي كيتكتبو بالكلاسات (Class Components). فهاد الدرس غادي نركزو على الدوال حيث هما الأسهل والأكثر استعمالا.

---

#### **13.2. Props مع TypeScript**

TypeScript كيعطيك إمكانية تحدد الأنواع ديال الـ Props باش الكود يكون واضح وخالي من الأخطاء.

مثال عملي:

```tsx
type WelcomeProps = {
  name: string;
  age?: number; // علامة الاستفهام تعني أن هاد الخاصية اختيارية
};

function Welcome({ name, age }: WelcomeProps) {
  return (
    <div>
      <p>مرحبا، {name}!</p>
      {age && <p>عندك {age} عام.</p>}
    </div>
  );
}
```

---

#### **13.3. State متقدم: التعامل مع الكائنات والمصفوفات**

تقدر تدير State مشي غير للأرقام أو النصوص، حتى للكائنات (Objects) والمصفوفات (Arrays).

مثال: لائحة المهام (To-Do List)

```tsx
import { useState } from "react";

type Task = {
  id: number;
  text: string;
};

function TodoList() {
  const [tasks, setTasks] = useState<Task[]>([]);
  const [input, setInput] = useState("");

  function addTask() {
    if (input.trim() === "") return;
    setTasks([...tasks, { id: Date.now(), text: input }]);
    setInput("");
  }

  return (
    <div>
      <input
        value={input}
        onChange={e => setInput(e.target.value)}
        placeholder="أضف مهمة جديدة"
      />
      <button onClick={addTask}>إضافة</button>
      <ul>
        {tasks.map(task => (
          <li key={task.id}>{task.text}</li>
        ))}
      </ul>
    </div>
  );
}
```

---

#### **13.4. رفع الحالة (Lifting State Up)**

أحيانا كتحتاج تشارك State بين مكونات مختلفة. الحل هو تدير State في المكون الأب وتبعث القيم والدوال للأبناء عبر Props.

مثال:

```tsx
type CounterProps = {
  count: number;
  onIncrement: () => void;
};

function Counter({ count, onIncrement }: CounterProps) {
  return (
    <div>
      <p>القيمة: {count}</p>
      <button onClick={onIncrement}>زيد 1</button>
    </div>
  );
}

function App() {
  const [count, setCount] = useState(0);

  return <Counter count={count} onIncrement={() => setCount(count + 1)} />;
}
```

---

#### **13.5. التعامل مع الأحداث (Events) و TypeScript**

TypeScript كيساعدك حتى فالأحداث. مثلا، فاش كتستعمل input:

```tsx
function App() {
  function handleChange(e: React.ChangeEvent<HTMLInputElement>) {
    console.log(e.target.value);
  }

  return <input onChange={handleChange} />;
}
```

---

#### **13.6. استعمال useEffect**

`useEffect` هو Hook كيسمح لك تدير عمليات جانبية (Side Effects) بحال جلب البيانات من API أو تغيير عنوان الصفحة.

مثال: تغيير عنوان الصفحة

```tsx
import { useEffect } from "react";

function App() {
  useEffect(() => {
    document.title = "مرحبا بك!";
  }, []); // [] تعني أن هاد الكود غادي يتنفذ غير مرة وحدة

  return <h1>شوف العنوان فوق!</h1>;
}
```

---

#### **13.7. جلب البيانات من API**

مثال عملي باستعمال `fetch` و`useEffect`:

```tsx
import { useEffect, useState } from "react";

type User = {
  id: number;
  name: string;
};

function UsersList() {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/users")
      .then(res => res.json())
      .then(data => {
        setUsers(data);
        setLoading(false);
      });
  }, []);

  if (loading) return <p>جار التحميل...</p>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

#### **13.8. التوجيه بين الصفحات (Routing) باستعمال react-router-dom**

باش تدير صفحات مختلفة فالتطبيق ديالك، خاصك تستعمل مكتبة `react-router-dom`.

1. ثبت المكتبة:
   ```sh
   npm install react-router-dom
   ```

2. استعملها فالكود:

```tsx
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

function Home() {
  return <h2>الصفحة الرئيسية</h2>;
}

function About() {
  return <h2>حول التطبيق</h2>;
}

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">الرئيسية</Link> | <Link to="/about">حول</Link>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}
```

---

#### **13.9. إضافة CSS وتزيين التطبيق**

تقدر تزيد CSS عادي فـ `src/App.css` أو تستعمل مكتبات بحال Tailwind CSS أو Material UI.

مثال بسيط:

```css
/* src/App.css */
h1 {
  color: #007bff;
  text-align: center;
}
```

واستورد الملف فـ `App.tsx`:

```tsx
import "./App.css";
```

---

#### **13.10. تنظيم المشروع بشكل احترافي**

من بعد ما يكبر المشروع ديالك، حاول تنظم الملفات والمجلدات:

- `src/components/` : جميع المكونات.
- `src/pages/` : الصفحات الرئيسية.
- `src/hooks/` : الدوال المساعدة (Custom Hooks).
- `src/types/` : أنواع TypeScript اللي كتستعملهم فالمشروع.

---

### **14. نصائح للمبتدئين**

- حاول تجرب بيدك كل مثال وتبدل فيه باش تفهم مزيان.
- استعمل خاصية البحث فالمتصفح ديالك أو فـ VS Code باش تلقى الحلول بسرعة.
- ماتخافش من الأخطاء، كل خطأ هو فرصة للتعلم.
- وثائق React وTypeScript غادي تعاونك بزاف:  
  [React Documentation](https://react.dev/)  
  [TypeScript Handbook](https://www.typescriptlang.org/docs/)

---

