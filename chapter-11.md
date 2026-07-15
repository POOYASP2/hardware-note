همانند فایل‌های قبلی، برای جلوگیری از به‌هم‌ریختگی متن در محیط‌های نمایش‌دهنده و حفظ ساختار راست‌چین (RTL) به‌صورت بی‌نقص، در ابتدای تمامی خطوط، پاراگراف‌ها و سلول‌های جداول که با کلمات انگلیسی شروع می‌شدند، یک کلمهٔ توصیفی فارسی (مانند: *مفهوم، حمله، مسیر، تکنیک، روش، گزینه* و...) اضافه کردم و اعداد تیترها را به فارسی برگرداندم.

فایل اصلاح‌شده و کاملاً خوانا با حفظ ۱۰۰ درصدی محتوای آموزشی شما تقدیم می‌شود:

---

این فایل قابل قبول است. اسم آپلودش **chapter-11.pdf** است، ولی محتوایش **Information Channels, Covert Channels and Side Channels** است و در لیست ترم تو با عنوان **10-Side Channels.pdf** آمده؛ پس تدریس شده و باید بخوانیم.

# گام ۱: آموزش مفهومی و مفصل

## بخش Deep Explanation — Information Channels, Covert Channels and Side Channels

### ۱. روند از Functional Design تا Physical Implementation؛ Side Channel از کجا می‌آید؟

وقتی یک سخت‌افزار طراحی می‌شود، اول فقط می‌گوییم این مدار باید چه کاری انجام دهد؛ این مرحله **Functional Description** است. بعد دقیق‌تر می‌شویم و می‌گوییم ورودی‌ها چه rangeی دارند، timing چقدر باید باشد، خروجی‌ها دقیقاً چه زمانی معتبرند؛ این می‌شود **Functional Specification**. سپس طراحی را با ابزارهایی مثل **Verilog، SystemC، SPICE** یا مدل‌های سطح بالا شبیه‌سازی می‌کنیم. بعد از **Verification**، مدار وارد مرحلهٔ **Physical Implementation** می‌شود؛ یعنی واقعاً روی silicon، PCB یا FPGA پیاده می‌شود. نکتهٔ اصلی فصل این است که حتی اگر functionality درست باشد، در مرحلهٔ physical implementation ممکن است اطلاعات ناخواسته از مدار نشت کند.

مثلاً یک مدار رمزنگاری از نظر functional ممکن است کاملاً درست باشد: ورودی plaintext و key را می‌گیرد و ciphertext درست تولید می‌کند. اما وقتی همین مدار روی سخت‌افزار واقعی اجرا می‌شود، مصرف برق آن، زمان اجرای آن، رفتار cache، تابش **Electromagnetic Radiation** یا حتی الگوی حرارتی آن ممکن است به key وابسته باشد. این اطلاعات در specification اصلی مدار نیامده‌اند، اما در implementation واقعی وجود دارند. همین‌ها می‌شوند **Side Channels**.

تشبیه ساده: فرض کن کسی داخل یک اتاق دربسته دارد رمز گاوصندوق را وارد می‌کند. تو صفحه‌کلید را نمی‌بینی، اما صدای فشرده‌شدن دکمه‌ها، مدت مکث بین دکمه‌ها یا لرزش میز را می‌شنوی. این اطلاعات مسیر رسمی ارتباط نیستند، اما می‌توانند دربارهٔ رمز چیزی لو بدهند. در سخت‌افزار هم مسیر رسمی همان input/output منطقی است؛ مسیر غیررسمی می‌تواند **Timing، Power، Cache، EM، Acoustic، Thermal** باشد.

پس پیام اصلی این بخش این است: امنیت فقط در سطح algorithm یا functional correctness تمام نمی‌شود. باید implementation فیزیکی را هم بررسی کرد. یک **Side-Channel Attack / SCA** معمولاً الگوریتم را مستقیماً نمی‌شکند؛ بلکه از اثرات جانبی اجرای الگوریتم برای استنتاج secret استفاده می‌کند.

---

### ۲. مفهوم Information Channel چیست؟

مفهوم **Information Channel** یعنی مسیری که information در آن حرکت، پردازش، ذخیره یا share می‌شود. فایل چهار حالت اصلی را معرفی می‌کند: **Information Processing** یعنی داده در حال دستکاری و محاسبه است؛ **Information Communication** یعنی داده در motion است؛ **Information Storage** یعنی داده در حالت at rest ذخیره شده؛ و **Information Sharing** یعنی داده بین بخش‌ها یا entities به اشتراک گذاشته می‌شود. این مفاهیم در صفحهٔ اول فایل در قالب یک نمودار نشان داده شده‌اند.

وقتی یک مدار کوچک چند input port و output port دارد، داده از ورودی وارد می‌شود، از چند gate و register عبور می‌کند و در نهایت خروجی تولید می‌شود. در این مسیر چند چیز قابل مشاهده است: **Timing** یعنی چقدر طول می‌کشد تا داده از input به output برسد؛ **Power Consumption** یعنی هر ورودی یا transition چقدر توان مصرف می‌کند؛ **Output Transition** یعنی تغییر output از 0 به 1 یا برعکس چه انرژی و delayی ایجاد می‌کند.

برای طراحی عادی، این‌ها فقط جزئیات performance و power هستند. اما برای attacker، همین‌ها information channel غیرمستقیم می‌شوند. اگر ورودی all-zero باعث switching کم شود و ورودی با تعداد زیادی 1 باعث switching زیاد شود، توان مصرفی می‌تواند دربارهٔ دادهٔ داخلی اطلاعات بدهد. اگر یک branch خاص طولانی‌تر اجرا شود، timing می‌تواند دربارهٔ مسیر اجرای program یا مقدار secret چیزی آشکار کند.

این‌جا باید فرق data flow و control flow را بفهمی. مفهوم **Data Flow** نشان می‌دهد داده کجا می‌رود و چطور تغییر می‌کند. مفهوم **Control Flow** نشان می‌دهد کدام branch، loop یا operation اجرا می‌شود. Side channel می‌تواند هر دو را لو بدهد: مثلاً timing می‌تواند control flow را نشان دهد، power می‌تواند data-dependent switching را نشان دهد.

---

### ۳. مفهوم Covert Channel چیست؟

مسیر **Covert Channel** یعنی یک مسیر غیرمجاز برای انتقال اطلاعات داخل یک سیستم؛ مسیری که با security policy سیستم سازگار نیست. طبق تعریف NIST در فایل، هر communication channel که یک process بتواند از آن برای انتقال اطلاعات برخلاف policy استفاده کند، covert channel است. نکتهٔ مهم: covert channel معمولاً عمدی‌تر از side channel است. یعنی یک process یا attacker از یک ویژگی سیستم به‌عنوان کانال مخفی برای ارسال اطلاعات استفاده می‌کند.

مثلاً دو process طبق policy نباید با هم ارتباط داشته باشند. اما process اول می‌تواند cache را پر یا خالی کند، و process دوم با timing بفهمد cache در چه وضعی است. این یک **Covert Storage Channel** یا **Covert Timing Channel** می‌شود. یا process اول می‌تواند load سیستم را بالا و پایین کند، و process دوم از روی زمان پاسخ system این را decode کند. این انتقال رسمی نیست، ولی information منتقل می‌شود.

در فایل چند نوع covert channel آمده است: انواع **Timing Covert Channel، Termination Covert Channel، Probability Covert Channel، Resource Utilization Covert Channel، Power Covert Channel**. برای attacker، معیارهای مهم covert channel عبارت‌اند از: آیا channel وجود دارد؟ **Bandwidth** آن چقدر است؟ noisy است یا noiseless؟ هرچه bandwidth بیشتر و noise کمتر باشد، exfiltration سریع‌تر و reliableتر می‌شود.

حذف کامل covert channel تقریباً غیرممکن است. چون سیستم واقعی همیشه timing، resource، power و shared state دارد. دفاع معمولاً سه کار می‌کند: channel را حذف یا کوچک می‌کند؛ bandwidth آن را با **Noise Injection** کم می‌کند؛ یا usage pattern را monitor می‌کند تا exploit تشخیص داده شود. یعنی هدف عملی اغلب mitigation است، نه elimination کامل.

---

### ۴. مفهوم Side Channel چیست و فرقش با Covert Channel چیست؟

مفهوم **Side Channel** یک conduit یا مسیر ناخواسته برای به‌دست‌آوردن اطلاعات دربارهٔ state یا operation سیستم است. مثال‌ها در فایل شامل **Power Usage، Electromagnetic Radiation، Execution Time، Timing Behavior، Optical Characteristics، Traffic Analysis، Acoustic Effects، Thermal Signature** هستند.

فرق مهم: در **Side Channel**، اطلاعات معمولاً ناخواسته leak می‌شود. attacker فقط مشاهده می‌کند و از leakage نتیجه می‌گیرد. در **Covert Channel**، معمولاً یک sender و receiver یا حداقل یک process مخرب از یک مسیر غیررسمی برای انتقال اطلاعات استفاده می‌کند. البته این دو می‌توانند overlap داشته باشند: cache می‌تواند هم side channel باشد، هم covert channel. اگر victim ناخواسته cache state را تغییر دهد و attacker observe کند، side channel است. اگر دو process عمداً cache را برای پیام‌رسانی استفاده کنند، covert channel است.

حمله **Side-Channel Attack / SCA** یعنی حمله‌ای که بر اساس تحلیل side-channel information انجام می‌شود. مثال‌ها: حملات **Power Analysis Attacks، Timing Attacks، Cache Attacks، EM Attacks**. این حملات معمولاً در taxonomy فصل قبل **Physical, Non-invasive, Passive** محسوب می‌شوند؛ چون attacker دستگاه را نمی‌شکند و فقط اندازه‌گیری می‌کند. البته بعضی حملات مثل **Fault Injection** همیشه side-channel نیستند؛ fault injection فعال است و دستگاه را به حالت غیرعادی می‌برد.

تشبیه: Side channel مثل دیدن دود از دودکش است و حدس‌زدن اینکه داخل خانه چه غذایی پخته می‌شود. Covert channel مثل این است که یک نفر داخل خانه عمداً با تعداد دودهای کوتاه و بلند پیام مخفی بفرستد. هر دو از مسیر اصلی ارتباط استفاده نمی‌کنند، اما نقش intentionality فرق دارد.

---

### ۵. حملات Microarchitectural Side Channels: Spectre و Meltdown

پردازنده‌های مدرن برای سرعت از تکنیک‌هایی مثل **Branch Prediction، Speculative Execution، Out-of-Order Execution، Return Stack Buffer / RSB** استفاده می‌کنند. همچنین منابع مشترکی مثل **Caches، TLB / Translation Lookaside Buffer، Cache Coherence، Multi-threading** دارند. این‌ها برای performance ضروری‌اند، اما از نظر امنیتی خطرناک‌اند؛ چون state این منابع shared می‌تواند توسط attacker مشاهده شود. در صفحهٔ ۵ فایل، نمودار پردازندهٔ **Out-of-Order Superscalar Processor** نشان می‌دهد که pipeline، cacheها، branch predictor، TLB و execution units چقدر پیچیده و shared هستند.

تکنیک **Speculative Execution** یعنی CPU قبل از اینکه مطمئن شود branch درست است، مسیر احتمالی را اجرا می‌کند. اگر حدس غلط بود، نتیجهٔ architectural حذف می‌شود؛ یعنی registerها و خروجی منطقی برنامه نباید اثر آن مسیر را ببینند. اما مشکل این است که اثرات **Microarchitectural** مثل cache state ممکن است باقی بماند. attacker بعداً با timing می‌فهمد کدام cache line گرم شده و از روی آن secret را حدس می‌زند.

حمله **Spectre** از speculative execution و mistraining branch predictor سوءاستفاده می‌کند و isolation بین applicationها را می‌شکند. حمله **Meltdown** از out-of-order execution سوءاستفاده می‌کند و به unprivileged application اجازه می‌دهد kernel memory را غیرمستقیم بخواند. این‌ها الگوریتم رمزنگاری را مستقیماً نمی‌شکنند؛ از رفتار microarchitecture برای leakage استفاده می‌کنند.

نکتهٔ تستی: اگر prediction غلط باشد، CPU نتیجهٔ منطقی را discard می‌کند، اما cache side effect ممکن است باقی بماند. همین تفاوت بین **Architectural State** و **Microarchitectural State** پایهٔ بسیاری از حملات مدرن است.

---

### ۶. مقایسه Brute Force vs Side-Channel

حمله **Brute Force Attack** یعنی attacker همهٔ حالت‌های ممکن را امتحان می‌کند تا جواب درست را پیدا کند. مثلاً تمام keyهای ممکن را یکی‌یکی تست کند. این روش noisy، پرهزینه و معمولاً قابل detect است. مثل این است که برای باز کردن گاوصندوق، با پتک به آن ضربه بزنی.

در مقابل، روش **Side-Channel Attack** ظریف‌تر است. attacker به‌جای امتحان مستقیم همهٔ keyها، اطلاعات جانبی جمع می‌کند: زمان اجرا، power، cache timing، EM leakage و غیره. سپس با تحلیل آماری یا منطقی فضای جست‌وجو را کوچک می‌کند یا key را مستقیماً reconstruct می‌کند. این روش کمتر noisy است و ممکن است monitoringهای ساده آن را نبینند.

در صفحهٔ ۶ فایل، این تفاوت با شکل گاوصندوق نشان داده شده: brute force تلاش مستقیم و پر سروصداست، ولی side channel مثل گوش‌دادن یا مشاهدهٔ اثرات جانبی است.

پس در تست، اگر گزینه‌ای گفت side-channel یعنی امتحان‌کردن همهٔ passwordها، غلط است. روش Side-channel یعنی استخراج indirect information از implementation.

---

### ۷. مبحث Timing Attacks

حمله **Timing Attack** تلاش می‌کند دادهٔ حساس را با اندازه‌گیری زمان محاسبه بازیابی کند. اگر اجرای یک operation به مقدار secret وابسته باشد، timing leakage داریم. منبع variability می‌تواند **Performance Optimization، Branching، Conditional Statements، Processor Instructions، RAM Hits، Cache Hits/Misses** باشد.

مثال مهم فایل **Timing Analysis on RSA** است. در RSA، عملیات **Modular Exponentiation** ممکن است بسته به bitهای secret exponent متفاوت اجرا شود. مثلاً اگر bit برابر 1 باشد، multiplication انجام می‌شود؛ اگر bit برابر 0 باشد، آن multiplication skip می‌شود. اگر multiplication زمان بیشتری بگیرد، attacker با اندازه‌گیری زمان اجرای هر مرحله می‌تواند bitهای key را حدس بزند.

دفاع کلاسیک این است که اجرای الگوریتم را **Constant-Time** کنیم. یعنی زمان اجرا به secret وابسته نباشد. مثلاً به‌جای اینکه برای bit=0 operation را skip کنیم، operation dummy یا masked انجام دهیم تا timing مشابه بماند. البته constant-time نوشتن در سطح high-level code کافی نیست؛ compiler، cache، branch predictor و microarchitecture هم می‌توانند timing leakage ایجاد کنند.

نکتهٔ امتحانی: حمله timing attack لزوماً به access مستقیم به data نیاز ندارد. attacker فقط زمان را اندازه می‌گیرد و با statistical analysis secret را استنتاج می‌کند.

---

### ۸. حملات Power Attacks: SPA و DPA

در **Power Attack**، attacker مصرف توان دستگاه را observe می‌کند. ایده ساده است: operationهای مختلف و dataهای مختلف باعث switching متفاوت در circuit می‌شوند، و switching متفاوت باعث power consumption متفاوت می‌شود. مفهوم **Static Power** baseline مصرفی مدار است، اما برای حمله معمولاً **Dynamic Power** مهم‌تر است، چون به workload و data وابسته است.

دو نوع اصلی در فایل آمده است: **SPA / Simple Power Analysis** و **DPA / Differential Power Analysis**. در **SPA** attacker یک یا چند trace توان را مستقیم نگاه می‌کند و از patternهای واضح نتیجه می‌گیرد. مثلاً در RSA، عملیات square و multiply ممکن است waveform متفاوت داشته باشند. اگر multiply فقط وقتی secret bit = 1 اجرا شود، شکل power trace می‌تواند bitهای key را نشان دهد. صفحهٔ ۸ فایل همین ایده را با نمودار عملیات **Square/Multiply** برای RSA نشان می‌دهد.

اما روش SPA محدودیت دارد. در سیستم واقعی noise، interrupts، multi-core execution، peripherals و processهای concurrent trace را خراب می‌کنند. اگر چند process روی سیستم اجرا شود یا cache shared باشد، power کل chip مخلوطی از فعالیت‌های مختلف است. پس pattern مستقیم همیشه قابل دیدن نیست.

روش **DPA** پیشرفته‌تر است. در DPA attacker تعداد زیادی power trace جمع می‌کند و با تحلیل آماری correlation بین power consumption و data/key hypothesis را پیدا می‌کند. ایده این است که noise با averaging کاهش می‌یابد، ولی leakage وابسته به secret باقی می‌ماند. اگر دو مجموعه trace واقعاً unrelated باشند، difference نزدیک صفر می‌شود؛ اگر correlation وجود داشته باشد، difference غیرصفر دیده می‌شود. صفحهٔ ۹ فایل مثال DPA را نشان می‌دهد که دو trace اولیه شبیه‌اند، اما difference trace بعد از magnification correlation را آشکار می‌کند.

دفاع رایج علیه DPA شامل **Random Process Interrupts** و **Dummy Instructions** است. این کار execution را desynchronize می‌کند تا cycleهای متناظر در traceها روی هم نیفتند. نتیجه این است که peaks در differential trace پخش می‌شوند؛ این اثر در فایل **Incoherent Averaging** نامیده شده است.

---

### ۹. مبحث Cache Side-Channel Attacks

مفهوم **Cache Side Channel** یعنی leakage ناخواسته از رفتار cache. Cacheها بین processها یا tenants مشترک‌اند و hit/miss latency متفاوت دارند. اگر داده در cache باشد، access سریع‌تر است؛ اگر نباشد، access کندتر است. همین اختلاف زمان می‌تواند نشان دهد victim به چه address یا memory regionی دسترسی داشته است.

در فایل threat model این‌طور بیان شده: تابعی مثل `F: K × M → D` داریم، که `K` مجموعهٔ keyها، `M` پیام‌ها و `D` ciphertextهاست. بخش attacker به `k` و `F(k,m)` دسترسی ندارد، اما می‌تواند characteristicهای physical implementation را observe کند. یعنی attacker به خروجی رسمی secret دسترسی ندارد، ولی cache timing یا power behavior را می‌بیند.

حملات cache از ویژگی‌هایی مثل **Shared Cache، Hit/Miss Latency، Cache Way and Set Organization، Coherence Invalidation** استفاده می‌کنند. فایل دو روش اصلی را نام می‌برد: تکنیک‌های **Flush+Reload** و **Prime+Probe**. در **Flush+Reload**، attacker یک memory line مشترک را flush می‌کند، بعد زمان reload را اندازه می‌گیرد تا بفهمد victim آن را access کرده یا نه. این روش precision بالا و noise کم دارد. در **Prime+Probe**، attacker cache setها را با دادهٔ خودش prime می‌کند، سپس بررسی می‌کند victim چه setهایی را evict کرده است؛ این روش در cloud environments کاربردی است.

مسیرهای Cache side-channelها در حملات بزرگی مثل **Spectre، Meltdown، Foreshadow** نقش covert channel را بازی می‌کنند. یعنی leakage اصلی ممکن است از speculative execution بیاید، ولی انتقال قابل مشاهده از طریق cache timing انجام می‌شود.

---

### ۱۰. حملات Cloud/FPGA Side Channels و Thermal Covert Channel

در cloudهای مدرن، گاهی **FPGA resources** بین userهای مختلف share می‌شوند. این اشتراک منابع می‌تواند covert channel بسازد. فایل مثال **Thermal Covert Channel in FPGAs** را می‌دهد: sender و receiver روی FPGAهای مشترک یا نزدیک اجرا می‌شوند. بخش sender با تغییر workload دما را بالا و پایین می‌برد؛ receiver دما را می‌سنجد. الگوی **High Temperature = 1** و **Low Temperature = 0**. این همان **OOK / On-Off Keying** در سطح thermal است.

این مثال مهم است چون نشان می‌دهد side/covert channel فقط power و timing نیست. حتی **Thermal Signature** هم می‌تواند information carrier شود. در سیستم‌های cloud، مشکل شدیدتر است چون multi-tenancy داریم: چند کاربر روی سخت‌افزار مشترک یا نزدیک اجرا می‌شوند و قرار است isolation داشته باشند.

تلهٔ امتحانی: سخت‌افزار FPGA در cloud فقط یک accelerator بی‌خطر نیست. چون منابع physical مشترک، interconnect، power grid و thermal coupling می‌توانند leakage ایجاد کنند.

---

### ۱۱. مکانیزم‌های Defenses: Leakage Reduction، Noise، Isolation، Obfuscation

فایل چند دفاع کلی می‌دهد: انواع **Leakage Reduction، Noise Injection، Key Update، Side-channel-resistant PUFs، Secure Scan Chains**. دو metric مهم هم مطرح می‌شود: چقدر secret information vulnerable است، و چند sample لازم است تا attacker secret را استخراج کند. اگر defense باعث شود attacker به sample بسیار زیاد نیاز داشته باشد، حمله در عمل سخت‌تر می‌شود.

تکنیک **Process Isolation** دفاع مهمی است، اما ساده نیست. نیاز به پشتیبانی **Architecture، OS، Compiler/Runtime، Software** دارد. مثلاً **SGX** می‌تواند enclave بسازد، اما هزینه دارد: utilization سخت‌افزار کاهش می‌یابد و trade-off بین secure but slow enclave و powerful but insecure cores ایجاد می‌شود. فایل صریح می‌گوید فعلاً راه ساده‌ای برای secure computation روی multi-core systems وجود ندارد.

روش **Hardware Obfuscation** یعنی تغییر description یا structure سخت‌افزار برای پنهان‌کردن functionality یا سخت‌کردن reverse engineering و side-channel analysis. برای cache obfuscation می‌توان چیزهایی مثل **Address Generation، Cache Lookup، Hit/Miss Detection، Data Retrieval، Cache Line Replacement، Write Operation، Power Consumption** را تغییر داد.

در صفحهٔ ۱۲، مثال **Janus Cache Behavior** آمده است. هر cache line یک **ON_OFF Flag / OOF** دارد. اگر OOF=0 باشد، block عادی کار می‌کند. اگر OOF=1 باشد، block قابل access نیست. همچنین instructionهای جدیدی برای روشن/خاموش‌کردن blockها وجود دارد. هدف این است که بر اساس access pattern، noise کنترل‌شده به power consumption اضافه شود. اما این کار performance را خراب می‌کند؛ جدول صفحهٔ ۱۲ نشان می‌دهد مثلاً الگوریتم **Bubble Sort** با obfuscation average execution time بیشتری می‌گیرد و max execution time هم بالا می‌رود.

پس دفاع‌های side-channel معمولاً free نیستند. یا performance را کم می‌کنند، یا area/power را زیاد می‌کنند، یا complexity طراحی را بالا می‌برند.

---

# گام ۲: خلاصه تستی و مقایسه‌ای

## بخش Bilingual Cheat-Sheet

### روند Functional Design to Physical Implementation

| مفهوم | معنی |
| --- | --- |
| مرحله **Functional Description** | مدار قرار است چه کاری انجام دهد |
| مرحله **Functional Specification** | مواردی مثل functionality + input ranges + timing requirements |
| مرحله **Functional & Implementation Modeling** | مدل‌سازی با **Verilog، SystemC، SPICE** |
| مرحله **Verification** | تست و simulation برای correctness |
| مرحله **Physical Implementation** | پیاده‌سازی واقعی؛ جایی که side channels ممکن است ظاهر شوند |

---

### بخش Information Channels

| Concept | توضیح |
| --- | --- |
| مسیر **Information Processing** | حالت data in manipulation |
| مسیر **Information Communication** | حالت data in motion |
| مسیر **Information Storage** | حالت data at rest |
| مسیر **Information Sharing** | حالت data sharing |
| مفهوم **Data Flow** | مسیر حرکت و تغییر داده |
| مفهوم **Control Flow** | مسیر اجرای branchها و operations |

---

### بخش Covert Channel vs Side Channel

| Feature | **Covert Channel** | **Side Channel** |
| --- | --- | --- |
| تعریف | مسیر illegal برای انتقال information برخلاف policy | مسیر ناخواسته برای کسب information از state/operation |
| معیار intentionality | معمولاً intentional communication | معمولاً unintended leakage |
| مثال | وضعیت cache used by two processes to communicate | وضعیت victim cache access leaks secret |
| هدف | انجام exfiltration یا bypass policy | عملیات inference of secret/state |
| حذف کامل | تقریباً غیرممکن | تقریباً غیرممکن |

---

### بخش Covert Channel Types

| Type | ایده |
| --- | --- |
| نوع **Timing Covert Channel** | انتقال اطلاعات از طریق زمان |
| نوع **Termination Covert Channel** | استفاده از پایان computation |
| نوع **Probability Covert Channel** | استفاده از event distribution |
| نوع **Resource Utilization Covert Channel** | استفاده از سطح resource |
| نوع **Power Covert Channel** | انتقال از طریق مصرف توان |
| نوع **Covert Storage Channel** | استفاده از storage موقت مثل cache |

---

### بخش Covert Channel Characteristics

| Characteristic | سؤال تستی |
| --- | --- |
| ویژگی **Existence** | آیا channel وجود دارد؟ |
| ویژگی **Bandwidth** | چقدر data قابل انتقال است؟ |
| ویژگی **Noiseless/Noisy** | آیا data بدون distortion منتقل می‌شود؟ |
| اقدام **Mitigation** | شامل eliminate/minimize، noise injection، monitoring |

---

### بخش Side Channel Sources

| Side Channel | اطلاعات نشت‌کرده |
| --- | --- |
| منبع **Power Usage** | فعالیت operation/data-dependent switching |
| منبع **EM Radiation** | فعالیت الکترومغناطیسی مدار |
| منبع **Execution Time / Timing** | بررسی branch، cache، instruction differences |
| منبع **Optical Characteristics** | ایجاد leakage نوری یا laser/optical effects |
| منبع **Traffic Analysis** | بررسی pattern ارتباطی |
| منبع **Acoustic Effects** | صدای operation یا component |
| منبع **Thermal Signature** | الگوی دما |
| منبع **Cache Behavior** | وضعیت hit/miss، access pattern |

---

### بخش Microarchitectural Features

| Feature | ریسک |
| --- | --- |
| تکنیک **Branch Prediction** | ایجاد mistraining و leakage |
| تکنیک **Speculative Execution** | باقی‌ماندن cache effects بعد از discard |
| تکنیک **Out-of-Order Execution** | اجرای زودهنگام و leakage |
| ساختار **Return Stack Buffer / RSB** | بروز prediction leakage |
| ساختار **Caches** | مشکل hit/miss timing channel |
| ساختار **TLB** | مشکل address translation leakage |
| قابلیت **Multi-threading** | بروز shared resource leakage |

---

### بخش Spectre vs Meltdown

| Attack | Exploits | Effect |
| --- | --- | --- |
| حمله **Spectre** | تکنیک **Speculative Execution + Branch Predictor Mistraining** | باعث breaks application isolation |
| حمله **Meltdown** | تکنیک **Out-of-Order Execution** | اجازه unprivileged app can read kernel memory indirectly |
| مسیر Common Channel | مشکل cache timing side channel | باعث indirect leakage |

---

### بخش Timing Attack

| Concept | نکته |
| --- | --- |
| مفهوم **Timing Attack** | اطلاعات secret از execution time استنتاج می‌شود |
| بخش Sources | مرتبط با branch، conditional، instruction، RAM/cache hit |
| مثال RSA Example | عملیات multiplication برای key bit=1 زمان بیشتری می‌برد |
| بخش Defense | اعمال **Constant-Time Implementation** |

---

### بخش Power Attacks

| Attack | روش |
| --- | --- |
| حمله **SPA / Simple Power Analysis** | مستقیم power trace را می‌خواند |
| حمله **DPA / Differential Power Analysis** | استفاده از traceهای زیاد + statistical correlation |
| مفهوم **Static Power** | توان baseline، معمولاً کمتر مهم برای attack |
| مفهوم **Dynamic Power** | توان وابسته به activity/data، مهم‌تر |
| بخش **DPA Defense** | شامل dummy instructions، random interrupts، desynchronization |

---

### بخش Cache Attacks

| Attack | روش |
| --- | --- |
| روش **Flush+Reload** | عملیات flush shared line، measure reload time |
| روش **Prime+Probe** | عملیات prime cache sets، observe eviction |
| ویژگی **Hit/Miss Latency** | وضعیت cache hit سریع‌تر از miss |
| ریسک **Cloud Risk** | موضوع shared resources بین tenants |
| بخش Used in | شامل Spectre، Meltdown، Foreshadow |

---

### بخش Cloud/FPGA Side Channel

| Concept | نکته |
| --- | --- |
| مفهوم **Thermal Covert Channel** | پارامتر temperature برای ارسال bit استفاده می‌شود |
| روش **OOK / On-Off Keying** | الگوی high temperature = 1، low temperature = 0 |
| محیط **Cloud FPGA** | مسئله sharing FPGA resources می‌تواند leakage بسازد |

---

### بخش Defenses

| Defense | اثر |
| --- | --- |
| دفاع **Leakage Reduction** | کم‌کردن وابستگی leakage به secret |
| دفاع **Noise Injection** | سخت‌کردن تحلیل |
| دفاع **Key Update** | کاهش زمان مفید key برای attacker |
| دفاع **Process Isolation** | محدودکردن sharing |
| دفاع **Side-channel-resistant PUFs** | کاهش leakage در PUF |
| دفاع **Secure Scan Chains** | جلوگیری از abuse در test infrastructure |
| دفاع **Hardware Obfuscation** | سخت‌کردن reverse engineering/analysis |
| دفاع **Cache Obfuscation** | تغییر cache behavior برای مخفی‌سازی access pattern |

---

# گام ۳: تله‌های امتحانی

## بخش Pitfalls

### ۱. روش Side Channel الگوریتم را مستقیماً نمی‌شکند

حمله Side-channel attack معمولاً از **implementation leakage** استفاده می‌کند، نه از ضعف ریاضی الگوریتم.

### ۲. مفاهیم Covert Channel و Side Channel یکی نیستند

* مفهوم **Covert Channel:** مسیر غیرمجاز برای انتقال عمدی information
* مفهوم **Side Channel:** ایجاد leakage ناخواسته از state/operation
اما یک mechanism مثل cache می‌تواند در هر دو نقش ظاهر شود.

### ۳. حذف کامل Side Channel تقریباً غیرممکن است

در سیستم واقعی همیشه timing، power، resource sharing یا thermal effect وجود دارد. هدف دفاع معمولاً mitigation است.

### ۴. حملات Spectre و Meltdown هر دو cache را به‌عنوان کانال استفاده می‌کنند، اما mechanismشان یکی نیست

* حمله **Spectre:** تکنیک speculative execution + branch predictor mistraining
* حمله **Meltdown:** تکنیک out-of-order execution + kernel memory leakage

### ۵. نتیجه Speculative result ممکن است discard شود، اما cache effect باقی بماند

این دقیقاً پایهٔ microarchitectural leakage است.

### ۶. روش‌های Brute Force و Side Channel را قاطی نکن

روش **Brute Force** یعنی امتحان همهٔ حالت‌ها.
روش **Side Channel** یعنی جمع‌آوری indirect information برای استنتاج secret.

### ۷. حمله Timing Attack فقط به branch مربوط نیست

مواردی مثل Cache hit/miss، RAM access، instruction latency و optimization هم timing leakage ایجاد می‌کنند.

### ۸. تکنیک‌های SPA و DPA فرق دارند

* تکنیک **SPA:** نگاه مستقیم به trace و patternهای واضح
* تکنیک **DPA:** جمع‌آوری traceهای زیاد + statistical correlation

### ۹. تکنیک DPA به physical implementation کامل نیاز ندارد

طبق فایل، تکنیک DPA معمولاً knowledge of algorithm می‌خواهد، نه الزاماً implementation physical دقیق.

### ۱۰. پارامتر Noise همیشه دفاع کامل نیست

تکنیک Noise injection می‌تواند sampleهای لازم را زیاد کند، اما اگر attacker trace کافی جمع کند، روش DPA هنوز ممکن است leakage را پیدا کند.

### ۱۱. تکنیک Fault Injection لزوماً Side Channel نیست

حمله Fault injection بیشتر active attack است. ممکن است با side-channel ترکیب شود، اما خود آن همیشه side-channel attack محسوب نمی‌شود.

### ۱۲. راهکار Process Isolation هزینه دارد

تکنیک Isolation مثل SGX امنیت را بهتر می‌کند، اما hardware utilization و performance trade-off دارد.

### ۱۳. دفاع Cache Obfuscation رایگان نیست

مثال **Janus Cache** نشان می‌دهد obfuscation می‌تواند execution time و variance را بالا ببرد.

---

# گام ۴: خودآزمایی

## بخش 100% English Quiz

*(در این بخش به دلیل ماهیت انگلیسی سؤالات، حفظ ساختار چپ‌به‌راست (LTR) منطقی و صحیح است)*

### Question 1

**Which statement best distinguishes a covert channel from a side channel?**

A. A covert channel is an unintended leakage path, while a side channel is always an officially specified communication path.
B. A covert channel is an unauthorized path used to transfer information in violation of a security policy, while a side channel is an unintended leakage path that reveals information about system state or operation.
C. A side channel always requires physical destruction of the device, while a covert channel never involves shared resources.
D. A covert channel only exists in analog circuits, while a side channel only exists in software.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **گزینه A غلط است:** این گزینه تعریف‌ها را جابه‌جا کرده و side channel را رسمی فرض کرده است.
* **گزینه B درست است:** مفهوم **Covert Channel** مسیر غیرمجاز برای انتقال information است؛ ویژگی **Side Channel** مسیر ناخواسته برای leakage از state/operation است.
* **گزینه C غلط است:** حملات Side-channelها معمولاً non-invasive هستند و shared resources مثل cache می‌توانند covert channel بسازند.
* **گزینه D غلط است:** هر دو می‌توانند در hardware، software و systems مشترک ظاهر شوند.

---

### Question 2

**Why can speculative execution create a microarchitectural side channel?**

A. Because speculatively executed instructions may leave observable effects in shared resources such as caches, even if their architectural results are discarded.
B. Because speculative execution permanently changes the program source code.
C. Because branch prediction encrypts all memory accesses using a secret key.
D. Because speculative execution disables all cache accesses.

**Correct Answer: A**

**تحلیل گزینه‌ها:**

* **گزینه A درست است:** حتی اگر نتیجهٔ architectural پاک شود، اثرات **Microarchitectural** مثل cache state می‌تواند باقی بماند و leak کند.
* **گزینه B غلط است:** محتوای source code تغییر نمی‌کند.
* **گزینه C غلط است:** تکنیک branch prediction کاری با encryption حافظه ندارد.
* **گزینه D غلط است:** برعکس؛ تکنیک speculative execution می‌تواند cache access ایجاد کند.

---

### Question 3

**What is the main difference between Simple Power Analysis (SPA) and Differential Power Analysis (DPA)?**

A. SPA requires many traces and statistical correlation, while DPA only visually inspects one trace.
B. SPA directly interprets visible patterns in power traces, while DPA uses multiple traces and statistical analysis to reveal data-dependent correlations.
C. SPA is an invasive attack, while DPA is a brute-force attack.
D. DPA does not require any power measurements.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **گزینه A غلط است:** این گزینه مفاهیم SPA و DPA را برعکس کرده است.
* **گزینه B درست است:** تکنیک **SPA** در واقع pattern مستقیم trace را بررسی می‌کند؛ اما **DPA** با traceهای زیاد و تحلیل آماری correlation پیدا می‌کند.
* **گزینه C غلط است:** هر دو معمولاً side-channel و non-invasive passive هستند، نه brute force.
* **گزینه D غلط است:** روش DPA دقیقاً بر power measurements تکیه دارد.