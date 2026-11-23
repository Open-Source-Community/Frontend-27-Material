# أمثلة تطبيقية جديدة لأساسيات CSS (Fresh Examples)

يحتوي هذا الملف على مجموعة أمثلة عملية جديدة ومختلفة لتطبيق جميع مفاهيم **CSS Basics** المشروحة في المحاضرة.

---

## 1. طرق ربط الـ CSS والمحددات (CSS Setup & Selectors)

### مثال: تنسيق تنبيه (Alert Banner) بطرق مختلفة
```html
<!-- 1. Inline CSS -->
<div style="background-color: #ffeaa7; color: #d63031; padding: 10px;">
  تنبيه سريع: يرجى حفظ التغييرات!
</div>

<!-- 2. Internal & External CSS -->
<header id="main-navbar">
  <a href="#" class="nav-link">الرئيسية</a>
  <a href="#" class="nav-link">المقالات</a>
</header>
```

```css
/* Element Selector */
a {
  text-decoration: none;
}

/* Class Selector */
.nav-link {
  color: #2d3436;
  margin-left: 15px;
}

/* ID Selector */
#main-navbar {
  background-color: #dfe6e9;
  padding: 15px;
}
```

---

## 2. تنسيق النصوص والألوان (Text & Colors)

### مثال: تنسيق مقال أو اقتباس (Article Quote)
```html
<blockquote class="article-quote">
  "التصميم الجيد يجعل المنتج مفهوماً وسهل الاستخدام."
</blockquote>
```

```css
.article-quote {
  color: #1e272e;                  /* لون النص */
  font-size: 22px;                 /* حجم الخط */
  font-weight: 700;                /* سُمك الخط */
  text-align: right;               /* محاذاة النص */
  line-height: 1.8;                /* المسافة بين الأسطر */
  text-decoration: underline dotted #00a8ff; /* تسطير منقط */
}
```

### أمثلة على صيغ الألوان (درجات اللون الأزرق الفيروزي):
```css
.color-name { color: teal; }
.color-hex  { color: #00A8FF; }
.color-rgb  { color: rgb(0, 168, 255); }
.color-hsl  { color: hsl(200, 100%, 50%); }
.color-rgba { background-color: rgba(0, 168, 255, 0.2); } /* شفافية 20% */
```

---

## 3. الخلفيات (Backgrounds)

### مثال: بانر ترحيبي (Hero Banner) بصورة وتدرج لوني
```css
.hero-banner {
  height: 300px;
  background-color: #0c2461; /* لون احتياطي في حال تأخر تحميل الصورة */
  background-image: linear-gradient(to right, rgba(12, 36, 97, 0.8), rgba(30, 55, 153, 0.4)), url("banner.jpg");
  background-repeat: no-repeat;
  background-position: right center;
  background-size: contain; /* إظهار الصورة بالكامل دون قص */
}

/* نفس الخصائص باختصار Shorthand */
.hero-shorthand {
  background: #0c2461 url("banner.jpg") no-repeat right center;
}
```

---

## 4. الخطوط (Fonts)

### مثال: تنسيق بطاقة منتج باستخدام عائلات خطوط مختلفة
```css
/* خط بـ Serif للعناوين الكلاسيكية */
.product-title {
  font-family: "Playfair Display", Georgia, serif;
  font-size: 26px;
  font-weight: 700;
}

/* خط Sans-serif للوصف الحديث */
.product-desc {
  font-family: "Tahoma", "Open Sans", sans-serif;
  font-size: 16px;
  font-style: italic;
}

/* خط Monospace للأكواد أو أرقام الخصم */
.promo-code {
  font-family: "Fira Code", "Courier New", monospace;
  font-weight: 600;
}
```

---

## 5. نموذج الصندوق والأبعاد (The Box Model & Sizing)

### مثال: زر شراء (CTA Button) وصندوق إشعار متجاوب
```css
.buy-button {
  box-sizing: border-box;
  width: 220px;
  min-width: 150px;
  max-width: 100%;
  height: 50px;
  
  /* من الداخل للخارج */
  padding: 12px 24px;            /* مسافة داخلية: أعلى/أسفل 12px، يمين/يسار 24px */
  border: 3px solid #009432;     /* إطار أخضر */
  margin: 20px 10px 30px 10px;   /* مسافة خارجية: أعلى يمين أسفل يسار */
  
  background-color: #A3CB38;
  color: #ffffff;
}
```

---

## 6. خصائص العرض والإخفاء (Display & Visibility)

### مثال 1: الفرق بين `block` و `inline` و `inline-block` في شريط وسوم (Tags)
```html
<h3 class="section-heading">الوسوم الشائعة:</h3>
<span class="tag-inline">#برمجة</span>
<span class="tag-inline">#تصميم</span>
<a href="#" class="badge-inline-block">ابدأ الآن</a>
```

```css
/* يأخذ العرض الكامل ويبدأ سطراً جديداً */
.section-heading {
  display: block;
  width: 100%;
}

/* يبقى في نفس السطر ويتجاهل width و height */
.tag-inline {
  display: inline;
  background-color: #f1f2f6;
  padding: 4px 8px;
}

/* يبقى في نفس السطر ويقبل width و height */
.badge-inline-block {
  display: inline-block;
  width: 120px;
  height: 40px;
  line-height: 40px;
  text-align: center;
  background-color: #ff4757;
  color: white;
}
```

### مثال 2: الفرق العملي بين `display: none` و `visibility: hidden`
```html
<div class="step">الخطوة 1</div>
<div class="step removed-step">الخطوة 2 (محذوفة من التخطيط)</div>
<div class="step">الخطوة 3 (تصعد مكان الخطوة 2)</div>

<hr>

<div class="step">الخطوة A</div>
<div class="step invisible-step">الخطوة B (مخفية مع بقاء مكانها فارغاً)</div>
<div class="step">الخطوة C (تبقى في مكانها دون تحرك)</div>
```

```css
.removed-step {
  display: none; /* تختفي تماماً ولا تحجز أي مساحة */
}

.invisible-step {
  visibility: hidden; /* تختفي بصرياً لكن مساحتها تظل محجوزة */
}
```

---

## 7. معركة الأولوية (Specificity Battle)

### مثال: تنسيق زر إرسال (Submit Button) متأثر بعدة قواعد
```html
<button id="checkout-btn" class="btn primary-btn">إتمام الطلب</button>
```

```css
/* 1. محدد العنصر (Element) -> أضعف أولوية (1 نقطة) */
button {
  background-color: gray;
}

/* 2. محدد الفئة (Class) -> أولوية متوسطة (10 نقاط) */
.btn {
  background-color: blue;
}

/* 3. قاعدة ثانية بنفس قوة الكلاس (10 نقاط) -> تفوز على .btn لأنها مكتوبة بعدها */
.primary-btn {
  background-color: teal;
}

/* 4. محدد المعرّف (ID) -> الأقوى هنا (100 نقطة) -> يفوز ويصبح لون الزر ذهبياً */
#checkout-btn {
  background-color: #e1b12c;
}
```

---

## 8. تطبيق عملي متكامل (Mini Project: Pricing Card)

مثال يجمع كل ما سبق لبناء بطاقة باقة اشتراك (Pricing Card):

```html
<div class="pricing-card">
  <span class="plan-badge">الأكثر طلباً</span>
  <h2 class="plan-title">الباقة الاحترافية</h2>
  <p class="plan-price">$19 / شهرياً</p>
  <ul class="plan-features">
    <li>وصول غير محدود للدروس</li>
    <li>مشاريع تطبيقية أسبوعية</li>
  </ul>
  <button class="subscribe-btn">اشترك الآن</button>
</div>
```

```css
.pricing-card {
  box-sizing: border-box;
  width: 90%;
  max-width: 320px;
  margin: 30px auto;
  padding: 25px;
  background-color: #ffffff;
  border: 2px solid #3742fa;
  border-radius: 12px;
  font-family: 'Cairo', sans-serif;
  text-align: center;
}

.plan-badge {
  display: inline-block;
  background-color: rgba(55, 66, 250, 0.1);
  color: #3742fa;
  padding: 5px 12px;
  font-size: 13px;
  font-weight: bold;
}

.plan-title {
  color: #2f3542;
  font-size: 24px;
  margin: 15px 0 5px 0;
}

.plan-price {
  color: #3742fa;
  font-size: 28px;
  font-weight: 800;
}

.subscribe-btn {
  display: block;
  width: 100%;
  padding: 12px;
  margin-top: 20px;
  background-color: #3742fa;
  color: #ffffff;
  border: none;
  font-size: 16px;
  font-weight: bold;
}
```