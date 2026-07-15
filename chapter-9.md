این فایل نیز دقیقاً مانند فایل قبلی، از نظر ساختار چپ‌به‌راست (LTR) به دلیل شروع شدن خطوط با کلمات انگلیسی، دچار مشکل به هم‌ریختگی بود.

برای رفع اصولی این مشکل و حفظ ساختار راست‌چین (RTL) بدون شکستن غیرطبیعیِ خطوط، در ابتدای هر خط یا پاراگرافی که با کلمه انگلیسی شروع می‌شد، یک کلمهٔ توصیفی فارسی (مثل: *مفهوم، ساختار، ویژگی، فرمول، قانون* و...) اضافه کردم.

فایل اصلاح‌شده و کاملاً خوانا با حفظ ۱۰۰ درصدی محتوای اصلی تقدیم شما:

---

این فایل قابل قبول است. اسم آپلودش **chapter-9.pdf** است، ولی محتوایش **Physical Unclonable Functions / PUFs** است و در لیست ترم تو با عنوان **07-Physical Unclonable Functions.pdf** آمده؛ پس تدریس شده و باید بخوانیم.

# گام ۱: آموزش مفهومی و مفصل

## Deep Explanation — Physical Unclonable Functions / PUFs

### ۱. ایدهٔ اصلی PUF چیست؟

مفهوم **PUF / Physical Unclonable Function** یعنی یک تابع سخت‌افزاری که خروجی‌اش از ویژگی‌های فیزیکی غیرقابل‌کنترل همان قطعه به‌دست می‌آید. در فرایند ساخت تراشه، حتی اگر دو **Integrated Circuit / IC** دقیقاً با یک طراحی، یک layout و یک technology ساخته شوند، باز هم کاملاً یکسان نیستند. دلیلش **Process Variation** است: تفاوت‌های ریز در transistorها، dopantها، طول مسیرها، delayها، leakage، threshold voltage و غیره. این تفاوت‌ها معمولاً برای طراح دردسرند، اما در PUF از همین تفاوت‌ها به‌عنوان «اثر انگشت سخت‌افزاری» استفاده می‌کنیم.

فرق مهم PUF با یک ID ساده این است که PUF فقط یک عدد ذخیره‌شده نیست. یک PUF در واقع یک **Function** است: ورودی می‌گیرد و خروجی می‌دهد. در ادبیات PUF به ورودی می‌گوییم **Challenge** و به خروجی می‌گوییم **Response**. پس یک زوج ورودی-خروجی می‌شود **CRP / Challenge-Response Pair**. مثلاً اگر در یک memory-based PUF آدرس حافظه را challenge بگیریم، محتوای خوانده‌شده از آن آدرس response است. اگر همان آدرس را روی دو چیپ ظاهراً یکسان بخوانیم، به‌دلیل تفاوت فیزیکی، responseها فرق می‌کنند.

تشبیه ساده: دو اثر انگشت انسان ممکن است از دور شبیه باشند، اما جزئیاتشان یکی نیست. سیستم PUF هم اثر انگشت سخت‌افزار است، ولی به‌جای اینکه فقط یک تصویر ثابت باشد، مثل یک دستگاه سؤال‌وجواب کار می‌کند. تو challenge می‌دهی، سخت‌افزار به‌خاطر ساختار فیزیکی خودش response خاص می‌دهد. اگر کسی بخواهد همان PUF را clone کند، صرف دانستن طراحی منطقی کافی نیست، چون باید همان variationهای فیزیکی را هم تکرار کند؛ کاری که عملاً بسیار سخت یا غیرممکن است.

از نظر امنیتی، PUF مثل یک **Hardware One-Way Function** در نظر گرفته می‌شود. یعنی محاسبهٔ response برای خود PUF آسان است، اما ساختن یک سخت‌افزار دیگر که برای همان challengeها همان responseها را بدهد سخت است. همچنین اگر فقط چند CRP را دیده باشیم، نباید بتوانیم response challengeهای جدید را پیش‌بینی کنیم. بنابراین سه ایدهٔ کلیدی PUF عبارت‌اند از: **Unclonability، Unpredictability، One-wayness**.

---

### ۲. ویژگی‌های اصلی PUF

یک PUF خوب باید چند ویژگی داشته باشد. اول **Computable** باشد؛ یعنی وقتی خود PUF و challenge `x` را داریم، محاسبهٔ `y = PUF(x)` باید آسان باشد. قرار نیست خود device برای پاسخ دادن خیلی کند یا غیرعملی باشد. دوم **Unique** باشد؛ یعنی response شامل اطلاعاتی دربارهٔ identity همان physical entity باشد. اگر همهٔ چیپ‌ها برای یک challenge پاسخ یکسان بدهند، PUF به درد identification نمی‌خورد.

سوم **Reproducible** یا **Repeatable** باشد. این قسمت ظریف است. سیستم PUF کاملاً دیجیتال ایده‌آل نیست که همیشه دقیقاً همان bitها را بدهد. چون temperature، voltage، aging و noise روی response اثر می‌گذارند. پس reproducibility در PUF یعنی response تقریباً تکرارپذیر باشد، با خطای کوچک. به همین دلیل معمولاً به **Error Correction Code / ECC** یا **Helper Data** نیاز داریم تا response ناپایدار را به یک key یا ID پایدار تبدیل کنیم.

چهارم **Unclonable** باشد؛ یعنی ساختن یک procedure یا device دیگر که برای همه یا تعداد زیادی challenge همان response را بدهد سخت باشد. پنجم **Unpredictable** باشد؛ یعنی اگر attacker یک مجموعه CRP داشته باشد، نتواند برای challenge جدید response را پیش‌بینی کند. ششم **One-way** باشد؛ یعنی اگر response را دیدیم، نتوانیم challenge متناظر را به‌راحتی پیدا کنیم.

نکتهٔ مهم امتحانی: این ویژگی‌ها جدا از هم‌اند اما به هم مرتبط‌اند. مثلاً یک PUF ممکن است unique باشد، اما اگر repeatability بدی داشته باشد، در عمل قابل استفاده نیست. یا ممکن است repeatable باشد، اما اگر با **Machine Learning** بتوان responseهای جدید را predict کرد، دیگر برای authentication امن نیست.

---

### ۳. مبحث Entropy در PUF

در PUF هم مثل **Random Number Generation**، مفهوم **Entropy** مهم است. اگر responseهای PUF واقعاً unpredictable باشند، entropy بالا داریم. اگر بعضی bitها همیشه 0 یا همیشه 1 باشند، آن bitها تقریباً هیچ entropy ندارند. پس باید entropy response را حتی در سطح تک‌تک bitها بررسی کنیم. اگر یک bit در response برای challengeهای مختلف تغییر نکند یا در اکثر deviceها یکسان باشد، آن bit برای authentication یا key generation مناسب نیست.

فصل دو مفهوم مهم می‌دهد: **Shannon Entropy** و **Min-Entropy**. معیار Shannon entropy میانگین عدم قطعیت را اندازه می‌گیرد. اما **Min-Entropy** برای امنیت مهم‌تر است، چون worst-case predictability را می‌سنجد؛ یعنی احتمال محتمل‌ترین output را نگاه می‌کند. اگر یک response خاص خیلی محتمل باشد، attacker همان را حدس می‌زند. پس حتی اگر میانگین خوب به نظر برسد، min-entropy پایین می‌تواند خطرناک باشد.

برای PUF، مقدار entropy فقط یک عدد تئوری نیست. در عمل یعنی responseها باید بین deviceهای مختلف متفاوت باشند، داخل یک response تعداد 0 و 1 متعادل باشد، و با دیدن CRPهای قبلی نتوان CRPهای جدید را یاد گرفت. اگر PUF response bias داشته باشد، attacker فضای جست‌وجو را کوچک‌تر می‌کند.

نکتهٔ ساده برای حفظ: **سیستم PUF باید شبیه اثر انگشت باشد، اما اثر انگشتی که هم پایدار است، هم قابل جعل نیست، هم از روی چند نمونه قابل پیش‌بینی نیست.**

---

### ۴. ساختار Silicon PUFs و Process Variation

خانواده **Silicon PUFs** از variationهای طبیعی در ساخت silicon استفاده می‌کنند. مثلاً در transistorها ممکن است **Random Dopant Fluctuation** وجود داشته باشد. یعنی تعداد و موقعیت ذرات dopant در کانال transistor دقیقاً یکسان نیست. این باعث می‌شود ویژگی‌هایی مثل delay، current، threshold voltage یا startup behavior کمی فرق کند. این تفاوت‌ها کوچک‌اند، ولی برای PUF کافی‌اند.

در حالت عادی، طراح مدار می‌خواهد این variationها اثر مخرب نگذارند. مثلاً اگر دو path منطقی باید delay مشابه داشته باشند، process variation می‌تواند timing را خراب کند. اما در PUF همین delayهای متفاوت تبدیل به feature امنیتی می‌شوند. مثلاً اگر دو مسیر ظاهراً مساوی داشته باشیم، در یک chip مسیر بالا ممکن است کمی سریع‌تر باشد، در chip دیگر مسیر پایین. همین «کمی سریع‌تر بودن» می‌تواند یک bit response بسازد.

پس Silicon PUF به این دلیل جذاب است که نیازی به ذخیرهٔ permanent secret ندارد. مقدار secret از خود physical structure استخراج می‌شود. این یعنی اگر chip خاموش باشد، key الزاماً جایی در memory ذخیره نشده است. هنگام نیاز، PUF response تولید می‌شود و از آن key ساخته می‌شود. البته برای پایدارسازی معمولاً helper data لازم است.

---

### ۵. مدل Ring Oscillator PUF

در **Ring Oscillator PUF** از چند **Ring Oscillator** استفاده می‌شود. یک Ring Oscillator معمولاً از تعداد فردی inverter در یک حلقه ساخته می‌شود و نوسان می‌کند. در حالت ایده‌آل اگر همهٔ oscillatorها یکسان باشند، فرکانسشان باید برابر باشد. اما در واقعیت به‌خاطر process variation، هر oscillator فرکانس کمی متفاوت دارد.

برای تولید response، معمولاً دو oscillator انتخاب می‌شوند و تعداد نوسان‌هایشان در یک بازهٔ زمانی شمرده می‌شود. اگر oscillator اول سریع‌تر باشد، خروجی مثلاً 1 می‌شود؛ اگر دومی سریع‌تر باشد، خروجی 0 می‌شود. پس challenge می‌تواند تعیین کند کدام دو oscillator با هم مقایسه شوند. پاسخ response هم نتیجهٔ مقایسهٔ فرکانس آن‌هاست.

اگر N oscillator داشته باشیم، تعداد pairهای ممکن برابر است با:
فرمول: **N(N − 1) / 2**

مثلاً برای **N = 16**، تعداد pairها می‌شود **120**. البته entropy واقعی کمتر از تعداد pairهاست، چون pairها مستقل کامل نیستند و همهٔ مقایسه‌ها اطلاعات جداگانهٔ کامل نمی‌دهند. فصل اشاره می‌کند که entropy در Ring Oscillator PUF کمتر از مقدار خام pairهاست.

از نظر امتحانی، مدل Ring Oscillator PUF یعنی: **frequency comparison caused by process variation**. حملهٔ مهم هم این است که اگر attacker تعداد زیادی CRP جمع کند، ممکن است با modeling یا Machine Learning بخشی از رفتار را پیش‌بینی کند.

---

### ۶. مدل Arbiter PUF

در **Arbiter PUF** دو signal race می‌دهند. تصور کن دو مسیر موازی داریم که از مجموعه‌ای از multiplexerها عبور می‌کنند. بخش challenge تعیین می‌کند در هر مرحله مسیرها مستقیم بمانند یا عوض شوند. در انتها یک **Arbiter**، معمولاً یک latch، تصمیم می‌گیرد کدام signal زودتر رسیده است. اگر مسیر بالا زودتر برسد، response مثلاً 1 است؛ اگر مسیر پایین زودتر برسد، response 0 است.

ایدهٔ امنیتی اینجاست که delay هر multiplexer و wire کمی با chip دیگر فرق دارد. پس برای یک challenge یکسان، چیپ‌های مختلف ممکن است race outcome متفاوت داشته باشند. پارامتر challenge عملاً مسیر propagation را تعیین می‌کند و physical delays نتیجه را مشخص می‌کنند.

مدل Arbiter PUF معمولاً به‌عنوان **Strong PUF** معرفی می‌شود، چون تعداد زیادی challenge-response pair می‌تواند تولید کند. اگر n مرحله multiplexer داشته باشیم، تعداد challengeهای ممکن زیاد می‌شود. برای authentication مفید است، چون verifier می‌تواند هر بار challenge جدید بدهد.

اما Arbiter PUF یک مشکل جدی دارد: چون ساختارش تا حد زیادی delay-based و نسبتاً قابل مدل‌سازی است، در برابر **Machine Learning Attacks** آسیب‌پذیر است. اگر attacker CRPهای زیادی جمع کند، می‌تواند modelی بسازد که response challengeهای ندیده را با accuracy بالا پیش‌بینی کند.

---

### ۷. مدل SRAM-Based PUF

در **SRAM-Based PUF** از startup state سلول‌های SRAM استفاده می‌شود. وقتی SRAM روشن می‌شود، هر cell باید بین 0 و 1 settle کند. در حالت ایده‌آل همهٔ inverterهای داخل SRAM cell متقارن‌اند، اما در واقعیت process variation باعث می‌شود یک طرف کمی قوی‌تر باشد. بنابراین بعضی cellها معمولاً به 0 می‌روند، بعضی به 1.

این startup pattern برای هر chip تقریباً unique است. اگر حافظه را قبل از هر نوشتنی بخوانیم، یک pattern می‌بینیم که می‌تواند مثل fingerprint استفاده شود. در اینجا challenge می‌تواند address باشد و response محتوای آن address یا مجموعه‌ای از addressها باشد. البته در عمل معمولاً کل یا بخش بزرگی از memory خوانده می‌شود تا ID یا key کافی استخراج شود.

مشکل SRAM PUF ویژگی stability است. همهٔ bitها کاملاً پایدار نیستند. بعضی cellها با تغییر temperature، voltage یا aging ممکن است گاهی 0 و گاهی 1 شوند. برای همین SRAM PUF معمولاً به **Helper Data** و **ECC** نیاز دارد. Helper data کمک می‌کند response noisy به یک key پایدار تبدیل شود.

در طبقه‌بندی فصل، **SRAM-Based PUF** معمولاً **Weak PUF** است، چون CRP set آن محدود است. اینجا “weak” یعنی تعداد CRP کم، نه اینکه حتماً insecure یا بی‌ارزش باشد.

---

### ۸. مفهوم CRP و تفاوت Weak PUF و Strong PUF

مفهوم **CRP / Challenge-Response Pair** یعنی یک challenge خاص و response متناظر آن. در authentication، قبل از deployment، از device تعدادی CRP جمع می‌شود و در یک database امن ذخیره می‌شود. بعداً برای احراز اصالت device، verifier یک challenge می‌فرستد و response را با database مقایسه می‌کند.

ساختار **Weak PUF** یعنی تعداد CRPها محدود است، معمولاً حتی نزدیک به یک response اصلی. مدل Weak PUF بیشتر برای **Device Identification** یا **Secret Key Generation** استفاده می‌شود. مثلاً SRAM PUF می‌تواند در هر chip یک key unique تولید کند. چون CRP کم است، access به آن باید شدیداً محدود باشد. اگر attacker همان response اصلی را بخواند، عملاً secret را گرفته است.

ساختار **Strong PUF** یعنی تعداد CRPها بسیار زیاد است، مثلاً بزرگ‌تر از **2⁸⁰**. مدل Strong PUF برای **Device Authentication** مناسب‌تر است، چون verifier می‌تواند challengeهای زیادی داشته باشد و هر بار challenge متفاوت بفرستد. حتی اگر attacker برخی CRPها را ببیند، نباید بتواند CRPهای ناشناخته را پیش‌بینی کند.

تلهٔ مهم: اصطلاح **Weak PUF یعنی PUF ضعیف از نظر امنیتی نیست.** اسمش به اندازهٔ CRP space اشاره دارد. یک Weak PUF می‌تواند برای key generation خیلی مفید باشد. از طرف دیگر، یک Strong PUF اگر با Machine Learning قابل مدل‌سازی شود، می‌تواند از نظر authentication خطرناک باشد.

---

### ۹. معیارهای ارزیابی PUF: Uniformity، Repeatability، Uniqueness

اولین معیار **Uniformity** است. یعنی در response یک PUF، تعداد 0 و 1 تقریباً برابر باشد. حالت ایده‌آل **50%** است. اگر response بیشترش 1 باشد یا بیشترش 0 باشد، bias داریم و entropy کم می‌شود. Uniformity معمولاً برای یک response روی bitهای آن سنجیده می‌شود.

دوم **Repeatability** یا **Reliability** است. یعنی وقتی یک device را با همان challenge چند بار تست می‌کنیم، response تقریباً همان باشد. اگر response هر بار زیاد تغییر کند، authentication یا key generation غیرقابل اعتماد می‌شود. پارامتر Repeatability تحت تأثیر **Aging، Temperature، Voltage، Noise** قرار می‌گیرد. هیچ راهی برای حذف کامل این اثرها نیست؛ فقط می‌توان آن‌ها را مدیریت کرد.

سوم **Uniqueness** است. یعنی response دو device مختلف برای challenge یکسان باید متفاوت باشد. این معمولاً با **Hamming Distance / HD** اندازه‌گیری می‌شود. اگر response دو device کاملاً random و مستقل باشد، انتظار داریم حدود 50% bitها متفاوت باشند. اگر HD خیلی کم باشد، یعنی deviceها خیلی شبیه‌اند و PUF قدرت identification خوبی ندارد.

دو معیار دیگر هم مهم‌اند: ویژگی **Unpredictability** یعنی با دانستن CRPهای قبلی نتوان CRP جدید را پیش‌بینی کرد. ویژگی **Unclonability** یعنی نتوان دو device با CRP set یکسان ساخت. این دو برای امنیت بسیار حیاتی‌اند، ولی اندازه‌گیری‌شان سخت‌تر از uniformity و repeatability است.

---

### ۱۰. کاربردهای PUF

کاربرد اصلی **Weak PUF** تولید **Device ID** یا **Secret Key** است. مثلاً به‌جای اینکه یک key را در non-volatile memory ذخیره کنیم، از SRAM startup pattern یک key می‌سازیم. این key می‌تواند برای encryption داده‌های ذخیره‌شده در circuit یا encryption keyهای موجود در non-volatile memory استفاده شود. مزیتش این است که key خام در حافظه ذخیره نمی‌شود؛ از physical behavior استخراج می‌شود.

برای پایدارسازی key، از **Helper Data** استفاده می‌شود. در مرحله enrollment phase، یک response اصلی و ECC/helper data تولید می‌شود. در field، وقتی response noisy خوانده شد، helper data کمک می‌کند خطاها اصلاح شوند و همان key پایدار بازسازی شود. محل مناسب ذخیرهٔ helper data می‌تواند **TPM** یا secure non-volatile memory باشد. اما helper data خودش ممکن است مقداری information leakage داشته باشد، پس باید مراقب طراحی آن بود.

کاربرد اصلی **Strong PUF**، فرایند **IC Authentication** است. بعد از ساخت، device با چندین challenge تست می‌شود و responseها در database امن ذخیره می‌شوند. هنگام authentication، بخش verifier چند challenge می‌فرستد و responseها را بررسی می‌کند. اگر تعدادی response match شود، device معتبر تلقی می‌شود. چون PUF noisy است، معمولاً protocol باید چند خطا را تحمل کند.

پس خلاصهٔ حفظی:
کاربرد **Weak PUF → Key generation / device identification**
کاربرد **Strong PUF → Challenge-response authentication**

---

### ۱۱. حملات علیه PUF

اولین حمله **Exhaustive Reading** روی Weak PUF است. چون Weak PUF CRPهای کمی دارد، اگر attacker بتواند response اصلی را بخواند، secret از بین می‌رود. مثلاً اگر SRAM PUF startup pattern از طریق on-chip channel قابل خواندن باشد، attacker می‌تواند آن را extract کند.

دومین حمله **Modeling Attack** روی Strong PUF است. مدل Strong PUF تعداد زیادی CRP دارد، اما اگر attacker بتواند subset بزرگی از CRPها را جمع کند، ممکن است با **Machine Learning** مدل رفتار PUF را بسازد. این مخصوصاً برای **Arbiter PUF** و بعضی **Ring Oscillator PUF**ها مطرح است. فصل اشاره می‌کند که prediction accuracy می‌تواند بسیار بالا، حتی 90% و بیشتر، شود.

سومین حمله **Side-Channel Analysis** است. هکر (attacker) ممکن است از power consumption، timing، EM leakage یا حتی helper data اطلاعات بگیرد. مثلاً در Arbiter PUF، بخش arbiter یا latch ممکن است power pattern قابل تحلیل ایجاد کند. یا helper data مربوط به ECC ممکن است بخشی از response را leak کند.

برای دفاع، response خام نباید مستقیم در کانال قابل مشاهده باشد. یکی از روش‌ها ترکیب response با **Hash** یا secret است. به‌جای ارسال مستقیم response، response وارد یک hash یا cryptographic function می‌شود. روش دیگر این است که challenge واقعی مستقیماً دیده نشود و به sub-challengeها یا transformationهای داخلی تبدیل شود. هدف این است که attacker نتواند مجموعهٔ واقعی input-output را برای training جمع کند.

---

# گام ۲: خلاصه تستی و مقایسه‌ای

## Bilingual Cheat-Sheet

### PUF Basics

| Keyword | نکتهٔ تستی |
| --- | --- |
| **PUF** | Physical Unclonable Function |
| **Physical** | وابسته به process variation و ویژگی فیزیکی hardware |
| **Function** | ورودی می‌گیرد و خروجی می‌دهد |
| **Challenge** | مقادیر input به PUF |
| **Response** | مقادیر output از PUF |
| **CRP** | Challenge-Response Pair |
| **Process Variation** | تفاوت‌های طبیعی ساخت IC |
| **Hardware One-Way Function** | محاسبه آسان روی device، clone/predict سخت |

---

### PUF Main Properties

| Property | معنی |
| --- | --- |
| **Computable** | محاسبهٔ `PUF(x)` روی device آسان است |
| **Unique** | مقادیر response حامل identity سخت‌افزار است |
| **Reproducible / Repeatable** | مقادیر response برای همان challenge تقریباً تکرارپذیر است |
| **Unclonable** | ساخت device مشابه با CRPهای مشابه سخت است |
| **Unpredictable** | با CRPهای معلوم، CRPهای جدید قابل حدس نیستند |
| **One-way** | از response نتوان challenge را پیدا کرد |

---

### Entropy

| Concept | نکته |
| --- | --- |
| **Shannon Entropy** | میانگین uncertainty |
| **Min-Entropy** | مقادیر worst-case predictability |
| **Bit-level Entropy** | هر bit response باید بررسی شود |
| **Fixed Bits** | آن bitهایی که تغییر نمی‌کنند entropy کمی دارند |
| **Bias** | اگر 0 یا 1 غالب باشد، security کاهش می‌یابد |

---

### Main PUF Types

| PUF Type | ایدهٔ اصلی | معمولاً |
| --- | --- | --- |
| **Silicon PUF** | استفاده از process variation در IC | دستهٔ کلی |
| **Ring Oscillator PUF** | مقایسهٔ frequency oscillatorها | Strong PUF در این فصل |
| **Arbiter PUF** | مسابقه (race) بین دو path و تصمیم arbiter | Strong PUF |
| **SRAM-Based PUF** | بررسی startup state سلول‌های SRAM | Weak PUF |
| **Optical / Acoustic PUF** | استفاده از ویژگی‌های نور/صوت | non-silicon |

---

### Ring Oscillator PUF

| Concept | نکته |
| --- | --- |
| **Ideal case** | همهٔ oscillatorها frequency یکسان دارند |
| **Reality** | ویژگی process variation باعث frequency متفاوت می‌شود |
| **Response bit** | نتیجهٔ مقایسهٔ دو oscillator |
| **Challenge** | انتخاب pairها |
| **Pairs** | برای N oscillator داریم **N(N−1)/2** pair |
| **Risk** | مباحث modeling و correlation بین pairها |

---

### Arbiter PUF

| Concept | نکته |
| --- | --- |
| **Challenge** | مسیر multiplexerها را انتخاب می‌کند |
| **Race** | دو signal در pathهای مختلف رقابت می‌کنند |
| **Arbiter/Latch** | مشخص می‌کند کدام signal زودتر رسیده |
| **Response** | مقادیر 0 یا 1 بر اساس fast path |
| **Strength** | ظرفیت CRP space زیاد |
| **Weakness** | آسیب‌پذیر به Machine Learning Modeling |

---

### SRAM-Based PUF

| Concept | نکته |
| --- | --- |
| **Entropy source** | بررسی startup state سلول‌های SRAM |
| **Challenge** | مقادیر address یا مجموعه addressها |
| **Response** | محتوای SRAM بعد از power-up |
| **Problem** | بعضی bitها unstable هستند |
| **Defense** | ترکیب Helper Data + ECC |
| **Classification** | Weak PUF |

---

### Weak vs Strong PUF

| Feature | **Weak PUF** | **Strong PUF** |
| --- | --- | --- |
| CRP count | محدود، معمولاً کم | بسیار زیاد، مثلاً > 2⁸⁰ |
| Main use | Key generation، Device ID | احراز اصالت Authentication |
| Access | باید restricted باشد | بخشی از CRPها ممکن است public باشد |
| Example | مدل SRAM PUF | مدل‌های Arbiter PUF، Ring Oscillator PUF |
| Main risk | Exhaustive reading | تکنیک‌های Modeling / Machine Learning |

---

### PUF Quality Metrics

| Metric | معنی | Ideal |
| --- | --- | --- |
| **Uniformity** | نسبت 0 و 1 در response | 50% |
| **Repeatability / Reliability** | مقدار response برای همان challenge ثابت بماند | نزدیک 100% |
| **Uniqueness** | تفاوت response بین deviceهای مختلف | HD حدود 50% |
| **Unpredictability** | مقادیر CRPهای جدید قابل پیش‌بینی نباشند | بالا |
| **Unclonability** | هرگز device مشابه ساخته نشود | بالا |

---

### Helper Data

| Concept | نکته |
| --- | --- |
| **Helper Data** | دادهٔ کمکی برای stabilizing response |
| **ECC** | الگوریتم Error Correction Code برای اصلاح bitهای noisy |
| **Use case** | مدل Weak PUF key generation |
| **Risk** | ممکن است بخشی از response را leak کند |
| **Storage** | بهتر است در TPM یا secure NVM ذخیره شود |

---

### Attacks on PUFs

| Attack | هدف |
| --- | --- |
| **Exhaustive Reading** | خواندن CRP محدود Weak PUF |
| **Modeling Attack** | ساخت model با CRPهای زیاد |
| **Machine Learning Attack** | پیش‌بینی CRPهای ناشناخته |
| **Side-Channel Analysis** | استخراج اطلاعات از power/timing/helper data |
| **Database Compromise** | سرقت CRP database و تقلید authentication |

---

# گام ۳: تله‌های امتحانی

## Pitfalls

### ۱. مفهوم PUF یک ID ذخیره‌شده نیست

دستگاه PUF مثل serial number نیست. PUF یک **function** است که challenge می‌گیرد و response تولید می‌کند.

### ۲. اصطلاح Weak PUF یعنی ناامن نیست

کلمه **Weak** در اینجا به تعداد CRP اشاره دارد، نه به کیفیت امنیتی مطلق. Weak PUF می‌تواند برای key generation بسیار مفید باشد.

### ۳. مدل Strong PUF الزاماً امن‌تر از Weak PUF نیست

مدل Strong PUF دارای CRP زیاد است، اما همین CRP زیاد می‌تواند زمینهٔ **Machine Learning Attack** فراهم کند.

### ۴. ویژگی Reproducibility در PUF یعنی دقیقاً ۱۰۰٪ نیست

ساختارهای PUFها noisy هستند. response باید تقریباً تکرارپذیر باشد و معمولاً نیاز به **ECC / Helper Data** دارد.

### ۵. مبحث Helper Data بدون هزینه نیست

داده‌های Helper data ویژگی stability را بهتر می‌کند، اما ممکن است information leakage ایجاد کند. پس باید امن ذخیره و طراحی شود.

### ۶. ساختار SRAM PUF از startup state استفاده می‌کند

مدل SRAM PUF به data نوشته‌شده بعداً کاری ندارد. نکتهٔ اصلی همان حالت اولیه بعد از power-up است.

### ۷. مدل Ring Oscillator PUF بر اساس frequency است

گزینه‌ای که بگوید RO PUF فقط مقدار منطقی memory را می‌خواند غلط است. ایدهٔ اصلی مقایسهٔ فرکانس oscillatorهاست.

### ۸. مدل Arbiter PUF بر اساس race/delay است

در **Arbiter PUF**، challenge مسیرها را تعیین می‌کند و arbiter می‌سنجد کدام signal زودتر رسیده است.

### ۹. قانون All CRPs نباید public شوند

اگر attacker database کامل CRPها را داشته باشد، می‌تواند device جعلی بسازد که فقط responseهای database را replay کند.

### ۱۰. سیستم PUF بدون protocol کافی نیست

برای authentication فقط PUF داشتن کافی نیست. باید protocol داشته باشیم: تعداد challengeها، خطای قابل قبول، retry، database security، response protection.

### ۱۱. معیار Uniformity تنها معیار نیست

ممکن است response نسبت 0 و 1 خوبی داشته باشد، ولی repeatability بد یا predictability بالا داشته باشد.

### ۱۲. معیار Uniqueness با Hamming Distance سنجیده می‌شود

برای دو device مختلف، response به challenge یکسان باید حدوداً 50% bit difference داشته باشد.

### ۱۳. حمله ML Attack برای Strong PUF جدی است

اگر CRPهای زیادی قابل مشاهده باشند، attacker می‌تواند مدل بسازد و unknown CRPها را پیش‌بینی کند.

### ۱۴. حمله Side Channel فقط برای crypto نیست

تکنولوژی PUF هم می‌تواند از power، timing یا helper data leakage ضربه بخورد.

---

# گام ۴: خودآزمایی

## 100% English Quiz

*(در این بخش به دلیل ماهیت انگلیسی سؤالات، حفظ ساختار چپ‌به‌راست (LTR) منطقی و صحیح است)*

### Question 1

**What is the main reason Physical Unclonable Functions are considered difficult to clone?**

A. They store a fixed secret key in non-volatile memory that cannot be read by software.
B. They rely on random manufacturing process variations that are difficult to reproduce exactly in another physical device.
C. They use only public cryptographic algorithms and therefore require no hardware uniqueness.
D. They generate responses by downloading them from a secure external database.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **گزینه A غلط است:** تکنولوژی PUF الزاماً secret key ذخیره‌شده در NVM نیست؛ secret از ویژگی فیزیکی device استخراج می‌شود.
* **گزینه B درست است:** پایهٔ PUF همان **Process Variation** است که تکرار دقیق آن در device دیگر عملاً سخت است.
* **گزینه C غلط است:** ماهیت PUF دقیقاً بر hardware uniqueness تکیه دارد.
* **گزینه D غلط است:** ساختار database ممکن است برای authentication CRPها استفاده شود، اما خود PUF response را مستقیماً از hardware تولید می‌کند.

---

### Question 2

**Which statement correctly distinguishes weak PUFs from strong PUFs?**

A. Weak PUFs are insecure by definition, while strong PUFs are always resistant to machine learning attacks.
B. Weak PUFs have a limited number of challenge-response pairs and are often used for key generation, while strong PUFs have a large CRP space and are often used for authentication.
C. Weak PUFs are always analog, while strong PUFs are always software-based.
D. Weak PUFs cannot be used in hardware security systems.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **گزینه A غلط است:** اصطلاح weak یعنی CRP کم، نه الزاماً insecure. Strong PUF هم ممکن است در برابر **Machine Learning** کاملاً آسیب‌پذیر باشد.
* **گزینه B درست است:** این دقیقاً تفاوت اصلی در این فصل است: **Weak PUF → key/ID** و **Strong PUF → authentication**.
* **گزینه C غلط است:** این تقسیم‌بندی analog/software کاملاً نامربوط است.
* **گزینه D غلط است:** ساختارهای Weak PUFها مثل SRAM PUF در hardware security بسیار کاربردی‌اند.

---

### Question 3

**In an Arbiter PUF, what determines the response bit for a given challenge?**

A. The value stored in a flash memory cell selected by the challenge.
B. The result of a race between two signal paths, where the arbiter outputs a bit depending on which signal arrives first.
C. The number of ones in a cryptographic hash of the challenge.
D. The temperature sensor value sampled at the moment of authentication.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **گزینه A غلط است:** این توصیف بیشتر شبیه memory-based idea است، نه Arbiter PUF.
* **گزینه B درست است:** در مدل **Arbiter PUF**، پارامتر challenge مسیر multiplexerها را تعیین می‌کند و arbiter مشخص می‌کند کدام signal زودتر رسیده است.
* **گزینه C غلط است:** مفهوم hash ممکن است برای محافظت response استفاده شود، اما مکانیزم اصلی Arbiter PUF نیست.
* **گزینه D غلط است:** متغیر temperature می‌تواند روی reliability اثر بگذارد، اما response اصلی Arbiter PUF با path delay race ساخته می‌شود.