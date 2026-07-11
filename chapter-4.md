برای **chapter-4.pdf** این ساختار را اجرا می‌کنم. موضوع فصل **Hardware Watermarking** است: یعنی چطور در یک **IP** یا مدار سخت‌افزاری یک امضای پنهان قرار بدهیم تا بعداً بتوانیم مالکیت یا استفادهٔ غیرمجاز را اثبات کنیم. فایل بین **Static Watermarking**، **Dynamic Watermarking**، **FSM-based Watermarking**، **Testing-based Watermarking** و **Side-Channel-based Watermarking** فرق می‌گذارد. 

---

# گام ۱: آموزش مفهومی و مفصل

## Deep Explanation — Hardware Watermarking

### 1. مسئله از کجا شروع می‌شود؟ IP-based Design و خطر IP Piracy

در فصل‌های قبلی دیدیم که طراحی یک چیپ مدرن معمولاً از صفر انجام نمی‌شود. شرکت‌ها برای ساختن یک **System-on-Chip / SoC** از چندین **IP / Intellectual Property** استفاده می‌کنند: مثلاً یک **Processor Core**، یک **Memory Controller**، یک **AES Accelerator**، یک **USB Interface** یا یک **AI Accelerator**. این IPها ممکن است توسط خود شرکت ساخته شده باشند یا از شرکت دیگری خریداری شده باشند. مزیت این روش واضح است: زمان طراحی کم می‌شود، هزینه پایین می‌آید و محصول سریع‌تر وارد بازار می‌شود.

اما مشکل امنیتی و حقوقی از همین‌جا شروع می‌شود. وقتی یک IP بین چند actor جابه‌جا می‌شود، یعنی **IP Designer**، **System Integrator** و **Foundry**، هرکدام ممکن است به بخشی از طراحی دسترسی داشته باشند. یک شرکت ممکن است IP تو را بدون اجازه کپی کند، در محصول خودش استفاده کند یا حتی آن را کمی تغییر دهد و بگوید مال خودش است. این همان **IP Piracy** است. اگر این کپی‌برداری در سطح تراشهٔ کامل انجام شود، بحث **IC Piracy** و **Counterfeiting** هم وارد می‌شود.

اینجا **Hardware Watermarking** به وجود می‌آید. ایده‌اش شبیه امضای مخفی روی یک اثر هنری است. مثلاً یک عکاس ممکن است گوشهٔ عکسش یک watermark بگذارد، یا در تصویر تغییرات ظریفی ایجاد کند که فقط خودش بتواند تشخیص دهد. در سخت‌افزار هم می‌خواهیم داخل IP یک امضای خاص بگذاریم تا اگر بعداً کسی آن IP را دزدید یا بدون مجوز استفاده کرد، بتوانیم ثابت کنیم این طراحی متعلق به ماست.

اما فرق بزرگ سخت‌افزار با عکس و ویدئو این است که در multimedia اگر چند pixel کمی تغییر کند، تصویر هنوز قابل قبول است. ولی در **Integrated Circuit / IC** نمی‌توانی عملکرد را حتی کمی خراب کنی. اگر watermark باعث شود خروجی مدار عوض شود، timing خراب شود، power زیاد شود یا critical path طولانی‌تر شود، کل طراحی ممکن است شکست بخورد. پس در Hardware Watermarking باید امضا را طوری اضافه کنیم که مدار دقیقاً همان functionality قبلی را داشته باشد و performance هم قابل قبول بماند.

---

### 2. Hard IP و Soft IP چه فرقی دارند؟

در فایل دو نوع اصلی IP معرفی شده است: **Hard IP** و **Soft IP**. در **Soft IP**، فروشنده معمولاً توصیف HDL طراحی را می‌دهد، مثلاً کد **VHDL** یا **Verilog**. یعنی IP هنوز در سطح نسبتاً abstract است و می‌توان آن را با بقیهٔ RTL سیستم integrate کرد. بعداً synthesis، placement و routing روی کل سیستم انجام می‌شود.

در **Hard IP**، فروشنده layout آماده می‌دهد. یعنی IP قبلاً تا سطح physical design جلو رفته و به‌صورت یک بلوک تقریباً آماده وارد چیپ می‌شود. مثلاً یک memory macro یا processor core که layout آن مشخص است. Hard IP معمولاً performance قابل پیش‌بینی‌تر دارد، اما انعطاف‌پذیری کمتری دارد. Soft IP انعطاف بیشتری دارد، اما ممکن است بعد از synthesis و P&R رفتار timing آن بسته به technology و constraints تغییر کند.

از نظر watermarking، این تفاوت مهم است. اگر IP به‌صورت **Soft IP** باشد، watermark می‌تواند در سطح HDL، FSM، synthesis constraints یا register allocation قرار بگیرد. اگر IP به‌صورت **Hard IP** باشد، شاید watermark بیشتر در layout، placement، routing یا ویژگی‌های فیزیکی قابل بررسی باشد. پس نوع IP تعیین می‌کند که کجا و چطور می‌توان watermark را embed کرد.

تشبیه ساده: **Soft IP** مثل دستور پخت غذاست؛ تو هنوز می‌توانی روش پخت، ظرف، زمان و جزئیات را تغییر بدهی. **Hard IP** مثل غذای آمادهٔ بسته‌بندی‌شده است؛ کمتر می‌توانی داخلش را عوض کنی، اما می‌توانی بررسی کنی آیا بسته همان بستهٔ اصلی است یا نه.

---

### 3. Watermarking دقیقاً چه کاری می‌کند و چه کاری نمی‌کند؟

نکتهٔ خیلی مهم: **Watermarking جلوی دزدی را نمی‌گیرد.** یعنی اگر کسی به طراحی دسترسی داشته باشد، watermark به‌تنهایی مانع کپی‌کردن نمی‌شود. کار اصلی watermark این است که بعداً بتوانی **prove ownership** کنی؛ یعنی ثابت کنی یک IP یا بخشی از یک IC از طراحی تو استفاده کرده است. پس watermark بیشتر ابزار **evidence** است، نه ابزار **prevention**.

برای اینکه یک watermark خوب باشد، باید چند ویژگی داشته باشد. اول، باید **hard to detect and remove** باشد. اگر مهاجم به‌راحتی بفهمد امضا کجاست، می‌تواند آن را حذف کند. دوم، باید **non-functional altering** باشد؛ یعنی عملکرد اصلی IP را تغییر ندهد. سوم، نباید **performance degradation** قابل توجه ایجاد کند. چهارم، نباید باعث **unintended information leakage** شود. پنجم، باید **verifiable** باشد؛ یعنی مالک بتواند بعداً وجود signature را اثبات کند.

اینجا یک trade-off داریم. اگر watermark خیلی قوی و واضح باشد، verification آسان‌تر می‌شود، اما احتمال detect و remove شدنش هم بیشتر است. اگر خیلی مخفی باشد، حذفش سخت‌تر است، اما اثبات مالکیت هم سخت‌تر می‌شود. طراحی watermark خوب یعنی پیدا کردن تعادل بین **robustness، invisibility، verifiability** و **low overhead**.

در سخت‌افزار، watermark می‌تواند در سطح‌های مختلف اضافه شود: **System Level، Register Allocation، Synthesis Level، Physical Synthesis / P&R Level** یا حتی runtime behavior مثل **FSM transition، scan chain response** یا **power signature**. هر سطح مزایا و مشکلات خودش را دارد.

---

### 4. Static Watermarking چیست؟

در **Static Watermarking** امضا در زمان طراحی داخل مدار embed می‌شود و بعداً معمولاً با بررسی ساختار مدار یا layout تأیید می‌شود. مثلاً ممکن است بعضی cellها در جای خاصی قرار داده شوند، بعضی constraints به synthesis داده شوند، یا register allocation به شکل خاصی انجام شود. این watermark در رفتار عادی مدار الزاماً دیده نمی‌شود؛ باید ساختار داخلی مدار بررسی شود.

مشکل بزرگ Static Watermarking این است که verification آن سخت و گران است. اگر بخواهیم بفهمیم یک چیپ واقعاً watermark ما را دارد، شاید لازم باشد package باز شود، لایه‌های IC بررسی شوند، microscopic analysis انجام شود و حتی reverse engineering روی layout صورت بگیرد. این کار مخرب، گران و زمان‌بر است. برای محصولات ارزان ارزش ندارد، اما برای IPهای بسیار گران یا litigation حقوقی ممکن است قابل توجیه باشد.

نوع مهم Static Watermarking در این فصل **Constraint-based Watermarking** است. ایده‌اش این است: طراحی دیجیتال معمولاً چندین جواب معتبر دارد. مثلاً یک عملیات را می‌توان در cycle اول یا دوم schedule کرد، یا دو متغیر را می‌توان به registerهای مختلف map کرد، یا cellها را می‌توان در چند جای مختلف قرار داد. اگر این آزادی‌ها را با constraints خاص محدود کنیم، یک signature پنهان ایجاد می‌شود.

مثلاً فرض کن در یک جدول امتحان، چند درس را می‌توان در روزهای مختلف برگزار کرد بدون اینکه تداخل ایجاد شود. اگر من عمداً درس‌ها را بر اساس یک الگوی خاص بچینم، برنامه هنوز درست است، اما داخل آن یک امضا وجود دارد. در مدار هم همین کار را می‌کنیم: functionality همان می‌ماند، اما choiceهای طراحی طوری انتخاب می‌شوند که یک signature بسازند.

---

### 5. Constraint-based Watermarking در System Level

در **System Level Watermarking**، امضا با تغییر schedule عملیات‌ها embed می‌شود. فرض کن یک مدار باید چند عملیات مثل جمع، ضرب یا انتقال داده را در چند clock cycle انجام دهد. بعضی عملیات‌ها الزاماً باید در یک cycle خاص انجام شوند، اما بعضی دیگر می‌توانند زودتر انجام شوند و نتیجه‌شان نگه داشته شود، یا کمی دیرتر انجام شوند، بدون اینکه خروجی نهایی تغییر کند.

این آزادی را **degree of freedom** می‌نامیم. اگر عملیات C1 در cycle دوم لازم باشد اما بتوان آن را در cycle اول انجام داد و نتیجه را freeze کرد، یعنی یک انتخاب داریم. همین انتخاب می‌تواند یک بیت از watermark را نشان دهد. مثلاً اگر عملیات را زودتر schedule کردیم یعنی 1، اگر در جای اصلی گذاشتیم یعنی 0. البته در عمل پیچیده‌تر است، اما ایده همین است.

این روش معمولاً functionality را تغییر نمی‌دهد، چون ترتیب وابستگی‌های واقعی داده هنوز رعایت می‌شود. اما schedule ظاهری و ساختار داخلی طراحی خاص می‌شود. اگر کسی همان algorithm را از صفر synthesize کند، احتمال اینکه دقیقاً همین schedule خاص را تولید کند پایین است؛ بنابراین این schedule می‌تواند به‌عنوان signature استفاده شود.

تلهٔ مهم: در این روش ما عملیات را خودسرانه جابه‌جا نمی‌کنیم. فقط operationهایی را تغییر می‌دهیم که از نظر data dependency و timing اجازه دارند. اگر عملیاتی وابسته به نتیجهٔ operation قبلی باشد، نمی‌توان آن را قبل از تولید داده اجرا کرد.

---

### 6. Register Allocation Watermarking و Graph Coloring

در **Register Allocation**، مسئله این است که متغیرهای موقت یک algorithm در چه registerهایی ذخیره شوند. هر temporary variable یک **lifetime** دارد: از لحظه‌ای که تولید می‌شود تا لحظه‌ای که آخرین بار استفاده می‌شود. اگر دو variable lifetime هم‌پوشان نداشته باشند، می‌توانند از یک register مشترک استفاده کنند. اگر lifetime آن‌ها هم‌پوشان داشته باشد، نمی‌توانند یک register داشته باشند، چون هم‌زمان به مقدارشان نیاز داریم.

این مسئله شبیه **Graph Coloring Problem** است. هر variable یک node است. اگر دو variable نتوانند یک register مشترک داشته باشند، بین آن‌ها edge می‌گذاریم. حالا رنگ‌ها نشان‌دهندهٔ registerها هستند. دو node که edge دارند، نباید رنگ یکسان بگیرند. چون Graph Coloring معمولاً چند جواب ممکن دارد، می‌توان با اضافه کردن constraints جدید، جواب را به سمت یک الگوی خاص هدایت کرد.

برای watermarking، می‌توانیم edgeهای اضافی اضافه کنیم. مثلاً فایل مثالی می‌دهد که برای embed کردن یک signature مثل `10000010110111` قانون می‌گذاریم:
**Embed 0 if the new connection connects to an even node**
**Embed 1 if the new connection connects to an odd node**

یعنی با اضافه کردن اتصال‌های خاص در graph، register allocation را به شکلی محدود می‌کنیم که signature در آن پنهان شود. مدار هنوز درست کار می‌کند؛ فقط allocation داخلی registerها یک امضای خاص دارد. این روش خوب است چون در سطح ساختار طراحی signature می‌سازد، نه در خروجی functional مدار.

---

### 7. Synthesis-level Watermarking و Critical Path

در **Synthesis Level Watermarking**، امضا هنگام logic synthesis اضافه می‌شود. اینجا باید خیلی مراقب **Critical Path** باشیم. **Critical Path** طولانی‌ترین مسیر combinational بین دو register است و تعیین می‌کند مدار با چه حداکثر فرکانسی می‌تواند کار کند. اگر watermark روی critical path اضافه شود، delay زیاد می‌شود و clock frequency کاهش پیدا می‌کند. این بد است.

پس ایدهٔ اصلی این روش: watermark را فقط در **non-critical paths** قرار بده. مسیرهای non-critical کوتاه‌ترند و slack دارند؛ یعنی کمی تغییر در آن‌ها maximum frequency را خراب نمی‌کند. مثلاً اگر critical path delay برابر 9ns باشد و یک مسیر دیگر 5ns delay داشته باشد، شاید بتوانی با اضافه کردن کمی logic آن را به 6ns برسانی، بدون اینکه maximum clock تغییر کند.

در فایل روش **K-M-Macrocell Mapping Approach** توضیح داده شده است. خلاصهٔ ایده این است که بعد از logic minimization، مرحلهٔ **Technology Mapping** باید مدار را به بلوک‌هایی با محدودیت‌های خاص map کند: مثلاً هر macrocell حداکثر **K inputs** و حداکثر **M product terms** داشته باشد. در مسیرهای non-critical، بعضی signalها انتخاب می‌شوند، mapping قبلی برداشته می‌شود، signalها به ورودی/خروجی‌های جدید شکسته می‌شوند، mapping دوباره اجرا می‌شود و سپس signalها patch می‌شوند.

نتیجه این است که functionality ثابت می‌ماند، اما mapping داخلی مدار خاص می‌شود. حتی اگر circuit، synthesizer، constraints و library یکی باشند، break و remap هدفمند می‌تواند یک نتیجهٔ unique تولید کند. این uniqueness همان signature است.

---

### 8. Physical Synthesis / P&R Level Watermarking

در سطح **Physical Synthesis** یا **Place & Route / P&R**، watermark در جایگذاری فیزیکی cellها یا استفاده از منابع فیزیکی embed می‌شود. برای **ASIC**، می‌توان constraint گذاشت که بعضی cellها در rowهای خاص قرار بگیرند. مثلاً sequential cells فقط در rowهای فرد و combinational cells در rowهای زوج قرار بگیرند. البته این فقط مثال است؛ در عمل باید مراقب timing، congestion و power distribution بود.

در **FPGA**، چون معمولاً همهٔ logic blocks استفاده نمی‌شوند، می‌توان از unused blocks برای embed کردن signature استفاده کرد. مثلاً برخی LUTها یا routing resources را به شکل خاص configure کنیم که functionality اصلی را تغییر ندهند اما امضا ایجاد کنند. این روش به ساختار FPGA و ابزار vendor وابسته است.

این سطح بسیار حساس است، چون placement و routing روی delay، power، temperature و حتی reliability اثر می‌گذارند. اگر watermark باعث شود wireها طولانی شوند یا critical netها بد route شوند، performance خراب می‌شود. بنابراین P&R-level watermarking باید با دقت زیاد انجام شود و معمولاً نباید critical timing را تحت تأثیر بگذارد.

---

### 9. Static vs Dynamic Watermarking

فرق اصلی این دو روش در نحوهٔ verification است. در **Static Watermarking** امضا در زمان design اضافه می‌شود و برای بررسی آن معمولاً باید ساختار داخلی circuit یا layout دیده شود. یعنی ممکن است لازم باشد reverse engineering انجام شود. این روش قوی است اما verification آن گران و سخت است.

در **Dynamic Watermarking** امضا باز هم در زمان design embed می‌شود، اما verification در runtime انجام می‌شود. یعنی مدار را با یک input pattern خاص اجرا می‌کنیم و یک output یا behavior خاص می‌بینیم. مزیت این است که نیازی به باز کردن package یا microscopic inspection نداریم. پس verification ارزان‌تر، سریع‌تر و non-destructive است.

مثلاً ممکن است یک مدار در حالت عادی کاملاً معمولی کار کند، اما اگر یک توالی خاص از ورودی‌ها اعمال شود، یک output خاص تولید کند، یا power consumption pattern خاصی نشان دهد. این behavior برای مالک قابل verification است، ولی برای کاربر عادی یا مهاجم نباید واضح باشد.

پس static بیشتر روی **structure** تکیه دارد، dynamic بیشتر روی **behavior** یا **observable response**. در امتحان اگر گزینه‌ای بگوید dynamic watermarking نیاز به physical inspection دارد، معمولاً غلط است.

---

### 10. FSM-based Watermarking

یک **Finite State Machine / FSM** ماشینی است که تعداد محدودی state دارد و طبق inputها از یک state به state دیگر می‌رود. رفتار FSM را می‌توان با **State Transition Graph / STG** نشان داد: stateها node هستند و transitionها edge. چون FSM ذاتاً memory دارد، state فعلی در یک register ذخیره می‌شود و combinational logic، next state و output را تولید می‌کند.

در این فصل دو نوع watermarking بر اساس FSM آمده است:

1. **State-based FSM Watermarking**


   در این روش stateهای اضافی به FSM اضافه می‌کنیم. این stateها برای functionality اصلی لازم نیستند، اما signature را حمل می‌کنند. مشکلش این است که اگر FSM minimization انجام شود، stateهای اضافی ممکن است حذف شوند. همچنین اگر مدار به‌طور واضح به stateهای بیشتری از حد لازم نیاز داشته باشد، watermark مشکوک و قابل شناسایی می‌شود.

2. **Transition-based FSM Watermarking**


   در این روش به‌جای اضافه کردن state جدید، از transitionهای آزاد یا unused input patterns استفاده می‌کنیم. یعنی در STG مسیرهایی اضافه می‌کنیم که فقط با input sequence خاص فعال شوند. این روش معمولاً پنهان‌تر است، مخصوصاً در **Mealy FSM**، چون output می‌تواند به state و input هر دو وابسته باشد و watermark از طریق output خاص آشکار شود.

در فایل همچنین فرق **Mealy FSM** و **Moore FSM** آمده است. در **Mealy FSM**، output به **current state + input** وابسته است. در **Moore FSM**، output فقط به **current state** وابسته است. این تفاوت برای watermarking مهم است، چون transition-based watermarking معمولاً با Mealy راحت‌تر است؛ چون input sequence می‌تواند مستقیماً output خاص تولید کند.

---

### 11. Testing-based Watermarking و Boundary Scan

در تولید IC، testing ضروری است. باید مطمئن شویم چیپ ساخته‌شده درست کار می‌کند. دو نوع testing داریم:

* **Offline Testing:** 

چیپ در محیط test قرار می‌گیرد و بررسی می‌شود.
* **Online Testing:** 

مدار وقتی داخل سیستم نصب شده و در حال کار یا idle است، تست می‌شود.

یکی از روش‌های معروف تست، **Boundary Scan** است. در این روش Flip-Flopهای خاصی به نام **Scan Flip-Flops** استفاده می‌شوند. هر Scan FF معمولاً یک multiplexer دارد تا انتخاب کند ورودی‌اش از مسیر عادی circuit بیاید یا از scan chain. وقتی در **Normal Mode** هستیم، circuit عادی کار می‌کند. وقتی در **Test Mode** هستیم، می‌توانیم از بیرون یک sequence از bitها را وارد زنجیرهٔ scan کنیم و پاسخ مدار را مشاهده کنیم.

در فایل گفته شده:
**TC = 0 → Normal Mode**
**TC = 1 → Test Mode**

برای watermarking، می‌توان connections داخل scan chain را به شکل خاصی تنظیم کرد. اگر کسی بخواهد circuit را clone کند، باید دقیقاً همان pattern اتصال Flip-Flopها را هم replicate کند. پس scan chain خودش می‌تواند signature شود. مزیت این روش این است که verification از طریق test interface ممکن است و نیازی به باز کردن چیپ ندارد.

اما تله: Boundary Scan در اصل برای testing طراحی شده، نه watermarking. Watermarking از این infrastructure استفاده می‌کند. پس اگر گزینه‌ای گفت scan chain فقط برای watermarking ساخته شده، غلط است.

---

### 12. Side-Channel-based Watermarking

در **Side-Channel-based Watermarking**، امضا از طریق یک اثر جانبی قابل اندازه‌گیری مثل **Power Consumption** embed می‌شود. یعنی مدار در حالت عادی کار خودش را انجام می‌دهد، اما اگر input sequence خاصی اعمال شود، یک power signature خاص تولید می‌کند. مالک می‌تواند با اندازه‌گیری توان مصرفی بفهمد watermark وجود دارد یا نه.

این روش به‌ظاهر عجیب است، چون در امنیت معمولاً side channel چیز بدی است. مهاجم از power یا timing برای استخراج secret استفاده می‌کند. اما اینجا خود طراح عمداً یک side-channel کنترل‌شده ایجاد می‌کند تا signature قابل verification باشد. البته باید بسیار مراقب باشد که این leakage اطلاعات حساس را لو ندهد و power overhead هم زیاد نشود.

در فایل تأکید شده که signature باید **below the noise floor** یا حداقل طوری باشد که در normal operation آشکار و مخرب نباشد. اگر power signature خیلی بزرگ باشد، هم مصرف توان را خراب می‌کند، هم ممکن است توسط attacker شناسایی و حذف شود. اگر خیلی کوچک باشد، verification سخت می‌شود. باز هم trade-off داریم.

دو مثال اصلی این بخش:

* **Ring Oscillator-based Watermark**
* **Leakage Circuit-based Watermark**

---

### 13. CMOS Power Consumption و ارتباطش با Watermarking

برای فهم Side-Channel Watermarking باید مصرف توان CMOS را بفهمیم. در فایل سه نوع power آمده است:

1. **Switching Power**


   وقتی خروجی gateها تغییر می‌کند، ظرفیت‌های خازنی شارژ و دشارژ می‌شوند. این شارژ/دشارژ انرژی مصرف می‌کند. فرمول انرژی هر charge/discharge تقریباً:
   **E = C × Vdd²**

   و توان دینامیک:
   **Pdyn = α × f × C × Vdd²**
   که در آن **α** احتمال switching، **f** فرکانس، **C** ظرفیت و **Vdd** ولتاژ تغذیه است.

2. **Short-Circuit Power**


   هنگام transition، برای زمان کوتاهی ممکن است nMOS و pMOS هر دو روشن باشند و مسیر مستقیم از Vdd به GND ایجاد شود. این باعث current spike می‌شود.

3. **Leakage Power**


   حتی وقتی transistor خاموش است، جریان‌های کوچک parasitic وجود دارند، مخصوصاً در فناوری‌های کوچک‌تر. این را **Sub-threshold leakage** یا device leakage می‌نامیم.

برای watermarking، می‌توانیم عمداً circuitی اضافه کنیم که فقط با trigger خاص active شود و power pattern خاصی بسازد. اما باید مراقب باشیم که این power consumption functionality یا thermal constraints را خراب نکند.

---

### 14. Ring Oscillator و Leakage Circuit

یک **Ring Oscillator** از تعداد فردی inverter به‌صورت حلقه ساخته می‌شود. اگر تعداد inverterها فرد باشد، خروجی هرگز به حالت پایدار نمی‌رسد و نوسان می‌کند. اگر تعداد inverterها زوج باشد، مدار می‌تواند stable شود و oscillation رخ نمی‌دهد. فرکانس oscillation به تعداد inverterها و **Propagation Delay** هر inverter بستگی دارد.

برای watermarking، می‌توان ring oscillator را فقط با یک input pattern خاص فعال کرد. وقتی فعال می‌شود، switching زیادی تولید می‌کند و power consumption بالا می‌رود. این افزایش توان می‌تواند signature باشد. مالک pattern خاص را اعمال می‌کند و power trace را بررسی می‌کند.

در **Leakage Circuit-based Watermarking**، مدار اضافه‌شده در حالت عادی خاموش یا کم‌اثر است. اما اگر یک pattern خاص وارد شود، leakage یا consumption خاصی تولید می‌کند. مثلاً وقتی watermark bit برابر 1 باشد مصرف اضافی ایجاد کند، و وقتی 0 باشد ایجاد نکند. این روش می‌تواند با **LFSR / Linear Feedback Shift Register** یا input masking پیچیده‌تر شود.

در **Spread Spectrum Based Watermarking** از LFSR استفاده می‌شود. **LFSR** زنجیره‌ای از Flip-Flopهاست که با XOR feedback تولید sequence می‌کند. اگر n-bit باشد، حداکثر **2ⁿ − 1** state غیرصفر می‌تواند تولید کند؛ اما فقط اگر polynomial مربوطه **Primitive Polynomial** باشد. اگر LFSR با همهٔ صفرها initialize شود، دیگر تغییر نمی‌کند، چون XOR صفرها باز هم صفر است. این یکی از تله‌های تستی قطعی است.

---

# گام ۲: خلاصه تستی و مقایسه‌ای

## Bilingual Cheat-Sheet

## IP Types

| Concept                        | توضیح تستی                                        |
| ------------------------------ | ------------------------------------------------- |
| **IP / Intellectual Property** | بلوک طراحی قابل استفاده مجدد                      |
| **Soft IP**                    | HDL model مثل VHDL/Verilog                        |
| **Hard IP**                    | Layout آمادهٔ IP                                  |
| **IP Designer**                | سازندهٔ IP                                        |
| **System Integrator**          | کسی که IPها را کنار هم قرار می‌دهد                |
| **Foundry**                    | کارخانهٔ ساخت silicon                             |
| **Third-party IP Risk**        | احتمال backdoor، piracy، leakage، licensing issue |

---

## Watermarking Basics

| ویژگی                       | معنی                                         |
| --------------------------- | -------------------------------------------- |
| **Watermarking**            | embed کردن signature مخفی در IP              |
| **Main goal**               | اثبات ownership یا unauthorized use          |
| **Does not prevent piracy** | جلوی دزدی را مستقیماً نمی‌گیرد               |
| **Verifiable**              | مالک باید بتواند وجود signature را اثبات کند |
| **Hard to remove**          | حذف watermark باید سخت باشد                  |
| **Non-alteration**          | functionality نباید تغییر کند                |
| **Low overhead**            | performance/power/area نباید زیاد خراب شود   |
| **No unintended leakage**   | نباید secret یا اطلاعات حساس لو برود         |

---

## Static vs Dynamic Watermarking

| Feature                     | **Static Watermarking**       | **Dynamic Watermarking**                        |
| --------------------------- | ----------------------------- | ----------------------------------------------- |
| زمان embed                  | Design time                   | Design time                                     |
| روش verification            | بررسی structure/layout        | اجرای pattern و مشاهده response                 |
| هزینه verification          | زیاد                          | کمتر                                            |
| نیاز به physical inspection | معمولاً بله                   | معمولاً نه                                      |
| مثال                        | Constraint-based watermarking | FSM, testing, side-channel                      |
| مزیت                        | قوی در ساختار طراحی           | non-destructive و runtime-verifiable            |
| عیب                         | expensive/intrusive           | باید trigger و response مخفی و قابل اعتماد باشد |

---

## Constraint-based Static Watermarking Levels

| Level                        | روش embed کردن signature                               |
| ---------------------------- | ------------------------------------------------------ |
| **System Level**             | تغییر scheduling عملیات‌های دارای freedom              |
| **Register Allocation**      | محدودکردن graph coloring با edgeهای اضافی              |
| **Synthesis Level**          | تغییر mapping در non-critical paths                    |
| **Physical Synthesis / P&R** | constraint روی placement/routing یا unused FPGA blocks |

---

## Critical Path

| Concept                 | نکته                                              |
| ----------------------- | ------------------------------------------------- |
| **Critical Path**       | طولانی‌ترین مسیر combinational                    |
| **Determines**          | maximum clock frequency                           |
| **Watermark placement** | بهتر است روی **non-critical paths** باشد          |
| **Wrong placement**     | اگر روی critical path باشد، performance کم می‌شود |

---

## Register Allocation Watermarking

| Keyword               | معنی                                                                        |
| --------------------- | --------------------------------------------------------------------------- |
| **Variable Lifetime** | مدت زمانی که یک temporary variable لازم است                                 |
| **Register Sharing**  | اگر lifetimeها overlap نکنند، دو variable می‌توانند یک register داشته باشند |
| **Graph Coloring**    | متغیرها node، محدودیت‌ها edge، registerها color                             |
| **Extra Edges**       | برای embed کردن signature اضافه می‌شوند                                     |

---

## FSM Watermarking

| Type                                  | توضیح                          | ضعف/مزیت                                                   |
| ------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| **State-based FSM Watermarking**      | اضافه کردن stateهای اضافی      | ساده ولی vulnerable to state minimization                  |
| **Transition-based FSM Watermarking** | استفاده از transitionهای آزاد  | مخفی‌تر و مناسب‌تر برای Mealy FSM                          |
| **Mealy FSM**                         | output وابسته به state و input | مناسب برای transition-based watermark                      |
| **Moore FSM**                         | output فقط وابسته به state     | behavior ساده‌تر ولی انعطاف کمتر برای transition watermark |

---

## Testing-based Watermarking

| Concept             | نکته                                       |
| ------------------- | ------------------------------------------ |
| **Offline Testing** | تست در محیط تست                            |
| **Online Testing**  | تست در حالی که مدار در سیستم است           |
| **Boundary Scan**   | زنجیره‌ای از Scan FFها برای inject/observe |
| **Scan FF**         | Flip-Flop با MUX برای normal/test input    |
| **TC = 0**          | Normal Mode                                |
| **TC = 1**          | Test Mode                                  |
| **Watermark idea**  | اتصال خاص scan chain به‌عنوان signature    |

---

## Side-Channel-based Watermarking

| Concept                  | توضیح                                                   |
| ------------------------ | ------------------------------------------------------- |
| **Power Signature**      | امضا از طریق power consumption قابل اندازه‌گیری         |
| **Ring Oscillator**      | تعداد فرد inverterها باعث oscillation می‌شود            |
| **Leakage Circuit**      | مدار اضافه که با trigger خاص power/leakage ایجاد می‌کند |
| **Below Noise Floor**    | watermark نباید خیلی واضح یا مخرب باشد                  |
| **LFSR**                 | Linear Feedback Shift Register                          |
| **Primitive Polynomial** | شرط رسیدن LFSR به sequence با طول حداکثری               |
| **All-zero LFSR state**  | گیر می‌کند و تغییر نمی‌کند                              |

---

## CMOS Power

| Power Type                  | علت                                                                           |
| --------------------------- | ----------------------------------------------------------------------------- |
| **Switching Power**         | charge/discharge شدن capacitance                                              |
| **Short-circuit Power**     | لحظه‌ای روشن بودن nMOS و pMOS باهم                                            |
| **Leakage Power**           | جریان‌های parasitic حتی در حالت off                                           |
| **Pdyn = α × f × C × Vdd²** | توان دینامیک وابسته به switching probability، frequency، capacitance، voltage |

---

# گام ۳: تله‌های امتحانی

## Pitfalls

### 1. Watermarking جلوی piracy را نمی‌گیرد

گزینه‌ای که بگوید **Watermarking prevents IP piracy** دقیق نیست. Watermarking معمولاً برای **proving ownership** است، نه جلوگیری مستقیم از دزدی.

---

### 2. Watermark نباید functionality را تغییر دهد

در multimedia تغییر جزئی pixel قابل قبول است. در IC، تغییر functionality قابل قبول نیست. اگر watermark output عادی مدار را تغییر دهد، طراحی خراب است.

---

### 3. Static و Dynamic را قاطی نکن

* **Static:** verification معمولاً با بررسی ساختار، layout، reverse engineering
* **Dynamic:** verification با input pattern و runtime response

---

### 4. Static watermarking ممکن است verification گران داشته باشد

اگر گزینه‌ای بگوید static watermarking همیشه cheap و non-invasive است، غلط است. ممکن است نیاز به depackaging و microscopic analysis داشته باشد.

---

### 5. Dynamic watermarking هم در design time embed می‌شود

تله: dynamic یعنی watermark بعداً runtime چک می‌شود، نه اینکه در runtime ساخته شود.

---

### 6. Register Allocation Watermarking یعنی اضافه کردن registerهای تصادفی نیست

ایده اصلی کنترل **mapping variables to registers** با استفاده از **lifetime** و **graph coloring** است.

---

### 7. Critical path را نباید دستکاری کرد

در synthesis-level watermarking باید non-critical pathها را هدف گرفت. دستکاری critical path ممکن است maximum frequency را کاهش دهد.

---

### 8. State-based FSM watermarking آسیب‌پذیر است

اگر stateهای اضافی برای functionality لازم نباشند، ممکن است با **state minimization** حذف شوند یا قابل تشخیص باشند.

---

### 9. Transition-based FSM watermarking معمولاً مخفی‌تر است

چون از unused transitions یا input patterns استفاده می‌کند و لزوماً state اضافی واضح ایجاد نمی‌کند.

---

### 10. Mealy و Moore را حفظ کن

* **Mealy:** output = f(current state, input)
* **Moore:** output = f(current state)

گزینه‌ای که این دو را جابه‌جا کند، غلط است.

---

### 11. Boundary Scan

 برای testing است، نه فقط watermarking

Watermarking

 از scan chain سوءاستفاده/استفادهٔ خلاقانه می‌کند. اصل boundary scan برای testability است.

---

### 12. TC

 را اشتباه نکن

طبق فایل:

* **TC = 0 → Normal Mode**
* **TC = 1 → Test Mode**

---

### 13. Ring Oscillator

 با تعداد فرد inverter نوسان می‌کند

تعداد **odd** از inverterها → oscillation
تعداد **even** → معمولاً stable می‌شود

---

### 14. LFSR

 با all-zero گیر می‌کند

اگر تمام stateهای LFSR صفر باشند، XOR feedback هم صفر می‌ماند. پس sequence شروع نمی‌شود.

---

### 15. هر LFSR به 2ⁿ − 1 state نمی‌رسد

فقط LFSRهایی که بر اساس **Primitive Polynomial** ساخته شده‌اند می‌توانند maximal-length sequence تولید کنند.

---

# گام ۴: خودآزمایی

## 100% English Quiz

### Question 1

**What is the primary purpose of hardware watermarking in IP-based design?**

A. To prevent any attacker from copying the IP.
B. To prove ownership or unauthorized use of an IP by embedding a hidden signature.
C. To increase the maximum clock frequency of the circuit.
D. To replace all third-party IPs with open-source components.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** **Watermarking** معمولاً جلوی کپی‌برداری را مستقیماً نمی‌گیرد؛ فقط بعداً می‌تواند برای اثبات مالکیت استفاده شود.
* **B درست است:** هدف اصلی watermark، قرار دادن **hidden signature** برای **ownership proof** یا اثبات استفادهٔ غیرمجاز است.
* **C غلط است:** watermark برای افزایش فرکانس نیست؛ حتی اگر بد طراحی شود ممکن است performance را کاهش دهد.
* **D غلط است:** بحث جایگزینی third-party IP با open-source IP نیست.

---

### Question 2

**Which statement correctly distinguishes static watermarking from dynamic watermarking?**

A. Static watermarking is verified by applying input patterns at runtime, while dynamic watermarking requires microscopic inspection.
B. Static watermarking usually embeds a structural signature that may require physical or layout inspection, while dynamic watermarking can be verified through runtime behavior.
C. Dynamic watermarking is added only after fabrication, while static watermarking is added only during testing.
D. Static watermarking always changes the circuit functionality, while dynamic watermarking never affects the design.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** این گزینه static و dynamic را برعکس کرده است.
* **B درست است:** **Static Watermarking** بیشتر structural است و ممکن است reverse engineering لازم داشته باشد؛ **Dynamic Watermarking** با pattern و behavior قابل verification است.
* **C غلط است:** dynamic هم معمولاً در design time embed می‌شود؛ فقط verification آن runtime است.
* **D غلط است:** هیچ watermark خوبی نباید functionality را تغییر دهد؛ این ویژگی فقط مخصوص dynamic نیست.

---

### Question 3

**In FSM-based watermarking, why is transition-based watermarking often harder to detect than state-based watermarking?**

A. Because it removes all states from the FSM.
B. Because it uses unused input patterns or transitions without necessarily adding obvious extra states.
C. Because it works only on Moore machines and ignores inputs.
D. Because it requires no state register.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** FSM بدون state معنی ندارد؛ transition-based watermarking stateها را حذف نمی‌کند.
* **B درست است:** این روش از **unused transitions** یا **free input patterns** استفاده می‌کند و معمولاً state اضافی واضح ایجاد نمی‌کند.
* **C غلط است:** در فایل گفته شده transition-based watermarking مخصوصاً برای **Mealy FSM** مناسب است، نه فقط Moore.
* **D غلط است:** FSM همچنان به **state register** نیاز دارد.

---

### Question 4

**Which condition is required for a ring oscillator to oscillate?**

A. It must contain an even number of inverters.
B. It must contain an odd number of inverters.
C. It must contain no inverters.
D. It must be implemented only with flip-flops.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** تعداد زوج inverterها معمولاً باعث حالت پایدار می‌شود، نه oscillation.
* **B درست است:** **Ring Oscillator** با تعداد فرد inverterها نوسان می‌کند.
* **C غلط است:** بدون inverter، ring oscillator کلاسیک ساخته نمی‌شود.
* **D غلط است:** ring oscillator از inverterهای combinational ساخته می‌شود، نه الزاماً flip-flop.

---

### Question 5

**In side-channel-based watermarking, what is a major design concern?**

A. The watermark must significantly change the normal output values.
B. The watermark should create a detectable signature without impairing functionality or causing unacceptable power overhead.
C. The watermark must always be placed on the critical path.
D. The watermark should disable all leakage currents.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** output عادی نباید تغییر کند.
* **B درست است:** در **Side-Channel-based Watermarking** باید signature قابل verification باشد، اما functionality، power و performance را خراب نکند.
* **C غلط است:** قرار دادن watermark روی critical path خطرناک است و ممکن است timing را خراب کند.
* **D غلط است:** leakage currents بخشی از رفتار فیزیکی‌اند؛ هدف watermarking حذف همهٔ leakage نیست.

---

### Question 6

**Which statement about an n-bit LFSR is correct?**

A. If initialized to all zeros, it will always generate the maximum-length sequence.
B. Every n-bit LFSR always cycles through exactly 2ⁿ states.
C. An LFSR initialized to all zeros remains stuck in the all-zero state.
D. Primitive polynomials prevent any state transition in an LFSR.

**Correct Answer: C**

**تحلیل گزینه‌ها:**

* **A غلط است:** all-zero state در LFSR گیر می‌کند و sequence تولید نمی‌کند.
* **B غلط است:** حتی maximal LFSR معمولاً حداکثر **2ⁿ − 1** non-zero states دارد، نه 2ⁿ.
* **C درست است:** چون XOR صفرها صفر می‌شود، LFSR از all-zero خارج نمی‌شود.
* **D غلط است:** **Primitive Polynomial** دقیقاً برای تولید sequence طولانی‌تر و maximal استفاده می‌شود، نه جلوگیری از transition.
