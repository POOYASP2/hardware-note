همان‌طور که درخواست کرده بودید، مشکل به‌هم‌ریختگی ظاهریِ عبارات انگلیسی را برطرف کردم. با افزودن یک کلمهٔ توصیفی فارسی در ابتدای تمام خطوط، پاراگراف‌ها و خانه‌های جداول، ساختار راست‌چین (RTL) به‌صورت ۱۰۰ درصدی در محیط‌های مختلف حفظ می‌شود و خوانایی متن کاملاً تضمین می‌گردد. همچنین اعداد تیترها به فارسی اصلاح شدند.

فایل استاندارد و اصلاح‌شده تقدیم شما:

---

این فایل قابل قبول است. اسم آپلودش **chapter-15.pdf** است، ولی محتوایش **Hardware Trojans** است و در لیست ترم تو با عنوان **12-Hardware Trojan.pdf** آمده؛ پس تدریس شده و باید بخوانیم.

# گام ۱: آموزش مفهومی و مفصل

## بخش Deep Explanation — Hardware Trojans

### ۱. مفهوم Hardware Trojan چیست؟

مفهوم **Hardware Trojan / HT** یعنی یک **malicious insertion** یا **malicious alteration** در طراحی یا ساخت یک **Integrated Circuit / IC**. یعنی کسی عمداً چیزی به مدار اضافه می‌کند یا چیزی را تغییر می‌دهد تا مدار در ظاهر عادی کار کند، اما در شرایط خاص رفتار مخرب نشان دهد. این رفتار مخرب می‌تواند از **Information Leakage** تا **Performance Degradation** یا حتی نابودی کامل circuit باشد. برخلاف نرم‌افزار malware که داخل فایل یا program دیده می‌شود، **Hardware Trojan** در خود طراحی، netlist، layout، fabrication یا packaging مخفی می‌شود.

تشبیه ساده: فرض کن یک قفل دیجیتال داری که ظاهراً درست کار می‌کند. هر بار رمز درست را می‌زنی، باز می‌شود؛ رمز غلط را می‌زنی، باز نمی‌شود. اما سازندهٔ بدخواه داخل مدار قفل یک شرط مخفی گذاشته: اگر دکمه‌ها با یک sequence خاص فشرده شوند، قفل بدون رمز باز شود. این شرط در استفادهٔ معمولی فعال نمی‌شود، در تست کارخانه هم شاید دیده نشود، اما مهاجم می‌داند چطور آن را فعال کند. این دقیقاً منطق **Trigger + Payload** در Hardware Trojan است.

در فایل، مثال مهمی آمده: توانایی cryptographic یک پردازنده می‌تواند با کاهش entropy یک **Random Number Generator** خراب شود؛ مثلاً به‌جای 128-bit entropy فقط 32-bit entropy باقی بماند. چنین تغییری ممکن است با تغییرات بسیار کوچک مثل تغییر polarity dopant در چند transistor انجام شود و از **Built-In Self-Test / BIST** یا inspection عادی عبور کند. پیام تستی این مثال: **Hardware Trojan لازم نیست بزرگ یا واضح باشد؛ گاهی یک تغییر بسیار کوچک کافی است.**

هدف‌های اصلی چنین حملاتی معمولاً در سیستم‌های حساس دیده می‌شود: سیستم‌های **Military Systems، Critical Infrastructures، Transportation، Telecommunication، Banking Systems**. چون در این سیستم‌ها یک تغییر کوچک می‌تواند پیامد بزرگ داشته باشد: سرقت secret، اختلال real-time، خراب‌کردن decision logic یا بازکردن backdoor.

---

### ۲. مفاهیم Trigger و Payload

هر سخت‌افزار **Hardware Trojan** معمولاً دو بخش اصلی دارد: **Trigger** و **Payload**.
بخش **Trigger** یعنی شرط فعال‌سازی. این بخش فقط نگاه می‌کند که آیا شرایط خاصی رخ داده یا نه.
بخش **Payload** یعنی بخش مخرب واقعی؛ وقتی trigger فعال شد، payload کار بد را انجام می‌دهد.

مثلاً در یک مدار encryption، شرط trigger می‌تواند یک sequence خاص از inputها باشد. وقتی آن sequence رخ داد، بخش payload خروجی encryption را bypass کند و plaintext را به بیرون leak کند. یا trigger می‌تواند یک clock cycle خاص، یک مقدار نادر در register، یک وضعیت خاص temperature یا یک مقدار **NaN / Not-a-Number** در floating-point operation باشد. بخش Payload هم می‌تواند output را force کند، key را leak کند، performance را پایین بیاورد یا system را crash کند.

سه ویژگی مهم برای Trojan خوب از دید مهاجم:

* ویژگی **Low probability of occurrence:** بخش trigger خیلی نادر رخ دهد.
* ویژگی **Small hardware overhead:** آن مدار اضافه‌شده خیلی کوچک باشد.
* ویژگی **Hard to detect:** آن اثر Trojan در timing، area، power یا behavior معمولی دیده نشود.

چرا trigger باید نادر باشد؟ چون اگر زیاد فعال شود، تست‌ها آن را می‌بینند. اگر payload همیشه فعال باشد، مدار سریع مشکوک می‌شود. پس مهاجم معمولاً triggerهایی انتخاب می‌کند که در **routine testing** و **normal operation** رخ نمی‌دهند، اما خودش بتواند آن‌ها را فعال کند.

---

### ۳. ورود Hardware Trojan در کجای Design Flow رخ می‌دهد؟

یکی از نکات اصلی این فصل این است که Trojan فقط در factory اضافه نمی‌شود. می‌تواند در چندین مرحله وارد شود:

1. مرحله **Pre-Silicon**
* در سطح **Source-level RTL**
* در سطح **3rd Party IP**
* در ابزارهای **CAD Tools**
* در اسکریپت‌های **Automation Scripts**
* در کتابخانه‌های **Libraries**


2. مرحله **Silicon**
* در کارخانه **Untrusted Foundry**
* با تغییر در mask
* با اضافه‌کردن wire
* با اضافه‌کردن standard cell
* با انواع backdoorهای کوچک


3. مرحله **Post-Silicon**
* در مرحله **Packaging**
* با جایگزینی malicious chip
* با تغییر hard IP یا package element



در تصویر صفحهٔ ۲ و ۳، مسیر **IC Design Flow** نشان داده شده:
روند **3rd-party Soft IP / In-house RTL → SoC Integration → SoC RTL → Synthesis → Gate-Level Netlist → Place & Route → Physical Design → Fabrication & Packaging → Finished Chip**. نکته این است که تقریباً در همهٔ این مرحله‌ها یک نقطهٔ ورود برای Trojan وجود دارد.

چرا این خطر زیاد است؟ چون طراحی مدرن chip از صفر انجام نمی‌شود. شرکت‌ها از **3rd Party IP**، ابزارهای synthesis، ابزارهای **Place & Route / P&R**، مجموعه libraryهای آماده و foundryهای بیرونی استفاده می‌کنند. اگر هرکدام از این‌ها untrusted باشد، امکان insertion وجود دارد. حتی نرم‌افزار **CAD Tool** هم می‌تواند malicious باشد: ابزار ممکن است هنگام synthesis یا P&R، بخش logic اضافی وارد کند، بدون اینکه طراح در RTL اولیه چیزی ببیند.

---

### ۴. رفتار Malicious Behavior: تروجان Trojan چه کاری می‌تواند بکند؟

رفتار مخرب Trojan معمولاً در سه دسته قرار می‌گیرد.

اول هدف **Leak Information**. یعنی Trojan اطلاعات حساس را بیرون بدهد. مثلاً **Secret Key**، مقادیر plaintext، داده‌های intermediate encryption values یا internal state را leak کند. این leakage ممکن است از طریق output رسمی، side channel، پورت unused port، پورت UART مخفی، EM emission یا تغییر power pattern انجام شود.

دوم هدف **Degrade Performance**. در این حالت Trojan الزاماً output غلط نمی‌دهد، اما سیستم را کند یا ناکارآمد می‌کند. مثال فایل: اگر Trojan کاری کند که cache entries همیشه miss شوند، latency زیاد می‌شود و bandwidth کم می‌شود. در سیستم عادی شاید این فقط یک کندی جزئی باشد، اما در **Real-Time Systems** می‌تواند فاجعه باشد، چون deadlineها از دست می‌روند.

سوم هدف **Violate Specifications** یا **Corrupt State**. یعنی سیستم در شرایط خاص برخلاف specification کار کند. مثلاً سطح privilege را بالا ببرد، clock را disable کند، FSM را به state غیرقابل‌بازگشت ببرد، پایه‌های VCC و GND را short کند یا output خاصی را force کند. این‌ها می‌توانند به **Denial of Service / DoS**، خراب‌کاری functional یا bypass امنیتی منجر شوند.

پس حضور Trojan فقط به معنی «دزدیدن key» نیست. می‌تواند confidentiality، integrity یا availability را هدف بگیرد.

---

### ۵. انواع Hardware Trojan

#### دسته Soft IP Trojans

یک **Soft IP Trojan** در سطح **RTL** نوشته می‌شود. مثلاً در **Verilog** یا **VHDL** داخل یک module ظاهراً عادی، functionality مخفی اضافه می‌شود. این حالت می‌تواند از **3rd Party IP** یا **Insider Threat** بیاید. مشکل این است که پروژه‌های RTL بزرگ‌اند و code review کامل، خسته‌کننده، گران و ناقص است.

در صفحهٔ ۴، یک نمونه code نشان داده شده که یک module ظاهراً عادی **RAM** دارد، اما یک module به نام `hardware_trojan` در آن instantiate شده است. همین مثال نشان می‌دهد که Trojan می‌تواند شبیه یک component عادی داخل طراحی پنهان شود.

#### دسته Firm 3rd Party IP Trojans

یک **Firm IP Trojan** در **synthesized netlist** یا **gate-level netlist** اضافه می‌شود. در این سطح طراحی کمتر human-readable است. یعنی به‌جای RTL واضح، با netlist پیچیده‌ای از gates و cells روبه‌رو هستیم. تروجان Trojan می‌تواند از source-level RTL آمده باشد یا دستی در netlist اضافه شود.

مشکل اینجا قابلیت **Obfuscation** است. فایل Netlist ممکن است عمداً طوری پیچیده شود که reverse engineering سخت شود. در نتیجه فهمیدن اینکه یک logic اضافه واقعاً لازم است یا malicious است، بسیار سخت‌تر از RTL است.

#### دسته Malicious CAD Tool Trojans

در این مدل، خود ابزار طراحی مخرب است. مثلاً **Synthesis Tool** یا **Place & Route Tool** هنگام optimization یک Trojan وارد کند. چون نرم‌افزارهای CAD tools بسیار پیچیده‌اند و معمولاً proprietary هستند، شرکت‌ها source code آن‌ها را نمی‌بینند. پس reasoning دربارهٔ اینکه ابزار دقیقاً چه logicی ساخته سخت است.

#### دسته Hard IP & Foundry Trojans

در این حالت، ماژول IP به‌صورت **VLSI black box** یا physical design آماده دریافت می‌شود، یا foundry در سطح mask/layout تغییر ایجاد می‌کند. مثلاً wire اضافه کند، standard cell وارد کند یا memory block را تغییر دهد. تشخیص این نوع بسیار سخت است، چون باید chip فیزیکی با میلیاردها transistor را بررسی کرد.

---

### ۶. انواع Triggerها: چه چیزی Trojan را فعال می‌کند؟

یک **Trigger** می‌تواند چند نوع باشد:

* نوع **Always Active Trigger**
* نوع **Internal Trigger**
* نوع **External Trigger**
* نوع **Software-controlled Trigger**
* نوع **Time-delay Trigger**
* نوع **Environmental Trigger**
* نوع **Logical Trigger**
* نوع **Sensor-based Trigger**

نوع **Always Active Trigger** نادر است، چون Trojan دائم اثر می‌گذارد و احتمال detect شدن بالا می‌رود. معمولاً triggerها طوری طراحی می‌شوند که فقط در شرایط خاص فعال شوند.

یک trigger خوب باید:

* در فرایند **regular testing** فعال نشود.
* ویژگی **Low toggle rate** داشته باشد.
* توسط خود attacker قابل فعال‌سازی باشد.
* عملکرد تصادفی و زیاد رخ ندهد.
* میزان footprint کوچک داشته باشد.

مثال‌های trigger:

* رخداد **Division by zero**
* داده‌های **Uninitialized signals**
* شرط‌های **Assertions**
* مقدار **NaN**
* مقدار **Infinity**
* یک مقدار **secret/uncommon value**
* یک الگوی sequence خاص از digital signals
* مقدار temperature خاص
* مقدار voltage خاص
* رفتار capacitor charging در analog circuit برای delay-based trigger

نکتهٔ تستی: یک trigger خوب نه خیلی رایج است، نه کاملاً غیرقابل‌کنترل. اگر خیلی رایج باشد، زود کشف می‌شود. اگر attacker نتواند آن را فعال کند، Trojan عملاً بی‌فایده است.

---

### ۷. انواع Payloadها: Trojan بعد از فعال‌شدن چه می‌کند؟

بخش **Payload** رفتار مخرب را اجرا می‌کند. در فایل سه payload اصلی آمده است:

#### 1. رفتار Corrupt the State

یعنی وضعیت سیستم را به حالت غیرمجاز ببرد. مثال‌ها:

* حمله **Privilege Elevation**
* تغییر internal state
* تغییر FSM state
* کاهش عملکرد performance
* تغییر روی control logic

#### 2. رفتار Denial of Service

یعنی سیستم از کار بیفتد یا دیگر درست کار نکند. مثال‌ها:

* وضعیت forcing FSM into invalid state
* وضعیت disabling clock
* وضعیت short-circuiting VCC and GND
* وضعیت stopping a component

#### 3. رفتار Leak Information

یعنی اطلاعات حساس بیرون داده شود. مثال‌ها:

* رخداد leaking secret key
* رخداد leaking plaintext
* رخداد leaking intermediate cryptographic values
* وضعیت creating a backdoor output path

پس بخش **Trigger** جواب می‌دهد: «کی فعال شود؟»
بخش **Payload** جواب می‌دهد: «وقتی فعال شد، چه کار کند؟»

---

### ۸. طبقه‌بندی Classification of Hardware Trojans

در صفحهٔ ۷، نمودار classification نشان می‌دهد که Hardware Trojan بر اساس سه محور دسته‌بندی می‌شود:

* محور **Form** یعنی Trojan از چه نوعی در circuit ظاهر می‌شود. ممکن است logic اضافه کند، side channel ایجاد کند، اندازه یا ساختار را تغییر دهد.
* محور **Activation Mechanism** یعنی چطور فعال می‌شود. می‌تواند داخلی باشد، خارجی باشد، همیشه فعال باشد یا trigger خاص داشته باشد.
* محور **Effects** یعنی اثر نهایی چیست: اطلاعات را transmit کند، specification را تغییر دهد یا functionality را modify کند.

این taxonomy مهم است چون در امتحان ممکن است از تو بخواهند یک Trojan را طبقه‌بندی کنی. مثلاً Trojanی که با temperature فعال می‌شود و key را از طریق EM leak می‌کند:
بخش Activation = external/environmental
بخش Effect = transmit/leak information
بخش Form = side-channel-related Trojan

---

### ۹. چرا فرایند Detection سخت است؟

تشخیص Hardware Trojan سخت‌تر از اضافه‌کردن آن است. چند دلیل:

* سایز designها بسیار بزرگ‌اند.
* انجام testing کامل همهٔ stateها ممکن نیست.
* رفتار Trojanها عمداً trigger نادر دارند.
* میزان overhead آن‌ها کوچک است.
* مبحث manufacturing variation باعث noise در measurement می‌شود.
* هزینه test time گران است.
* عملیات detection باید ترجیحاً non-destructive و cost-effective باشد.
* هیچ فریم‌ورک detection framework عمومی و قطعی وجود ندارد.

حتی اگر تست خیلی زیاد انجام دهیم، ممکن است trigger خاص فعال نشود. مخصوصاً در **Sequential Circuits**، تعداد stateها بسیار زیاد است. یک Trojan می‌تواند فقط بعد از هزاران cycle، یا در ترکیب خاصی از signals فعال شود. پس standard testing معمولاً کافی نیست.

در صفحهٔ ۹، نمودار Trojan Detection نشان می‌دهد detection به دو دستهٔ کلی تقسیم می‌شود: روش‌های **Destructive** و **Non-destructive**. روش‌های non-destructive می‌توانند invasive یا non-invasive باشند. در non-invasive هم detection می‌تواند در **Run-time** یا **Test-time** باشد و test-time می‌تواند **Logic Test** یا **Side-Channel** باشد.

---

### ۱۰. روش‌های Detection Methods

#### دسته Destructive Detection

در این روش chip باز می‌شود، package برداشته می‌شود و layout/mask/transistorها بررسی می‌شوند. این روش دقیق‌تر است، اما:

* بسیار گران است.
* بسیار زمان‌بر است.
* خود chip را از بین می‌برد.
* برای همهٔ قطعات batch عملی نیست.

#### دسته Non-destructive Detection

این روش chip را نابود نمی‌کند. چند نمونه:

* روش **Runtime Monitoring**
* روش **Side-Channel Verification**
* روش **Power Analysis**
* روش **EM Analysis**
* روش **Benchmark-based Anomaly Detection**
* روش **Direct I/O Access Testing**
* روش **X-Ray Inspection**

در صفحهٔ ۱۰، بخش **EM Side Channel Information Leakage** می‌گوید هر wire در IC مثل یک آنتن کوچک رفتار می‌کند و می‌تواند EM emission ایجاد کند. اگر Trojan الگوی خاصی در EM radiation بسازد، ممکن است با antenna نزدیک chip دیده شود. اما چون همهٔ خطوط chip emission دارند، تشخیص pattern غیرعادی سخت است.

در صفحهٔ ۱۱، روش **X-Ray Inspection** به‌عنوان روشی non-invasive برای بررسی PCB و componentها معرفی شده است. این روش می‌تواند modificationهای پنهان، bond wires یا PCB traces غیرعادی را نشان دهد، اما ساده و ارزان نیست.

---

### ۱۱. استراتژی‌های Mitigation Strategies

در صفحهٔ ۱۰، نمودار mitigation دو دسته می‌دهد: استراتژی‌های Pre-Silicon و Post-Silicon.

#### استراتژی Formal Verification

مفهوم **Formal Verification** یعنی با روش ریاضی ثابت کنیم طراحی برای همهٔ configurationها و inputهای ممکن propertyهای مشخص را رعایت می‌کند. این برای کشف تغییرات functional مفید است، اما اگر Trojan در foundry یا packaging اضافه شود، formal verification قبل از fabrication آن را نمی‌بیند.

#### استراتژی Runtime Monitoring

سیستم هنگام اجرا رفتار خود را monitor می‌کند. مثلاً پارامترهای power، EM، timing، performance یا stateهای غیرعادی را بررسی می‌کند. این روش می‌تواند Trojanهایی را که در operation فعال می‌شوند detect کند، اما false positive و overhead دارد.

#### استراتژی Reverse Engineering

در فرایند **Reverse Engineering**، قطعه chip یا PCB بررسی می‌شود تا ببینیم آیا با design مورد انتظار یکی است یا نه. برای IC می‌تواند شامل تکنیک‌های **X-Ray** و **Decapsulation** باشد. این دقیق‌تر است، اما گران و زمان‌بر است.

#### استراتژی Circuit Obfuscation

تکنیک **Circuit Obfuscation** یعنی design را عمداً پیچیده کنیم تا foundry یا attacker نتواند functionality واقعی و خطوط حساس را بفهمد. مثلاً با تکنیک **XOR Obfuscation**، بخش foundry layout را می‌بیند اما key مربوط به XOR gates را ندارد. در صفحهٔ ۱۱، نمونهٔ **Circuit Obfuscation with XOR Gates** نشان داده شده است.

#### استراتژی Split Manufacturing

در تکنیک **Split Manufacturing**، فرایند ساخت بین چند foundry یا layer تقسیم می‌شود تا هیچ foundry کل design را نبیند. مثلاً transistor placement در یک foundry انجام شود و interconnectها در foundry دیگر. ایده این است که یک foundry تنها نتواند طراحی کامل را بفهمد یا Trojan هدفمند اضافه کند.

---

# گام ۲: خلاصه تستی و مقایسه‌ای

## بخش Bilingual Cheat-Sheet

### بخش Hardware Trojan Basics

| Keyword | نکتهٔ تستی |
| --- | --- |
| مفهوم **Hardware Trojan / HT** | یعنی malicious insertion یا alteration در IC design |
| مفهوم **Trigger** | شرط فعال‌سازی Trojan |
| مفهوم **Payload** | رفتار مخرب Trojan |
| قانون **Low occurrence trigger** | یک trigger باید نادر باشد تا در تست دیده نشود |
| قانون **Small overhead** | یک Trojan باید area/power/timing کمی اضافه کند |
| قانون **Hard to detect** | هدف Trojan پنهان‌ماندن در testing و operation است |

---

### بخش Main Targets

| Target System | چرا مهم است |
| --- | --- |
| سیستم‌های **Military Systems** | مقوله امنیت و reliability حیاتی |
| زیرساخت **Critical Infrastructure** | شبکه‌های power grids، nuclear plants |
| سیستم‌های **Transportation / Telecommunication** | بخش‌های trains، satellites، networks |
| سیستم‌های **Banking Systems** | مقوله financial security و trust |

---

### بخش Design Flow Vulnerabilities

| Stage | Trojan Risk |
| --- | --- |
| فاز **Source-level RTL** | تامین‌کننده 3rd party IP یا insider می‌تواند logic مخرب اضافه کند |
| فاز **Firm RTL / Netlist** | کدهای synthesized netlist سخت‌تر خوانده می‌شود |
| فاز **Gate-Level Netlist** | بخش logic اضافه می‌تواند پنهان شود |
| فاز **Physical Design** | خروجی P&R یا hard IP می‌تواند دستکاری شود |
| فاز **Fabrication** | کارخانه untrusted foundry می‌تواند mask/cells/wires را تغییر دهد |
| فاز **Packaging** | قطعه malicious package یا chip replacement |

---

### بخش Trojan Types

| Type | محل ورود |
| --- | --- |
| نوع **Soft IP Trojan** | کدهای RTL / HDL |
| نوع **Firm IP Trojan** | کدهای synthesized netlist |
| نوع **Malicious CAD Tool Trojan** | ابزار synthesis یا P&R tool |
| نوع **Hard IP Trojan** | فایل physical design black box |
| نوع **Foundry Trojan** | مراحل mask/layout/fabrication |
| نوع **Packaging Trojan** | فاز post-silicon / packaging |

---

### بخش Payload Classes

| Payload | اثر |
| --- | --- |
| کلاس **Corrupt State** | تغییر در state، privilege escalation، performance degradation |
| کلاس **Denial of Service** | اجرای disable clock، invalid FSM state، short VCC/GND |
| کلاس **Leak Information** | داده key/plaintext/intermediate values را leak می‌کند |

---

### بخش Trigger Classes

| Trigger | مثال |
| --- | --- |
| نوع **Always Active** | حالت rare، چون سریع detect می‌شود |
| نوع **Software-controlled** | یک command یا sequence خاص |
| نوع **Time Delay** | وضعیت فعال شدن بعد از زمان/cycle خاص |
| نوع **Environmental** | مقادیر temperature، voltage |
| نوع **Logical** | یک ترکیب خاص signals |
| نوع **Physical/Analog** | رفتار capacitor charging، analog condition |
| نوع **Special Values** | مقادیر NaN، infinity، divide by zero |

---

### بخش Classification

| Axis | Categories |
| --- | --- |
| محور **Form** | مقادیر Logic/Test، Side-Channel، Type، Size |
| محور **Activation Mechanism** | مقادیر Internal، External، Always On، Triggered |
| محور **Effects** | مقادیر Transmit، Modify Specification، Modify Function |

---

### بخش Detection Challenges

| Challenge | چرا سخت است |
| --- | --- |
| مانع **Huge state space** | همهٔ stateها تست نمی‌شوند |
| مانع **Rare triggers** | در routine testing فعال نمی‌شوند |
| مانع **Small footprint** | متغیرهای area/power/timing کم تغییر می‌کند |
| مانع **Process variation** | روش anomaly detection را سخت می‌کند |
| مانع **Expensive test time** | تست کامل اقتصادی نیست |
| مانع **No general framework** | هیچ روش عمومی با guarantee کامل وجود ندارد |

---

### بخش Detection Methods

| Method | توضیح |
| --- | --- |
| روش **Destructive Inspection** | بازکردن chip و بررسی layout/masks |
| روش **Non-destructive Testing** | بدون نابودکردن chip |
| روش **Runtime Monitoring** | بررسی abnormal behavior هنگام اجرا |
| روش **Side-Channel Verification** | وجود power/EM/timing anomaly |
| روش **Logic Test** | روش تست functional behavior |
| روش **X-Ray Inspection** | بررسی non-invasive PCB/IC structures |
| روش **Reverse Engineering** | تطبیق chip با expected design |

---

### بخش Mitigations

| Mitigation | Stage |
| --- | --- |
| راهکار **Formal Verification** | مرحله Pre-Silicon |
| راهکار **Trusted Foundry** | مرحله Pre/Post-Silicon supply chain |
| راهکار **Runtime Monitoring** | مرحله Operation |
| راهکار **Reverse Engineering** | مرحله Post-Silicon |
| راهکار **Circuit Obfuscation** | مفهوم Design protection |
| راهکار **XOR Obfuscation** | پنهان‌کردن logic با key |
| راهکار **FPGA-based Implementation** | کارخانه foundry design واقعی را نمی‌بیند |
| راهکار **Split Manufacturing** | هیچ foundry کل design را نمی‌بیند |
| راهکار **Side-Channel Analysis** | وضعیت detection با power/EM/timing anomalies |

---

# گام ۳: تله‌های امتحانی

## بخش Pitfalls

### ۱. مفهوم Hardware Trojan همان Software Trojan نیست

مفهوم **Hardware Trojan** در IC design، netlist، layout، fabrication یا packaging پنهان می‌شود، نه فقط در برنامهٔ اجراشده.

### ۲. مفهوم Trojan همیشه فعال نیست

بیشتر Trojanها با **rare trigger** فعال می‌شوند. **Always-active Trojan** معمولاً راحت‌تر detect می‌شود.

### ۳. مفاهیم Trigger و Payload را قاطی نکن

* مفهوم **Trigger:** چه زمانی فعال شود؟
* مفهوم **Payload:** وقتی فعال شد، چه کاری انجام دهد؟

### ۴. بدافزار Trojan لازم نیست output را همیشه غلط کند

ممکن است فقط performance را degrade کند، entropy RNG را کم کند، یا side channel مخفی باز کند.

### ۵. کوچک بودن Trojan دلیل بی‌خطر بودن نیست

حتی تغییر چند transistor می‌تواند security را خراب کند؛ مثلاً کاهش entropy در RNG.

### ۶. عملیات Testing کامل عملاً ممکن نیست

به‌خصوص در **Sequential Circuits**، تعداد stateها خیلی زیاد است. وجود triggerهای نادر در تست‌های عادی فعال نمی‌شوند.

### ۷. مفهوم 3rd Party IP یک attack vector جدی است

امروزه طراحی از صفر تقریباً غیرعملی است. استفاده از **3rd Party IP** یعنی وجود یک trust problem.

### ۸. ابزار CAD Tool هم می‌تواند مخرب باشد

حتی اگر RTL پاک باشد، malicious synthesis یا P&R tool می‌تواند Trojan وارد کند.

### ۹. کارخانه Foundry کاملاً trusted فرض نمی‌شود

یک **Untrusted Foundry** می‌تواند در mask، wire، cell یا layout تغییر ایجاد کند.

### ۱۰. عملیات Reverse Engineering قطعی و ساده نیست

در chipهای مدرن میلیاردها transistor وجود دارد. پیدا کردن یک malicious node کوچک بسیار سخت است.

### ۱۱. عملیات Obfuscation حذف کامل ریسک نیست

تکنیک **Circuit Obfuscation** حمله را سخت‌تر می‌کند، اما اگر key لو برود یا obfuscation ضعیف باشد، دفاع می‌شکند.

### ۱۲. تکنیک Split Manufacturing هم guarantee کامل نیست

استفاده از Split manufacturing ریسک یک foundry مخرب را کم می‌کند، اما هزینه و complexity دارد و همهٔ حملات را حذف نمی‌کند.

### ۱۳. روش Side-Channel Verification خودش noisy است

متغیرهای Power و EM تحت تأثیر process variation، workload و محیط هستند. یک anomaly همیشه Trojan نیست.

### ۱۴. عملیات Detection می‌تواند destructive یا non-destructive باشد

استفاده از **X-Ray** معمولاً non-invasive است؛ اما **Decapsulation** مسلماً destructive/invasive است. این‌ها را جابه‌جا نکن.

---

# گام ۴: خودآزمایی

## بخش 100% English Quiz

*(در این بخش به دلیل ماهیت انگلیسی سؤالات، حفظ ساختار چپ‌به‌راست (LTR) منطقی و صحیح است)*

### Question 1

**Which statement best describes the relationship between a trigger and a payload in a Hardware Trojan?**

A. The trigger performs the malicious action, while the payload only stores the original design files.
B. The trigger activates the Trojan under specific conditions, while the payload performs the malicious behavior.
C. The trigger is used only during software compilation, while the payload is used only during packaging.
D. The trigger and payload are identical terms for the same hardware component.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **گزینه A غلط است:** کار مخرب اصلی را **Payload** انجام می‌دهد، نه Trigger.
* **گزینه B درست است:** پارامتر **Trigger** شرط فعال‌سازی است؛ اما **Payload** اثر مخرب مثل leakage، DoS یا corrupt state را اجرا می‌کند.
* **گزینه C غلط است:** یک Trojan می‌تواند در چند مرحله طراحی/ساخت حضور داشته باشد؛ این تعریف محدود و نادرست است.
* **گزینه D غلط است:** واژه‌های Trigger و Payload دو مفهوم کاملاً متفاوت‌اند.

---

### Question 2

**Why are rare triggers commonly used in Hardware Trojans?**

A. Because frequent triggers make the Trojan easier to activate accidentally and therefore easier to detect during normal testing or operation.
B. Because rare triggers require no hardware overhead at all.
C. Because rare triggers always guarantee that the payload cannot be detected by side-channel analysis.
D. Because rare triggers are used only in software Trojans and not in hardware Trojans.

**Correct Answer: A**

**تحلیل گزینه‌ها:**

* **گزینه A درست است:** اگر trigger زیاد رخ دهد، Trojan در testing یا normal operation زودتر دیده می‌شود. اما trigger نادر باعث stealth می‌شود.
* **گزینه B غلط است:** یک trigger نادر هم ممکن است hardware overhead داشته باشد، هرچند مهاجم تلاش می‌کند overhead کوچک باشد.
* **گزینه C غلط است:** وجود rare trigger مسلماً detection را سخت‌تر می‌کند، اما guarantee کامل علیه side-channel analysis نمی‌دهد.
* **گزینه D غلط است:** مفهوم rare trigger یکی از مفاهیم اصلی در Hardware Trojan است.

---

### Question 3

**Which mitigation technique reduces the risk of an untrusted foundry understanding or modifying the complete chip design by dividing manufacturing across multiple foundries or layers?**

A. Differential Power Analysis
B. Split Manufacturing
C. Simple Power Analysis
D. Always-on Triggering

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **گزینه A غلط است:** روش **Differential Power Analysis** در واقع یک side-channel attack یا detection method است، نه یک روش تقسیم ساخت.
* **گزینه B درست است:** تکنیک **Split Manufacturing** طراحی/ساخت را بین چند foundry یا layer تقسیم می‌کند تا یک foundry کل design را نبیند.
* **گزینه C غلط است:** روش **Simple Power Analysis** مربوط به تحلیل مستقیم power traces است.
* **گزینه D غلط است:** روش **Always-on Triggering** یک نوع trigger ضعیف از نظر stealth است، نه یک راهکار mitigation.