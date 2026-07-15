همان‌طور که در فایل قبلی توافق کردیم، برای رفع مشکل به‌هم‌ریختگی و حفظ بی‌نقصِ ساختار راست‌چین (RTL)، در ابتدای تمام خطوط و پاراگراف‌هایی که با کلمات انگلیسی شروع می‌شدند، یک کلمهٔ توصیفی فارسی (مثل: *مفهوم، محور، هدف، تارگت، تکنیک، گزینه* و...) اضافه کردم و اعداد تیترها را به فارسی برگرداندم.

فایل اصلاح‌شده، کاملاً خوانا و بدون هیچ‌گونه به‌هم‌ریختگی تقدیم شما:

---

این فایل قابل قبول است. اسم آپلودش **chapter-10.pdf** است، ولی محتوایش **Hardware Attacks Taxonomy** است و در لیست ترم تو با عنوان **09-Hardware Attacks Taxonomy.pdf** آمده؛ پس تدریس شده و باید بخوانیم.

# گام ۱: آموزش مفهومی و مفصل

## Deep Explanation — Hardware Attacks Taxonomy

### ۱. مفهوم Hardware Attack یعنی چه؟

مفهوم **Hardware Attack** یعنی مهاجم از یک **Hardware Vulnerability** استفاده کند تا به هدف امنیتی خودش برسد. این هدف می‌تواند دزدیدن اطلاعات، خراب‌کردن داده یا از کار انداختن یک سرویس باشد. نکتهٔ مهم این فصل این است که حملهٔ سخت‌افزاری را فقط با اسم حمله حفظ نکنیم؛ باید بتوانیم آن را از چند زاویه طبقه‌بندی کنیم: **Goal، Target، Domain، Modality**. نمودار صفحهٔ اول دقیقاً همین چهار شاخه را نشان می‌دهد: هدف حمله، هدف مورد حمله، دامنهٔ حمله، و روش اجرای حمله.

تشبیه ساده: فرض کن یک گاوصندوق داری. یک مهاجم ممکن است بخواهد پول داخلش را بدزدد؛ این می‌شود **Stealing**. مهاجم دیگر ممکن است پول‌ها را بسوزاند یا سندها را تغییر دهد؛ این می‌شود **Corrupting**. مهاجم سوم شاید فقط بخواهد قفل را طوری خراب کند که صاحب گاوصندوق نتواند به آن دسترسی پیدا کند؛ این می‌شود **Inhibiting**. در سخت‌افزار هم همین منطق وجود دارد. مهاجم ممکن است **Cryptographic Key** را بدزدد، یک **Memory Word** را تغییر دهد، یا یک **Security Mechanism** را از کار بیندازد.

این طبقه‌بندی برای امتحان خیلی مهم است، چون طراح سؤال ممکن است اسم حمله را بدهد و از تو بخواهد آن را از نظر هدف، روش یا دامنه تشخیص دهی. مثلاً **Power Analysis** معمولاً یک حملهٔ **Non-invasive Passive Physical Attack** است، چون دستگاه را باز نمی‌کند، فقط توان مصرفی را اندازه‌گیری می‌کند. اما **Fault Injection** معمولاً **Non-invasive Active Physical Attack** است، چون مهاجم با glitch، laser یا clock manipulation دستگاه را به حالت غیرعادی می‌برد. اگر این چهار بعد را بلد باشی، حتی اگر اسم حمله جدید باشد، می‌توانی منطقی تحلیلش کنی.

---

### ۲. محور Goal: مفاهیم Steal, Corrupt, Inhibit و ارتباط با CIA

اولین محور طبقه‌بندی **Goal** است. فصل سه هدف اصلی می‌دهد: **Steal، Corrupt، Inhibit**. این سه در واقع همان سه ضلع معروف **CIA Triad** هستند، فقط با زبان حمله بیان شده‌اند. اگر مهاجم چیزی را **Steal** کند، یعنی **Confidentiality** شکسته شده است. مثال: دزدیدن **Secret Key، Password، Firmware، Intellectual Property** یا دادهٔ حساس.

اگر مهاجم چیزی را **Corrupt** کند، یعنی **Integrity** شکسته شده است. مثلاً یک بیت در حافظه را عوض کند، permission file را تغییر دهد، firmware را modify کند، یا عملکرد circuit را طوری تغییر دهد که خروجی اشتباه بدهد. این نوع حمله همیشه دنبال خواندن secret نیست؛ گاهی هدف این است که سیستم تصمیم غلط بگیرد.

اگر مهاجم چیزی را **Inhibit** کند، یعنی **Availability** شکسته شده است. مثال: از کار انداختن یک service، متوقف‌کردن یک defense mechanism، ایجاد **Denial of Service / DoS**، یا خاموش‌کردن یک sensor مهم. این هدف در سیستم‌هایی که باید زنده بمانند، خیلی مهم است؛ مثل medical device، automotive controller، industrial control و avionics.

نکتهٔ ظریف: هدف حمله معمولاً با هدف اصلی سیستم مرتبط است. اگر یک hardware برای محافظت از key طراحی شده، مهاجم احتمالاً دنبال **Stealing** است. اگر hardware برای همیشه فعال ماندن طراحی شده، مثل safety-critical controller، مهاجم ممکن است دنبال **Inhibiting** باشد. پس threat model باید متناسب با مأموریت سیستم باشد.

---

### ۳. محور Target: دسته‌بندی Information یا Property

دومین محور **Target** است؛ یعنی دقیقاً چه چیزی هدف حمله است. فصل target را به دو دسته تقسیم می‌کند: **Information** و **Property**. اگر هدف یک داده باشد، مثل key، credential، memory word، file یا secret، تارگت از نوع **Information** است. مثلاً در **Side-Channel Attack**، هدف معمولاً استخراج یک **Cryptographic Key** است، پس target اطلاعاتی است.

اما اگر هدف یک ویژگی یا دارایی متعلق به سخت‌افزار باشد، تارگت از نوع **Property** است. مثال: **Intellectual Property / IP** طراحی، قابلیت خاص circuit، resource سخت‌افزاری، عملکرد یک accelerator یا حتی availability یک module. مثلاً در **Reverse Engineering** ممکن است هدف دزدیدن طراحی یک chip باشد؛ این بیشتر به property یا IP مربوط است.

فرق این دو همیشه در گزینه‌های تستی واضح نیست. اگر سؤال گفت «attacker tries to extract AES key»، این **Information Target** است. اگر گفت «attacker tries to clone the design or steal the circuit architecture»، این **Property Target** است. اگر گفت «attacker disables a hardware security module»، هدف از نظر goal می‌شود **Inhibit**، ولی target می‌تواند یک property/functionality سخت‌افزاری باشد.

پس حواست باشد **Goal** و **Target** یکی نیستند. محور Goal می‌گوید مهاجم چه کاری می‌خواهد انجام دهد: steal/corrupt/inhibit. محور Target می‌گوید روی چه assetی این کار را انجام می‌دهد: information/property.

---

### ۴. محور Domain: دسته‌بندی Logical یا Physical

سومین محور **Domain** است. در این فصل حملات از نظر domain به دو دسته تقسیم می‌شوند: **Logical** و **Physical**. نوع **Logical Attack** حمله‌ای است که از طریق software اجراشده روی hardware انجام می‌شود، اما در نهایت از یک hardware vulnerability سوءاستفاده می‌کند. مثال: **Privilege Escalation via Software** که باعث access به hardware resource یا protected memory می‌شود.

در مقابل، **Physical Attack** مستقیماً خود hardware vulnerability را هدف می‌گیرد. یعنی مهاجم با رفتار فیزیکی یا الکتریکی دستگاه کار دارد: power، timing، electromagnetic emission، optical behavior، laser، fault، microprobing و غیره. در اینجا لازم نیست حتماً software bug داشته باشیم. ضعف ممکن است در فیزیک implementation باشد.

تشبیه ساده: اگر با یک اپلیکیشن مخرب از داخل سیستم به data دسترسی پیدا کنی، مسیر logical است. اگر با oscilloscope، laser یا probe از بیرون مدار را تحلیل یا تحریک کنی، مسیر physical است. هر دو می‌توانند به یک هدف برسند، مثلاً دزدیدن key؛ اما domain آن‌ها فرق دارد.

نکتهٔ امتحانی: اصطلاح **Physical** همیشه به معنی بازکردن chip نیست. یک **Timing Attack** یا **Power Analysis** هم physical domain دارد، چون از ویژگی فیزیکی اجرای سخت‌افزار استفاده می‌کند، حتی اگر دستگاه باز نشده باشد. پس physical را با invasive یکی نکن.

---

### ۵. محور Modality: دسته‌بندی Invasive و Non-invasive

چهارمین محور **Modality** است؛ یعنی روش عملی اجرای حمله چقدر به device دست‌اندازی می‌کند. مدل **Invasive Attack** نیاز به اقدامات مستقیم علیه سخت‌افزار دارد؛ مثلاً **Desoldering، Repackaging، Disconnecting، Decapsulation، Microprobing** یا دسترسی به internal components. این حملات معمولاً تجهیزات آزمایشگاهی گران‌تر و effort بیشتری برای هر chip نیاز دارند.

در مقابل، **Non-invasive Attack** بدون تماس فیزیکی مستقیم یا بدون بازکردن و تخریب device انجام می‌شود. در نمودار صفحهٔ دوم، non-invasive به دو دستهٔ **Passive** و **Active** تقسیم شده است. روش **Passive Non-invasive** یعنی مهاجم فقط اندازه‌گیری و تحلیل می‌کند، درحالی‌که device در محدودهٔ مشخصات عادی خودش کار می‌کند. مثال‌ها در نمودار: **Timing Attacks، Power Attacks، Electromagnetic Attacks، Acoustic Attacks، Optical Attacks**.

روش **Active Non-invasive** یعنی مهاجم دستگاه را وادار به وضعیت غیرعادی می‌کند تا رسیدن به هدف آسان‌تر شود. مثال‌ها: **Fault Attacks** و **Test-Infrastructure-based Attacks**. متن فصل مثال‌هایی مثل **Power Glitches** و **Laser Pulses** را می‌آورد و می‌گوید این‌ها در این taxonomy invasive حساب نمی‌شوند، چون trace فیزیکی دائمی باقی نمی‌گذارند.

این قسمت یکی از خطرناک‌ترین تله‌های امتحانی است: ویژگی **Active** بودن به معنی **Invasive** بودن نیست. ممکن است یک حمله active باشد، اما non-invasive باقی بماند. مثلاً voltage glitch دستگاه را تحریک می‌کند، اما chip را باز نمی‌کند. از طرف دیگر، microprobing کاملاً invasive است، حتی اگر هدف فقط مشاهدهٔ passive internal signal باشد.

---

# گام ۲: خلاصه تستی و مقایسه‌ای

## Bilingual Cheat-Sheet

### Hardware Attack Taxonomy

| Dimension | Categories | معنی |
| --- | --- | --- |
| محور **Goal** | **Steal / Corrupt / Inhibit** | مهاجم چه کاری می‌خواهد انجام دهد |
| محور **Target** | **Information / Property** | دارایی یا asset مورد حمله چیست |
| محور **Domain** | **Logical / Physical** | حمله از چه مسیر یا دامنه‌ای انجام می‌شود |
| محور **Modality** | **Invasive / Non-invasive** | میزان دخالت فیزیکی در device |

---

### Goal vs CIA

| Goal | CIA Property Broken | مثال |
| --- | --- | --- |
| هدف **Steal** | **Confidentiality** | دزدیدن **Cryptographic Key** یا password |
| هدف **Corrupt** | **Integrity** | تغییر **Memory Word** یا firmware |
| هدف **Inhibit** | **Availability** | از کار انداختن service یا defense mechanism |

---

### Target

| Target Type | توضیح | مثال |
| --- | --- | --- |
| تارگت **Information** | داده‌ای که hardware پردازش یا نگه‌داری می‌کند | مواردی مثل key، credential، memory word |
| تارگت **Property** | ویژگی، resource، functionality یا IP متعلق به hardware | مواردی مثل design IP، resource، functionality |

---

### Domain

| Domain | تعریف | مثال |
| --- | --- | --- |
| حمله **Logical Attack** | با software اجراشده روی hardware، از hardware vulnerability سوءاستفاده می‌کند | حمله privilege escalation exploiting hardware flaw |
| حمله **Physical Attack** | مستقیماً از ویژگی یا ضعف فیزیکی hardware سوءاستفاده می‌کند | حملات power analysis، fault injection |

---

### Modality

| Modality | تعریف | هزینه/اثر |
| --- | --- | --- |
| روش **Invasive** | نیاز به دستکاری مستقیم hardware مثل desoldering یا microprobing | تجهیزات گران‌تر، effort بیشتر برای هر chip |
| روش **Non-invasive Passive** | فقط اندازه‌گیری physical dynamic entities؛ device عادی کار می‌کند | کم‌خطرتر، بدون ایجاد abnormal state |
| روش **Non-invasive Active** | تحریک device به abnormal state برای آسان‌تر شدن حمله | هزینه متوسط، cost per device پایین‌تر |

---

### Examples from Page 2 Diagram

| Category | Examples |
| --- | --- |
| دسته **Invasive** | حملات **Microprobing، Reverse Engineering، Data Remanence Attacks** |
| دسته **Passive Non-invasive** | حملات **Timing Attacks، Power Attacks، Electromagnetic Attacks، Acoustic Attacks، Optical Attacks** |
| دسته **Active Non-invasive** | حملات **Fault Attacks، Test-Infrastructure-based Attacks** |

---

### One-line Exam Memory

* هدف **Steal → Confidentiality**
* هدف **Corrupt → Integrity**
* هدف **Inhibit → Availability**
* تارگت **Information → data**
* تارگت **Property → hardware-owned capability/IP**
* دامنه **Logical → via software**
* دامنه **Physical → via hardware behavior**
* روش **Invasive → physical intrusion**
* روش **Passive → observe**
* روش **Active → perturb**

---

# گام ۳: تله‌های امتحانی

## Pitfalls

### ۱. مفاهیم Goal و Target را یکی نگیر

مفهوم **Goal** یعنی مهاجم چه می‌خواهد بکند: **Steal، Corrupt، Inhibit**.
مفهوم **Target** یعنی روی چه چیزی این کار را می‌کند: **Information** یا **Property**.
مثال: دزدیدن AES key
مورد Goal = **Steal**
مورد Target = **Information**

---

### ۲. هدف Steal فقط برای data نیست

هدف **Steal** می‌تواند برای **Cryptographic Key**، **Password**، **IP Design** یا حتی resource باشد. اما از نظر CIA، همیشه به **Confidentiality** ضربه می‌زند.

---

### ۳. هدف Corrupt یعنی Integrity، نه Availability

اگر memory word یا firmware تغییر کند، مشکل اصلی **Integrity** است. حتی اگر بعداً سیستم crash کند، هدف مستقیم corrupt کردن یعنی integrity violation.

---

### ۴. هدف Inhibit یعنی Availability

اگر attacker بخش service، defense mechanism یا access به data را از کار بیندازد، goal می‌شود **Inhibit** و property شکسته‌شده **Availability** است.

---

### ۵. حمله Physical Attack الزاماً Invasive نیست

حملات **Power Analysis**، **Timing Attack** و **EM Attack** ذاتاً physical هستند، اما معمولاً non-invasive passive هستند.

---

### ۶. روش Active Non-invasive با Invasive فرق دارد

تکنیک **Power Glitch** یا **Laser Pulse** در این taxonomy نوعی active non-invasive حساب می‌شود، چون device را abnormal می‌کند ولی الزاماً trace یا intrusion دائمی باقی نمی‌گذارد.

---

### ۷. روش Passive یعنی device عادی کار می‌کند

در **Passive Attack** مهاجم فقط مشاهده و اندازه‌گیری می‌کند. دستگاه (device) داخل specification خودش کار می‌کند.

---

### ۸. روش Active یعنی attacker device را تحریک می‌کند

در **Active Attack** مهاجم عمداً device را وارد abnormal state می‌کند؛ مثلاً با voltage glitch، clock glitch، laser یا fault injection.

---

### ۹. تکنیک Reverse Engineering معمولاً Invasive است

اگر این حمله شامل بازکردن، لایه‌برداری، microprobing یا تحلیل داخلی chip باشد، در taxonomy این فصل زیر مجموعه **Invasive** می‌رود.

---

### ۱۰. مفاهیم Domain و Modality مستقل‌اند

یک حمله می‌تواند physical domain و non-invasive modality داشته باشد.
برای مثال **Timing Attack**:
دامنه Domain = **Physical**
روش Modality = **Non-invasive Passive**

---

# گام ۴: خودآزمایی

## 100% English Quiz

*(در این بخش به دلیل ماهیت انگلیسی سؤالات، حفظ ساختار چپ‌به‌راست (LTR) منطقی و صحیح است)*

### Question 1

**A hardware attacker measures the power consumption of a cryptographic device during normal operation in order to recover a secret key. How should this attack be classified?**

A. Logical, invasive, corrupting a property.
B. Physical, non-invasive passive, stealing information.
C. Physical, invasive active, inhibiting availability.
D. Logical, non-invasive active, corrupting information.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **گزینه A غلط است:** حمله از طریق software نیست و invasive هم نیست؛ مهاجم فقط power را اندازه می‌گیرد.
* **گزینه B درست است:** حملهٔ **Power Analysis** یک حملهٔ **Physical** و معمولاً **Non-invasive Passive** است. هدف هم دزدیدن key است، پس **Steal Information** محسوب می‌شود.
* **گزینه C غلط است:** ویژگی invasive ندارد و هدف availability نیست.
* **گزینه D غلط است:** حمله logical نیست و corrupt کردن هم رخ نداده است.

---

### Question 2

**Which mapping between attack goal and security property is correct?**

A. Stealing a cryptographic key breaks integrity.
B. Corrupting a memory word breaks confidentiality.
C. Inhibiting a service breaks availability.
D. Inhibiting a defense mechanism primarily improves confidentiality.

**Correct Answer: C**

**تحلیل گزینه‌ها:**

* **گزینه A غلط است:** دزدیدن key یعنی **Confidentiality** شکسته می‌شود.
* **گزینه B غلط است:** تغییر memory word یعنی **Integrity** شکسته می‌شود.
* **گزینه C درست است:** از کار انداختن service یعنی **Availability** شکسته شده است.
* **گزینه D غلط است:** عمل inhibiting روی یک defense mechanism معمولاً availability یا security functionality را مختل می‌کند، نه اینکه confidentiality را بهتر کند.

---

### Question 3

**Which statement best distinguishes invasive attacks from non-invasive active attacks in hardware security?**

A. Invasive attacks require physical intrusion or manipulation of the device, while non-invasive active attacks perturb the device into abnormal states without necessarily leaving physical traces.
B. Invasive attacks are always logical attacks, while non-invasive active attacks are always software attacks.
C. Non-invasive active attacks only observe the device, while invasive attacks never require lab equipment.
D. Invasive attacks target only availability, while non-invasive active attacks target only confidentiality.

**Correct Answer: A**

**تحلیل گزینه‌ها:**

* **گزینه A درست است:** روش **Invasive** یعنی دستکاری/نفوذ فیزیکی مثل desoldering یا microprobing؛ اما **Non-invasive Active** یعنی تحریک دستگاه با glitch، laser و غیره بدون لزوماً بازکردن یا به جای گذاشتن trace دائمی.
* **گزینه B غلط است:** حمله invasive معمولاً physical است، نه logical.
* **گزینه C غلط است:** ماهیت observe-only مربوط به **Passive Non-invasive** است، نه active.
* **گزینه D غلط است:** هر دو modality می‌توانند بسته به هدف، شاخص‌های confidentiality، integrity یا availability را هدف بگیرند.