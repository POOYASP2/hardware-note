این فایل قابل قبول است. هرچند اسم آپلودش **chapter-6.pdf** است، محتوایش **Hardware-Based Security** است و در لیست ترم تو با عنوان **08-Hardware-Based Security.pdf** آمده. پس تدریس شده و باید بخوانیم. 

# گام ۱: آموزش مفهومی و مفصل

## Deep Explanation — Hardware-Based Security

### 1. چرا اصلاً Hardware-Based Security لازم شد؟

در معماری کامپیوتر سنتی، هدف اصلی پردازنده‌ها performance بود: سریع‌تر اجرا کردن برنامه‌ها، کاهش latency، افزایش throughput و استفادهٔ بهتر از منابع. برای همین تکنیک‌هایی مثل **Caching، Pipelining، Prediction، Parallelization، Indirection** و **Specialization** ایجاد شدند. این‌ها برای سرعت عالی‌اند، اما از نظر امنیتی مشکل می‌سازند. مثلاً **Cache** باعث می‌شود داده‌های پرکاربرد سریع‌تر در دسترس باشند، اما همین cache می‌تواند اطلاعاتی دربارهٔ رفتار برنامه لو بدهد. **Prediction** و **Speculative Execution**

 باعث افزایش سرعت می‌شوند، اما حملاتی مثل **Spectre** دقیقاً از همین رفتار سوءاستفاده می‌کنند. یعنی چیزی که برای performance طراحی شده، می‌تواند تبدیل به attack surface
 
  شود.

یک پردازندهٔ معمولی در حالت سنتی فرض می‌کند اجزای زیادی قابل اعتمادند: خود processor، bus، memory، I/O devices، operating system، hypervisor و device drivers. اما در امنیت مدرن این فرض غلط است. اگر attacker بتواند روی **system bus** شنود کند، می‌تواند داده‌های بین CPU و memory را ببیند. اگر memory فیزیکی قابل دسترسی باشد، attacker می‌تواند محتوای آن را extract یا modify کند. اگر یک **I/O Device** مخرب باشد، ممکن است با **DMA / Direct Memory Access** به حافظه دسترسی بگیرد بدون اینکه CPU مستقیماً درگیر باشد.

ایدهٔ **Hardware-Based Security** این است که امنیت فقط با software حل نمی‌شود. اگر OS یا Hypervisor خودش malicious یا compromised باشد، دیگر نمی‌توان به آن برای محافظت از application اعتماد کرد. بنابراین باید خود hardware امکاناتی فراهم کند که حتی در حضور software غیرقابل اعتماد، بخشی از code و data محافظت شود. اینجا مفاهیمی مثل **Secure Processor Architecture، TEE / Trusted Execution Environment، TCB / Trusted Computing Base، Enclave، Secure Boot، Remote Attestation** و **Memory Encryption**

 وارد می‌شوند.

تشبیه ساده: در مدل قدیمی، OS مثل نگهبان اصلی ساختمان بود و همه به او اعتماد می‌کردند. اما اگر خود نگهبان فاسد باشد چه؟ **Secure Processor Architecture** مثل ساختن یک گاوصندوق داخل ساختمان است که حتی نگهبان ساختمان هم نتواند محتویات آن را ببیند یا تغییر دهد. این گاوصندوق همان **TEE** یا **Enclave** است. ولی خود گاوصندوق هم باید درست ساخته شده باشد؛ یعنی **TCB** باید کوچک، قابل بررسی و امن باشد.

---

### 2. Secure Processor و Secure Processor Architecture

**Secure Processor** 

یک پردازندهٔ عمومی است که قابلیت‌های امنیتی اضافه دارد. این قابلیت‌ها معمولاً برای ایجاد isolation، محافظت از code/data، کنترل access و monitor کردن execution استفاده می‌شوند. اما نکتهٔ مهم این است: secure processor معجزه نمی‌کند. خودش هم ممکن است vulnerability، hardware bug، side-channel leakage یا حتی supply-chain issue داشته باشد.

**Secure Processor Architecture**

 یعنی معماری‌ای که processor را با hardware و software features گسترش می‌دهد تا اجرای امن software ممکن شود. بخش محافظت‌شدهٔ code/data معمولاً **Enclave** نامیده می‌شود. البته enclave همیشه فقط یک function کوچک نیست؛ گاهی می‌تواند یک **Trusted Software Module**، یک **Virtual Machine**، یا حتی یک whole OS باشد. هدف اصلی این است که protected code/data از untrusted software مثل OS و Hypervisor جدا شود.

نکتهٔ کلیدی فصل این است که دیگر حتی **Hypervisor** هم لزوماً trusted نیست. در virtualization سنتی، hypervisor بسیار privileged بود و guest OSها به آن وابسته بودند. اما اگر hypervisor compromised باشد، می‌تواند VMها را بخواند یا تغییر دهد. معماری‌هایی مثل **Intel SGX** سعی می‌کنند application enclave را حتی از OS و hypervisor جدا کنند. معماری‌هایی مثل **AMD SEV** بیشتر روی محافظت از whole VM یا guest OS تمرکز می‌کنند.

این isolation فقط نباید در سطح architectural state باشد. باید **Micro-architectural State** هم در نظر گرفته شود: cache، branch predictor، TLB، pipeline buffers، execution units و منابع shared. اگر protected code اجرا شود و بعد cache state باقی بماند، یک process بعدی ممکن است با cache timing بفهمد enclave چه کرده است. پس امنیت واقعی یعنی محافظت از **Architectural State + Micro-architectural State + Spatial/Temporal Sharing of Hardware**.

---

### 3. مشکل مدل سنتی privilege rings

مدل سنتی software protection از **Protection Rings** استفاده می‌کند. معمولاً:

* **Ring 3:** Applications،


 کمترین privilege
* **Ring 0:** OS Kernel،

 بسیار privileged


* **Ring -1:** Hypervisor / VMM
* **Ring -2:** System Management Mode / SMM
* **Ring -3:** Platform Management Engine یا management processor

در مدل خطی سنتی، هرچه به hardware نزدیک‌تر باشی، privilegedتر و trustedتر فرض می‌شوی. مشکل اینجاست که این **Linear Trust Model** خطرناک است. چون اگر یک لایهٔ پایین‌تر compromised شود، همهٔ لایه‌های بالاتر را می‌تواند کنترل کند. مثلاً یک hypervisor مخرب می‌تواند guest OSها را attack کند. یک SMM آسیب‌پذیر می‌تواند حتی از OS هم پنهان‌تر و خطرناک‌تر باشد.

برای شکستن این مدل، معماری‌های جدید **Horizontal Privilege Separation** اضافه می‌کنند. یعنی علاوه بر ring number، یک بعد دیگر به نام secure/normal world داریم. مثال واضح آن **ARM TrustZone** است: سیستم به **Normal World** و **Secure World** تقسیم می‌شود. ممکن است کدی در secure world اجرا شود که از نظر ring شبیه حالت عادی باشد، اما از نظر security domain جداست.

در فصل چند مدل مقایسه شده‌اند: **Normal Computer، Bastion، SGX، SEV**. در normal computer تقریباً همهٔ لایه‌های پایین باید trusted باشند. در **Bastion** تا hypervisor

 باید trusted باشد. در **SGX** لازم نیست OS و hypervisor را trusted بدانیم، چون enclave در hardware-protected area اجرا می‌شود. در **SEV** علاوه بر protected execution، guest OS یا VM هم محافظت می‌شود. نکتهٔ تستی: این معماری‌ها trust boundary یکسان ندارند.

---

### 4. TEE و TCB

**TEE / Trusted Execution Environment** 


محیطی است که در آن code و data با سطحی از اعتماد و محافظت اجرا می‌شوند. هدف TEE این است که حتی اگر بخش‌هایی از سیستم untrusted باشند، protected software محرمانگی و صحت خود را حفظ کند. TEE می‌تواند برای **Enclave**، **Trusted Module** یا **whole VM** ساخته شود.

اما TEE خودش از هوا ایجاد نمی‌شود. مجموعهٔ سخت‌افزار و نرم‌افزاری که TEE را پیاده‌سازی می‌کند، **TCB / Trusted Computing Base** نام دارد. **TCB** بخشی از سیستم است که باید به آن اعتماد کنیم. اگر TCB bug داشته باشد یا attack شود، TEE عملاً بی‌معنی می‌شود. جملهٔ مهم: **A vulnerability in the TCB nullifies TEE protections.**

امنیت TEE معمولاً دو ویژگی اصلی می‌دهد:

* **Confidentiality:** 

دادهٔ protected توسط بخش‌های غیرمجاز دیده نشود.
* **Integrity:**

 داده یا code بدون اجازه تغییر نکند.

اما **Availability** معمولاً تضمین نمی‌شود. یعنی اگر attacker بتواند سیستم را crash کند یا اجرای enclave را متوقف کند، بسیاری از TEEs جلوی آن را نمی‌گیرند. پس گزینه‌ای که بگوید TEE همیشه confidentiality، integrity و availability کامل می‌دهد، غلط است.

یک اصل مهم design: باید **TCB را کوچک نگه داریم**. هرچه TCB بزرگ‌تر باشد، auditing سخت‌تر می‌شود، bug بیشتر می‌شود و attack surface بزرگ‌تر می‌شود. پس secure architecture باید تلاش کند فقط حداقل componentهای لازم را داخل TCB قرار دهد.

---

### 5. محافظت از state در protected software

وقتی protected software اجرا می‌شود، state آن فقط داخل یک متغیر یا memory نیست. State در جاهای مختلف پخش می‌شود: registers، cache، pipeline، TLB، branch predictor، memory، I/O buffers و security modules. اگر فقط memory را encrypt کنیم ولی cache state را باقی بگذاریم، leakage هنوز ممکن است. بنابراین باید **state isolation** جدی گرفته شود.

یک مثال ساده: فرض کن process A یک secret را پردازش کرده و در cache اثر گذاشته است. بعد process B اجرا می‌شود. اگر cache flush یا partition نشود، process B می‌تواند با timing بفهمد کدام cache lineها گرم هستند و از روی آن دربارهٔ secret حدس بزند. این پایهٔ بسیاری از **Cache Timing Attacks** است. بنابراین secure processor باید یا state را flush کند، یا isolate کند، یا sharing را کنترل کند.

ایدهٔ ایده‌آل این است که protected execution هیچ **visible side-effect** برای untrusted components باقی نگذارد. یعنی قبل از اجرای protected software سیستم در یک state است، protected software اجرا می‌شود و state را تغییر می‌دهد، اما وقتی تمام یا interrupt شد، اثرات قابل مشاهده باید پاک شوند. در عمل این سخت است، چون پیدا کردن همهٔ microarchitectural stateها دشوار است.

نکتهٔ مهم دیگر: secure processor architecture معمولاً فرض می‌کند code داخل TEE **benign** است. یعنی اگر خود protected software bug داشته باشد، TEE الزاماً آن را نجات نمی‌دهد. مثلاً اگر enclave خودش buffer overflow داشته باشد یا secret را داوطلبانه leak کند، hardware نمی‌تواند همیشه جلوی آن را بگیرد. پس TEE از protected software در برابر بیرون محافظت می‌کند، نه الزاماً از بیرون در برابر protected software.

---

### 6. Root of Trust و Processor Key

کل امنیت سیستم باید از یک نقطهٔ پایه شروع شود: **Root of Trust**. در این فصل Root of Trust عملاً یک secret cryptographic key است که فقط TCB components به آن دسترسی دارند. از این root key، کلیدهای دیگر برای encryption، signing، verification و attestation derive می‌شوند. اگر root key لو برود، زنجیرهٔ اعتماد فرو می‌ریزد.

این key باید در جایی بسیار امن ذخیره شود، معمولاً داخل **Processor SoC**. همچنین باید برای هر قطعهٔ hardware یکتا باشد. اگر همهٔ پردازنده‌ها یک کلید مشترک داشته باشند و آن کلید leak شود، تمام سیستم‌ها compromise می‌شوند. پس **per-device unique secret** بسیار مهم است.

دو روش برای ایجاد این secret مطرح است. یکی اینکه manufacturer در کارخانه آن را بسوزاند، مثلاً با **OTP Fuses / One-Time Programmable Fuses**. مشکل این روش این است که باید به manufacturer و supply chain اعتماد کنیم. روش دیگر استفاده از **PUF / Physically Unclonable Function** است. PUF از ویژگی‌های فیزیکی تصادفی و غیرقابل تکثیر chip برای derive کردن key استفاده می‌کند. البته PUF هم hardware اضافه، reliability mechanism و certificate infrastructure می‌خواهد.

از Root of Trust معمولاً کلیدهایی مثل **Public Key برای encrypt کردن داده به processor** و **Signature Verification Key برای بررسی داده‌های signed توسط processor/TCB** ساخته یا مدیریت می‌شوند. جزئیات distribution در PUF-based design متفاوت است، چون key ممکن است به‌جای ذخیره‌شدن مستقیم، در زمان لازم از ویژگی فیزیکی chip بازسازی شود.

---

### 7. Software Measurement، Secure Boot و Remote Attestation

**Software Measurement**


 یعنی محاسبهٔ cryptographic hash روی code/data لایه‌های مختلف سیستم برای فهمیدن اینکه دقیقاً چه چیزی در حال اجراست. اگر SecE یا secure hardware در boot process اولین کنترل را داشته باشد، می‌تواند هر لایه را قبل از اجرا measure کند: firmware، hypervisor، OS bootloader و غیره. این اساس **Secure Boot** است.

در **Secure Boot**، اگر measurement درست نباشد، سیستم می‌تواند boot را abort کند. یا ممکن است boot را ادامه دهد ولی secrets را decrypt نکند. نکتهٔ مهم: legitimate software update هم measurement را تغییر می‌دهد، چون hash code جدید فرق می‌کند. پس secure boot باید mechanism رسمی برای update و signature verification داشته باشد.

**Remote Attestation**


 یعنی hardware برای یک external verifier گواهی تولید کند که چه softwareی در حال اجراست. مثلاً یک server می‌خواهد مطمئن شود client واقعاً نسخهٔ سالم software را اجرا می‌کند. hardware یک certificate یا signed report تولید می‌کند که measurements را نشان می‌دهد. معمولاً این با public-key crypto ترکیب می‌شود تا فقط طرف مجاز بتواند نتیجه را بخواند.

**Sealed Storage**


 هم بر اساس measurement کار می‌کند. در sealed storage، داده فقط وقتی unlock یا unseal می‌شود که ترکیب مشخصی از hardware/software وجود داشته باشد. یعنی secret به یک platform configuration خاص bind می‌شود. اگر OS bootloader یا firmware تغییر کند، measurement فرق می‌کند و secret آزاد نمی‌شود.

---

### 8. TOC-TOU و monitoring

**TOC-TOU / Time-of-Check to Time-of-Use**


 نوعی race condition امنیتی است. یعنی سیستم اول یک چیز را check می‌کند، بعد کمی بعد از آن استفاده می‌کند. attacker در فاصلهٔ بین check و use وضعیت را تغییر می‌دهد. مثلاً برنامه بررسی می‌کند فایل امن است، اما قبل از بازکردن، attacker فایل را با چیز دیگری جایگزین می‌کند.

در hardware-based security، صرف measurement لحظه‌ای کافی نیست. چون ممکن است چیزی هنگام boot سالم باشد، اما بعداً تغییر کند. برای همین فصل روی **continuous monitoring** و **continuous authentication** تأکید می‌کند. یعنی TCB یا TEE باید در طول execution هم monitor شود، نه فقط اول کار.

اما این هم ساده نیست. Continuous monitoring نیاز دارد رفتار صحیح سیستم را بشناسیم. مثلاً از **performance counters**، fingerprinting و anomaly detection استفاده کنیم. attacker هم ممکن است تغییراتش را آن‌قدر کوچک انجام دهد که counters زیاد تغییر نکنند. پس monitoring مفید است، اما کامل و قطعی نیست.

---

### 9. Memory Protection: confidentiality، integrity، freshness و access pattern

Memory

 یکی از مهم‌ترین اهداف attacker است. حملات memory می‌توانند از software غیرقابل اعتماد، bus snooping، malicious DMA device، cold boot attack، یا memory manipulation بیایند. فصل چند نوع attack روی memory معرفی می‌کند:

* **Snooping:**
*  passive read از data.
* **Spoofing:** 
* inject کردن memory command جدید.
* **Splicing:**
*  ترکیب بخش‌هایی از commandهای معتبر برای ساخت command مخرب.
* **Replay:**
*  ارسال دوبارهٔ command قدیمی.
* **Disturbance:**
*  ایجاد اختلال مثل DoS یا **Rowhammer**.

برای **Confidentiality** از **Memory Encryption** استفاده می‌شود. داده وقتی از CPU خارج می‌شود، توسط **Encryption Engine** رمز می‌شود و ciphertext در DRAM ذخیره می‌شود. هنگام برگشت، decrypt می‌شود. معمولاً **AES-CTR** مناسب است چون می‌تواند padها را precompute کند و encryption/decryption عمدتاً XOR شود. اما encryption engine باید نزدیک CPU یا داخل SoC باشد، چون plaintext نباید روی external bus ظاهر شود.

برای **Integrity** از **Hash Tree / Merkle Tree** استفاده می‌شود. برگ‌های tree داده‌های memory هستند، parentها hash فرزندان‌اند، و root خلاصه‌ای از کل memory است. اگر attacker داده را تغییر دهد، root hash تغییر می‌کند. اما اگر خود hash tree هم در untrusted memory باشد، attacker ممکن است آن را هم تغییر دهد. برای همین root یا بعضی cached nodes باید on-chip و trusted باشند. برای freshness در برابر replay هم counterها وارد hash می‌شوند.

برای protection قوی‌تر می‌توان از **MAC** و **Bonsai Hash Tree** استفاده کرد. MAC keyed digest است؛ attacker بدون key نمی‌تواند MAC درست برای دادهٔ تغییرکرده بسازد. این integrity را قوی‌تر می‌کند، ولی computation و storage overhead دارد.

حتی اگر memory encrypted و integrity-protected باشد، attacker هنوز می‌تواند **Access Pattern** را ببیند: کدام آدرس‌ها، چه زمانی، چند بار access می‌شوند. از این traffic analysis می‌توان دربارهٔ رفتار برنامه اطلاعات گرفت. برای حل این مشکل از **ORAM / Oblivious RAM** مثل **Path ORAM** استفاده می‌شود. ORAM طوری accessها را تغییر می‌دهد که pattern فیزیکی memory مستقل از الگوریتم اصلی به نظر برسد. این امنیت بیشتری می‌دهد، اما overhead زیادی دارد.

---

### 10. NVM/NVRAM Security

**NVM / Non-Volatile Memory**

 داده را حتی بدون power نگه می‌دارد. **NVRAM** نوعی NVM است که می‌تواند نقش main memory را داشته باشد یا DRAM را تکمیل کند. مثال‌ها: **ReRAM، FeRAM، MRAM، PCM**. مزیت این memoryها persistence است؛ اما همین persistence امنیتی هم خطرناک است.

در DRAM وقتی power قطع شود، داده به‌مرور از بین می‌رود. اما در NVRAM داده بعد از reboot، crash یا shutdown باقی می‌ماند. این یعنی **Data Remanence**. اگر secret در NVRAM باقی بماند، attacker بعداً می‌تواند آن را extract کند. حتی deletion هم همیشه کامل نیست؛ در بعضی memoryها اثر residual charge یا wear pattern ممکن است اطلاعات قبلی را leak کند.

NVRAM

 همچنین مسئلهٔ **Atomicity** دارد. اگر سیستم هنگام write crash کند، باید یا کل update persisted شده باشد یا هیچ‌کدام. برای داده‌های بزرگ‌تر از یک word، این سخت‌تر می‌شود. امنیت integrity tree هم باید با atomic update سازگار باشد؛ یعنی data و security metadata مثل hash/MAC/counter باید بعد از reboot یا crash consistent باشند.

پس برای NVRAM فقط encryption کافی نیست. باید مشخص کنیم چه داده‌ای persistent باشد، granularity persistence چیست، بعد از reboot state امنیتی چطور restore شود، و اگر memory هنگام خاموشی تغییر کرد، boot-time verification بتواند آن را detect کند.

---

### 11. Multiprocessor، SMP، DSM/NUMA و Many-Core Protection

در سیستم‌های multiprocessor دو مدل مهم داریم:



 **SMP / Symmetric Multi-Processing** و **DSM/NUMA / Distributed Shared Memory / Non-Uniform Memory Access**.
 
 
 
  در SMP چند CPU یک memory مرکزی و معمولاً یک shared bus دارند. این shared bus attack surface بزرگی است، چون attacker یا component untrusted ممکن است traffic را snoop کند.

در DSM/NUMA هر CPU یا node memory خودش را دارد و ارتباط از طریق interconnection network انجام می‌شود. در اینجا memory shared به شکل SMP نیست، اما inter-processor communication مهم می‌شود. باید confidentiality، integrity و حتی communication pattern protection برای پیام‌های بین processors داشته باشیم.

در **Many-Core**، trust boundary کوچک‌تر می‌شود. ممکن است فقط یک core trusted باشد، اما دیگر cores، routers، interconnect یا IP blocks untrusted باشند. اینجا معماری امنیت با hardware security ترکیب می‌شود: untrusted third-party IP، malicious foundry، supply-chain attack و hardware Trojan دیگر فقط موضوع manufacturing نیستند؛ روی secure processor architecture هم اثر می‌گذارند.

برای محافظت از inter-processor communication باید encryption، MAC و گاهی pattern protection استفاده شود. مشکل اصلی performance است. interconnectها بسیار سریع‌اند، و crypto ممکن است bottleneck بسازد. پس باز هم trade-off داریم: security level، latency، power، area و expected lifetime protection.

---

### 12. اصول Secure Processor Architecture Design و Pitfalls

فصل چهار اصل طراحی می‌دهد:

1. **Protect off-chip communication and memory**


   حافظه و ارتباطات خارج chip untrusted هستند؛ باید encryption، hashing/MAC و access pattern protection داشته باشند.

2. **Isolate processor state among TEE execution and other software**


   architectural و microarchitectural state باید isolate یا flush شود.

3. **Allow TCB introspection**

   باید بتوان TCB را monitor، fingerprint و authenticate کرد.

4. **Authenticate and continuously monitor TEE and TCB**

   فقط boot-time check کافی نیست؛ runtime monitoring هم لازم است.

در پایان فصل چند pitfall مهم دارد. **Security by obscurity** کافی نیست. **Hardware immutability** خطرناک است، چون hardware را مثل software به‌راحتی patch نمی‌کنیم. **Wrong threat model** باعث شکست می‌شود؛ مثل اینکه side-channelها را در threat model نیاوریم. **Fixed threat model** برای همهٔ سیستم‌ها کافی نیست. استفاده از **outdated/custom crypto** خطرناک است. همچنین **zero-overhead security** توهم است؛ امنیت واقعی معمولاً هزینهٔ performance، area یا energy دارد.

نکتهٔ آخر: فقط تمرکز روی **Speculative Attacks** کافی نیست. اگر فقط Spectre/Meltdown را در نظر بگیریم ولی memory attacks، DMA attacks، side channels، supply chain و TCB bugs


 را نادیده بگیریم، طراحی هنوز ناامن است.

---

# گام ۲: خلاصه تستی و مقایسه‌ای

## Bilingual Cheat-Sheet

## Performance Principles vs Security Risk

| Principle          | هدف performance                    | ریسک امنیتی                          |
| ------------------ | ---------------------------------- | ------------------------------------ |
| **Caching**        | کاهش memory latency                | cache side channels، leakage         |
| **Pipelining**     | کاهش critical path                 | transient/microarchitectural effects |
| **Predicting**     | اجرای speculative                  | Spectre-like attacks                 |
| **Parallelizing**  | اجرای هم‌زمان                      | shared resource leakage              |
| **Indirection**    | virtual-to-physical abstraction    | address translation leakage          |
| **Specialization** | acceleration با dedicated circuits | attack surface جدید در custom units  |

---

## Secure Processor Concepts

| Keyword                           | معنی                                                      |
| --------------------------------- | --------------------------------------------------------- |
| **Secure Processor**              | processor با security features اضافه                      |
| **Secure Processor Architecture** | architecture برای محافظت از software با hardware support  |
| **Enclave**                       | محیط protected برای code/data                             |
| **TEE**                           | Trusted Execution Environment                             |
| **TCB**                           | Trusted Computing Base، اجزای trusted لازم برای اجرای TEE |
| **Root of Trust**                 | secret cryptographic base برای کلیدها و اعتماد            |
| **SecE**                          | Secure Engine / security component                        |
| **SMM**                           | System Management Mode                                    |

---

## Baseline Unsecure Hardware

| Component       | خطر                                      |
| --------------- | ---------------------------------------- |
| **Processor**   | cache/core state leakage                 |
| **Bus**         | snooping یا manipulation                 |
| **Memory**      | extraction یا modification               |
| **I/O Devices** | malicious DMA، access به components دیگر |

---

## Privilege Rings

| Ring                      | نقش                                           |
| ------------------------- | --------------------------------------------- |
| **Ring 3**                | Applications                                  |
| **Ring 0**                | OS Kernel                                     |
| **Ring -1**               | Hypervisor / VMM                              |
| **Ring -2**               | SMM                                           |
| **Ring -3**               | Platform Management Engine                    |
| **Horizontal Separation** | جداکردن normal/secure world مثل ARM TrustZone |

---

## Architecture Examples

| Architecture        | Trust idea                                  |
| ------------------- | ------------------------------------------- |
| **Normal Computer** | لایه‌های پایین‌تر باید trusted باشند        |
| **Bastion**         | اعتماد تا Hypervisor لازم است               |
| **SGX**             | application enclave حتی از OS/HV جدا می‌شود |
| **SEV**             | محافظت از VM/Guest OS                       |

---

## TEE vs TCB

| Concept                          | توضیح                               |
| -------------------------------- | ----------------------------------- |
| **TEE**                          | محیط اجرای protected                |
| **TCB**                          | اجزایی که TEE را درست پیاده می‌کنند |
| **TCB vulnerability**            | حفاظت TEE را nullify می‌کند         |
| **TEE provides**                 | معمولاً Confidentiality + Integrity |
| **TEE usually does not provide** | Availability                        |
| **Minimize TCB**                 | کاهش bugs و attack surface          |

---

## State Protection

| State Type                   | مثال                                            |
| ---------------------------- | ----------------------------------------------- |
| **Architectural State**      | registers، program counter                      |
| **Microarchitectural State** | cache، predictor، TLB، pipeline                 |
| **Spatial Sharing**          | چند process/core از یک resource استفاده کنند    |
| **Temporal Sharing**         | resource بعد از یک process به process دیگر برسد |
| **Defense**                  | flush، isolate، partition، monitor              |

---

## Root of Trust

| Method                | مزیت/عیب                                                                     |
| --------------------- | ---------------------------------------------------------------------------- |
| **OTP Fuses**         | ساده، اما نیازمند trust به manufacturer/supply chain                         |
| **PUF**               | unique physical key derivation، اما نیازمند hardware و reliability mechanism |
| **Per-device secret** | هر hardware باید secret یکتا داشته باشد                                      |
| **Derived Keys**      | encryption/signing/verification keys از root derive می‌شوند                  |

---

## Software Measurement Uses

| Mechanism                 | کاربرد                                              |
| ------------------------- | --------------------------------------------------- |
| **Software Measurement**  | hash گرفتن از code/data برای integrity/authenticity |
| **Secure Boot**           | جلوگیری از boot شدن code بدون signature معتبر       |
| **Remote Attestation**    | اثبات به طرف خارجی که چه softwareی اجرا می‌شود      |
| **Sealed Storage**        | آزادکردن secret فقط با measurement درست             |
| **Continuous Monitoring** | کاهش TOC-TOU و runtime tampering                    |

---

## Memory Attacks

| Attack          | Type    | توضیح                      |
| --------------- | ------- | -------------------------- |
| **Snooping**    | Passive | خواندن data/bus traffic    |
| **Spoofing**    | Active  | تزریق memory command       |
| **Splicing**    | Active  | ترکیب commandهای معتبر     |
| **Replay**      | Active  | ارسال دوباره command قدیمی |
| **Disturbance** | Active  | DoS، aging، Rowhammer      |

---

## Memory Protections

| Protection                  | دفاع در برابر                           |
| --------------------------- | --------------------------------------- |
| **Encryption**              | confidentiality، snooping               |
| **AES-CTR**                 | مناسب چون pad precompute و XOR سریع است |
| **Hash Tree / Merkle Tree** | integrity                               |
| **Counters**                | freshness، replay protection            |
| **MAC / Bonsai Tree**       | keyed integrity                         |
| **ORAM / Path ORAM**        | access pattern protection               |

---

## NVRAM Security

| Concept                     | نکته                                      |
| --------------------------- | ----------------------------------------- |
| **NVM**                     | data بدون power باقی می‌ماند              |
| **NVRAM**                   | NVM مناسب main memory                     |
| **Data Remanence**          | passive extraction آسان‌تر                |
| **Atomicity**               | update باید کامل یا اصلاً persisted نشود  |
| **Persistence Granularity** | تعیین اینکه کدام data persistent باشد     |
| **Boot-time verification**  | تغییر memory هنگام خاموشی قابل detect شود |

---

## Multiprocessor Protection

| Architecture  | ویژگی                          | Protection                                            |
| ------------- | ------------------------------ | ----------------------------------------------------- |
| **SMP**       | shared bus + central memory    | bus encryption، Merkle trees، MACs                    |
| **DSM/NUMA**  | per-node memory + interconnect | encrypted communication، MAC، local memory protection |
| **Many-Core** | trust boundary کوچک‌تر         | protect cores/interconnect/IPs                        |

---

## Design Principles

| Principle                                     | معنی                                                |
| --------------------------------------------- | --------------------------------------------------- |
| **Protect off-chip communication and memory** | memory/bus بیرون chip untrusted است                 |
| **Isolate processor state**                   | architectural و microarchitectural state محافظت شود |
| **Allow TCB introspection**                   | TCB قابل monitor و authenticate باشد                |
| **Continuously monitor TEE/TCB**              | فقط boot-time check کافی نیست                       |

---

# گام ۳: تله‌های امتحانی

## Pitfalls

### 1. Secure Processor


 همان processor کاملاً امن نیست

**Secure Processor**

 security features
 
 
 
  دارد، اما خودش می‌تواند vulnerable باشد: bugs، side channels، hardware Trojans، supply-chain attacks.

### 2. TEE و TCB یکی نیستند

* **TEE:**
* 
*  محیط protected execution
* **TCB:** 
* 
* اجزای trusted که TEE را پیاده می‌کنند

اگر TCB خراب شود، TEE دیگر قابل اعتماد نیست.

### 3. TEE معمولاً Availability نمی‌دهد

TEE

 معمولاً روی **Confidentiality** و **Integrity** تمرکز دارد. DoS یا توقف execution معمولاً خارج از guarantee است.

### 4. OS و Hypervisor همیشه trusted نیستند

در معماری‌هایی مثل **SGX**، حتی OS و Hypervisor هم untrusted فرض می‌شوند.

### 5. Ring پایین‌تر همیشه کافی برای امنیت نیست

مدل خطی ringها شکسته شده است. **Horizontal privilege separation** مثل TrustZone یعنی secure/normal domain هم مهم است.

### 6. فقط architectural state کافی نیست

Cache، TLB، branch predictor

 و pipeline
 
 
  هم می‌توانند leak کنند. پس microarchitectural state باید isolate یا flush شود.

### 7. Encryption فقط confidentiality می‌دهد

Memory encryption


 جلوی خواندن plaintext را می‌گیرد، اما integrity، replay و access pattern leakage را حل نمی‌کند.

### 8. Hash Tree بدون trusted root کافی نیست

اگر attacker هم data و هم hash tree را تغییر دهد، باید root یا cached nodes on-chip trusted باشند تا دستکاری detect شود.

### 9. Counter برای freshness لازم است

برای replay protection، hash/MAC باید counters را هم وارد کند. بدون counter، old valid data ممکن است replay شود.

### 10. ORAM برای confidentiality محتوای memory نیست

ORAM


 برای مخفی‌کردن **access pattern** است. معمولاً روی encryption و integrity اضافه می‌شود.

### 11. PUF رایگان و ساده نیست

PUF


 نیاز به reliability، error correction، certificate management و hardware اضافه دارد.

### 12. Secure Boot با update مشکل مفهومی دارد

Update


 قانونی hash را عوض می‌کند. پس باید signature/update mechanism درست وجود داشته باشد.

### 13. Remote Attestation با Secure Boot یکی نیست

**Secure Boot**


 محلی تصمیم می‌گیرد boot کند یا نه.
**Remote Attestation**


 به طرف خارجی گزارش می‌دهد چه چیزی اجرا می‌شود.

### 14. NVRAM خطر remanence بیشتری دارد

چون داده بعد از power-off باقی می‌ماند. deletion و crash recovery باید درست طراحی شوند.

### 15. Zero-overhead security توهم است

امنیت واقعی معمولاً هزینهٔ performance، energy یا area دارد. گزینه‌ای که security کامل با zero overhead وعده می‌دهد مشکوک است.

---

# گام ۴: خودآزمایی

## 100% English Quiz

### Question 1

**Which statement best describes the relationship between TEE and TCB?**

A. A TEE is the set of all untrusted applications, while the TCB is the attacker model.
B. A TEE is a protected execution environment, while the TCB is the set of trusted hardware and software components that correctly implement it.
C. A TCB is always outside the processor chip and never includes hardware.
D. A vulnerability in the TCB has no impact on the security of the TEE.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** TEE محیط اجرای محافظت‌شده است، نه مجموعهٔ برنامه‌های untrusted.
* **B درست است:** **TEE** محیط protected است؛ **TCB** مجموعهٔ اجزای trusted است که این محیط را پیاده‌سازی می‌کنند.
* **C غلط است:** TCB می‌تواند شامل hardware، firmware و software باشد.
* **D غلط است:** اگر TCB آسیب‌پذیر شود، protectionهای TEE عملاً از بین می‌روند.

---

### Question 2

**Why is memory encryption alone insufficient for complete memory protection?**

A. Because encryption only protects confidentiality and does not by itself guarantee integrity, freshness, or access-pattern privacy.
B. Because encryption prevents the CPU from reading its own data.
C. Because encryption removes the need for any Root of Trust.
D. Because encryption is used only for non-volatile memories and not for DRAM.

**Correct Answer: A**

**تحلیل گزینه‌ها:**

* **A درست است:** encryption جلوی snooping محتوای plaintext را می‌گیرد، اما برای integrity به **Hash Tree/MAC**، برای replay به **counters**، و برای access pattern به **ORAM** نیاز داریم.
* **B غلط است:** CPU داده را قبل از استفاده decrypt می‌کند.
* **C غلط است:** encryption خودش به key و در نتیجه Root of Trust نیاز دارد.
* **D غلط است:** memory encryption برای DRAM/off-chip memory هم استفاده می‌شود.

---

### Question 3

**What is the main purpose of Remote Attestation in secure processor architectures?**

A. To increase the clock frequency of the processor.
B. To allow an external verifier to check what software is running on a platform using hardware-backed measurements.
C. To erase all microarchitectural state after every instruction.
D. To replace all encryption keys with public data.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** Remote Attestation برای performance نیست.
* **B درست است:** در **Remote Attestation**، hardware-backed report یا certificate تولید می‌شود تا طرف خارجی بفهمد چه softwareی اجرا می‌شود.
* **C غلط است:** این بیشتر به state isolation/flush مربوط است، نه attestation.
* **D غلط است:** attestation از cryptographic keys استفاده می‌کند؛ keys را public data جایگزین نمی‌کند.
