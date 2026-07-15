همانند فایل‌های قبلی، برای جلوگیری از هرگونه به‌هم‌ریختگی ظاهری و حفظ بی‌نقص ساختار راست‌چین (RTL) در تمام خطوط و پاراگراف‌هایی که با کلمات انگلیسی آغاز می‌شدند، یک کلمهٔ توصیفی فارسی (مانند: *مفهوم، ویژگی، روش، مرحله، تکنیک، گزینه* و...) اضافه کردم و اعداد تیترها را نیز به فارسی برگرداندم.

فایل اصلاح‌شده و کاملاً خوانا، با حفظ ۱۰۰ درصدی محتوای آموزشی شما تقدیم می‌شود:

---

این فایل قابل قبول است. اسم آپلودش **chapter-12.pdf** است، ولی محتوایش **Fault Attacks** است و در لیست ترم تو با عنوان **11-Fault Attacks.pdf** آمده؛ پس تدریس شده و باید بخوانیم.

# گام ۱: آموزش مفهومی و مفصل

## بخش Deep Explanation — Fault Attacks

### ۱. مفهوم Fault Attack چیست؟

حمله **Fault Attack** یعنی مهاجم عمداً در اجرای عادی یک سخت‌افزار خطا ایجاد کند تا دستگاه وارد یک state غیرعادی شود و از همان رفتار غیرعادی، اطلاعات محرمانه مثل **Secret Key** را استخراج کند یا یک کنترل امنیتی را دور بزند. فرق مهم این حمله با **Side-Channel Attack** این است که در side-channel معمولاً attacker فقط مشاهده می‌کند؛ مثلاً power یا timing را اندازه می‌گیرد. اما در **Fault Attack** attacker فعالانه دستگاه را دستکاری می‌کند: ولتاژ را تغییر می‌دهد، clock را خراب می‌کند، دما را تغییر می‌دهد، laser می‌زند، یا با **Electromagnetic Injection** بیت‌ها را تغییر می‌دهد.

تشبیه ساده: فرض کن یک دستگاه خودپرداز وقتی همه‌چیز عادی است درست کار می‌کند. اگر کسی فقط صدای دستگاه یا زمان پاسخ آن را بررسی کند، شبیه side-channel است. اما اگر لحظه‌ای برق دستگاه را کم و زیاد کند تا دستگاه در وسط بررسی رمز دچار خطا شود و اشتباهاً پول بدهد، این شبیه **Fault Attack** است. شخص attacker نمی‌خواهد همیشه دستگاه را خراب کند؛ می‌خواهد خطا را دقیقاً در زمان و مکان مناسب ایجاد کند تا از نتیجهٔ غلط چیزی بفهمد یا یک check امنیتی را skip کند.

در متن فصل گفته شده fault attack با تزریق deliberate یا malicious faults انجام می‌شود و هدف آن آوردن دستگاه به stateهایی است که از آن‌ها بتوان private internal information مثل key را استخراج کرد. یعنی خطا خودش هدف نهایی نیست؛ خطا وسیله‌ای است برای گرفتن اطلاعات، bypass کردن کنترل‌ها، یا ایجاد **Denial of Service**.

این حملات مخصوصاً علیه **Cryptographic Implementations** خطرناک‌اند. چون اگر یک encryption یا signature در حالت عادی خروجی درست بدهد، ولی با fault خروجی غلط بدهد، attacker می‌تواند خروجی درست و غلط را مقایسه کند. این مقایسه ممکن است اطلاعاتی دربارهٔ key بدهد. به این روش کلی‌تر **Differential Fault Analysis / DFA** می‌گوییم.

---

### ۲. بررسی Temporary Fault و Permanent Fault

نوع Faultها دو حالت کلی دارند: **Temporary Fault** و **Permanent Fault**. در نوع **Temporary Fault** خطا فقط برای مدت کوتاهی اثر دارد و بعد از بین می‌رود. مثلاً یک voltage glitch باعث می‌شود یک register برای یک cycle مقدار اشتباه بگیرد، اما بعد از reset یا گذر زمان دستگاه دوباره عادی شود. این نوع برای attacker خیلی جذاب است، چون اثر دائمی نمی‌گذارد و حمله ممکن است چند بار تکرار شود.

در نوع **Permanent Fault** خطا باقی می‌ماند. مثلاً یک حافظه، transistor، wire یا fuse برای همیشه تغییر کند یا خراب شود. این نوع گاهی برای تخریب یا **Denial of Service** مناسب است، اما برای استخراج پنهانی secret همیشه ایده‌آل نیست، چون احتمالاً device را خراب می‌کند و trace باقی می‌گذارد. البته در بعضی حملات permanent modification می‌تواند برای bypass دائمی security mechanism استفاده شود.

فصل یک مثال ساده می‌دهد: فرض کن attacker بتواند یک bit از **Secret Key** را اجباراً 0 کند. اول خروجی درست را داریم:

فرمول: **COK = E(P)**

بعد همان plaintext را encrypt می‌کنیم ولی هنگام اجرا fault تزریق می‌کنیم:

فرمول: **C′ = E(P) while injecting a fault**

اگر نتیجه **C′ = COK** باشد، یعنی fault اثر نکرده؛ پس bit هدف از قبل 0 بوده است. اگر نتیجه **C′ ≠ COK** باشد، یعنی bit قبل از fault احتمالاً 1 بوده و با forced 0 خروجی تغییر کرده است. با تکرار این ایده bit-by-bit می‌توان اطلاعاتی دربارهٔ key گرفت. این مثال ساده‌شده است، اما منطق اصلی را نشان می‌دهد: مفهوم **compare correct computation with faulty computation**.

---

### ۳. روش‌های Fault Injection Techniques

برای تزریق fault چند روش وجود دارد. یکی تکنیک **Underpowering / Overpowering** است؛ یعنی تغییر supply voltage. اگر ولتاژ خیلی کم یا خیلی زیاد شود، gates و flip-flopها ممکن است در زمان درست switch نکنند یا مقدار غلط بگیرند. این را می‌توان نوعی **Power Supply Attack** دانست. مثلاً یک circuit در ولتاژ عادی خروجی درست می‌دهد، اما اگر دقیقاً هنگام check کردن access control ولتاژ drop کند، ممکن است check اشتباه pass شود.

روش دوم تکنیک **Altering the Clock** یا **Clock Glitch Attack** است. مدارهای دیجیتال با clock هماهنگ کار می‌کنند. اگر attacker یک pulse خیلی کوتاه، خیلی بلند، یا با timing غیرعادی تزریق کند، ممکن است بعضی registerها مقدار جدید را زودتر یا دیرتر sample کنند. نتیجه می‌تواند skip شدن instruction، خراب شدن control flow یا ثبت مقدار غلط باشد. این حمله مخصوصاً برای bypass کردن conditional checks خطرناک است.

روش سوم تکنیک **Temperature Attack** است. دما روی delay، leakage و رفتار transistorها اثر دارد. با سرد یا گرم‌کردن غیرعادی device، attacker می‌تواند timing margins را تغییر دهد و احتمال fault را بالا ببرد. روش چهارم حملات **Optical / Electromagnetic Attacks** است. در **Laser Fault Injection** نور laser می‌تواند state transistorها را تغییر دهد یا charge ایجاد کند. این روش بسیار دقیق است، اما معمولاً نیاز به **De-packaging** دارد تا silicon die کاملاً exposed شود. در تکنیک **Electromagnetic Fault Injection / EMFI** attacker با pulseهای EM یا harmonic signals رفتار circuit را مختل می‌کند.

فصل همچنین اشاره می‌کند که برای precision بالا باید fault از نظر **Space** و **Time** دقیق باشد. یعنی فقط کافی نیست دستگاه را خراب کنیم؛ باید بدانیم کجا و چه زمانی fault بزنیم. روش **Laser Injection** دقیق‌ترین روش است، ولی هزینه و آماده‌سازی بیشتری دارد. روش **Electromagnetic Injection** می‌تواند برای analog circuits با harmonic signals و برای digital circuits با pulse signals استفاده شود.

---

### ۴. بررسی اهداف Fault Attack

فصل سه هدف اصلی برای fault attacks می‌دهد. اول موضوع **Bypassing Access / Right Control Verification** است. یعنی attacker کاری کند که device یک check امنیتی را درست انجام ندهد. مثلاً اگر کد می‌گوید `if PIN is correct then unlock`، یک fault ممکن است باعث شود branch اشتباه اجرا شود و device بدون PIN درست unlock شود. این هدف بیشتر با **Integrity** و **Access Control** مرتبط است.

دوم موضوع **Generating Faulty Encryptions / Signatures** است. این مورد برای cryptanalysis مهم است. بخش attacker چند خروجی درست و چند خروجی faulty جمع می‌کند، سپس با تحلیل تفاوت‌ها تلاش می‌کند key را recover کند. این دقیقاً فضای **Differential Fault Analysis** است. در امضاهای دیجیتال هم اگر signature با fault تولید شود، ممکن است رابطه‌ای از secret signing key لو برود.

سوم موضوع **Denial of Service / DoS** است. این‌جا attacker ممکن است فقط بخواهد دستگاه را از کار بیندازد. مثلاً fault طوری تزریق شود که firmware crash کند، memory خراب شود، یا defense mechanism کاملاً fail شود. در سیستم‌های safety-critical مثل خودرو، پزشکی یا صنعتی، حمله DoS می‌تواند بسیار خطرناک باشد.

پس حمله fault attack همیشه برای دزدیدن key نیست. گاهی برای **bypass** است، گاهی برای **cryptanalysis**، گاهی برای **availability attack**. در تست‌ها اگر گزینه‌ای بگوید fault attacks فقط برای key extraction هستند، کامل نیست.

---

### ۵. تحلیل Differential Fault Analysis / DFA

مفهوم **Differential Fault Analysis / DFA** یعنی attacker چند بار fault تزریق کند، خروجی‌های faulty را جمع کند، و آن‌ها را با خروجی‌های correct مقایسه کند تا به secret information برسد. در صفحهٔ دوم فایل، دیاگرام DFA مسیر را این‌طور نشان می‌دهد: رویهٔ **Identify Target Device → Inject Faults → Collect Faulty Outputs → Compare with Correct Results → Compromise Secret Information**.

ایدهٔ DFA این است که خروجی غلط تصادفی بی‌ارزش نیست. اگر attacker بداند الگوریتم چیست و fault حدوداً کجا اثر گذاشته، خروجی غلط می‌تواند مثل یک معادلهٔ اضافی دربارهٔ key عمل کند. هر fault یک constraint جدید می‌دهد. با تعداد کافی fault و تحلیل correlation، attacker می‌تواند بخشی یا کل secret را recover کند.

مثلاً در یک encryption algorithm، اگر fault در round خاصی تزریق شود، difference بین ciphertext درست و ciphertext غلط می‌تواند اطلاعاتی دربارهٔ round key بدهد. بعد از recover کردن round key، attacker ممکن است key اصلی را derive کند. در بعضی cryptosystemها مثل decapsulation هم attacker با دیدن success/failure بعد از fault می‌تواند دربارهٔ secret-dependent noise یا internal value نتیجه بگیرد.

تکنیک DFA معمولاً قوی‌تر از یک fault ساده است، چون فقط به یک خروجی نگاه نمی‌کند؛ چندین fault، چندین خروجی و تحلیل آماری/ریاضی دارد. همچنین می‌تواند با **Side-Channel Attacks** ترکیب شود. مثلاً attacker اول با side-channel زمان مناسب اجرای round را پیدا کند، بعد fault را دقیقاً همان‌جا تزریق کند.

---

### ۶. مکانیزم‌های Countermeasures: Fault Injection Detection

اولین دستهٔ دفاع‌ها مربوط به **Detecting the Fault Injection** است؛ یعنی خود لحظهٔ حمله را تشخیص دهیم. مثلاً اگر attacker با laser حمله کند، می‌توان از **Laser Light Sensors** استفاده کرد. اگر laser باعث جریان غیرعادی در bulk شود، سنسورهای **Bulk Current Sensors** می‌توانند آن را تشخیص دهند. اگر attacker روی voltage یا clock را دستکاری کند، سنسورهای **Glitch Detectors** می‌توانند deviation را detect کنند.

وقتی عمل fault detection رخ دهد، سیستم می‌تواند کارهای مختلف انجام دهد: shutdown کند، result را reject کند، reset شود، secretها را erase کند، یا وارد safe mode شود. متن فایل اشاره می‌کند که سنسورهای bulk current sensors می‌توانند برای trigger کردن code eraser یا flush mechanism استفاده شوند تا حمله قبل از استخراج اطلاعات بی‌اثر شود.

اما این روش‌ها کامل نیستند. اول اینکه ممکن است **False Positive** بدهند؛ یعنی noise یا شرایط محیطی عادی به‌اشتباه به‌عنوان حمله تشخیص داده شود. دوم اینکه ممکن است fault injection رخ دهد ولی error قابل detect ایجاد نکند. سوم اینکه اگر detector خودش bypass شود، defense از بین می‌رود.

نکتهٔ مهم: مفهوم **Fault Detection** با **Fault Tolerance** یکی نیست. مفهوم Fault tolerance یعنی سیستم بتواند با وجود fault همچنان درست یا قابل قبول کار کند. مفهوم Fault detection یعنی فقط تشخیص دهیم fault رخ داده و بعد تصمیم بگیریم result را رد کنیم یا سیستم را خاموش کنیم.

---

### ۷. مکانیزم‌های Countermeasures: Error Detection و Redundancy

دومین دسته دفاع‌ها موضوع **Detecting the Fault Effect / Error Detection** است. یعنی لازم نیست خود injection را ببینیم؛ کافی است اثر آن را در output یا intermediate state تشخیص دهیم. اینجا مفهوم **Redundancy** مهم می‌شود. فایل سه سطح redundancy را معرفی می‌کند: مفاهیم **Space Redundancy، Information Redundancy، Time Redundancy**.

در تکنیک **Space Redundancy** چند compute element همان computation را هم‌زمان انجام می‌دهند و نتیجه‌ها مقایسه می‌شوند. اگر یکی فرق داشت، fault احتمالی detect می‌شود. این شبیه سیستم‌های high-risk و fault-tolerant است که چند module یک محاسبه را انجام می‌دهند و با consensus تصمیم می‌گیرند.

در تکنیک **Time Redundancy** همان computation را دو بار یا چند بار در زمان‌های مختلف انجام می‌دهیم و نتیجه‌ها را مقایسه می‌کنیم. اگر fault موقتی باشد، احتمالاً فقط یکی از اجراها را خراب می‌کند. در تکنیک **Information Redundancy** از کدهای اضافی مثل **Error Detection Code / EDC**، ساختار parity، checksum یا coding استفاده می‌شود تا خطا در داده یا output کاملاً detect شود.

مثال ساده: یک encryption انجام دهیم، بعد operation معکوس را انجام دهیم و ببینیم آیا plaintext اولیه برمی‌گردد یا نه. یا همان operation را دوبار اجرا کنیم و خروجی‌ها را compare کنیم. این روش‌ها overhead دارند. استفاده از **EDC** هم area و delay اضافه می‌کند. هرچه protection بیشتر بخواهیم، معمولاً area و latency بیشتری می‌پردازیم؛ یعنی دوباره trade-off امنیت/هزینه داریم.

---

### ۸. مکانیزم‌های Countermeasures: De-synchronization

حملهٔ fault injection به timing دقیق وابسته است. بخش attacker باید بداند چه زمانی secret در register است، چه زمانی branch بررسی می‌شود، یا چه زمانی round حساس اجرا می‌شود. پس یک دفاع مؤثر این است که زمان‌بندی execution را غیرقابل پیش‌بینی کنیم. این را **De-synchronization** می‌گوییم.

روش‌های de-synchronization شامل تکنیک‌های **Jitter Clock**، **Dummy Instructions** و **Randomized Operation Flow** هستند. تکنیک **Jitter Clock** یعنی clock دقیقاً ثابت و قابل پیش‌بینی نباشد. تکنیک **Dummy Instructions** یعنی instructionهایی اجرا کنیم که کاری مهم انجام نمی‌دهند ولی timing را جابه‌جا می‌کنند. این dummyها می‌توانند در سطح **ISA** یا توسط **Compiler** اضافه شوند. روش **Randomized Operation Flow** هم ترتیب عملیات را تا حدی تغییر می‌دهد.

هدف این نیست که fault غیرممکن شود؛ هدف این است که attacker نتواند با precision کافی fault را در لحظهٔ درست تزریق کند. اگر fault یک cycle زودتر یا دیرتر بخورد، ممکن است فقط باعث crash شود و اطلاعات مفید ندهد. این برای DFA مهم است، چون DFA به جمع‌آوری خروجی‌های faulty مفید نیاز دارد، نه خطاهای بی‌ربط.

اما روش de-synchronization هم هزینه دارد: execution کندتر می‌شود، power بیشتر می‌شود، طراحی و verification سخت‌تر می‌شود. همچنین یک attacker حرفه‌ای ممکن است با trigger یا side-channel، خودش را دوباره synchronize کند.

---

### ۹. مکانیزم‌های Countermeasures: Package Hardening

بعضی روش‌های fault injection techniques، مخصوصاً تکنیک **Laser Injection**، نیاز دارند attacker به silicon die نزدیک شود یا آن را expose کند. بنابراین رویکرد **Package Hardening** می‌تواند حمله را سخت‌تر کند. اگر package به‌راحتی باز شود، attacker راحت‌تر به chip دسترسی پیدا می‌کند. اگر package مقاوم، چندلایه یا شیمیایی باشد، هزینه و ریسک حمله بالا می‌رود.

فایل چند مثال می‌دهد: معماری‌های **Smartcards** معمولاً آسان‌تر باز می‌شوند. ساختار **Metallic Package** را می‌توان مکانیکی باز کرد. ساختار **Epoxy Package** برای بازکردن نیاز به chemical attacks دارد. معماری **System-in-Package / SiP** بسیار سخت‌تر باز می‌شود.

تکنیک Package hardening یک defense مطلق نیست. افراد attackerهای حرفه‌ای با تجهیزات کافی ممکن است هنوز package را باز کنند. اما در security engineering، هدف اغلب افزایش cost و effort حمله است. اگر attack از نظر اقتصادی بی‌صرفه شود، آن‌گاه defense عملی موفق بوده است.

نکتهٔ تستی: تکنیک package hardening بیشتر علیه physical access و invasive/precise injection کمک می‌کند، ولی در برابر همهٔ faultها کافی نیست. مثلاً voltage glitch یا clock glitch ممکن است بدون بازکردن package انجام شود؛ پس باید با detectors و redundancy هم ترکیب شود.

---

# گام ۲: خلاصه تستی و مقایسه‌ای

## بخش Bilingual Cheat-Sheet

### بخش Fault Attack Basics

| Keyword | نکتهٔ تستی |
| --- | --- |
| حمله **Fault Attack** | تزریق عمدی خطا برای رسیدن به state مفید برای attacker |
| عمل **Fault Injection** | ایجاد fault با voltage، clock، laser، EM، temperature و غیره |
| نوع **Temporary Fault** | خطا موقتی است و بعداً از بین می‌رود |
| نوع **Permanent Fault** | خطا دائمی است و باقی می‌ماند |
| روش **DFA** | مخفف Differential Fault Analysis؛ مقایسهٔ output درست و faulty |
| فرض **Fault Model** | فرض attacker دربارهٔ نوع، زمان و مکان fault |

---

### بخش Goals of Fault Attacks

| Goal | توضیح |
| --- | --- |
| هدف **Bypass Access/Right Control** | عبور از checkهای امنیتی |
| هدف **Generate Faulty Encryptions/Signatures** | تولید خروجی غلط برای cryptanalysis |
| هدف **Denial of Service** | از کار انداختن device یا service |

---

### بخش Fault Injection Techniques

| Technique | ایده |
| --- | --- |
| روش **Underpowering / Overpowering** | تغییر supply voltage |
| روش **Clock Glitching** | دستکاری clock و timing |
| روش **Temperature Attack** | تغییر دما برای تغییر delay/behavior |
| روش **Laser Injection** | تغییر state transistor با نور؛ بسیار دقیق |
| روش **Electromagnetic Injection / EMFI** | ایجاد fault با pulse یا harmonic EM signals |
| روش **RAM Attacks** | هدف‌گرفتن memory یا data paths |

---

### بخش Precision

| Concept | نکته |
| --- | --- |
| متغیر **Spatial Precision** | اینکه fault دقیقاً کجا اثر کند |
| متغیر **Temporal Precision** | اینکه fault دقیقاً چه زمانی اثر کند |
| تکنیک **Laser Injection** | دقیق‌ترین روش، اما معمولاً نیازمند de-packaging |
| تکنیک **EM Injection** | قابل استفاده برای analog/digital circuits؛ precision کمتر از laser |

---

### بخش Differential Fault Analysis

| Step | معنی |
| --- | --- |
| مرحله **Identify Target Device** | انتخاب device هدف |
| مرحله **Inject Faults** | ایجاد خطا در اجرای الگوریتم |
| مرحله **Collect Faulty Outputs** | جمع‌آوری خروجی‌های غلط |
| مرحله **Compare with Correct Results** | مقایسه با خروجی درست |
| مرحله **Compromise Secret Information** | استخراج جزئی یا کامل secret |

---

### بخش Fault Detection vs Fault Tolerance

| Concept | معنی |
| --- | --- |
| روش **Fault Detection** | تشخیص fault و مثلاً shutdown یا reject result |
| روش **Fault Tolerance** | ادامهٔ کار درست یا قابل قبول با وجود fault |
| ریسک **False Positive** | شرایطی که detector به‌اشتباه attack تشخیص دهد |
| ریسک **Undetected Fault** | وضعیت fault رخ دهد ولی detector آن را نبیند |

---

### بخش Injection Detection Sensors

| Sensor | کاربرد |
| --- | --- |
| سنسور **Laser Light Sensor** | فرایند detect کردن laser injection |
| سنسور **Bulk Current Sensor** | فرایند detect کردن جریان غیرعادی، مخصوصاً laser-induced |
| سنسور **Glitch Detector** | فرایند detect کردن voltage/clock glitches |

---

### بخش Error Detection / Redundancy

| Redundancy Type | ایده |
| --- | --- |
| نوع **Space Redundancy** | چند compute element هم‌زمان محاسبه می‌کنند |
| نوع **Time Redundancy** | یک operation چند بار اجرا و مقایسه می‌شود |
| نوع **Information Redundancy** | استفاده از EDC، parity، checksum، coding |
| تکنیک **Inverse Operation Check** | انجام operation و inverse برای بررسی consistency |
| تکنیک **Duplicate Computation** | اجرای دوباره و compare کردن results |

---

### بخش De-synchronization

| Technique | هدف |
| --- | --- |
| روش **Jitter Clock** | غیرقابل پیش‌بینی کردن timing |
| روش **Dummy Instructions** | جابه‌جاکردن زمان عملیات حساس |
| روش **Randomized Operation Flow** | سخت‌کردن fault timing |
| متغیر **Main Effect** | کاهش precision در سمت attacker |

---

### بخش Package Hardening

| Package | سختی حمله |
| --- | --- |
| مدل **Smartcard** | نسبتاً آسان‌تر برای بازکردن |
| مدل **Metallic Package** | قابل بازکردن به‌صورت مکانیکی |
| مدل **Epoxy Package** | نیازمند انواع chemical attacks |
| مدل **System-in-Package / SiP** | بسیار سخت‌تر برای بازکردن |

---

# گام ۳: تله‌های امتحانی

## بخش Pitfalls

### ۱. حمله Fault Attack با Side-Channel Attack یکی نیست

در **Side-Channel Attack** معمولاً attacker فقط مشاهده می‌کند.
در **Fault Attack** همواره attacker فعالانه خطا تزریق می‌کند.

### ۲. حمله Fault Attack همیشه permanent نیست

خیلی از انواع fault attacks موقتی‌اند. مثلاً voltage glitch یا clock glitch ممکن است فقط یک cycle را خراب کند.

### ۳. اثر Temporary Fault می‌تواند خطرناک‌تر از Permanent Fault باشد

چون trace کمتری می‌گذارد و attacker می‌تواند حمله را بارها تکرار کند.

### ۴. حمله Fault Attack فقط memory attack نیست

فایل می‌گوید بسیاری از fault attacks علیه memory/data paths هستند، اما این روش‌ها می‌توانند clock، voltage، temperature، laser، EM و logic execution را هم هدف بگیرند.

### ۵. تکنیک Laser Injection دقیق است، ولی معمولاً نیاز به de-packaging دارد

اگر گزینه‌ای بگوید استفاده از laser همیشه ساده و بدون آماده‌سازی است، غلط است.

### ۶. ویژگی Active بودن الزاماً invasive بودن نیست

تکنیک‌های Clock glitch یا voltage glitch مسلماً active هستند، اما ممکن است بدون بازکردن package انجام شوند.

### ۷. مفهوم DFA فقط یک fault ساده نیست

تکنیک **DFA** یعنی چندین fault + جمع‌آوری outputهای faulty + تحلیل تفاوت با outputهای correct.

### ۸. مفاهیم Fault Detection و Error Detection فرق دارند

* رویکرد **Fault Detection:** خود injection یا شرایط حمله detect می‌شود.
* رویکرد **Error Detection:** اثر خطا در computation/output به‌صورت مجزا detect می‌شود.

### ۹. قابلیت Fault Tolerance با Security Countermeasure یکی نیست، ولی کمک می‌کند

راهکارهای safety و fault-tolerant مثل redundancy می‌توانند countermeasure خوبی علیه fault attacks باشند.

### ۱۰. پیاده‌سازی Redundancy هزینه دارد

تکنیک Space redundancy نیازمند area زیاد است؛ تکنیک time redundancy میزان latency را زیاد می‌کند؛ تکنیک information redundancy هم area/delay اضافی تحمیل می‌کند.

### ۱۱. تکنیک De-synchronization حمله را غیرممکن نمی‌کند

فقط timing precision شخص attacker را سخت‌تر می‌کند. یک attacker ممکن است با side-channel دوباره synchronize شود.

### ۱۲. راهکار Package Hardening کافی نیست

این روش برای مقابله با laser خوب است، اما برای همهٔ attackها نه. حملاتی نظیر voltage/clock/EM attacks ممکن است همچنان ممکن باشند.

### ۱۳. ماژول‌های Detectorها false positive دارند

وجود Noise یا شرایط محیطی ممکن است detector را trigger کند. طراحی detector باید بین sensitivity و false alarm تعادل داشته باشد.

### ۱۴. اگر fault اثر قابل مشاهده نگذارد، attack ممکن است بی‌فایده باشد

برای اجرای DFA، شخص attacker به faulty outputs مفید نیاز دارد. هر crash یا random error الزاماً اطلاعات secret نمی‌دهد.

---

# گام ۴: خودآزمایی

## بخش 100% English Quiz

*(در این بخش به دلیل ماهیت انگلیسی سؤالات، حفظ ساختار چپ‌به‌راست (LTR) منطقی و صحیح است)*

### Question 1

**What is the main idea behind a Differential Fault Analysis attack?**

A. To passively measure the power consumption of a device without modifying its behavior.
B. To inject faults during cryptographic execution and compare faulty outputs with correct outputs to infer secret information.
C. To try all possible secret keys until the correct one is found.
D. To encrypt the same plaintext with many different public keys.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **گزینه A غلط است:** این توصیف بیشتر به **Power Side-Channel Attack** مربوط است، نه DFA.
* **گزینه B درست است:** در تکنیک **DFA**، یک attacker اقدام به تزریق fault می‌کند، خروجی‌های غلط را جمع‌آوری کرده و با خروجی درست مقایسه می‌کند تا secret مثل key را استخراج کند.
* **گزینه C غلط است:** این دقیقاً همان **Brute Force** است.
* **گزینه D غلط است:** این تعریف ارتباطی با DFA ندارد و به تحلیل fault مربوط نمی‌شود.

---

### Question 2

**Which of the following is an example of a countermeasure based on de-synchronization?**

A. Adding dummy instructions or clock jitter to make the timing of sensitive operations harder to predict.
B. Publishing all faulty outputs so that the attacker cannot distinguish them.
C. Removing all redundancy from the circuit to reduce area.
D. Storing the secret key in plaintext memory.

**Correct Answer: A**

**تحلیل گزینه‌ها:**

* **گزینه A درست است:** تکنیک‌های **Dummy Instructions** و **Jitter Clock** باعث می‌شوند attacker نتواند fault را دقیقاً در زمان مناسب تزریق کند.
* **گزینه B غلط است:** انتشار دادن faulty outputs اتفاقاً حمله را آسان‌تر می‌کند.
* **گزینه C غلط است:** مکانیسم redundancy یکی از دفاع‌هاست، حذف آن قطعاً دفاع را ضعیف‌تر می‌کند.
* **گزینه D غلط است:** قرار دادن plaintext key storage در سیستم، خودش یک vulnerability است.

---

### Question 3

**Which statement correctly distinguishes fault detection from error detection in the context of fault attack countermeasures?**

A. Fault detection identifies the injection event or abnormal condition, while error detection identifies the incorrect effect produced by the fault in computation or output.
B. Fault detection and error detection are identical and always implemented using the same sensor.
C. Error detection prevents all laser attacks before they happen, while fault detection only compares duplicated outputs.
D. Fault detection is used only for software bugs, while error detection is used only for network attacks.

**Correct Answer: A**

**تحلیل گزینه‌ها:**

* **گزینه A درست است:** مفهوم **Fault Detection** یعنی تشخیص خود حمله یا شرایط غیرعادی؛ در حالی که **Error Detection** یعنی تشخیص نتیجهٔ غلط ناشی از fault.
* **گزینه B غلط است:** این دو concept کاملاً متفاوت‌اند و implementationهای متفاوتی هم دارند.
* **گزینه C غلط است:** مکانیسم error detection یک حمله را قبل از وقوع جلوگیری نمی‌کند؛ بلکه اثر خطا را پیدا می‌کند.
* **گزینه D غلط است:** هر دوی این مفاهیم در سخت‌افزار برای hardware fault attack countermeasures استفاده می‌شوند.