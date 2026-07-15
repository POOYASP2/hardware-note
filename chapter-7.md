## Deep Explanation — Hardware Trust Introduction

### 1. ایدهٔ اصلی فصل: چرا Trust را باید از Hardware شروع کنیم؟

در امنیت نرم‌افزار معمولاً می‌گوییم باید **OS، Application، Key Management، Access Control** و **Logging** امن باشند. اما این فصل یک سؤال بنیادی‌تر می‌پرسد: از کجا می‌فهمیم چیزی که این امنیت را اجرا می‌کند خودش قابل اعتماد است؟ اگر سیستم‌عامل compromised شود، می‌تواند logها را پاک کند، keyها را بدزدد یا نتیجهٔ checkهای امنیتی را جعل کند. بنابراین باید یک نقطهٔ پایه‌ای داشته باشیم که به آن اعتماد کنیم. این نقطه در سخت‌افزار همان **Root of Trust / RoT** است.

**Hardware-Based Security**


 یعنی استفاده از خود سخت‌افزار برای محافظت از سیستم در برابر حملاتی که از ضعف software یا component

های دیگر سوءاستفاده می‌کنند. مثلاً به‌جای اینکه secret key را در RAM یا فایل سیستم نگه داریم، آن را داخل یک ماژول سخت‌افزاری مثل **TPM / Trusted Platform Module** قرار می‌دهیم. چون حمله به hardware معمولاً سخت‌تر از تغییر یک فایل یا process

 نرم‌افزاری است، سخت‌افزار می‌تواند پایهٔ قابل اعتمادتری برای امنیت باشد.

تشبیه روزمره: فرض کن خانه‌ات قفل خوب، پنجرهٔ مقاوم و دوربین دارد. اگر دزد وارد شود و فیلم دوربین را دستکاری کند، دیگر داشتن دوربین کمکی نمی‌کند. پس باید به خود دوربین، حافظهٔ ذخیره‌سازی‌اش، timestamp آن و مسیر گزارش‌دهی آن اعتماد داشته باشی. در سیستم کامپیوتری هم همین است. اگر logها، measurementها یا reportها قابل جعل باشند، امنیت ظاهری است. **Root of Trust**

 باید تضمین کند که گزارش‌ها و اندازه‌گیری‌ها معتبر، سالم و قابل اتکا هستند. 

پس فصل روی این ایده می‌چرخد: امنیت را نمی‌شود روی چیزی بنا کرد که خودش قابل تغییر، جعل یا subversion باشد. باید یک پایهٔ کوچک، سخت‌افزاری، مقاوم و قابل verification داشته باشیم. از آن پایه، یک **Chain of Trust** ساخته می‌شود؛ یعنی هر لایهٔ بعدی به لایهٔ قبلی اعتماد می‌کند، چون لایهٔ قبلی توسط Root of Trust اندازه‌گیری، تأیید یا محافظت شده است.

---

### 2. Trust، Root of Trust و Chain of Trust

در این فصل، **Trust** یعنی یک component، operation یا process رفتاری قابل پیش‌بینی دارد و در برابر subversion توسط software

 مخرب، virus یا سطح مشخصی از physical interference مقاوم است. یعنی trusted بودن به معنی «هیچ‌وقت خراب نمی‌شود» نیست؛ یعنی در threat model مشخص، رفتارش قابل اتکا است. این نکته مهم است چون trust همیشه نسبی به **Threat Model** است.

**Root of Trust**


 مجموعه‌ای از hardware، firmware یا software componentهای بسیار قابل اعتماد است که security functionهای critical را انجام می‌دهند. این componentها ذاتاً trusted فرض می‌شوند، پس باید **secure by design** باشند. چون اگر Root of Trust خراب باشد، تمام chain بالای آن هم بی‌ارزش می‌شود. مثلاً اگر secure bootloader اولیه قابل تغییر باشد، دیگر نمی‌توان به صحت OS bootloader یا kernel اعتماد کرد.

**Chain of Trust**


 یعنی زنجیره‌ای که از Root of Trust شروع می‌شود و هر مرحله مرحلهٔ بعدی را validate می‌کند. مثلاً یک ROM immutable ابتدا bootloader اول را چک می‌کند. bootloader اول bootloader دوم را چک می‌کند. bootloader دوم kernel را چک می‌کند. kernel driverها و serviceها را چک می‌کند. اگر هر حلقه درست verify شود، سیستم می‌تواند با یک وضعیت قابل اعتماد بالا بیاید.

در بحث confidentiality هم فقط داشتن encryption کافی نیست. باید key management، key storage، monitoring و تغییرات security configuration هم کنترل شوند. اگر attacker بتواند key را عوض کند، key را extract کند یا encryption را خاموش کند، الگوریتم قوی هم فایده ندارد. پس confidentiality واقعی به **strong encryption + secure key management + monitoring**


 نیاز دارد.

---

### 3. Trust Anchor و Hardware Root of Trust

**Trust Anchor**


 معمولاً یک **Public Key** یا **Symmetric Key** است که مستقیماً در hardware/software قرار داده شده یا به‌صورت out-of-band امن provision شده است. فرقش با certificate عادی این است که اعتمادش از entity دیگری نیامده؛ خودش نقطهٔ شروع اعتماد است. در hardware، Trust Anchor می‌تواند یک component باشد که یک **unique secure identifier** یا secret key را امن ذخیره می‌کند.

**Hardware Root of Trust / HRoT**



 یعنی Root of Trust در سخت‌افزار پیاده‌سازی شود. فایل سه نگاه می‌دهد: از نگاه NIST، HRoT ترکیبی ذاتاً trusted از hardware و firmware است که integrity اطلاعات را حفظ می‌کند؛ از نگاه عملی، building block پایه‌ای برای security schemes و protocols است؛ از نگاه رسمی، component سخت‌افزاری immutable یا مجموعه‌ای از componentهاست که در threat model مشخص بدون شرط trusted فرض می‌شوند. 

اینجا «immutable» مهم است. اگر Root of Trust به‌راحتی قابل rewrite باشد، attacker می‌تواند آن را تغییر دهد و بعد کل chain را جعل کند. برای همین Root of Trust معمولاً در ROM، OTP memory، secure element، TPM یا بخشی از SoC پیاده می‌شود. البته immutable بودن هم مشکل دارد: اگر bug داشته باشد، patch کردنش سخت است. پس طراحی اولیه باید خیلی دقیق باشد.

در فایل به سه راه برای ایجاد hardware trust اشاره می‌شود: **Proof of Authenticity/Provenance** مثل **PUF / Physically Unclonable Function**، **Immutable Hardware Components** و **Anti-Tamper Features**، و anchor کردن trust نسبت به یک **Threat Model** مشخص. یعنی باید بدانیم قرار است در برابر چه کسی مقاوم باشیم: attacker نرم‌افزاری؟ attacker با دسترسی فیزیکی؟ حملهٔ invasive؟ supply-chain attacker؟

---

### 4. RTM، RTS و RTR در Trusted Computing

در **Trusted Computing**، Root of Trust به چند نقش تقسیم می‌شود. اول **RTM / Root of Trust for Measurement** است. وظیفهٔ RTM شروع measurement process است؛ یعنی ثبت اینکه چه software یا firmwareی در حال اجراست. مثلاً هنگام boot، RTM می‌تواند hash هر مرحله را محاسبه کند. این hashها بعداً برای secure boot یا remote attestation استفاده می‌شوند.

دوم **RTS / Root of Trust for Storage** است. RTS یک مکان shielded برای ذخیرهٔ اطلاعات حساس است؛ مثلاً keyها، measurementها یا registerهای خاص با confidentiality و integrity. در TPM، این قسمت یعنی جایی که secretها و measurementها طوری نگه داشته می‌شوند که software عادی نتواند آن‌ها را تغییر دهد یا بخواند.

سوم **RTR / Root of Trust for Reporting** است. RTR از cryptography استفاده می‌کند تا به third party اطمینان دهد وضعیت سیستم چیست. مثلاً اگر یک سرور بیرونی بپرسد «آیا این دستگاه با firmware معتبر boot شده؟»، RTR می‌تواند یک signed report تولید کند. این همان پایهٔ **Remote Attestation** است.

این سه نقش را با یک مثال حفظ کن: **RTM اندازه می‌گیرد، RTS ذخیره می‌کند، RTR گزارش می‌دهد.** اگر یکی از این‌ها خراب باشد، trust chain ناقص می‌شود. اگر measurement درست باشد ولی storage قابل تغییر باشد، attacker hashها را عوض می‌کند. اگر storage امن باشد ولی reporting قابل جعل باشد، طرف خارجی گزارش دروغ می‌گیرد.


---

۴. نقش‌های RTM، RTS و RTR در Trusted Computing
در حوزهٔ Trusted Computing، ریشه اعتماد (Root of Trust) به چند نقش اساسی تقسیم می‌شود:

اندازه‌گیری (RTM / Root of Trust for Measurement):
وظیفهٔ RTM شروع فرآیند اندازه‌گیری (Measurement Process) است؛ یعنی ثبت اینکه چه نرم‌افزار (Software) یا فرم‌وری (Firmware) در حال اجراست. مثلاً هنگام Boot، سیستم RTM می‌تواند Hash هر مرحله را محاسبه کند. این هش‌ها بعداً برای Secure Boot یا Remote Attestation استفاده می‌شوند.

ذخیره‌سازی (RTS / Root of Trust for Storage):
بخش RTS یک مکان محافظت‌شده (Shielded) برای ذخیرهٔ اطلاعات حساس است؛ اطلاعاتی مانند Keyها، Measurementها یا Registerهای خاص، همراه با حفظ محرمانگی (Confidentiality) و یکپارچگی (Integrity). در یک چیپ TPM، این قسمت یعنی جایی که Secretها و مقادیر اندازه‌گیری‌شده طوری نگه داشته می‌شوند که نرم‌افزار عادی نتواند آن‌ها را تغییر دهد یا بخواند.

گزارش‌دهی (RTR / Root of Trust for Reporting):
نقش RTR استفاده از رمزنگاری (Cryptography) است تا به یک شخص ثالث (Third Party) اطمینان دهد وضعیت سیستم چیست. مثلاً اگر یک سرور بیرونی بپرسد «آیا این دستگاه با فرم‌ور معتبر بوت شده؟»، RTR می‌تواند یک گزارش امضاشده (Signed Report) تولید کند. این کار، پایه و اساس Remote Attestation است.

یک فرمول ساده برای حفظ کردن این سه نقش:
RTM اندازه می‌گیرد، RTS ذخیره می‌کند، RTR گزارش می‌دهد.

اهمیت یکپارچگی در زنجیره اعتماد (Trust Chain):
اگر یکی از این سه بخش خراب باشد، کل زنجیره اعتماد ناقص می‌شود. به عنوان مثال:

اگر سیستمِ اندازه‌گیری درست باشد ولی Storage قابل تغییر باشد، مهاجم (Attacker) به‌راحتی هش‌ها را عوض می‌کند.

اگر Storage امن باشد ولی Reporting قابل جعل باشد، طرف خارجی یک گزارش دروغ دریافت می‌کند.


---

### 5. TPM چیست و چرا مهم است؟

**TPM / Trusted Platform Module**


 یک microcontroller اختصاصی است که برای secure hardware operations طراحی شده است. وظایف اصلی TPM شامل **Generating, storing, and managing cryptographic keys**
 
 
 ، اجرای cryptographic operations در محیط امن، پشتیبانی از **Secure Boot**، و ارائهٔ **Platform Integrity Measurements** است. TPM می‌تواند discrete chip روی motherboard باشد، یا integrated داخل CPU، یا firmware/software/virtual implementation داشته باشد. 

TPM


 را مثل یک گاوصندوق کوچک داخل سیستم تصور کن. سیستم‌عامل ممکن است compromised شود، اما keyهای حساس نباید مستقیماً در اختیار آن باشند. TPM می‌تواند keyها را داخل خود نگه دارد و فقط operationهایی مثل signing، decrypting یا attestation را انجام دهد. یعنی key بیرون نمی‌آید؛ request داخل TPM پردازش می‌شود و خروجی کنترل‌شده برمی‌گردد.

در فایل گفته می‌شود TPM در قلب **TCB / Trusted Computing Base** قرار دارد و به‌عنوان hardware-based trust anchor عمل می‌کند. یک key یکتا که داخل هر TPM burn شده، می‌تواند برای device authentication استفاده شود. TPM همچنین chain of trust


 را هنگام boot از سطح hardware بالا می‌سازد. اگر boot componentها اندازه‌گیری شوند و measurementها در TPM ذخیره شوند، بعداً می‌توان platform integrity را report کرد. 

TPM


 معمولاً شامل چند بخش است: **Storage**، **Random Number Generation**، و **Cryptographic Function/Processing**. همچنین memoryهای volatile و non-volatile دارد. **Persistent Memory** اطلاعاتی مثل unique ID را نگه می‌دارد که باید ثابت بمانند. Cryptographic operations داخل TPM انجام می‌شوند تا secretها در bus یا RAM معمولی expose نشوند.

---

### 6. Security Kernel و TCB

**Security Kernel**


 مجموعه‌ای از hardware، firmware و software elements در TCB است که مفهوم **Reference Monitor** را پیاده می‌کند. Reference Monitor یعنی چیزی که همهٔ accessها را کنترل می‌کند، خودش از modification محافظت شده، و correctness آن قابل verification است. اگر چیزی قرار است همهٔ دسترسی‌ها را mediate کند، خودش نباید دور زده شود.

**TCB / Trusted Computing Base**


 یعنی مجموعهٔ componentهایی که برای اجرای policyهای امنیتی باید به آن‌ها اعتماد کنیم. هر سیستم امن بالاخره یک TCB دارد. هیچ راهی نیست که بدون اعتماد به هیچ چیز security بسازی. ولی اصل طراحی این است: **TCB باید کوچک، isolated و قابل verification باشد.** هرچه TCB بزرگ‌تر شود، احتمال bug و attack surface بیشتر می‌شود.

نکتهٔ ظریف امتحانی: TPM خودش کل TCB نیست. TPM یک anchor مهم در TCB است. TCB می‌تواند شامل hardware، firmware، boot ROM، microcode، secure monitor، TPM و بخش‌هایی از software باشد. اما TPM به TCB authentication، integrity و attestation می‌دهد.

اگر vulnerability در TCB وجود داشته باشد، امنیت کل سیستم تهدید می‌شود. مثلاً اگر bootloader trusted یک bug داشته باشد که attacker بتواند از آن code execution بگیرد، حتی اگر TPM سالم باشد، chain of trust آسیب می‌بیند.

---

### 7. TPM Basic Features

فایل پنج feature اصلی برای TPM می‌دهد:

 **Secure Boot & Firmware Integrity، Certification، Attestation and Authentication، Protected Location، Integrity Measurements and Reporting**.

در **Secure Boot**، هدف ایجاد یک initial state امن است. boot process از یک component اولیهٔ protected شروع می‌شود. bootloader

 اول integrity و authenticity bootloader دوم را verify می‌کند. bootloader دوم OS kernel و firmware را verify می‌کند. معمولاً bootloader اول immutable یا کمتر قابل تغییر است، اما bootloader دوم می‌تواند update شود. این chain باعث می‌شود code غیرمجاز قبل از OS اجرا نشود.

در **Certification**، TPM می‌تواند certificate و credentials مربوط به keyها را نگه دارد. یک certified key می‌تواند platform data را sign کند و نشان دهد این داده‌ها واقعاً توسط TPM معتبر تولید شده‌اند. در 


**Attestation and Authentication**،


 TPM
 
 
  از unique identifiers یا keyها استفاده می‌کند تا platform خودش را به entity بیرونی اثبات کند. گاهی unique identifier می‌تواند با **PUF** هم تولید شود.

در **Protected Location**، اطلاعات داخل TPM در **Shielded Location** نگه داشته می‌شود. یعنی محتوا فقط وقتی افشا می‌شود که policy اجازه دهد. اگر sensitive data بیرون TPM ذخیره شود، باید encrypted باشد و keyها نباید TPM را ترک کنند. در **Integrity Measurements and Reporting**،


 سیستم hash یا measurement مربوط به code/data را ثبت می‌کند. Digest این measurementها باید statistically unique باشد، یعنی تغییر کوچک در code باعث تغییر hash شود.

---

### 8. Trusted Execution Aware Design و TEE

در **Trusted Execution Aware Design**، مسئله این است که در یک chip ممکن است trusted و untrusted applications روی trusted، untrusted یا unknown cores


 اجرا شوند. طراحی باید طوری باشد که protected data از مسیرهای untrusted عبور نکند. فایل از مفهوم **trusted, non-trusted, unknown island partitioning**
 
 
  صحبت می‌کند. یعنی بخش‌های مختلف سیستم از نظر trust domain جدا می‌شوند.

**TEE / Trusted Execution Environment**


 یک secure area داخل main processor است که confidentiality و integrity code و data داخل خودش را حفظ می‌کند. TEE معمولاً با **Hardware-based Memory Encryption**، **Enclaves** یا memory regions isolated، و گاهی secure OS روی hardware isolation پیاده‌سازی می‌شود. مثال معروف: **ARM TrustZone**. 

TEE


 برای اجرای امن application یا بخش‌هایی از code مناسب است. مثلاً mobile payment، DRM، biometric processing یا key management
 
 
  می‌توانند داخل TEE اجرا شوند. در TEE، trusted applicationها از بقیهٔ device جدا می‌شوند. فقط trusted applications می‌توانند به منابع خاص مثل secure memory، peripherals یا crypto operations دسترسی داشته باشند.

اما TEE معمولاً **Trust Anchor** اصلی نیست. TEE برای trustworthiness خودش به TPM یا hardware trust anchor دیگر نیاز دارد. یعنی TPM می‌تواند platform integrity و secure boot را anchor کند، و TEE runtime isolation را فراهم کند. در بعضی implementationها، TEE خودش برای serviceهای بالاتر trust anchor می‌شود، اما معمولاً پایهٔ اصلی اعتماد یک hardware root مثل TPM است.


---

۸. طراحی آگاه از اجرای امن (Trusted Execution Aware Design) و TEE
در مبحث طراحی آگاه از اجرای امن (Trusted Execution Aware Design)، مسئله اصلی این است که در یک تراشه (Chip)، ممکن است برنامه‌های مورد اعتماد (Trusted) و غیرقابل اعتماد (Untrusted) روی هسته‌های پردازشیِ امن، غیرامن یا ناشناخته (Unknown Cores) اجرا شوند.

قانون طراحی: معماری سیستم باید به گونه‌ای باشد که داده‌های محافظت‌شده (Protected Data) هرگز از مسیرهای غیرامن عبور نکنند.

جزیره‌بندی (Island Partitioning): متن به مفهوم Trusted, Non-trusted, Unknown Island Partitioning اشاره می‌کند؛ یعنی بخش‌های مختلف سیستم باید از نظر دامنه اعتماد (Trust Domain) به‌طور کامل از هم جدا و به اصطلاح جزیره‌بندی شوند.

محیط اجرای امن (TEE / Trusted Execution Environment)
محیط TEE یک ناحیه امن (Secure Area) داخل پردازنده اصلی (Main Processor) است که وظیفه دارد محرمانگی (Confidentiality) و یکپارچگی (Integrity) کدها و داده‌های درون خود را تضمین کند.

نحوه پیاده‌سازی: این محیط معمولاً از طریق رمزنگاری حافظه مبتنی بر سخت‌افزار (Hardware-based Memory Encryption)، مناطق ایزوله‌شده حافظه معروف به Enclaves، و گاهی اجرای یک سیستم‌عامل امن روی سخت‌افزار ایزوله پیاده‌سازی می‌شود.

مثال معروف: تکنولوژی ARM TrustZone.

کاربردها: محیط TEE برای اجرای امنِ برنامه‌ها یا بخش‌های حساسی از کد استفاده می‌شود. مواردی مانند پرداخت موبایلی (Mobile Payment)، مدیریت حقوق دیجیتال (DRM)، پردازش اطلاعات بیومتریک (مثل اثر انگشت)، یا مدیریت کلیدها می‌توانند با امنیت کامل درون TEE اجرا شوند.

دسترسی محافظت‌شده: در این محیط، برنامه‌های مورد اعتماد (Trusted Applications) به‌طور کامل از بقیه بخش‌های دستگاه ایزوله می‌شوند. تنها همین برنامه‌ها مجازند به منابع خاصی مانند حافظه امن (Secure Memory)، لوازم جانبی، یا عملیات رمزنگاری دسترسی داشته باشند.

رابطه TEE و لنگر اعتماد (Trust Anchor)
نکته بسیار مهم این است که TEE معمولاً خودش لنگر اعتمادِ اصلی سیستم نیست.

محیط TEE برای اثبات اعتبار و امنیت خود (Trustworthiness)، همچنان به TPM یا یک لنگر اعتماد سخت‌افزاری دیگر نیازمند است.

تقسیم کار: TPM وظیفه دارد یکپارچگی پلتفرم و بوت امن (Secure Boot) را لنگرگذاری و تایید کند، در حالی که TEE وظیفه دارد ایزوله‌سازی در زمان اجرا (Runtime Isolation) را برای برنامه‌ها فراهم کند.

تبصره: هرچند در برخی پیاده‌سازی‌ها ممکن است TEE برای سرویس‌های سطح بالاتر به عنوان لنگر اعتماد عمل کند، اما پایه و اساس اصلیِ اعتماد در سیستم، همواره یک ریشه سخت‌افزاری (Hardware Root) مانند TPM است.

---

### 9. TPM vs TEE

این قسمت بسیار امتحانی است. **TPM** بیشتر روی secure storage، key management، cryptographic operations، secure boot و platform integrity تمرکز دارد. یعنی TPM بیشتر answer این سؤال است: «آیا این platform قابل اعتماد است؟ آیا keyها امن‌اند؟ آیا boot chain درست بوده؟»

در مقابل، **TEE** روی secure execution تمرکز دارد. یعنی TEE answer این سؤال است: «آیا این application یا code می‌تواند در runtime جدا از بقیهٔ سیستم اجرا شود؟ آیا data آن هنگام execution محافظت می‌شود؟» پس TPM بیشتر **Root of Trust / Measurement / Key Storage** است؛ TEE بیشتر **Protected Runtime Environment** است.

از نظر integration، TPM می‌تواند separate chip یا module جدا داخل processor باشد. TEE معمولاً داخل main processor و با استفاده از بخشی از منابع همان processor پیاده می‌شود. از نظر functionality، TPM standardized cryptographic functions می‌دهد، اما TEE flexibleتر است و می‌تواند applicationهای مختلف را اجرا کند. 

این دو رقیب نیستند؛ مکمل‌اند. TPM می‌تواند trust را در hardware anchor کند و TEE می‌تواند آن trust را به runtime execution گسترش دهد. برای تست، جملهٔ حفظی: **TPM anchors trust; TEE executes trusted code.**

---

### 10. Architectural Level Solution: MPU

در سطح معماری، فایل از **MPU / Memory Protection Unit** صحبت می‌کند. MPU در بسیاری از processorها وجود دارد و تعیین می‌کند هر memory page یا region توسط چه task/processهایی قابل **Read، Write، Execute** باشد. Access rights را kernel که privileged است مشخص می‌کند، اما خود MPU addressهای ارسال‌شده به memory را به‌صورت خودکار check می‌کند.

اگر task دسترسی غیرمجاز انجام دهد، violation باعث abortion فوری task می‌شود. این برای embedded systems مهم است، چون خیلی از microcontrollerها MMU کامل ندارند، ولی MPU دارند. MPU جلوی این را می‌گیرد که یک task اشتباه یا مخرب memory مربوط به task دیگر یا code region را تغییر دهد.

همچنین دسترسی به external peripherals معمولاً با **MMIO / Memory-Mapped I/O** انجام می‌شود. یعنی peripheralها مثل addressهای memory دیده می‌شوند. اگر access به این addressها کنترل نشود، یک task می‌تواند registerهای peripheral را تغییر دهد. MPU می‌تواند access به MMIO regions را هم محدود کند.

نکتهٔ مهم: MPU مثل TPM یا TEE نیست. MPU فقط memory access enforcement در سطح architecture است. TPM trust anchor و key storage است. TEE secure execution environment است.

---

### 11. Device Level Solutions: Tamper-Evident و Tamper-Resistant

**Tamper-Evident Devices**


 دستگاه‌هایی هستند که اگر کسی سعی کند فیزیکی دستکاری‌شان کند، نشانه‌ای از compromise باقی می‌گذارند. هدف این نیست که حتماً جلوی حمله را بگیرند؛ هدف این است که حمله detectable باشد. مثال: packaging که با باز شدن آسیب می‌بیند، light detector، temperature sensor یا sensorهایی برای تشخیص laser attack. در سطح software هم logging و auditing می‌تواند فعالیت‌های مشکوک را trace کند. 

**Tamper-Resistant Devices**


 یک مرحله قوی‌تر هستند. این‌ها طوری طراحی می‌شوند که سطح حملهٔ فیزیکی کم شود یا حمله سخت‌تر شود. مثلاً coating قوی، epoxy، metallization layer روی circuit برای جلوگیری از microprobing، یا طراحی‌ای که اگر attacker بخواهد decapsulation انجام دهد، chip تخریب شود.

فرق مهم: **Tamper-Evident** یعنی دستکاری آشکار می‌شود. **Tamper-Resistant** یعنی دستکاری سخت‌تر می‌شود. این دو یکی نیستند. یک پاکت نامه با چسب امنیتی tamper-evident است؛ یک گاوصندوق ضخیم tamper-resistant است.

فایل همچنین به **Counterfeiting Protections** اشاره می‌کند: پنهان کردن نام device و serial number از روی package تا attacker نتواند راحت مدل دقیق قطعه را پیدا کند و vulnerabilityهای شناخته‌شدهٔ آن را exploit کند. این دفاع کامل نیست، ولی reverse engineering و attack preparation را سخت‌تر می‌کند.

---

### 12. Security-Oriented Components: Hardware Ciphers، Smart Cards، SIM، Secure Storage

**Hardware Ciphers**


 قطعات اختصاصی برای انجام cryptographic operations هستند. نسبت به software implementation معمولاً سریع‌تر و کمتر قابل exploit هستند. می‌توانند از main processor جدا باشند و فقط subset کوچکی از entityها به آن‌ها دسترسی داشته باشند. حتی اگر main system compromised شود، اگر hardware cipher درست isolate شده باشد، integrity آن می‌تواند حفظ شود. این hardwareها می‌توانند **Symmetric Key Encryption، Hash Algorithms، Public-Key Algorithms** را پیاده کنند. 

**Smart Cards**



 دستگاه‌هایی هستند که security functionهایی مثل authentication بر اساس **something the user has** فراهم می‌کنند. داخلشان معمولاً CPU و I/O system وجود دارد. می‌توانند hardware-accelerated cryptography، public/secret keys، secure key generation و secure storage داشته باشند. **SIM Card** هم نوعی Smart Card است که برای mobile network استفاده می‌شود.

**Secure Storage Devices**


 برای محافظت از data at rest طراحی می‌شوند. این‌ها باید tamper-evident، tamper-resistant، encrypted و دارای secure authentication باشند. نکتهٔ مهم فایل: شکستن authentication mechanism معمولاً از شکستن encryption algorithm آسان‌تر است. یعنی attacker احتمالاً AES را نمی‌شکند؛ PIN pad را تحلیل می‌کند، software PIN را replay می‌کند، wireless bridge را clone می‌کند یا fingerprint input را bypass می‌کند. 

در secure storage، **Drive Controller Security** بسیار مهم است. controller باید جلوی brute-force را بگیرد: بعد از چند تلاش ناموفق block کند، keyها را delete کند، و هرگز اجازه ندهد password، PIN یا encryption key مستقیماً از controller درخواست شود. همچنین **Secure Erase** فقط overwrite ساده نیست؛ در SSDها ممکن است residual charge باقی بماند، پس برای حذف correlation با data قبلی، multiple write یا sanitization درست لازم است.

---

### 13. Built-in Security Features

فایل در انتها به **Open Security Platforms** و platformهایی مثل **SEcube** و **USB Armory** اشاره می‌کند. این‌ها platformهایی هستند که با cybersecurity in mind طراحی شده‌اند. ویژگی‌هایی مثل **Hardware Crypto Accelerators، Anti-Tamper، Secure Boot، ARM TrustZone**



 و componentهای certified
 
 
 
  می‌توانند داخلشان وجود داشته باشند. 

همچنین بسیاری از microcontrollerهای مدرن built-in security features دارند. بعضی از این featureها ابتدا برای safety آمده‌اند، ولی اگر درست استفاده شوند، امنیت embedded system را هم بالا می‌برند. مثال‌هایی مثل **CRC** برای integrity check، secure boot، memory protection، watchdog، readout protection و hardware crypto در microcontrollerهای جدید رایج‌اند.

نکتهٔ مهم این است که وجود feature به‌تنهایی security نمی‌آورد. اگر developer secure boot را فعال نکند، debug port را باز بگذارد، key را در flash عادی ذخیره کند یا MPU را پیکربندی نکند، سخت‌افزار امن هم خروجی امن نمی‌دهد. Security feature باید درست configure و در threat model مناسب استفاده شود.

پس پیام کل فصل این است: **Hardware Trust** یعنی ایجاد یک پایهٔ سخت‌افزاری قابل اتکا برای identity، key storage، measurement، reporting، secure execution و tamper resistance. بدون این پایه، software security روی زمین سست ساخته می‌شود.

---

# گام ۲: خلاصه تستی و مقایسه‌ای

## Bilingual Cheat-Sheet

## Core Concepts

| Keyword                           | نکتهٔ تستی                                                              |
| --------------------------------- | ----------------------------------------------------------------------- |
| **Hardware-Based Security**       | استفاده از hardware برای محافظت از سیستم در برابر ضعف componentهای دیگر |
| **Trust**                         | رفتار predictable و مقاوم در برابر subversion در threat model مشخص      |
| **Root of Trust / RoT**           | پایهٔ trusted برای security-critical functions                          |
| **Chain of Trust**                | زنجیره‌ای که هر component، component بعدی را verify می‌کند              |
| **Trust Anchor**                  | key یا componentی که ذاتاً trusted است، نه به‌خاطر certificate دیگر     |
| **Hardware Root of Trust / HRoT** | RoT پیاده‌شده با hardware/firmware immutable یا highly trusted          |

---

## RTM / RTS / RTR

| Root of Trust Type                      | کار اصلی                                             |
| --------------------------------------- | ---------------------------------------------------- |
| **RTM / Root of Trust for Measurement** | شروع measurement و ثبت software/firmware در حال اجرا |
| **RTS / Root of Trust for Storage**     | shielded storage برای keyها و measurementها          |
| **RTR / Root of Trust for Reporting**   | report/attestation cryptographic به third party      |

حفظی: **RTM measures, RTS stores, RTR reports.**

---

## TPM

| Feature                           | توضیح                                                              |
| --------------------------------- | ------------------------------------------------------------------ |
| **TPM / Trusted Platform Module** | microcontroller امن برای cryptographic keys و platform integrity   |
| **Discrete TPM**                  | چیپ جدا روی motherboard                                            |
| **Integrated TPM**                | داخل CPU یا trust zone                                             |
| **Firmware TPM**                  | پیاده‌سازی firmware-based                                          |
| **Virtual TPM**                   | TPM مجازی برای cloud/VM                                            |
| **Primary role**                  | key storage، crypto operations، secure boot، integrity measurement |
| **TCB Anchor**                    | TPM به‌عنوان hardware trust anchor برای TCB                        |

---

## TPM Basic Features

| Feature                                | معنی                                          |
| -------------------------------------- | --------------------------------------------- |
| **Secure Boot & Firmware Integrity**   | verify کردن boot chain                        |
| **Certification**                      | credentials/certificates برای keys و platform |
| **Attestation & Authentication**       | اثبات وضعیت platform به entity بیرونی         |
| **Protected Location**                 | storage امن برای sensitive data               |
| **Integrity Measurements & Reporting** | hash/digest گرفتن و گزارش integrity state     |

---

## TCB and Security Kernel

| Concept                          | توضیح                                                                     |
| -------------------------------- | ------------------------------------------------------------------------- |
| **TCB / Trusted Computing Base** | مجموعه hardware/software مورد اعتماد برای enforce کردن security           |
| **Security Kernel**              | بخش TCB که reference monitor را پیاده می‌کند                              |
| **Reference Monitor**            | mediate all accesses، protected from modification، verifiable correctness |
| **Small TCB**                    | بهتر، چون audit ساده‌تر و attack surface کمتر است                         |
| **TCB vulnerability**            | امنیت کل سیستم را jeopardize می‌کند                                       |

---

## TEE

| Concept                                 | توضیح                                                                                    |
| --------------------------------------- | ---------------------------------------------------------------------------------------- |
| **TEE / Trusted Execution Environment** | secure area در main processor                                                            |
| **Enclave**                             | isolated memory/code region                                                              |
| **ARM TrustZone**                       | مثال معروف TEE                                                                           |
| **TEE Security**                        | confidentiality و integrity برای code/data داخل TEE                                      |
| **TEE Features**                        | isolation، secure boot support، remote attestation، separation between secure/non-secure |
| **TEE relies on Trust Anchors**         | معمولاً خودش Root اصلی نیست؛ به TPM/HRoT وابسته است                                      |

---

## TPM vs TEE

| معیار         | **TPM**                                          | **TEE**                                   |
| ------------- | ------------------------------------------------ | ----------------------------------------- |
| Focus         | key storage، RoT، attestation، measurements      | secure runtime execution                  |
| Scope         | cryptographic keys و platform integrity          | application/code/data execution           |
| Integration   | separate chip یا distinct module                 | داخل main processor                       |
| Functionality | standardized crypto/storage                      | flexible secure application execution     |
| Use cases     | secure boot، disk encryption، platform integrity | mobile payment، DRM، biometric processing |
| جملهٔ حفظی    | **TPM anchors trust**                            | **TEE executes trusted code**             |

---

## MPU

| Concept                          | نکته                                             |
| -------------------------------- | ------------------------------------------------ |
| **MPU / Memory Protection Unit** | کنترل read/write/execute روی memory regions      |
| **Access Rights**                | توسط privileged kernel تعیین می‌شوند             |
| **Automatic Checking**           | addressها بدون دخالت مستقیم kernel بررسی می‌شوند |
| **Violation**                    | task abort می‌شود                                |
| **MMIO**                         | دسترسی به peripherals به‌صورت memory-mapped      |

---

## Device-Level Solutions

| Solution                      | هدف                                  |
| ----------------------------- | ------------------------------------ |
| **Tamper-Evident Device**     | دستکاری را آشکار می‌کند              |
| **Tamper-Resistant Device**   | دستکاری را سخت می‌کند                |
| **Anti-Tamper Sensors**       | light، temperature، laser detection  |
| **Epoxy / Coating**           | سخت‌کردن physical access             |
| **Extra Metallization Layer** | جلوگیری از microprobing              |
| **Counterfeiting Protection** | پنهان‌کردن device name/serial number |

---

## Security-Oriented Components

| Component                   | نقش                                                  |
| --------------------------- | ---------------------------------------------------- |
| **Hardware Cipher**         | cryptographic operations سریع‌تر و isolated          |
| **Smart Card**              | authentication، key storage، hardware crypto         |
| **SIM Card**                | نوعی Smart Card برای mobile network                  |
| **Secure Storage Device**   | encrypted/tamper-resistant storage                   |
| **Random Number Generator** | تولید randomness برای keys/nonces/security protocols |

---

## Secure Storage Attacks

| Mechanism                  | Pitfall                                                         |
| -------------------------- | --------------------------------------------------------------- |
| **PIN Pad**                | دکمه‌های پرکاربرد ممکن است لو بروند                             |
| **Software PIN Input**     | PIN نباید در software ذخیره شود؛ replay risk                    |
| **Wireless Bridge**        | ممکن است clone شود                                              |
| **Fingerprint**            | sensor ضعیف ممکن است bypass شود                                 |
| **Secure Erase**           | overwrite ساده در SSD کافی نیست؛ residual charge ممکن است بماند |
| **Brute Force Protection** | lockout یا key deletion لازم است                                |

---

# گام ۳: تله‌های امتحانی

## Pitfalls

### 1. Root of Trust و Trust Anchor دقیقاً یکی نیستند

**Root of Trust** component/function پایه‌ای برای ساختن security است.
**Trust Anchor** معمولاً key یا identifierی است که مستقیماً trusted فرض می‌شود. در عمل overlap دارند، ولی مترادف کامل نیستند.

### 2. Trust همیشه نسبت به Threat Model است

گزینه‌ای که بگوید hardware component «absolutely secure» است، مشکوک است. درست‌تر: trusted against a well-defined threat model.

### 3. Encryption به‌تنهایی Chain of Trust نمی‌سازد

برای confidentiality واقعی باید **encryption + secure key management + monitoring** داشته باشی. فقط strong cipher کافی نیست.

### 4. RTM، RTS، RTR را قاطی نکن

* **RTM:** measurement را شروع می‌کند.
* **RTS:** secure storage می‌دهد.
* **RTR:** cryptographic reporting/attestation می‌دهد.

### 5. TPM فقط encryption engine نیست

TPM فقط encrypt/decrypt نمی‌کند. کارهای مهم‌تر: **key management، secure boot، attestation، integrity measurements، protected storage**.

### 6. TPM و TEE را جابه‌جا نکن

* **TPM:** hardware root of trust و key/integrity anchor
* **TEE:** secure execution environment برای code/data

### 7. TEE معمولاً Trust Anchor اصلی نیست

TEE معمولاً برای trustworthiness خودش به TPM یا HRoT تکیه می‌کند. البته ممکن است برای serviceهای بالاتر نقش trust anchor بگیرد.

### 8. TCB باید کوچک باشد

گزینه‌ای که TCB بزرگ‌تر را امن‌تر بداند غلط است. TCB بزرگ‌تر یعنی audit سخت‌تر و bugs بیشتر.

### 9. Secure Boot همان Attestation نیست

**Secure Boot** local boot chain را verify می‌کند.
**Attestation** وضعیت platform را به third party گزارش می‌دهد.

### 10. Protected Location هنوز Integrity لازم دارد

حتی اگر data در protected location باشد، integrity measurement همچنان مهم است. protected بودن فقط به معنی «هرگز تغییر نمی‌کند» نیست.

### 11. Tamper-Evident و Tamper-Resistant فرق دارند

* **Tamper-Evident:** دستکاری را قابل مشاهده می‌کند.
* **Tamper-Resistant:** دستکاری را سخت می‌کند.

### 12. شکستن Authentication معمولاً آسان‌تر از شکستن Encryption است

در secure storage، attacker اغلب PIN، fingerprint، controller logic یا replay را هدف می‌گیرد، نه AES.

### 13. Secure Erase در SSD با یک overwrite ساده تمام نمی‌شود

به‌خاطر residual charge و wear-leveling، data sanitization در solid-state storage پیچیده‌تر است.

### 14. وجود hardware security feature کافی نیست

TPM، TEE، MPU یا Secure Boot اگر درست configure نشوند، امنیت واقعی ایجاد نمی‌کنند.

---

# گام ۴: خودآزمایی

## 100% English Quiz

### Question 1

**Which statement best describes the role of a Hardware Root of Trust in a secure computing system?**

A. It is an optional software library used to speed up cryptographic algorithms.
B. It is a foundational trusted hardware or hardware-firmware component used to build a chain of trust for security-critical functions.
C. It is a network firewall that filters malicious packets before they reach the operating system.
D. It is a temporary session key generated by an application during runtime.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** **Hardware Root of Trust** software library نیست؛ پایهٔ سخت‌افزاری/firmware برای trust است.
* **B درست است:** HRoT نقطهٔ پایه‌ای اعتماد است و chain of trust از آن ساخته می‌شود.
* **C غلط است:** firewall یک network security mechanism است، نه Root of Trust.
* **D غلط است:** session key موقتی است؛ Root of Trust معمولاً foundational و پایدارتر است.

---

### Question 2

**What is the main difference between a TPM and a TEE?**

A. A TPM mainly provides a hardware root of trust, secure key storage, measurements, and attestation, while a TEE provides an isolated runtime environment for trusted code and data.
B. A TPM executes full applications, while a TEE only stores random numbers.
C. A TPM is always implemented in software, while a TEE is always a separate chip soldered to the motherboard.
D. A TPM and a TEE are identical concepts with different names.

**Correct Answer: A**

**تحلیل گزینه‌ها:**

* **A درست است:** **TPM** بیشتر برای key storage، Root of Trust، measurement و attestation است؛ **TEE** محیط اجرای امن برای code/data است.
* **B غلط است:** اجرای applicationها بیشتر به TEE مربوط است، نه TPM.
* **C غلط است:** TPM می‌تواند discrete، integrated، firmware یا virtual باشد؛ TEE معمولاً داخل main processor است.
* **D غلط است:** TPM و TEE مکمل‌اند، اما یکسان نیستند.

---

### Question 3

**Which option correctly distinguishes tamper-evident devices from tamper-resistant devices?**

A. Tamper-evident devices make physical manipulation detectable, while tamper-resistant devices are engineered to make physical attacks harder to perform.
B. Tamper-evident devices encrypt memory, while tamper-resistant devices perform remote attestation.
C. Tamper-evident devices are used only in software, while tamper-resistant devices are used only in cloud systems.
D. Tamper-evident devices prevent all invasive attacks, while tamper-resistant devices only log software events.

**Correct Answer: A**

**تحلیل گزینه‌ها:**

* **A درست است:** **Tamper-Evident** یعنی دستکاری قابل تشخیص شود؛ **Tamper-Resistant** یعنی حملهٔ فیزیکی سخت‌تر شود.
* **B غلط است:** encryption و remote attestation مفاهیم جدا هستند.
* **C غلط است:** هر دو عمدتاً در device/hardware security استفاده می‌شوند.
* **D غلط است:** Tamper-evident حمله را الزاماً متوقف نمی‌کند؛ فقط evidence ایجاد می‌کند.
