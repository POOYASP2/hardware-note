کاملاً حق با شماست. در پردازشگرهای متنی (مثل همین محیط Markdown)، وقتی یک پاراگراف یا خط با حروف انگلیسی شروع می‌شود، سیستم جهت متن را به‌صورت خودکار «چپ‌به‌راست» (LTR) در نظر می‌گیرد و چیدمان کلمات فارسی و انگلیسی کاملاً به هم می‌ریزد.

برای حل اصولی و تمیزِ این مشکل، بدون اینکه نیازی به شکستن غیرطبیعی خطوط باشد، در ابتدای هر خط یا پاراگرافی که با کلمه انگلیسی شروع می‌شد، یک کلمهٔ توصیفی فارسی (مثل: *ویژگی، سیستم، الگوریتم، مبحث، ساختار* و...) اضافه کردم. این کار باعث می‌شود تمام خطوط با حروف فارسی شروع شوند و ساختار راست‌چین (RTL) به‌صورت بی‌نقص حفظ شود.

در ادامه، فایل اصلاح‌شده با ساختار کاملاً راست‌چین و خوانا تقدیم شما:

---

# گام ۱: آموزش مفهومی و مفصل

## Deep Explanation — Random Number Generators

### ۱. چرا Random Number Generation در امنیت سخت‌افزار حیاتی است؟

در امنیت و رمزنگاری، خیلی چیزها به یک فرض ساده وابسته‌اند: attacker نباید بتواند secret را حدس بزند. اما secret معمولاً از هیچ ساخته نمی‌شود؛ باید با یک **Random Number Generator / RNG** تولید شود. اگر RNG ضعیف باشد، حتی اگر الگوریتم‌هایی مثل **AES، RSA، ECC، SHA-256** کاملاً قوی باشند، کل سیستم قابل شکستن می‌شود. چون attacker به‌جای شکستن خود الگوریتم، فقط خروجی RNG یا seed آن را پیش‌بینی می‌کند.

مثلاً در یک سیستم امن، random number برای ساختن **Session Key، Nonce، Initialization Vector / IV، Large Prime Numbers، Digital Signature Parameters** و حتی challengeهای authentication استفاده می‌شود. اگر این مقدارها قابل پیش‌بینی باشند، attacker ممکن است session key را حدس بزند، nonce تکراری پیدا کند، signature را جعل کند یا authentication را دور بزند. پس جملهٔ مهم این فصل این است: **A cryptographic system is only as secure as the randomness it depends on.**

تشبیه ساده: فرض کن برای هر بار ورود به خانه، یک رمز موقت تولید می‌کنی. اگر این رمزها واقعاً random باشند، حدس‌زدنشان سخت است. اما اگر رمزها مثلاً این‌طور باشند: `1234, 1235, 1236...` یا با یک فرمول ساده تولید شوند، ظاهرشان شاید متغیر باشد ولی secure نیستند. در کامپیوتر هم همین است. یک sequence ممکن است «تصادفی به نظر برسد»، اما اگر از یک فرمول deterministic آمده باشد، attacker با دیدن چند خروجی می‌تواند ادامهٔ sequence را پیش‌بینی کند.

به همین دلیل، فصل میان **True Random Number / TRN** و **Pseudo-Random Number / PRN** فرق می‌گذارد. سیستم **TRNG / True Random Number Generator** از پدیده‌های فیزیکی غیرقابل پیش‌بینی entropy می‌گیرد. سیستم **PRNG / Pseudo-Random Number Generator** با یک algorithm و یک **Seed** مقدارهای ظاهراً random تولید می‌کند. یک PRNG برای simulation خوب است، ولی برای cryptography معمولی کافی نیست. برای امنیت واقعی معمولاً به **CSPRNG / Cryptographically Secure Pseudo-Random Number Generator** نیاز داریم.

---

### ۲. سه ویژگی اصلی Randomness

یک RNG خوب باید سه ویژگی اصلی داشته باشد: **Unpredictability، Statistical Independence، Uniform Distribution**. ویژگی اول (Unpredictability) یعنی اگر attacker خروجی‌های قبلی را ببیند، نتواند خروجی بعدی را پیش‌بینی کند. این مهم‌ترین ویژگی امنیتی است. اگر sequence از نظر آماری قشنگ باشد ولی قابل پیش‌بینی باشد، برای cryptography بد است. مثلاً اعداد `1, 2, 3, 4...` از نظر توزیع روی یک بازه ممکن است بعد از مدت طولانی متوازن به نظر برسند، اما کاملاً قابل پیش‌بینی‌اند.

ویژگی **Statistical Independence** یعنی خروجی‌ها نباید رابطهٔ قابل استفاده با هم داشته باشند. اگر بعد از عدد خاصی معمولاً عدد خاص دیگری بیاید، attacker می‌تواند probability خروجی بعدی را بهتر حدس بزند. رسیدن به independence کامل در عمل سخت است، مخصوصاً در سیستم‌های دیجیتال، چون دیجیتال یعنی state، clock، transition و رفتار deterministic.

ویژگی **Uniform Distribution** یعنی روی تعداد زیاد نمونه، هر مقدار ممکن تقریباً به یک اندازه ظاهر شود. مثلاً اگر RNG برای تولید عدد بین 0 تا 255 استفاده می‌شود، نباید بعضی عددها خیلی بیشتر از بقیه ظاهر شوند. اگر bias وجود داشته باشد، attacker search space را کم می‌کند. مثلاً اگر keyها ظاهراً 128-bit باشند ولی نصف bitها بیشتر اوقات صفر باشند، امنیت واقعی خیلی کمتر از 128 بیت است.

نکتهٔ امتحانی: ویژگی **Uniformity** کافی نیست. یک LFSR maximal-length می‌تواند distribution نسبتاً خوبی داشته باشد، اما چون deterministic و قابل بازسازی است، cryptographically secure نیست. پس در تست‌ها اگر گزینه‌ای گفت «اگر sequence uniform باشد، برای cryptography امن است»، مشکوک و معمولاً غلط است.

---

### ۳. LSR، LFSR و مشکل پیش‌بینی‌پذیری

ساختار **LSR / Linear Shift Register** یک register است که bitها را در یک جهت shift می‌دهد. bitهای انتهایی خارج می‌شوند و جای خالی باید با مقدار جدید پر شود. ساختار **LFSR / Linear Feedback Shift Register** نسخهٔ پیشرفته‌تر است: بعضی bitهای register با یک function، معمولاً **XOR**، ترکیب می‌شوند و نتیجه به register برمی‌گردد. انتخاب این bitها با یک **Feedback Polynomial** یا **Characteristic Polynomial** مشخص می‌شود.

الگوریتم **LFSR** در سخت‌افزار خیلی جذاب است، چون ارزان، سریع و ساده است. فقط چند flip-flop و XOR لازم دارد. برای test pattern generation، scrambling، simulation و بعضی کاربردهای غیرحساس خوب است. اما برای cryptography خام مناسب نیست، چون linear است. اگر attacker به اندازهٔ کافی خروجی ببیند، می‌تواند polynomial یا state داخلی را reverse-engineer کند.

یک نکتهٔ حیاتی: حالت **All-zero state** در LFSR خطرناک است. اگر همهٔ bitها صفر باشند، XOR صفرها هم صفر می‌شود، پس register تا ابد صفر می‌ماند. به همین دلیل در LFSRهای maximal-length، تمام stateها به‌جز all-zero قابل پیمایش‌اند. پس یک n-bit LFSR maximal-length حداکثر $2^n - 1$ state دارد، نه $2^n$.

اگر polynomial مناسب و **Primitive Polynomial** باشد، ساختار LFSR می‌تواند **Maximum-Length Sequence** تولید کند. یعنی از تمام stateهای غیرصفر عبور می‌کند و بعد تکرار می‌شود. این sequence ویژگی‌هایی مثل balance بین صفر و یک، توزیع runها، و دورهٔ طولانی دارد. اما همچنان deterministic است. پس maximal-length بودن مساوی cryptographic security نیست.

---

### ۴. TRNG: تولید تصادف واقعی از دنیای فیزیکی

سیستم **TRNG / True Random Number Generator** از یک پدیدهٔ فیزیکی entropy می‌گیرد. دنیای analog پر از noise و ناپایداری است: مواردی مانند **Thermal Noise / Johnson Noise** در resistor، noise در semiconductor diode، jitter، oscillator instability، environmental noise، microphone noise، network timing، mouse movement، keyboard timing و غیره. سیستم TRNG این پدیده‌ها را sample می‌کند و به bit تبدیل می‌کند.

ایدهٔ اصلی: در یک TRNG، مبحث **Entropy Source** باید خارج از کنترل attacker و غیرقابل پیش‌بینی باشد. سپس یک **Extractor** یا sampler آن را به bitهای دیجیتال تبدیل می‌کند. مثلاً noise کوچک resistor را amplify می‌کنیم، به comparator یا **Schmitt Trigger** می‌دهیم، و خروجی را با clock sample می‌کنیم. اگر noise واقعاً unpredictable باشد، bitهای خروجی می‌توانند entropy واقعی داشته باشند.

اما raw output یک TRNG معمولاً perfect نیست. ممکن است bias داشته باشد؛ مثلاً 1 کمی بیشتر از 0 بیاید. برای همین از فرآیند **Whitening** استفاده می‌شود. فرآیند Whitening یعنی اعمال transformation برای کاهش bias و pattern. مثال‌ها: **XOR Whitening، Hash-Based Whitening، Linear Transformation**. مثلاً چند bit خام را XOR می‌کنیم یا با SHA-256 digest می‌گیریم تا output uniformتر شود.

مشکل سیستم **TRNG** این است که analog source ممکن است کند، پرهزینه، noisy، وابسته به محیط یا قابل attack باشد. مثلاً attacker با تغییر دما، ولتاژ، نور، electromagnetic field یا clock می‌تواند entropy source را خراب کند. پس سیستم TRNG هم نیاز به **Diagnostic Tests** و monitoring دارد. اینکه چیزی «فیزیکی» است، به‌تنهایی آن را امن نمی‌کند.

---

### ۵. PRNG: تصادفی‌نما، نه تصادفی واقعی

سیستم **PRNG / Pseudo-Random Number Generator** یک algorithm است که از یک مقدار اولیه به نام **Seed** شروع می‌کند و sequence تولید می‌کند. اگر seed و algorithm یکی باشد، خروجی همیشه یکی است. این deterministic بودن برای simulation عالی است، چون می‌توان آزمایش را دقیقاً تکرار کرد. اما برای security خطرناک است، چون اگر attacker seed یا internal state را بفهمد، کل خروجی‌های آینده و گاهی گذشته قابل پیش‌بینی می‌شوند.

یک PRNG معمولاً دوره دارد؛ یعنی بعد از مدتی sequence تکرار می‌شود. طول دوره به internal state size و algorithm بستگی دارد. یک PRNG خوب باید period خیلی طولانی داشته باشد، اما period طولانی کافی نیست. اگر خروجی‌ها با چند مشاهده قابل prediction باشند، period هرچقدر هم بزرگ باشد امنیت cryptographic نمی‌دهد.

مبحث **Seed Criticality** نکتهٔ اصلی است. اگر seed ضعیف باشد، کل sequence ضعیف است. مثلاً seed گذاشتن برابر timestamp فعلی خطرناک است، چون attacker می‌تواند زمان تقریبی را حدس بزند. برای cryptographic use، مقدار seed باید از TRNG یا CSPRNG امن بیاید و secret بماند. همچنین بهتر است PRNGها periodically reseed شوند تا اگر state تا حدی compromise شد، سیستم بتواند بازیابی نسبی داشته باشد.

پس تفاوت اصلی: سیستم **TRNG** مستقیماً entropy را از physical world می‌گیرد؛ در حالی که سیستم **PRNG** صرفاً entropy را گسترش می‌دهد. سیستم PRNG از هیچ، entropy تولید نمی‌کند. اگر seed فقط 32 بیت entropy داشته باشد و خروجی 1GB تولید کند، امنیت واقعی همچنان محدود به همان entropy اولیه است.

---

### ۶. Hardware PRNG و ALFSR

در سخت‌افزار، رایج‌ترین ساختار PRNG شامل انواع ساده **LFSR** و **ALFSR / Autonomous Linear Feedback Shift Register** است. مدل **ALFSR** هیچ external input ندارد؛ از initial state شروع می‌کند و با polynomial مشخص state بعدی را تولید می‌کند. اگر polynomial primitive باشد، یک n-bit ALFSR می‌تواند دورهٔ maximal یعنی $2^n - 1$ تولید کند.

این فصل تأکید می‌کند که فقط گفتن «32-bit LFSR» کافی نیست. باید polynomial را هم بدانیم. دو LFSR با طول یکسان، ولی polynomial متفاوت، می‌توانند period کاملاً متفاوت داشته باشند. پس در سؤال تستی، اگر فقط bit-length داده شود و دربارهٔ maximal بودن نتیجه‌گیری شود، باید شک کنی.

برای بهتر کردن PRNG سخت‌افزاری، گاهی از یک ALFSR بزرگ‌تر استفاده می‌شود و فقط بخشی از state را output می‌دهیم. مثلاً یک **128-bit ALFSR** داشته باشیم، اما فقط **32 bit** را به‌عنوان random value بخوانیم. ایده این است که 96 bit پنهان باقی بماند و attacker با دیدن 32 bit نتواند کل state را راحت reconstruct کند. البته اگر طراحی linear و output selection ثابت باشد، attacker با خروجی کافی همچنان می‌تواند تحلیل کند.

این روش‌ها برای کاربردهای غیرحساس مفیدند، اما برای cryptography خام خطرناک‌اند. اگر نیاز امنیتی داریم، باید از CSPRNG، ترکیب entropy واقعی، hash، block cipher یا designهای استاندارد استفاده کنیم، نه LFSR خام.

---

### ۷. Software PRNG و LCG

در محیط software، یک PRNG کلاسیک همان **LCG / Linear Congruential Generator** است. فرمول آن:

$$X_{n+1} = (aX_n + c) \bmod M$$

اینجا $X_0$ نقش seed را دارد، و **a، c، M** پارامترهای generator هستند. اگر این پارامترها درست انتخاب شوند، sequence می‌تواند دورهٔ خوبی داشته باشد. اما LCG از نظر امنیتی ضعیف است، چون ساختار ریاضی ساده و قابل تحلیل دارد. با دیدن چند خروجی، attacker می‌تواند پارامترها یا state را حدس بزند.

در زبان C، تابع `rand()` معمولاً یک PRNG ساده شبیه LCG است. برای simulation و بازی‌های ساده شاید کافی باشد، اما برای key، nonce، password reset token یا session ID نباید استفاده شود. در Python هم module معروف **random** از الگوریتم **Mersenne Twister** استفاده می‌کند. این generator دوره یا period بسیار بزرگی دارد، اما cryptographically secure نیست. خود Python هم برای security توصیه می‌کند از **secrets** استفاده شود، نه random.

در زبان C++ هم engineهایی مثل **std::linear_congruential_engine**، **std::mersenne_twister_engine**، **std::subtract_with_carry_engine** وجود دارند. این‌ها برای کاربردهای آماری و simulation هستند، نه برای cryptographic secrets. در Java هم generatorهای معمولی محدودیت دارند و نباید برای crypto استفاده شوند؛ باید از libraryهای secure مثل **SecureRandom** استفاده کرد.

حفظی مهم: ویژگی **Large period ≠ cryptographic security.** الگوریتم Mersenne Twister دارای period عظیم است، اما اگر state آن reconstruct شود، خروجی‌های بعدی قابل پیش‌بینی‌اند.

---

### ۸. CSPRNG: PRNG امن برای cryptography

سیستم **CSPRNG / Cryptographically Secure PRNG** برای این ساخته شده که مشکل predictability در PRNGهای معمولی را حل کند. یک CSPRNG باید حداقل دو ویژگی داشته باشد. اول تست **Next-bit Test**: اگر attacker بتواند k bit اول خروجی را بداند، نباید بتواند bit بعدی را با منابع محاسباتی معقول پیش‌بینی کند. دوم تست **State Compromise Extension Resistance**: اگر attacker به state فعلی نفوذ کند (بفهمد)، نباید بتواند خروجی‌های قبلی را reconstruct کند.

یک CSPRNG معمولاً از ترکیب entropy سیستم‌عامل و یک generator قوی استفاده می‌کند. مثلاً periodically reseed می‌شود: وقتی entropy جدید از OS، interruptها، disk I/O، hardware RNG یا user input می‌آید، internal state به‌روزرسانی می‌شود. این کار باعث می‌شود sequence از گذشته و آینده disconnect شود و تحلیل برای attacker سخت‌تر شود.

روش‌های ساخت CSPRNG می‌توانند بر پایهٔ **Cryptographic Hash Functions**، **Block Cipher in Counter Mode**، **Stream Cipher** یا مسائل سخت ریاضی مثل **Integer Factorization Problem / IFP**، **Discrete Logarithm Problem / DLP**، **Elliptic-Curve DLP / ECDLP** باشند. نمونه‌های معروف design مثل **Yarrow** و **Fortuna** هم در همین دسته‌اند.

در پردازنده‌های مدرن (CPUها) هم سخت‌افزارهای RNG/CSPRNG وجود دارد؛ مثلاً تکنولوژی **Intel DRNG**. اما استفاده از hardware RNG باید از طریق API درست انجام شود. همچنین طراحی در پردازنده‌های Intel، ARM و AMD ممکن است فرق کند. پس portability و correctness مهم‌اند. برای برنامه‌نویسی امن، باید از libraryهای cryptographically secure استفاده کرد: مثلاً در Python از ماژول **secrets** و در C از APIهای امن سیستم استفاده می‌شود، نه `rand()`.

---

### ۹. مدل مفهومی RNG: Pool و Extractor

فصل یک مدل مفهومی مفید ارائه می‌دهد: فرض کن می‌خواهیم به تعداد **K random values** خروجی تولید کنیم. یک استخر یا **Pool** داریم شامل **N values** که N خیلی بزرگ‌تر از K است. بعد یک استخراج‌گر یا **Extractor** داریم که از pool مقدارهای خروجی را انتخاب یا تولید می‌کند. کیفیت RNG به هر دو وابسته است: اینکه pool چقدر entropy دارد، و extractor چقدر امن و درست کار می‌کند.

بخش **Pool** می‌تواند known یا unpredictable باشد، محدود یا نامحدود باشد، constant یا changing with time باشد، analog یا digital باشد. مثلاً در یک TRNG، منبعِ pool معمولاً analog و در اصل نامحدود است. در یک PRNG، بخش pool ممکن است همان finite internal state باشد. در سیستم‌عامل Linux، منابعِ entropy pool از رویدادهای سیستم پر می‌شود و `/dev/random` یا APIهای مشابه از آن استفاده می‌کنند.

بخش **Extractor** می‌تواند **Combinational** یا **Sequential** باشد. نوع Combinational یعنی خروجی فقط به input فعلی وابسته است. نوع Sequential یعنی خروجی به input فعلی و internal state وابسته است. سیستم‌های PRNG مبتنی بر FSM (مثل LFSR)، همگی sequential هستند. یک Hash-based generator می‌تواند pool بزرگ را با hash به digest تبدیل کند. مدل Polynomial-divider هم می‌تواند مثل LFSR sequence تولید کند.

این مدل برای امتحان مفید است، چون کمک می‌کند مفاهیم TRNG و PRNG را مقایسه کنی: در یک TRNG، منبعِ pool همان physical entropy است و extractor نقش sampler/whitener را دارد. در یک PRNG، منبعِ pool همان internal state/seed است و extractor نقش algorithmic دارد. در سیستم CSPRNG، منبعِ pool با entropy سیستم مرتب reseed می‌شود و extractor نقش cryptographic را ایفا می‌کند.

---

### ۱۰. Randomness Validation و Diagnostic Tests

بعد از تولید random sequence باید کیفیت آن را بررسی کنیم. یک راه ریاضی، استفاده از **Entropy Analysis** است؛ مثلاً با **Shannon Entropy** مقدار uncertainty را اندازه می‌گیریم. اگر distribution خیلی biased باشد، entropy پایین می‌آید. اما entropy analysis فقط یک بعد را نشان می‌دهد؛ یک sequence ممکن است entropy ظاهری خوبی داشته باشد ولی کاملاً قابل پیش‌بینی باشد.

روش‌های عملی ساده هم وجود دارد: تعداد 0 و 1 را بشماریم، ببینیم تقریباً برابرند یا نه. جریانِ bitstream را به گروه‌های 4-bit تقسیم کنیم و ببینیم همهٔ 16 حالت تقریباً به‌یک اندازه می‌آیند یا نه. یا طول runها را بررسی کنیم. این‌ها تست‌های اولیه‌اند، نه guarantee و تضمین‌کنندهٔ امنیت.

برای فرآیند validation جدی، از test suiteهایی مثل **Diehard، ent، NIST Statistical Test Suite** استفاده می‌شود. اما باز هم passing statistical tests مساوی cryptographic security نیست. یک سیستمِ PRNG deterministic خوب ممکن است همهٔ تست‌های آماری را pass کند ولی با دانستن state قابل پیش‌بینی باشد.

برای تجهیزاتِ embedded systems، تست‌های **FIPS 140-2 Diagnostic Tests** مهم‌اند، چون سریع و قابل پیاده‌سازی‌اند. این تست‌ها شامل **Monobit Test، Poker Test، Runs Test، Long Run Test** هستند. مثلاً در Monobit Test، در یک bitstream 20000 بیتی، تعداد 1ها باید در بازهٔ مشخصی باشد. در Long Run Test بررسی می‌شود که run طولانی‌تر از حد مجاز وجود نداشته باشد. این تست‌ها می‌توانند noise خراب یا attack روی entropy source را خیلی زود detect کنند، اما هرگز تضمین نمی‌کنند که سیستم RNG کاملاً امن است.

---

# گام ۲: خلاصه تستی و مقایسه‌ای

## Bilingual Cheat-Sheet

### RNG Core Concepts

| Keyword | نکتهٔ تستی |
| --- | --- |
| **RNG** | تولید sequence از values که unpredictable به نظر برسند |
| **Entropy** | میزان uncertainty / unpredictability |
| **Seed** | مقدار اولیهٔ استفاده شده در PRNG |
| **TRNG** | از منبع physical entropy source استفاده می‌کند |
| **PRNG** | مبتنی بر deterministic algorithm و دریافتِ seed |
| **CSPRNG** | یک PRNG امن شده برای cryptographic use |
| **Nonce** | عددی که فقط یک‌بار استفاده می‌شود |
| **Whitening** | حذف bias و pattern از raw random data |

---

### Three Randomness Properties

| Property | معنی |
| --- | --- |
| **Unpredictability** | خروجی بعدی هرگز از روی قبلی‌ها قابل حدس نباشد |
| **Statistical Independence** | خروجی‌ها هیچ رابطهٔ قابل استفاده با هم نداشته باشند |
| **Uniform Distribution** | همهٔ مقادیر (values) تقریباً با احتمال برابر ظاهر شوند |

نکتهٔ کلیدی: داشتن **Uniform Distribution alone is not enough for cryptographic security.**

---

### TRNG vs PRNG vs CSPRNG

| Feature | **TRNG** | **PRNG** | **CSPRNG** |
| --- | --- | --- | --- |
| Source | Physical entropy | Seed + algorithm | Entropy + crypto algorithm |
| Deterministic? | No | Yes | Yes, but hardened/reseeded |
| Predictability | ideally unpredictable | predictable if seed/state known | designed to resist prediction |
| Speed | ممکن است کندتر باشد | سریع | سریع و امن‌تر |
| Use | key generation seed, entropy source | simulation, non-security | cryptographic secrets |
| Weakness | entropy source attack/bias | seed/state recovery | implementation/reseed failure |

---

### LFSR / ALFSR

| Concept | نکته |
| --- | --- |
| **LFSR** | ساختار shift register با گرفتن feedback از عملگر XOR |
| **ALFSR** | نوعی LFSR autonomous کاملاً بدون external input |
| **Feedback Polynomial** | تعیین می‌کند که دقیقاً کدام bits وارد XOR شوند |
| **Primitive Polynomial** | برای ایجاد maximal-length sequence کاملاً لازم است |
| **Maximal Length** | ایجاد دورهٔ $2^n - 1$ برای یک n-bit LFSR |
| **All-zero state** | حالت invalid؛ چون سیستم LFSR در صفر گیر می‌کند |
| **Cryptographic use** | نوعِ LFSR خام امن نیست، چون خطی (linear) و قابل reverse-engineering است |

---

### LCG

| Concept | توضیح |
| --- | --- |
| **LCG** | مخفف Linear Congruential Generator |
| Formula | $$X_{n+1} = (aX_n + c) \bmod M$$

 |
| Seed | استفاده از مقدار $X_0$ |
| Full Period | زمانی رخ می‌دهد که پارامترها طوری باشند که کل cycle طی شود |
| Weakness | دارای ساختار ساده و بسیار قابل پیش‌بینی |
| Example | تابع `rand()` در زبان C معمولاً ساختاری LCG-like دارد |

---

### Programming RNGs

| Language/Library | نکته |
| --- | --- |
| تابع **C rand()** | از نظر امنیت کاملاً cryptographically insecure است |
| تابع **C srand(seed)** | وظیفه‌اش تنظیم seed است؛ اگر seed ضعیف باشد = output ضعیف است |
| کتابخانه **C++ STL random engines** | فقط مناسب simulation است، نه لزوماً امور crypto |
| کتابخانه **Python random** | مبتنی بر Mersenne Twister است؛ برای security use کاملاً ممنوع است |
| ماژول **Python secrets** | گزینه‌ای مناسب برای cryptographic randomness |
| کلاس **Java Random** | برای امور crypto به‌هیچ‌وجه مناسب نیست |
| کلاس **Java SecureRandom** | جایگزینی امن‌تر و مناسب‌تر برای امور crypto |

---

### CSPRNG Requirements

| Requirement | معنی |
| --- | --- |
| بررسی **Next-bit Test** | دانستن k بیت نباید هرگز bit بعدی را قابل پیش‌بینی کند |
| بررسی **State Compromise Extension Resistance** | حتی compromise شدن state فعلی نباید خروجی‌های قبلی را آشکار کند |
| مکانیزم **Reseeding** | افزودن entropy جدید به‌طور مستمر برای تغییر دادن internal state |
| منبع **OS Entropy Pool** | مخزن و منبع اصلیِ entropy در سطح سیستم‌عامل |
| پیاده‌سازی **Hash/Block Cipher Based** | یک روش رایج و استاندارد برای ساخت CSPRNG |

---

### Conceptual Model

| Part | TRNG | PRNG |
| --- | --- | --- |
| بخش **Pool** | مبتنی بر analog entropy source | مبتنی بر internal state / seed |
| بخش **Extractor** | مبتنی بر sampler, comparator, whitening | مبتنی بر algorithm, FSM, hash, polynomial divider |
| رفتار **Pool behavior** | ممکن است unlimited و به‌طور پیوسته changing باشد | دارای ماهیتی finite و deterministic است |
| ریسک **Security issue** | حملاتی از نوع bias/attack بر روی entropy source | حملاتی از نوع seed/state prediction |

---

### Validation Tests

| Test | هدف |
| --- | --- |
| رویکرد **Entropy Analysis** | اندازه‌گیری دقیق سطح uncertainty |
| تست **Monobit Test** | سنجش تعادل تعداد 1ها در کل bitstream |
| تست **Poker Test** | بررسی توزیع الگوهای n-bit patterns (مثل 4-tuples) |
| تست **Runs Test** | بررسی و تحلیل runهای پشت‌سرهمِ 0 و 1 |
| تست **Long Run Test** | اطمینان از نبود هیچ‌گونه run خیلی طولانی و غیرعادی |
| ابزارهای **Diehard / ent / NIST Suite** | پکیج‌های نرم‌افزاری جهت statistical validation کامل |
| تست‌های **FIPS 140-2 Tests** | نوعی از diagnostic tests مناسب برای محیط‌های embedded systems |

---

# گام ۳: تله‌های امتحانی

## Pitfalls

* ظاهر **Random-looking** یعنی Secure نیست: یک sequence ممکن است ظاهراً کاملاً تصادفی باشد و حتی بعضی statistical tests را به‌راحتی pass کند، اما اگر ماهیتِ deterministic و قابل پیش‌بینی داشته باشد، برای cryptography مطلقاً ناامن است.
* سیستم‌های **PRNG** و **TRNG** را قاطی نکن: سیستم **TRNG** از مقادیرِ entropy فیزیکی می‌آید؛ اما سیستم **PRNG** از طریق ترکیب seed و algorithm خروجی می‌سازد. دقت کنید که PRNG خودش هیچ entropy واقعی تولید نمی‌کند؛ بلکه فقط مقادیر entropy مربوط به seed اولیه را گسترش می‌دهد.
* مقدار **Seed** اگر لو برود، PRNG می‌شکند: در یک سیستم PRNG معمولی، اگر attacker بتواند seed و نوعِ algorithm را بداند، کل sequence را به‌راحتی تولید می‌کند. پس مقدار seed همیشه باید secret باقی بماند و دارای entropy بالایی باشد.
* الگوریتم **LFSR** با all-zero state گیر می‌کند: در ساختار LFSR، حالتِ همه صفر (تمام‌صفر) کاملاً invalid است. چون نتیجهٔ عملگر XOR روی صفرها همواره صفر است و state در نتیجه تغییر مفیدی نخواهد کرد.
* دورهٔ **n-bit LFSR maximal-length** برابر $2^n$ نیست: حداکثر دوره برای یک سیستمِ maximal-length LFSR همیشه برابر $2^n - 1$ است، چراکه حالتِ all-zero state همواره حذف می‌شود.
* عبارت **Primitive Polynomial** ضروری است: فقط داشتنِ مقدار n بیت در رجیستر کافی نیست. اگر polynomial به‌صورت primitive طراحی نشده باشد، دوره یا period دستگاه ممکن است بسیار کوتاه شود.
* الگوریتم **Maximal-length LFSR** هنوز crypto-secure نیست: این سیستم گرچه دورهٔ بلند و balance خوبی دارد، اما ذاتاً خطی (linear) است و با داشتن خروجیِ کافی، کاملاً قابل تحلیل و پیش‌بینی است.
* عمل **Whitening** باعث خلق entropy نمی‌شود: تکنیک Whitening تنها bias را کاهش می‌دهد و توزیع (distribution) را بهتر می‌کند، اما اگر منبع اصلیِ entropy واقعاً ضعیف باشد، whitening نمی‌تواند معجزه کند و امنیت را بالا ببرد.
* منابعی مثل **/dev/random** یا OS entropy همیشه بی‌نهایت نیستند: مخزن Entropy pool کاملاً محدود است. بعضی منابع کمکی (مثل تایمینگ‌های keyboard/mouse) همگی human-biased هستند و هرگز perfect randomness به شما نمی‌دهند.
* توابعی مثل `rand()` در **C** و `random` در **Python** برای security ممنوع‌اند: برای تولید key، ساختن token، مقدار nonce، لینک‌های password reset و تمامیِ cryptographic secrets باید حتماً از توابع اختصاصیِ CSPRNG استفاده شود، نه PRNGهای عمومی و آماری.
* ویژگی **Large Period** امنیت cryptographic نمی‌آورد: الگوریتمی مثل **Mersenne Twister** یک period عظیم دارد، اما برای استفاده در امور cryptography هرگز امن نیست. داشتن period بلند فقط یکی از ده‌ها معیار ارزیابی است.
* پاس کردن **randomness tests** کافی نیست: تست‌های آماری (Statistical tests) می‌توانند کیفیتِ bad randomness را به‌خوبی detect کنند، اما اگر تستی pass شد، مطلقاً proof یا اثبات‌کنندهٔ امنیتِ cryptographic نیست.
* یک **CSPRNG** باید در برابر state compromise مقاوم باشد: در این سیستم‌ها حتی اگر state فعلی لو رفت و هک شد، خروجی‌های قبلیِ تولید شده نباید توسط مهاجم قابل reconstruct شدن باشند. این ویژگیِ حیاتی در PRNGهای معمولی وجود ندارد.
* هر **RNG** یک single point of failure است: چون بسیاری از کلیدها و secretهای پایه‌ای از طریق RNG تأمین و ساخته می‌شوند، در صورت compromise شدن موتور RNG، ممکن است کل سیستم crypto از هم بپاشد و بشکند.

---

# گام ۴: خودآزمایی

## 100% English Quiz

*(در این بخش به دلیل ماهیت انگلیسی سؤالات، حفظ ساختار چپ‌به‌راست (LTR) منطقی و صحیح است)*

### Question 1

**Which statement best explains why a standard LFSR is not suitable as a cryptographically secure random number generator?**

A. Because an LFSR cannot generate long sequences under any condition.
B. Because an LFSR is linear and its internal state or feedback polynomial can often be reconstructed from enough output observations.
C. Because an LFSR always produces only zeros regardless of its initial state.
D. Because an LFSR requires analog noise and therefore cannot be implemented digitally.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **گزینه A غلط است:** الگوریتم LFSR با استفاده از یک **Primitive Polynomial** به‌راحتی می‌تواند sequence بلند با دورهٔ $2^n - 1$ تولید کند.
* **گزینه B کاملاً درست است:** مشکل اصلی الگوریتم LFSR خام، همان linear بودن و predictability است. یک attacker با دیدن خروجی کافی می‌تواند بلافاصله state یا polynomial را به‌صورت ریاضی تحلیل کند.
* **گزینه C غلط است:** در این الگوریتم فقط **all-zero state** است که گیر می‌کند؛ سایر stateهای غیرصفر به‌درستی sequence تولید می‌کنند.
* **گزینه D غلط است:** ساختار LFSR به‌صورت کاملاً دیجیتال پیاده‌سازی می‌شود و به هیچ analog noise محیطی نیاز ندارد.

---

### Question 2

**What is the key difference between a TRNG and a PRNG?**

A. A TRNG derives randomness from a physical entropy source, while a PRNG deterministically expands a seed using an algorithm.
B. A TRNG is always faster than a PRNG because it uses no hardware.
C. A PRNG is non-deterministic, while a TRNG always repeats the same sequence for the same seed.
D. A PRNG does not require a seed or internal state.

**Correct Answer: A**

**تحلیل گزینه‌ها:**

* **گزینه A کاملاً درست است:** دستگاه **TRNG** ذاتاً از منابع entropy فیزیکی مثل thermal noise استفاده می‌کند؛ در حالی که **PRNG** از یک فرمول deterministic algorithm برای گسترش استفاده می‌کند.
* **گزینه B غلط است:** دستگاه TRNG معمولاً به سخت‌افزار یا analog source نیاز دارد و نسبت به الگوریتم‌های نرم‌افزاری ممکن است کندتر باشد.
* **گزینه C غلط است:** این تعریف دقیقاً برعکس است؛ الگوریتم PRNG است که در صورت گرفتن seed یکسان، همواره همان sequence تکراری را تولید می‌کند.
* **گزینه D غلط است:** الگوریتم PRNG برای شروعِ کارش شدیداً به پارامترهای seed/internal state نیاز دارد.

---

### Question 3

**Which property is required for a PRNG to be considered cryptographically secure?**

A. It must only have a very long period, regardless of predictability.
B. It must pass the next-bit test, meaning that knowing previous output bits should not allow an efficient prediction of the next bit.
C. It must be implemented using a simple linear congruential formula.
D. It must produce exactly the same number of zeros and ones in every short output block.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **گزینه A غلط است:** داشتنِ period طولانی به‌تنهایی کافی نیست. اگر مقادیرِ output قابل پیش‌بینی باشند، هرگز سیستم cryptographically secure نخواهد بود.
* **گزینه B کاملاً درست است:** موفقیت در تست **Next-bit Test** یکی از کلیدی‌ترین و اصلی‌ترین requirements برای هر الگوریتم **CSPRNG** است.
* **گزینه C غلط است:** پیاده‌سازی‌های مبتنی بر **LCG** معمولاً بسیار ضعیف و از نظر امنیتی cryptographically insecure هستند.
* **گزینه D غلط است:** ایجاد balance در هر short block برای اثبات امنیت، نه لازم است و نه کافی؛ در ضمن randomness واقعی صرفاً با equality بسیار دقیق در هر بلوکِ کوتاه تعریف نمی‌شود.