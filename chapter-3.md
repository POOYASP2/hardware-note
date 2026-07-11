برای فایل **chapter-3.pdf** این ساختار را اجرا می‌کنم. محور فصل از **Flip-Flop به Register**، سپس **VLSI Design Styles، RTL/HDL Design Flow، Logic Synthesis، Netlist، Place & Route، FPGA Bitstream** و در آخر **IP-based Design** است. 

---

# گام ۱: آموزش مفهومی و مفصل

## Deep Explanation — From Flip-Flop to Registers, Design Flow, and IPs

### 1. از Flip-Flop به Register

در فصل قبل فهمیدیم که یک **D-type Flip-Flop** می‌تواند فقط **یک بیت** داده را ذخیره کند. یعنی اگر ورودی آن `D` باشد، در لحظهٔ مناسب clock مقدار آن را می‌گیرد و در خروجی `Q` نگه می‌دارد. حالا اگر بخواهیم به‌جای یک بیت، مثلاً ۴ بیت، ۸ بیت یا ۳۲ بیت ذخیره کنیم، یک Flip-Flop کافی نیست. برای هر بیت یک Flip-Flop لازم داریم. پس یک **Register** در ساده‌ترین تعریف، مجموعه‌ای از چند **D-type Flip-Flop** است که هم‌زمان کار می‌کنند.

در فایل گفته شده که یک **Parallel Register** برای دادهٔ n بیتی، از n عدد Flip-Flop ساخته می‌شود. همهٔ این Flip-Flopها معمولاً **Clock** و **Reset** مشترک دارند، اما هرکدام روی بیت خودش کار می‌کند. مثلاً در یک **4-bit register**، بیت صفر وارد Flip-Flop اول می‌شود، بیت یک وارد Flip-Flop دوم، و همین‌طور تا بیت سوم. وقتی لبهٔ clock می‌آید، همهٔ آن‌ها هم‌زمان sample می‌کنند. پس Register مثل یک گروه از دوربین‌هاست که همگی دقیقاً در یک لحظه از صحنه عکس می‌گیرند؛ هر دوربین فقط یک بخش از تصویر را ثبت می‌کند.

نکتهٔ مفهومی مهم: **Register چیز جدیدی جدا از Flip-Flop نیست؛ گسترش Flip-Flop برای چند بیت است.** هر ویژگی‌ای که برای Flip-Flop داشتیم، برای Register هم داریم: **Setup Time، Hold Time، Clock-to-Output Delay، Reset، Metastability** و غیره. اگر ورودی یک بیت نزدیک لبهٔ clock ناپایدار باشد، همان بیت ممکن است مشکل پیدا کند. اگر reset بد طراحی شود، مقدار کل register می‌تواند نامعلوم باشد.

از نگاه طراحی دیجیتال، Registerها پایهٔ **RTL / Register Transfer Level** هستند. اسم RTL دقیقاً از همین‌جا می‌آید: طراحی در سطحی که در آن داده‌ها بین Registerها منتقل می‌شوند و بین این Registerها مدارهای **Combinational Logic** قرار می‌گیرند. مثلاً در مثال فایل، دو ورودی A و B ابتدا وارد Register می‌شوند، سپس وارد یک **4-bit Adder** می‌شوند، و خروجی sum دوباره در یک Register ذخیره می‌شود. این مدل، مدل اصلی طراحی پردازنده‌ها، کنترلرها، شتاب‌دهنده‌ها و اکثر مدارهای دیجیتال مدرن است.

---

### 2. چرا FPGA به‌جای NAND/NOR از LUT استفاده می‌کند؟

در طراحی‌های قدیمی‌تر یا ساده‌تر، می‌توانستیم منطق را با گیت‌هایی مثل **NAND، NOR، NOT** بسازیم. از نظر تئوری، این کافی است؛ چون **NAND** یا **NOR** می‌تواند هر تابع Boolean را بسازد. اما در **FPGA**، هدف این نیست که برای هر تابع کوچک، فیزیک گیت‌ها را از نو بسازیم. FPGA باید بعد از تولید قابل برنامه‌ریزی باشد. یعنی کارخانه یک آرایهٔ آماده از سلول‌ها می‌سازد و کاربر بعداً تصمیم می‌گیرد آن سلول‌ها چه منطقی را پیاده کنند.

به همین دلیل، FPGAهای مدرن از **Lookup Table / LUT** استفاده می‌کنند. یک LUT را مثل یک جدول کوچک در نظر بگیر. اگر LUT چهار ورودی داشته باشد، می‌تواند برای هر ترکیب ورودی، خروجی دلخواه را ذخیره کند. پس به‌جای اینکه تابع را با چند NAND و NOR فیزیکی بسازی، خروجی تابع را در یک جدول برنامه‌پذیر قرار می‌دهی. این باعث می‌شود FPGA انعطاف‌پذیر باشد: امروز یک LUT می‌تواند AND باشد، فردا XOR، بعداً MUX، یا هر تابع Boolean دیگری با تعداد ورودی محدود.

در شکل صفحهٔ اول، **Basic Cell of an Intel-Altera FPGA** نشان داده شده است. این سلول فقط LUT ندارد؛ داخل آن **Full Adder، Multiplexer** و **Flip-Flop / Register** هم دیده می‌شود. دلیل وجود **Full Adder** این است که addition یکی از رایج‌ترین عملیات‌ها در مدارهای دیجیتال است. اگر جمع را فقط با LUT بسازیم، کندتر و پرهزینه‌تر می‌شود. پس سازندهٔ FPGA مسیرهای خاص و سریع برای عملیات جمع قرار می‌دهد تا performance بهتر شود.

پس در امتحان باید این را بفهمی: FPGA یک ساختار کاملاً آزاد مثل custom silicon نیست. ساختار آن از قبل توسط vendor ساخته شده، اما قابل configuration است. تو با طراحی HDL نمی‌گویی «این ترانزیستور را اینجا بگذار»؛ می‌گویی «این تابع را می‌خواهم»، بعد ابزار تصمیم می‌گیرد کدام LUT، کدام Flip-Flop، کدام multiplexer و کدام routing resource استفاده شود.

---

### 3. سه سبک اصلی طراحی: Custom Design، Semi-Custom Design، PLD/FPGA

در **Custom Design**، طراح کنترل کامل دارد. یعنی اندازه و مکان هر transistor، مسیر هر wire، و جزئیات layout دستی یا بسیار دقیق کنترل می‌شود. این روش مثل ساختن یک ماشین مسابقه‌ای سفارشی است: همه‌چیز برای بهترین performance تنظیم می‌شود. اما هزینه و زمان طراحی بالاست. برای میلیون‌ها ترانزیستور، طراحی کاملاً custom معمولاً غیرعملی است، مگر برای بخش‌های خیلی حساس مثل memory cells، analog blocks یا مسیرهای بحرانی پردازنده.

در **Semi-Custom Design**، به‌جای طراحی تک‌تک transistorها، از یک **Library of Cells** استفاده می‌شود. این library شامل بلوک‌های آماده مثل **AND، OR، NOT، XOR، D-FF، MUX، Full Adder** و غیره است. این را می‌توانی مثل LEGO تصور کنی. خودت آجرها را نمی‌سازی؛ از آجرهای آماده استفاده می‌کنی و آن‌ها را درست کنار هم می‌گذاری. نتیجه نسبت به Custom کمتر optimized است، ولی طراحی بسیار سریع‌تر و قابل مدیریت‌تر می‌شود.

در **Programmable Logic Devices / PLDs** مثل **FPGA**، حتی cells هم قبلاً روی تراشه ساخته شده‌اند. تو فقط آن‌ها را برنامه‌ریزی می‌کنی. یعنی نه transistor را می‌سازی، نه standard cell را روی silicon قرار می‌دهی؛ بلکه ساختار آمادهٔ FPGA را configure می‌کنی. این روش برای **Prototyping** بسیار مناسب است. قبل از اینکه میلیون‌ها دلار برای ASIC خرج شود، می‌توان طراحی را روی FPGA تست کرد. عیبش این است که معمولاً FPGA از ASIC کندتر، بزرگ‌تر و پرمصرف‌تر است.

پس این سه روش یک trade-off دارند:
**Custom Design** بیشترین optimization و کمترین flexibility را دارد.
**Semi-Custom Design** تعادل بین performance و زمان طراحی است.
**FPGA/PLD** بیشترین flexibility و سریع‌ترین development را دارد، ولی کمترین optimization را نسبت به ASIC می‌دهد.

---

### 4. RTL، Netlist و مسیر کلی Design Flow

وقتی مدار بزرگ می‌شود، دیگر نمی‌توانیم دستی از RTL به gates برویم. در فایل گفته شده که با افزایش complexity، ترجمهٔ دستی RTL به logic غیرممکن می‌شود و باید از **Computer-Aided Design / CAD Tools** و مخصوصاً **Logic Synthesizer** استفاده کنیم. این ابزارها کاری را انجام می‌دهند که اگر دستی انجام شود، بسیار وقت‌گیر و خطاپذیر است.

برای شروع synthesis، سه چیز لازم داریم:

1. **RTL Description**

   یعنی توضیح اینکه مدار چه کاری باید انجام دهد. این توصیف معمولاً با **HDL** مثل **VHDL، Verilog، SystemVerilog** نوشته می‌شود.

2. **Technology Information**

   اگر ASIC طراحی می‌کنی، این یعنی **Standard Cell Library**، فایل‌های timing، power، area و اطلاعات technology. اگر FPGA طراحی می‌کنی، یعنی اطلاعات بلوک‌های موجود در FPGA که vendor در اختیار ابزار قرار می‌دهد.

3. **Constraints**
 
   یعنی محدودیت‌های طراحی، مثل **Timing، Area، Power**. نمونهٔ مهم آن **SDC / Synopsys Design Constraints** است، مثلاً تعریف clock period با چیزی مثل:
   `create_clock -name MYCLK -period 3.5 CLK`

بعد **Logic Synthesizer** این سه ورودی را می‌گیرد و RTL را به **Netlist** تبدیل می‌کند. **Netlist** لیستی از سلول‌ها و اتصال بین آن‌هاست. مثلاً می‌گوید این D-FF به این wire وصل است، این XOR ورودی‌اش از این سیگنال‌هاست، این Full Adder خروجی‌اش به این Register می‌رود. پس Netlist دیگر یک توصیف رفتاری سطح بالا نیست؛ یک توصیف ساختاری‌تر از مدار است.

---

### 5. HDL زبان برنامه‌نویسی نیست

یکی از تله‌های مهم همین‌جاست. **HDL** مخفف **Hardware Description Language** است، اما نباید آن را مثل Python، Java یا C تصور کرد. در programming language، معمولاً دستورها به‌ترتیب توسط CPU اجرا می‌شوند. اما در HDL، تو داری hardware را توصیف می‌کنی. Hardware ذاتاً **Concurrent** است؛ یعنی چندین بخش مدار هم‌زمان کار می‌کنند.

مثلاً اگر در Verilog چند `assign` داشته باشی، این‌ها مثل چند خط برنامه نیستند که یکی بعد از دیگری اجرا شوند. هر `assign` یک مدار combinational را توصیف می‌کند که همیشه فعال است. همچنین یک `always @(posedge CLK)` معمولاً یک sequential process را توصیف می‌کند، یعنی رفتار Flip-Flop یا Register. در VHDL هم `process(CLK)` با شرط `CLK'event and CLK = '1'` یعنی رخداد روی **Rising Edge**.

در فایل روی جدایی **Interface** و **Implementation** تأکید شده است. Interface یعنی مرز بیرونی مدار: ورودی‌ها، خروجی‌ها، اسم سیگنال‌ها، جهت‌ها و typeها. مثلاً در مثال 4-bit adder، ورودی‌ها **A، B، CLK** و خروجی **S** هستند. Implementation یعنی داخل مدار چه اتفاقی می‌افتد: چه signal داخلی داریم، چه processهایی، چه assignmentهایی، و چه componentهایی instantiate شده‌اند.

در HDL سه روش مهم برای توصیف implementation داریم:

* **Components:** برای hierarchical design؛ یعنی ساختن مدار از بلوک‌های کوچک‌تر.
* **Processes / always blocks:** برای توصیف sequential elements یا decoderهای پیچیده.
* **Assignments:** برای combinational logic ساده، مثل arithmetic و logic functions.

---

### 6. Logic Synthesizer، Liberty File و Netlist

**Logic Synthesizer**

 ابزار صنعتی‌ای است که RTL را به ساختار واقعی‌تر مدار تبدیل می‌کند. اما synthesizer بدون دانستن سلول‌های موجود نمی‌تواند تصمیم بگیرد. برای ASIC، اطلاعات سلول‌ها معمولاً در فایل **Liberty File / .lib** قرار دارد. این فایل اطلاعاتی مثل **Area، Timing، Power** هر cell را به ابزار می‌دهد. مثلاً برای یک `nand2` می‌گوید چقدر area دارد، delay آن چقدر است، توان مصرفی آن چقدر است.

در FPGA، اطلاعات مشابه معمولاً proprietary و در قالب فایل‌های vendor-specific است. چون FPGA ساختار داخلی خاص خودش را دارد و vendor فقط مقدار لازم از اطلاعات را در اختیار ابزار می‌گذارد. برای ASIC ابزارهایی مثل **YOSYS** وجود دارند؛ برای FPGA ابزارهای vendor مانند **Vivado، Vitis** و مشابه آن استفاده می‌شوند.

مراحل synthesis در فایل این‌گونه است:

1. خواندن **Liberty Description** برای دریافت area/timing/power سلول‌ها.
2. خواندن **HDL Description** و بررسی syntax و logical consistency.
3. تبدیل HDL به یک **Generic RTL Level** شامل adders، registers، multiplexers و غیره.
4. خواندن **Constraints** مثل clock period.
5. map/optimize کردن RTL روی available cells.
6. تولید **Netlist**، معمولاً در قالب Verilog.

نکتهٔ مهم: در Netlist دیگر `process` یا `always` به شکل رفتاری اولیه وجود ندارد. Netlist شامل instanceهای واقعی‌تر از cellهاست. یعنی مثلاً به‌جای اینکه بنویسد “on rising edge store A”، می‌گوید اینجا یک `dff` قرار دارد که ورودی‌اش فلان wire و خروجی‌اش فلان wire است. در فایل هم تأکید شده که processes در netlist وجود ندارند، چون netlist mapping فیزیکی‌تری از مدار است.

---

### 7. از Netlist به Layout: Place & Route

بعد از synthesis، هنوز chip واقعی نداریم. **Netlist** فقط می‌گوید چه سلول‌هایی لازم‌اند و چطور به هم وصل‌اند. اما نمی‌گوید روی silicon دقیقاً کجا قرار بگیرند و wireها از کجا عبور کنند. برای تبدیل Netlist به **Layout** باید مرحلهٔ **Place & Route / P&R** انجام شود.

برای P&R، علاوه بر netlist به اطلاعات هندسی cellها نیاز داریم. این اطلاعات معمولاً در **LEF / Library Exchange Format** قرار دارد. تفاوت مهم:

* **Liberty File:**  

اطلاعات منطقی/زمانی/توان/مساحت cellها را می‌دهد.
* **LEF File:** 

اطلاعات هندسی و فیزیکی layout cellها و layerها را می‌دهد.

در ASIC P&R سه مرحلهٔ اصلی داریم:

1. **Floorplanning**

   تصمیم‌گیری دربارهٔ سازمان کلی chip: محل pins، power supply، ناحیهٔ logic، ناحیهٔ routing و macroها.

2. **Placement**

   قرار دادن standard cellها در مکان‌های مناسب. معمولاً cellها ارتفاع یکسان و عرض متفاوت دارند تا در ردیف‌های منظم کنار هم قرار بگیرند.

3. **Routing**

   اتصال فیزیکی سلول‌های placed شده با metal layers. اینجا باید قوانین spacing، direction و layer constraints رعایت شوند.

در FPGA هم P&R داریم، اما ساده‌تر است. چون ساختار FPGA قبلاً توسط manufacturer ساخته شده است. pins، I/O blocks، LUTها، routing resources و programmable cells از قبل وجود دارند. ابزار فقط باید تصمیم بگیرد کدام منابع استفاده شوند و چطور connectionها برنامه‌ریزی شوند.

---

### 8. Bitstream در FPGA

بعد از اینکه طراحی FPGA از مرحلهٔ Place & Route عبور کرد، هنوز باید خود FPGA فیزیکی برنامه‌ریزی شود. خروجی ابزار یک **Bitstream** است: دنباله‌ای از bitها که طبق قواعد vendor کدگذاری شده‌اند. این bitstream مشخص می‌کند هر LUT چه محتوایی داشته باشد، کدام multiplexer چه مسیری را انتخاب کند، کدام routing switch وصل یا قطع شود، و کدام Flip-Flopها استفاده شوند.

Bitstream

 را معمولاً از طریق USB یا interface دیگر از PC به board منتقل می‌کنند. اینجا یک نکتهٔ امنیتی مهم وجود دارد: اگر کسی bitstream را بدزدد، ممکن است design را clone کند. اگر کسی bitstream را modify کند، می‌تواند عملکرد مدار را تغییر دهد. پس در hardware security، حفاظت از **FPGA Bitstream** یک موضوع جدی است.

در فایل گفته شده که FPGA یا PLD معمولاً حافظه‌ای برای ذخیرهٔ bitstream دارد که می‌تواند **SRAM-based** یا **Flash-based** باشد. در **SRAM-based FPGA** معمولاً configuration volatile است؛ یعنی با قطع power از بین می‌رود و هنگام روشن‌شدن باید دوباره load شود. در **Flash-based FPGA** configuration می‌تواند non-volatile باشد. این تفاوت از نظر امنیت، boot time، reliability و attack surface مهم است.

---

### 9. Intellectual Properties / IPs

در آخر فصل، بحث **IP-based Design** مطرح می‌شود. در طراحی مدرن، شرکت‌ها معمولاً همه‌چیز را از صفر نمی‌سازند. به‌جای آن از بلوک‌های آماده و reusable استفاده می‌کنند که **Intellectual Property / IP** نام دارند. مثال‌ها: **Microprocessor Core، Memory Controller، Hardware Accelerator، Cryptographic Block، Interface/Protocol Controller** و غیره.

این روش باعث افزایش abstraction و کاهش زمان طراحی می‌شود. اگر قرار باشد هر شرکت برای هر پروژه از صفر CPU، memory، bus، USB controller، Ethernet MAC، AES engine و غیره طراحی کند، development بسیار کند و پرهزینه می‌شود. بنابراین IPها مثل قطعات آماده در یک سیستم بزرگ استفاده می‌شوند. سیستم نهایی اغلب شبکه‌ای از IPهاست که با interfaceهای دقیق به هم وصل شده‌اند.

اما این روش چالش امنیتی دارد. اگر IP از **Third-Party Vendor** گرفته شود، باید به منبع آن اعتماد کنیم. IP ممکن است bug داشته باشد، اطلاعات حساس را leak کند، backdoor داشته باشد، licensing مشکل داشته باشد یا با بقیهٔ سیستم سازگار نباشد. اگر IP کاملاً **In-house** باشد، کنترل بیشتری داریم؛ اما زمان و هزینهٔ توسعه بالاتر می‌رود. اگر third-party باشد، time-to-market بهتر می‌شود، ولی risk بیشتر است.

پس پیام امنیتی فصل این است: طراحی سخت‌افزار مدرن فقط طراحی transistor نیست. یک زنجیرهٔ طولانی داریم: **RTL → Synthesis → Netlist → Place & Route → Layout/Bitstream → IP Integration**. در هر مرحله، ابزار، library، vendor، constraint یا IP می‌تواند روی امنیت سیستم اثر بگذارد.

---

# گام ۲: خلاصه تستی و مقایسه‌ای

## Bilingual Cheat-Sheet

## Flip-Flop vs Register

| Concept               | نکتهٔ تستی                                                      |
| --------------------- | --------------------------------------------------------------- |
| **Flip-Flop**         | ذخیرهٔ 1 بیت                                                    |
| **Register**          | مجموعه‌ای از n عدد Flip-Flop برای n بیت                         |
| **Parallel Register** | همهٔ bitها هم‌زمان sample می‌شوند                               |
| **Shared Signals**    | Flip-Flopهای register معمولاً **Clock** و **Reset** مشترک دارند |
| **Per-bit operation** | هر Flip-Flop روی بیت خودش کار می‌کند                            |
| **RTL**               | سطح طراحی مبتنی بر انتقال داده بین Registerها                   |

---

## FPGA Basic Cell

| Element                  | نقش                                                   |
| ------------------------ | ----------------------------------------------------- |
| **LUT / Lookup Table**   | پیاده‌سازی flexible برای combinational functions      |
| **Full Adder**           | جمع سریع‌تر؛ برای performance داخل cell گذاشته می‌شود |
| **Multiplexer / MUX**    | انتخاب مسیر یا خروجی                                  |
| **Flip-Flop / Register** | ذخیرهٔ state                                          |
| **Routing Resources**    | اتصال programmable بین بلوک‌ها                        |

---

## VLSI Design Styles

| Style                  | کنترل طراح | مزیت                         | عیب                      | کاربرد                     |
| ---------------------- | ---------: | ---------------------------- | ------------------------ | -------------------------- |
| **Custom Design**      |  خیلی زیاد | بهترین optimization          | بسیار کند و پیچیده       | بخش‌های critical           |
| **Semi-Custom Design** |      متوسط | تعادل بین سرعت طراحی و کیفیت | کمتر optimized از custom | ASICهای رایج               |
| **PLD / FPGA**         |       کمتر | flexible و مناسب prototyping | کندتر/پرمصرف‌تر از ASIC  | تست، prototype، تغییر سریع |

---

## RTL to Netlist Flow

| مرحله                      | معنی                                    |
| -------------------------- | --------------------------------------- |
| **RTL Description**        | توصیف رفتار مدار با HDL                 |
| **Technology Information** | اطلاعات standard cells یا FPGA blocks   |
| **Constraints**            | محدودیت‌های timing، area، power         |
| **Logic Synthesis**        | تبدیل RTL به Netlist                    |
| **Netlist**                | لیست cellها و connectionها              |
| **Place & Route**          | تبدیل netlist به layout یا FPGA mapping |
| **Bitstream**              | فایل configuration برای FPGA            |

---

## HDL Concepts

| Keyword                    | توضیح                                                       |
| -------------------------- | ----------------------------------------------------------- |
| **HDL**                    | Hardware Description Language، نه programming language عادی |
| **VHDL**                   | verboseتر، interface و architecture جدا                     |
| **Verilog**                | compactتر، با `module`                                      |
| **SystemVerilog**          | گسترش Verilog برای verification و طراحی پیچیده              |
| **Interface**              | ports، directions، types                                    |
| **Implementation**         | components، processes، assignments                          |
| **Process / always block** | معمولاً برای sequential behavior                            |
| **Assignment / assign**    | برای combinational logic                                    |
| **Concurrent execution**   | بخش‌های hardware هم‌زمان فعال‌اند                           |

---

## Synthesis Files

| File/Info                 | استفاده                                    |
| ------------------------- | ------------------------------------------ |
| **Liberty File / .lib**   | area، timing، power اطلاعات cellها         |
| **LEF File**              | geometry، layers، cell shapes برای layout  |
| **SDC**                   | design constraints مثل clock period        |
| **Standard Cell Library** | cells آماده مثل NAND، DFF، MUX             |
| **Vendor FPGA Info**      | اطلاعات proprietary دربارهٔ FPGA resources |

---

## ASIC P&R vs FPGA P&R

| Feature                       | **ASIC P&R**                      | **FPGA P&R**                        |
| ----------------------------- | --------------------------------- | ----------------------------------- |
| Structure                     | از روی silicon planning می‌شود    | ساختار از قبل توسط vendor تعیین شده |
| Floorplanning                 | مهم و گسترده                      | ساده‌تر                             |
| Placement                     | قرار دادن standard cells          | انتخاب LUT/FF/blocks موجود          |
| Routing                       | metal routing واقعی               | programmable routing                |
| Output                        | Layout / GDS-like physical design | Bitstream                           |
| Flexibility after fabrication | کم                                | زیاد                                |

---

## IP-based Design

| مفهوم                          | نکته                                         |
| ------------------------------ | -------------------------------------------- |
| **IP / Intellectual Property** | بلوک reusable برای طراحی                     |
| **Examples**                   | CPU core، memory، accelerator، interface     |
| **Third-party IP**             | سریع‌تر ولی دارای security/licensing risk    |
| **In-house IP**                | کنترل بیشتر ولی هزینه و زمان بیشتر           |
| **Compatibility**              | interface باید دقیق و قابل verification باشد |
| **Security Risk**              | احتمال leak، bug، backdoor، Trojan           |

---

# گام ۳: تله‌های امتحانی

## Pitfalls

### 1. Register را با یک Flip-Flop اشتباه نگیر

یک **Flip-Flop** فقط 1 بیت ذخیره می‌کند.
یک **Register** برای n بیت، n عدد Flip-Flop دارد.

گزینه‌ای که بگوید “a register stores one bit” فقط برای **1-bit register** درست است، نه به‌صورت عمومی.

---

### 2. همهٔ Flip-Flopهای Register مستقل کامل نیستند

هر Flip-Flop بیت خودش را دارد، اما معمولاً **Clock** و **Reset** مشترک دارند. پس register یعنی چند FF که هم‌زمان عمل می‌کنند، نه چند حافظهٔ کاملاً جدا.

---

### 3. FPGA فقط مجموعه‌ای از NAND/NOR نیست

در FPGAهای مدرن، عنصر اصلی **LUT** است، نه NAND/NOR خام.
اگر گزینه‌ای گفت “modern FPGAs implement logic mainly by fixed NAND/NOR arrays”، برای این فصل غلط است.

---

### 4. Full Adder
 داخل FPGA cell برای performance است

Full Adder

 داخل basic cell تصادفی نیست. چون addition زیاد استفاده می‌شود، داشتن مسیر dedicated سریع‌تر از ساختن addition فقط با LUT است.

---

### 5. Custom Design همیشه بهترین انتخاب نیست

درست است که **Custom Design** بسیار optimized است، اما برای مدارهای بزرگ time-consuming و complex است. بنابراین گزینهٔ “custom design is always preferred for all large systems” غلط است.

---

### 6. Semi-Custom و FPGA یکی نیستند

هر دو از بلوک‌های آماده استفاده می‌کنند، اما:

* **Semi-Custom:** با standard cells طراحی و بعد fabricated می‌شود.
* **FPGA/PLD:** ساختار فیزیکی از قبل ساخته شده و بعداً configured می‌شود.

---

### 7. HDL مثل C یا Python sequential نیست

تلهٔ بزرگ: در HDL، چند assignment هم‌زمان سخت‌افزارهای هم‌زمان را توصیف می‌کنند.
گزینه‌ای که بگوید “HDL statements are executed sequentially by a CPU” غلط است.

---

### 8. Interface و Implementation را قاطی نکن

* **Interface:** ورودی/خروجی‌ها، نام‌ها، typeها، directionها.
* **Implementation:** رفتار داخلی، components، processes، assignments.

---

### 9. Process در Netlist باقی نمی‌ماند

در RTL ممکن است **process** یا **always block** داشته باشی.
اما **Netlist** شامل component instances و connections است. پس “netlist contains behavioral processes” معمولاً غلط است.

---

### 10. Liberty و LEF را اشتباه نگیر

* **Liberty / .lib:** timing، area، power
* **LEF:** geometry، layers، physical dimensions

این یکی خیلی تستی است.

---

### 11. Netlist پایان طراحی نیست

Netlist هنوز layout نیست. بعد از netlist باید **Place & Route** انجام شود.
گزینه‌ای که بگوید “after synthesis the chip layout is complete” غلط است.

---

### 12. FPGA P&R خروجی‌اش layout فیزیکی جدید برای ساخت چیپ نیست

در FPGA، chip قبلاً ساخته شده. خروجی نهایی معمولاً **Bitstream** است، نه mask/layout برای foundry.

---

### 13. Bitstream فقط یک فایل معمولی نیست

**Bitstream** عملاً طراحی را روی FPGA configure می‌کند. از نظر security، theft یا tampering آن می‌تواند خطرناک باشد.

---

### 14. IP باعث کاهش زمان می‌شود، اما ریسک امنیتی دارد

**Third-party IP** همیشه خوب نیست. ممکن است backdoor، bug، leakage یا licensing problem داشته باشد.
پس گزینهٔ “third-party IP always reduces risk” غلط است.

---

# گام ۴: خودآزمایی

## 100% English Quiz

### Question 1

**Which statement best describes a parallel register?**

A. A single flip-flop that stores multiple bits by changing its clock frequency.
B. A set of D-type flip-flops that share clock and reset signals and store multiple bits concurrently.
C. A combinational circuit made only of NAND gates.
D. A programmable lookup table used only in FPGAs.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** یک Flip-Flop معمولی فقط یک بیت ذخیره می‌کند. با تغییر فرکانس clock چندبیتی نمی‌شود.
* **B درست است:** **Parallel Register** از چند **D-type Flip-Flop** ساخته می‌شود؛ هر Flip-Flop یک بیت را نگه می‌دارد و همه هم‌زمان sample می‌کنند.
* **C غلط است:** Register مدار sequential است، نه فقط combinational NAND logic.
* **D غلط است:** **LUT** مربوط به FPGA logic implementation است، نه تعریف register.

---

### Question 2

**Which set of inputs is required by a logic synthesizer to translate an RTL design into a netlist?**

A. RTL description, technology information, and design constraints.
B. Only the final layout and the silicon wafer.
C. A bitstream, a flash memory, and an oscilloscope.
D. Only a truth table, without any timing or technology information.

**Correct Answer: A**

**تحلیل گزینه‌ها:**

* **A درست است:** Synthesizer به **RTL Description**، **Technology Information** مثل standard cells یا FPGA resources، و **Constraints** مثل timing/area/power نیاز دارد.
* **B غلط است:** layout بعد از مراحل synthesis و P&R تولید می‌شود؛ ورودی synthesizer نیست.
* **C غلط است:** bitstream خروجی مسیر FPGA بعد از P&R است، نه ورودی logic synthesis.
* **D غلط است:** truth table ممکن است برای یک تابع ساده کافی باشد، اما برای طراحی واقعی RTL، timing و technology information لازم است.

---

### Question 3

**What is the main difference between a Liberty file and a LEF file in ASIC design flow?**

A. A Liberty file describes FPGA bitstream encryption, while a LEF file describes HDL syntax.
B. A Liberty file contains timing, area, and power information, while a LEF file contains geometric and physical layout information.
C. A Liberty file is used only for reset signals, while a LEF file is used only for clock signals.
D. A Liberty file is the final chip layout, while a LEF file is the final netlist.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** این دو فایل مربوط به ASIC library و physical design هستند، نه bitstream encryption یا HDL syntax.
* **B درست است:** **Liberty / .lib** برای timing/area/power سلول‌هاست؛ **LEF** برای geometry، layers و physical dimensions سلول‌هاست.
* **C غلط است:** این فایل‌ها مخصوص reset یا clock نیستند.
* **D غلط است:** Liberty layout نهایی نیست و LEF هم netlist نیست. Netlist لیست اتصال cellهاست؛ layout بعد از P&R ساخته می‌شود.
