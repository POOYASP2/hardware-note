
# گام ۱: آموزش مفهومی و مفصل

## Deep Explanation — Basics of VLSI Design and IP

### 1. چرا اصلاً VLSI Design به وجود آمد؟

**VLSI Design** یعنی **Very Large Scale Integration Design**؛ یعنی طراحی مدارهایی که تعداد بسیار زیادی ترانزیستور را داخل یک **Integrated Circuit / IC** یا همان چیپ جا می‌دهند. وقتی مدار فقط چند ترانزیستور دارد، طراح می‌تواند تقریباً تک‌تک اجزا را دستی کنترل کند. مثل این است که بخواهی یک اتاق کوچک را با چند تکه مبلمان بچینی؛ همه‌چیز قابل کنترل است. اما وقتی مدار شامل میلیون‌ها یا میلیاردها ترانزیستور می‌شود، دیگر طراحی دستی ممکن نیست. اینجا ابزارهای طراحی خودکار مثل **CAD Tools**، کتابخانه‌های آماده، روش‌های **Semi-Custom Design** و مدل‌های سطح بالاتر مثل **HDL/RTL** لازم می‌شوند.

در جزوه مثال خوبی زده شده: پردازنده‌های قدیمی مثل **Intel 4004** تعداد کمی ترانزیستور داشتند و طراحی آن‌ها تا حد زیادی قابل مدیریت دستی بود. اما در پردازنده‌های پیچیده‌تر مثل **Intel 80486** و نسل‌های بعد، دیگر نمی‌توان هر ترانزیستور را جداگانه طراحی کرد. در طراحی مدرن، طراح معمولاً از سطح سیستم شروع می‌کند: اول می‌پرسد سیستم قرار است چه کاری انجام دهد، بعد آن را به ماژول‌ها، گیت‌ها، ترانزیستورها و نهایتاً شکل‌های هندسی روی سیلیکون تبدیل می‌کند.

نکتهٔ مهم این است که در دنیای **VLSI**، یک ترانزیستور فقط یک مفهوم الکتریکی نیست؛ از نگاه ساخت، یعنی مجموعه‌ای از شکل‌های هندسی، عمدتاً مستطیل‌ها، روی لایه‌های مختلف چیپ. عرض و طول این مستطیل‌ها روی رفتار الکتریکی ترانزیستور اثر می‌گذارد. مثلاً **Width / W** و **Length / L** کانال مستقیماً روی مقدار جریان اثر دارند. پس طراحی VLSI ترکیبی از منطق دیجیتال، فیزیک نیمه‌رسانا، هندسه، ابزار طراحی و محدودیت‌های کارخانهٔ ساخت است.

از نظر امنیت سخت‌افزار، این بخش پایه‌ای است؛ چون اگر ندانی یک مدار چطور از ترانزیستور ساخته می‌شود، بعداً درک **Hardware Trojan، Side-Channel Leakage، Fault Injection، Physical Tampering** و حتی **IP Piracy** سخت می‌شود. خیلی از حملات سخت‌افزاری دقیقاً از همین جزئیات فیزیکی و هندسی سوءاستفاده می‌کنند: اینکه ترانزیستور چقدر جریان می‌کشد، چقدر delay دارد، چطور layout شده، و آیا یک بلوک مخفی می‌تواند در طراحی اضافه شود یا نه.

---

### 2. MOSFET چیست و چطور مثل یک کلید کار می‌کند؟

در این فصل، ترانزیستور اصلی که توضیح داده می‌شود **MOSFET** است؛ یعنی **Metal-Oxide-Semiconductor Field-Effect Transistor**. بیشتر مدارهای مجتمع امروزی، مخصوصاً مدارهای دیجیتال، بر پایهٔ **MOSFET** ساخته می‌شوند. یک **MOS Transistor** معمولاً سه پایهٔ معروف دارد: **Gate، Source، Drain**. اما از نظر فیزیکی یک پایهٔ چهارم هم دارد: **Bulk** یا **Body**. در نمودار صفحهٔ اول فایل، همین ساختار دیده می‌شود: یک بدنهٔ نیمه‌رسانا، یک لایهٔ عایق، و یک لایهٔ فلزی یا پلی‌سیلیکونی برای Gate.

تشبیه ساده: MOSFET مثل یک شیر آب کنترل‌شده با ولتاژ است. مسیر آب بین **Source** و **Drain** قرار دارد، و دستگیرهٔ شیر همان **Gate** است. وقتی به Gate ولتاژ مناسب بدهی، یک **Channel** بین Source و Drain شکل می‌گیرد و جریان عبور می‌کند. وقتی Gate ولتاژ مناسب نداشته باشد، کانال از بین می‌رود و جریان قطع می‌شود. پس در دیجیتال، MOSFET اساساً مثل یک **Switch** عمل می‌کند: روشن یا خاموش، یعنی منطق 1 یا 0.

اما یک نکتهٔ بسیار مهم: چرا جریان وارد **Gate** نمی‌شود؟ چون بین Gate و بدنهٔ نیمه‌رسانا یک **Insulator Layer / Oxide Layer** وجود دارد. عایق اجازهٔ عبور جریان مستقیم را نمی‌دهد. بنابراین Gate بیشتر با میدان الکتریکی روی کانال اثر می‌گذارد، نه با تزریق مستقیم جریان. این دقیقاً دلیل نام **Field-Effect Transistor** است: کنترل جریان از طریق میدان الکتریکی.

جریان بین **Drain** و **Source** به دو ولتاژ اصلی وابسته است: **Gate-to-Source Voltage / VGS** و **Drain-to-Source Voltage / VDS**. در جزوه آمده که **VGS** عرض مؤثر کانال را کنترل می‌کند و **VDS** مثل فشار در سیستم آب عمل می‌کند. هرچه VGS مناسب‌تر باشد، کانال بهتر شکل می‌گیرد؛ و هرچه VDS بیشتر باشد، جریان بیشتری تمایل به حرکت دارد. اما این رابطه کاملاً مثل مقاومت ثابت نیست؛ کانال ترانزیستور شبیه یک **Variable Resistor** است.

---

### 3. نقش Geometry: چرا W و L مهم‌اند؟

در طراحی ترانزیستور، دو پارامتر هندسی بسیار مهم‌اند: **Width / W** و **Length / L**. در صفحهٔ دوم و سوم، فرمول جریان **IDS** نشان می‌دهد که جریان به نسبت **W/L** وابسته است. یعنی اگر **W** را زیاد کنیم، مسیر عبور جریان پهن‌تر می‌شود و جریان بیشتری عبور می‌کند. اگر **L** را زیاد کنیم، مسیر طولانی‌تر و مقاوم‌تر می‌شود و جریان کمتر می‌شود.

تشبیه روزمره: فرض کن جریان الکتریکی مثل حرکت ماشین‌ها در یک جاده است. **W** عرض جاده است؛ هرچه جاده پهن‌تر باشد، ماشین‌های بیشتری همزمان عبور می‌کنند. **L** طول مسیر است؛ هرچه مسیر طولانی‌تر باشد، رسیدن سخت‌تر و کندتر می‌شود. پس یک ترانزیستور با **large W** و **small L** معمولاً می‌تواند جریان بیشتری بدهد، اما ممکن است مصرف توان، نشتی و مسائل فیزیکی بیشتری داشته باشد.

در **Triode Region**، ترانزیستور تا حدی مثل مقاومت متغیر رفتار می‌کند. اما وقتی **VDS** به مقدار خاصی برسد، جریان دیگر با افزایش VDS زیاد نمی‌شود و تقریباً ثابت می‌ماند. این حالت **Saturation** نام دارد. در جزوه رابطهٔ مهم آمده:
**VDS,sat = VGS − VT**
یعنی وقتی ولتاژ Drain-to-Source به اختلاف Gate-to-Source و Threshold Voltage برسد، ترانزیستور وارد saturation می‌شود.

در مدار دیجیتال، معمولاً با جزئیات آنالوگ فرمول‌ها کمتر کار می‌کنیم، اما باید نتیجهٔ مفهومی را بلد باشیم: ترانزیستور با ولتاژهای low/high کنترل می‌شود، ولی در واقعیت یک قطعهٔ فیزیکی است، delay دارد، جریان دارد، ظرفیت خازنی دارد و ایده‌آل نیست. همین غیرایده‌آل بودن بعداً در بحث **Propagation Delay، Timing Constraint، Power Consumption** و **Side-Channel Leakage** مهم می‌شود.

---
<div dir="rtl">

### 4. تفاوت nMOS و pMOS

**nMOS Transistor** ترانزیستوری است که جریان اصلی آن توسط الکترون‌ها حمل می‌شود. برای ساخت nMOS معمولاً به یک **P-type substrate** نیاز داریم و دو ناحیهٔ **N+ diffusion** برای Source و Drain ساخته می‌شود. Gate روی یک لایهٔ عایق قرار می‌گیرد. وقتی ولتاژ Gate نسبت به Source به اندازهٔ کافی مثبت باشد، یک کانال الکترونی ایجاد می‌شود و nMOS روشن می‌شود. پس نکتهٔ تستی مهم: **nMOS turns ON with logic 1 at the gate**.

در مقابل، **pMOS Transistor** تقریباً رفتار مکمل nMOS را دارد. در pMOS، حامل‌های مؤثر **holes** هستند و Source/Drain از نوع **P+ diffusion** هستند. برای ساخت pMOS معمولاً در بستر P یک **N-well** ایجاد می‌کنند تا pMOS روی ناحیهٔ N ساخته شود. در pMOS، ترانزیستور وقتی روشن می‌شود که Gate نسبت به Source ولتاژ پایین داشته باشد. پس نکتهٔ تستی: **pMOS turns ON with logic 0 at the gate**.

این مکمل بودن پایهٔ فناوری **CMOS / Complementary MOS** است. در CMOS، از nMOS و pMOS با هم استفاده می‌کنیم تا مدارهایی بسازیم که در حالت پایدار جریان DC کمی مصرف کنند. مثلاً در یک **CMOS Inverter** وقتی ورودی 0 است، pMOS روشن و nMOS خاموش است، پس خروجی به **Vdd** وصل می‌شود و 1 می‌شود. وقتی ورودی 1 است، nMOS روشن و pMOS خاموش است، پس خروجی به **GND** وصل می‌شود و 0 می‌شود.

این طراحی از نظر توان خیلی مهم است. چون در حالت پایدار، معمولاً مسیر مستقیم دائمی از Vdd به GND وجود ندارد. یکی از ترانزیستورها خاموش است و جریان DC خیلی کم است. البته هنگام switching، هر دو ترانزیستور برای مدت کوتاهی ممکن است نیمه‌فعال باشند و همچنین ظرفیت‌های خازنی شارژ و دشارژ می‌شوند؛ بنابراین مصرف توان صفر نیست، اما نسبت به طراحی‌های قدیمی‌تر بسیار بهتر است.

---
</div>



### 5. CMOS Inverter، NAND و NOR

ساده‌ترین مدار CMOS، **CMOS Inverter** است. در این مدار، یک pMOS بالا قرار می‌گیرد و به **Vdd** وصل است؛ یک nMOS پایین قرار می‌گیرد و به **GND** وصل است. Gate هر دو به ورودی وصل می‌شود و Drain آن‌ها خروجی را می‌سازد. این چینش باعث می‌شود اگر **Vin = 0** باشد، pMOS روشن و nMOS خاموش شود، پس **Vout = 1**. اگر **Vin = 1** باشد، nMOS روشن و pMOS خاموش شود، پس **Vout = 0**.

در صفحهٔ ۶ فایل، همین رفتار با دو حالت توضیح داده شده است. نکتهٔ ظریف آن استفاده از **KCL / Kirchhoff’s Current Law** است. وقتی یکی از شاخه‌ها خاموش است، جریان آن صفر است، و در حالت پایدار جریان شاخهٔ دیگر هم به صفر می‌رسد؛ بنابراین افت ولتاژ ایجاد نمی‌شود و خروجی دقیقاً به Vdd یا GND می‌نشیند. یعنی Inverter فقط یک مفهوم منطقی نیست؛ رفتار آن از فیزیک ترانزیستورها و قوانین مدار به دست می‌آید.

با ترکیب چند nMOS و pMOS می‌توان **NAND Gate** و **NOR Gate** ساخت. برای NAND معمولاً nMOSها به صورت **series** در شبکهٔ pull-down قرار می‌گیرند؛ یعنی خروجی فقط وقتی به زمین وصل می‌شود که همهٔ nMOSها روشن باشند. پس خروجی فقط وقتی 0 می‌شود که همهٔ ورودی‌ها 1 باشند. از طرف دیگر، pMOSها به صورت complementary قرار می‌گیرند تا اگر حداقل یکی از ورودی‌ها 0 باشد، خروجی به Vdd وصل شود.

برای NOR وضعیت برعکس می‌شود: nMOSها معمولاً **parallel** هستند، چون اگر هر ورودی 1 شود، یک مسیر به GND ایجاد می‌شود و خروجی 0 می‌شود. pMOSها در NOR به صورت **series** هستند، چون فقط وقتی همهٔ ورودی‌ها 0 باشند، خروجی به Vdd وصل می‌شود. نکتهٔ مهم امتحانی: با **NOT، NAND، NOR** می‌توان همهٔ توابع بولی را ساخت. حتی **NAND alone** یا **NOR alone** هم از نظر منطقی functional complete هستند.

---

### 6. PDK، Design Rules و Layout

برای اینکه بتوانیم واقعاً یک مدار را روی سیلیکون پیاده کنیم، فقط دانستن جدول حقیقت کافی نیست. به **Process Design Kit / PDK** نیاز داریم. **PDK** مجموعه‌ای از لایه‌ها، قوانین هندسی، مدل‌های ترانزیستور و اطلاعات ساخت است که معمولاً توسط **Foundry** یا کارخانهٔ ساخت چیپ ارائه می‌شود. در صفحهٔ ۸ فایل گفته شده که PDK مشخص می‌کند چطور باید layout را رسم کنیم؛ یعنی همان مستطیل‌ها و فاصله‌ها و لایه‌ها.

**Design Rules** مثل قوانین شهرسازی برای چیپ هستند. مثلاً می‌گویند دو خط فلزی نباید بیش از حد به هم نزدیک باشند، چون ممکن است **Short Circuit** ایجاد شود. یا عرض یک مسیر فلزی نباید از حد مشخصی کمتر باشد، چون ممکن است در ساخت خراب شود یا مقاومت زیادی ایجاد کند. بنابراین حتی اگر منطق مدار درست باشد، اگر Design Rules رعایت نشوند، چیپ ممکن است قابل تولید نباشد.

در تصویر صفحهٔ ۵، نمونه‌ای از **nMOS Layout** همراه با **Substrate Contact** نشان داده شده است. این یعنی در layout واقعی، فقط Gate و Source و Drain نداریم؛ باید Bulk یا substrate هم درست bias شود. در غیر این صورت، رفتارهای ناخواسته رخ می‌دهد. این نکته برای امنیت هم مهم است، چون اشتباه در layout یا biasing می‌تواند باعث leakage، glitch، fault sensitivity یا رفتار غیرمنتظره شود.

ابزارهایی مثل **Magic** و **KLayout** برای طراحی و بررسی layout استفاده می‌شوند. برخی PDKها تجاری و محرمانه‌اند و دسترسی به آن‌ها نیازمند **NDA / Non-Disclosure Agreement** است. یک نمونهٔ آموزشی و open-source، **SkyWater 130nm PDK** است. این فناوری مثل 3nm مدرن نیست، اما برای آموزش و حتی ساخت نمونه‌های پایه مناسب است.

---

### 7. Full Custom، Semi-Custom، ASIC و FPGA

در **Full Custom Design**، طراح همه‌چیز را در پایین‌ترین سطح کنترل می‌کند: اندازهٔ هر ترانزیستور، مکان آن، مسیرهای اتصال و layout. این روش بیشترین کنترل و معمولاً بهترین optimization را می‌دهد، اما بسیار زمان‌بر و گران است. برای بخش‌های حساس مثل SRAM، analog blocks یا مسیرهای فوق‌حساس پردازنده ممکن است هنوز از full custom استفاده شود.

در **Semi-Custom Design**، به‌جای طراحی تک‌تک ترانزیستورها، از بلوک‌های آماده استفاده می‌کنیم. مثل LEGO: قطعه‌ها آماده‌اند، تو فقط آن‌ها را کنار هم می‌گذاری. این روش برای طراحی دیجیتال بزرگ بسیار رایج است. کتابخانه‌هایی مثل **Standard Cell Libraries** شامل گیت‌هایی مثل NAND، NOR، Flip-Flop و MUX هستند. طراح با HDL مدار را توصیف می‌کند و ابزارهای synthesis آن را به گیت‌های آماده تبدیل می‌کنند.

**ASIC / Application-Specific Integrated Circuit** 

چیپی است که برای یک کاربرد خاص ساخته می‌شود. وقتی تولید انبوه باشد، ASIC می‌تواند بسیار سریع، کم‌مصرف و ارزان در هر واحد باشد، اما هزینهٔ طراحی و ساخت اولیه بالاست. در مقابل، **Programmable Devices** مثل **PAL، PLA، CPLD، FPGA** بعد از تولید قابل پیکربندی هستند. مخصوصاً **FPGA / Field-Programmable Gate Array** برای prototype و آزمایش قبل از ساخت ASIC بسیار کاربردی است.

تفاوت مهم: در ASIC معمولاً با گیت‌های واقعی و standard cells کار می‌کنیم؛ اما در FPGA عنصر پایه اغلب **LUT / Lookup Table** است. یعنی یک تابع منطقی به‌جای اینکه مستقیماً با NAND/NOR فیزیکی ساخته شود، در یک جدول قابل برنامه‌ریزی ذخیره می‌شود. از نظر امنیت، FPGA هم مزایا دارد هم ریسک: قابلیت reconfiguration مفید است، اما bitstream، پیکربندی و IPهای استفاده‌شده باید محافظت شوند.

---

### 8. Combinational و Sequential Circuits

در طراحی دیجیتال دو خانوادهٔ اصلی مدار داریم: **Combinational Circuits** و **Sequential Circuits**. در **Combinational Circuit**، خروجی فقط به ورودی فعلی بستگی دارد:
**O(t) = f(I(t))**
مثلاً AND، OR، NOT، MUX، Half Adder، Full Adder. این مدارها حافظه ندارند. اگر ورودی تغییر کند، خروجی بعد از یک delay کوتاه تغییر می‌کند.

اما در **Sequential Circuit**، خروجی فقط به ورودی فعلی وابسته نیست؛ به گذشته هم وابسته است:
**O(t) = g(I(t), past state)**
یعنی مدار حافظه دارد. حافظه می‌تواند با **Latch** یا **Flip-Flop** ساخته شود. اینجا مفهوم clock مهم می‌شود. بیشتر مدارهای دیجیتال مدرن **Synchronous Sequential Circuits** هستند؛ یعنی تغییر state در هماهنگی با یک سیگنال **Clock** رخ می‌دهد.

در مدارهای **Asynchronous Sequential Circuits**، تغییرات بدون clock رخ می‌دهد. این مدارها می‌توانند سریع و منعطف باشند، اما طراحی، تحلیل و تست آن‌ها سخت‌تر است. در مقابل، **Synchronous Circuits** قابل پیش‌بینی‌ترند، چون همه‌چیز با clock هماهنگ می‌شود. به همین دلیل بیشتر پردازنده‌ها و مدارهای دیجیتال مدرن از **Positive Edge-Triggered Flip-Flops** استفاده می‌کنند.

---

### 9. Propagation Delay و محدودیت سرعت

یک تابع منطقی روی کاغذ instant است؛ مثلاً می‌گویی خروجی AND برابر A·B است. اما یک **Logic Gate** مدار فیزیکی ساخته‌شده از ترانزیستور است. ترانزیستور برای روشن و خاموش شدن زمان لازم دارد. سیم‌ها مقاومت و ظرفیت خازنی دارند. پس خروجی بلافاصله تغییر نمی‌کند. این تأخیر را **Propagation Delay / tpd** می‌نامیم.

مثلاً در صفحهٔ ۱۰ و ۱۱، بحث Full Adder آمده است. اگر یک **Full Adder** delay برابر ε داشته باشد، در یک جمع‌کنندهٔ چندبیتی که چند Full Adder پشت سر هم قرار گرفته‌اند، delayها جمع می‌شوند. برای یک 4-bit ripple-carry adder، ممکن است delay خروجی آخر حدود 4ε شود. در 8-bit adder، delay می‌تواند 8ε شود. این یعنی هرچه مدار combinational بزرگ‌تر و زنجیره‌ای‌تر باشد، حداکثر سرعت clock کمتر می‌شود.

قاعدهٔ ساده: اگر ورودی‌ها هر **T** ثانیه تغییر کنند، باید delay مدار کمتر از T باشد:
**tpd < T**
اگر delay بیشتر باشد، خروجی قبل از تغییر بعدی ورودی هنوز پایدار نشده و نتیجه غلط می‌شود. این نکته در امتحان خیلی مهم است، چون طراحان سؤال معمولاً delay را با frequency یا clock period ربط می‌دهند.

از نظر امنیت، timing فقط مسئلهٔ عملکرد نیست. تفاوت delay در مسیرهای مختلف می‌تواند باعث **Timing Side Channel** شود. همچنین glitchها و race conditionها می‌توانند در برخی حملات fault یا leakage نقش داشته باشند. پس propagation delay یک مفهوم پایه‌ای برای هم طراحی و هم امنیت است.

---

### 10. Latch، Flip-Flop، Setup Time، Hold Time و Metastability

**Level-Triggered Latch**

 وقتی clock در یک سطح خاص است، معمولاً high، شفاف می‌شود؛ یعنی ورودی تقریباً مستقیم به خروجی منتقل می‌شود. وقتی clock در سطح دیگر است، مقدار قبلی را نگه می‌دارد. به همین دلیل گفته می‌شود latch یک **Transparency Phase** و یک **Memory Phase** دارد. مشکل latch این است که در فاز شفافیت، delayهای ورودی می‌توانند از چند stage عبور کنند و تحلیل timing سخت‌تر شود.

**Edge-Triggered Flip-Flop**

 فقط در لحظهٔ لبهٔ clock، معمولاً **Rising Edge**، مقدار ورودی را نمونه‌برداری می‌کند. تشبیه جزوه دقیق است: Flip-Flop مثل دوربینی است که در لحظهٔ لبهٔ clock از ورودی عکس می‌گیرد. اگر ورودی در آن لحظه پایدار باشد، مقدار درست ذخیره می‌شود. اگر ورودی نزدیک به لبهٔ clock تغییر کند، ممکن است مقدار نامشخص ثبت شود.

اینجا دو مفهوم حیاتی داریم: **Setup Time / tsu** و **Hold Time / thold**.


**Setup Time** 

یعنی ورودی باید مدتی قبل از لبهٔ clock پایدار باشد.
**Hold Time** 

یعنی ورودی باید مدتی بعد از لبهٔ clock هم پایدار بماند.
اگر این دو شرط نقض شوند، ممکن است **Metastability** رخ دهد؛ یعنی خروجی Flip-Flop موقتاً نه 0 قطعی است نه 1 قطعی. در دنیای دیجیتال این وضعیت خطرناک است، چون مدارهای بعدی انتظار مقدار binary مشخص دارند.

فرمول مهم timing در فایل آمده است:
**Tclk ≥ tco + tpd + tsu**
یعنی دورهٔ clock باید حداقل برابر مجموع **Clock-to-Output Delay** از Flip-Flop اول، **Propagation Delay** مدار combinational، و **Setup Time** Flip-Flop دوم باشد. همچنین برای hold باید مسیر آن‌قدر زود تغییر نکند که شرط hold نقض شود. این مفاهیم تقریباً همیشه در تست‌ها می‌آیند.

---

### 11. Reset و چرا معمولاً Asynchronous Active-Low است؟

در مدارهای sequential، اگر state اولیه مشخص نباشد، مدار می‌تواند در وضعیت unknown شروع کند. این خطرناک است. برای همین به Flip-Flopها معمولاً ورودی **Reset / RST** اضافه می‌شود. وظیفهٔ reset این است که خروجی را به مقدار مشخص، معمولاً 0، مجبور کند. در صفحهٔ ۱۳ فایل، reset با نام **RSTn** آمده است؛ حرف **n** یعنی **active-low**.

**Active-low reset** یعنی وقتی RST = 0 باشد، مدار reset می‌شود؛ وقتی RST = 1 باشد، مدار اجازهٔ کار عادی دارد. در شماتیک، active-low بودن معمولاً با یک دایرهٔ کوچک روی ورودی نشان داده می‌شود. تلهٔ امتحانی: اگر روی ورودی reset دایره باشد، فعال شدن آن با 0 است، نه با 1.

چرا reset باید **Asynchronous** باشد؟ چون هنگام روشن‌شدن سیستم، ممکن است clock هنوز پایدار نباشد یا اصلاً در حال کار نباشد. اما ما همچنان می‌خواهیم بتوانیم مدار را به وضعیت امن و معلوم ببریم. بنابراین reset نباید منتظر clock بماند. در جزوه تأکید شده که reset بالاترین priority را دارد و مدار فقط وقتی شروع به sampling می‌کند که RST به 1 برگردد.

از نظر امنیت سخت‌افزار، reset بسیار مهم است. یک reset بد طراحی‌شده می‌تواند باعث state نامعلوم، bypass شدن منطق امنیتی، glitch، یا رفتار غیرقابل پیش‌بینی هنگام power-on شود. در سیستم‌های امن، **Secure Boot، Key Storage، Access Control Logic** و FSMهای امنیتی باید با reset قابل اعتماد شروع شوند.

---

# گام ۲: خلاصه تستی و مقایسه‌ای

## Bilingual Cheat-Sheet

## VLSI Basics

| Keyword                     | نکتهٔ تستی                                     |
| --------------------------- | ---------------------------------------------- |
| **VLSI**                    | طراحی مدار با تعداد بسیار زیاد ترانزیستور      |
| **IC / Integrated Circuit** | مدار مجتمع؛ چندین ترانزیستور و مدار روی یک die |
| **Die**                     | یک چیپ جداشده از wafer                         |
| **MOSFET**                  | ترانزیستور اصلی در اکثر مدارهای دیجیتال مدرن   |
| **MOS**                     | Metal-Oxide-Semiconductor                      |
| **Gate**                    | کنترل‌کنندهٔ کانال                             |
| **Source / Drain**          | دو سر مسیر جریان                               |
| **Bulk / Body**             | پایهٔ چهارم فیزیکی ترانزیستور                  |

---

## MOSFET Operation

| مفهوم                      | توضیح                                             |
| -------------------------- | ------------------------------------------------- |
| **Gate current ≈ 0**       | چون Gate با **oxide/insulator** جدا شده است       |
| **VGS**                    | کنترل ایجاد و قدرت کانال                          |
| **VDS**                    | کنترل فشار حرکت جریان بین Drain و Source          |
| **VT / Threshold Voltage** | حداقل ولتاژ لازم برای روشن‌شدن ترانزیستور         |
| **W/L**                    | نسبت هندسی مؤثر روی جریان                         |
| **Triode Region**          | ترانزیستور مثل **variable resistor** رفتار می‌کند |
| **Saturation Region**      | جریان تقریباً ثابت می‌شود؛ شبیه current source    |

---

## nMOS vs pMOS

| ویژگی               | **nMOS**          | **pMOS**        |
| ------------------- | ----------------- | --------------- |
| Carrier             | Electrons         | Holes           |
| Substrate/Well      | P-type substrate  | N-well          |
| Source/Drain        | N+ diffusion      | P+ diffusion    |
| Turns ON when       | Gate = logic 1    | Gate = logic 0  |
| Pulls output to     | GND               | Vdd             |
| Common role in CMOS | Pull-down network | Pull-up network |

---

## CMOS Inverter

| Input       | nMOS | pMOS | Output             |
| ----------- | ---- | ---- | ------------------ |
| **Vin = 0** | OFF  | ON   | **Vout = 1 / Vdd** |
| **Vin = 1** | ON   | OFF  | **Vout = 0 / GND** |

Key idea:

* **nMOS passes strong 0**
* **pMOS passes strong 1**
* CMOS uses complementary behavior.
* Static CMOS ideally has very low DC current in stable states.

---

## NAND vs NOR in CMOS

| Gate     | nMOS Network | pMOS Network | Output becomes 0 when   |
| -------- | ------------ | ------------ | ----------------------- |
| **NAND** | Series       | Parallel     | all inputs are 1        |
| **NOR**  | Parallel     | Series       | at least one input is 1 |

نکتهٔ حفظی:

* **NAND:** nMOS series
* **NOR:** nMOS parallel
* pMOS همیشه topology مکمل nMOS دارد.

---

## PDK and Layout

| Keyword                      | توضیح                                                       |
| ---------------------------- | ----------------------------------------------------------- |
| **PDK / Process Design Kit** | قوانین، لایه‌ها و مدل‌های لازم برای طراحی در یک فناوری ساخت |
| **Design Rules**             | محدودیت‌های هندسی مثل minimum width و minimum spacing       |
| **Layout**                   | پیاده‌سازی فیزیکی مدار با مستطیل‌ها روی لایه‌های مختلف      |
| **Foundry**                  | کارخانهٔ ساخت چیپ                                           |
| **NDA**                      | قرارداد محرمانگی برای دسترسی به PDK تجاری                   |
| **Magic / KLayout**          | ابزارهای طراحی و مشاهدهٔ layout                             |
| **SkyWater 130nm**           | نمونهٔ open-source PDK آموزشی                               |

---

## Design Methodologies

| روش                    | توضیح                               | مزیت                             | عیب                               |
| ---------------------- | ----------------------------------- | -------------------------------- | --------------------------------- |
| **Full Custom Design** | طراحی دستی ترانزیستورها و layout    | کنترل و optimization بالا        | زمان‌بر، گران، سخت                |
| **Semi-Custom Design** | استفاده از بلوک‌ها و standard cells | سریع‌تر و scalable               | کنترل کمتر از full custom         |
| **ASIC**               | چیپ مخصوص یک کاربرد                 | سریع، کم‌مصرف، مناسب تولید انبوه | هزینهٔ اولیه بالا                 |
| **FPGA**               | قابل برنامه‌ریزی پس از ساخت         | مناسب prototyping و تغییر        | معمولاً کندتر و پرمصرف‌تر از ASIC |

---

## Combinational vs Sequential Circuits

| ویژگی         | **Combinational Circuit** | **Sequential Circuit**     |
| ------------- | ------------------------- | -------------------------- |
| وابستگی خروجی | فقط ورودی فعلی            | ورودی فعلی + state قبلی    |
| Memory        | ندارد                     | دارد                       |
| Feedback      | معمولاً ندارد             | ممکن است داشته باشد        |
| مثال          | AND, OR, MUX, Adder       | Latch, Flip-Flop, Register |
| فرمول         | O(t)=f(I(t))              | O(t)=g(I(t), state)        |

---

## Timing Concepts

| Keyword                         | معنی                                          |
| ------------------------------- | --------------------------------------------- |
| **Propagation Delay / tpd**     | زمان لازم برای اثرگذاری تغییر ورودی روی خروجی |
| **Clock-to-Output Delay / tco** | delay خروجی Flip-Flop بعد از clock edge       |
| **Setup Time / tsu**            | ورودی باید قبل از clock edge پایدار باشد      |
| **Hold Time / thold**           | ورودی باید بعد از clock edge پایدار بماند     |
| **Metastability**               | وضعیت نامطمئن بین 0 و 1                       |
| **Tclk ≥ tco + tpd + tsu**      | شرط اصلی حداکثر سرعت clock                    |
| **Reset / RST**                 | برگرداندن state به مقدار معلوم                |
| **Active-low reset / RSTn**     | reset وقتی فعال است که مقدارش 0 باشد          |

---

# گام ۳: تله‌های امتحانی

## Pitfalls

### 1. Gate جریان مستقیم زیادی نمی‌کشد

گزینه‌ای که بگوید **current flows into the MOS gate** معمولاً غلط است. Gate با **oxide/insulator** جدا شده، پس جریان DC ایده‌آل تقریباً صفر است. البته در واقعیت leakage وجود دارد، اما مفهوم پایه این است که Gate با میدان الکتریکی کنترل می‌کند.

---

### 2. nMOS و pMOS برعکس روشن می‌شوند

* **nMOS ON → gate high / logic 1**
* **pMOS ON → gate low / logic 0**

گزینه‌هایی که بگویند هر دو با logic 1 روشن می‌شوند غلط‌اند.

---

### 3. pMOS فقط nMOS با اسم متفاوت نیست

pMOS از **holes** استفاده می‌کند، در **N-well** ساخته می‌شود، و رفتار منطقی آن مکمل nMOS است. این تفاوت پایهٔ **CMOS** است.

---

### 4. CMOS Inverter در حالت stable ideally جریان DC ندارد

وقتی ورودی 0 یا 1 پایدار است، یکی از ترانزیستورها خاموش است. پس مسیر مستقیم پایدار از Vdd به GND نداریم. اما هنگام switching مصرف توان داریم.

---

### 5. NAND و NOR را با topology اشتباه نگیری

* **CMOS NAND:** nMOS سری، pMOS موازی
* **CMOS NOR:** nMOS موازی، pMOS سری

طراح سؤال معمولاً topology را جابه‌جا می‌کند.

---

### 6. Logic Function با Logic Gate یکی نیست

**Logic Function** انتزاعی و ریاضی است.
**Logic Gate** فیزیکی است و delay، capacitance، resistance و power دارد.

پس گزینه‌ای که بگوید gates are instantaneous غلط است.

---

### 7. Propagation delay سرعت را محدود می‌کند

اگر **tpd ≥ Tclk** یا مسیر combinational بیش از حد طولانی باشد، خروجی به‌موقع پایدار نمی‌شود. شرط پایه:

**Tclk ≥ tco + tpd + tsu**

---

### 8. Latch و Flip-Flop یکی نیستند

* **Latch:** level-triggered، در بخشی از clock شفاف است.
* **Flip-Flop:** edge-triggered، فقط روی edge نمونه‌برداری می‌کند.

گزینه‌ای که بگوید latch فقط روی rising edge کار می‌کند غلط است.

---

### 9. Edge-triggered latency اضافه می‌کند

Flip-Flop زمان‌بندی را قابل پیش‌بینی‌تر می‌کند، اما معمولاً **one-clock-cycle latency** ایجاد می‌کند. پس گزینهٔ “edge-triggered circuits have no latency” مشکوک و معمولاً غلط است.

---

### 10. Setup و Hold جهت زمانی متفاوت دارند

* **Setup Time:** قبل از clock edge
* **Hold Time:** بعد از clock edge

این دو را جابه‌جا نکن.

---

### 11. Metastability یعنی خروجی فوراً غلط منطقی قطعی نیست

**Metastability** یعنی Flip-Flop موقتاً در حالت نامطمئن بین 0 و 1 گیر می‌کند. ممکن است بعداً resolve شود، اما تا آن زمان مدار بعدی می‌تواند رفتار غلط ببیند.

---

### 12. Reset معمولاً active-low و asynchronous است

در نمودار اگر **RSTn** یا دایرهٔ کوچک روی ورودی reset دیدی، یعنی reset با 0 فعال می‌شود.
**Asynchronous reset** یعنی مستقل از clock عمل می‌کند.

---

# گام ۴: خودآزمایی

## 100% English Quiz

### Question 1

**Which statement correctly describes the basic behavior of nMOS and pMOS transistors in CMOS logic?**

A. Both nMOS and pMOS turn on when the gate voltage is logic 1.
B. nMOS turns on with logic 1 at the gate, while pMOS turns on with logic 0 at the gate.
C. nMOS is used only in analog circuits, while pMOS is used only in digital circuits.
D. pMOS pulls the output to ground, while nMOS pulls the output to Vdd.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** nMOS با logic 1 روشن می‌شود، اما pMOS با logic 0 روشن می‌شود.
* **B درست است:** این رفتار مکمل پایهٔ فناوری **CMOS** است.
* **C غلط است:** هر دو در مدارهای دیجیتال CMOS استفاده می‌شوند.
* **D غلط است:** معمولاً **nMOS** در pull-down network خروجی را به **GND** می‌کشد و **pMOS** در pull-up network خروجی را به **Vdd** وصل می‌کند.

---

### Question 2

**In a synchronous circuit with two flip-flops and a combinational block between them, which condition is required to avoid setup-time violation?**

A. Tclk must be smaller than tco + tpd + tsu.
B. Tclk must be greater than or equal to tco + tpd + tsu.
C. tpd must always be zero.
D. Setup time is only relevant for asynchronous circuits.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** اگر clock period کوچک‌تر از مجموع delayها باشد، داده به‌موقع به Flip-Flop دوم نمی‌رسد.
* **B درست است:** شرط اصلی این است: **Tclk ≥ tco + tpd + tsu**.
* **C غلط است:** در مدار واقعی tpd صفر نیست؛ ترانزیستورها و interconnectها delay دارند.
* **D غلط است:** **Setup Time** مخصوصاً در synchronous flip-flop-based circuits بسیار مهم است.

---

### Question 3

**What is the main difference between a level-triggered latch and an edge-triggered flip-flop?**

A. A latch has no memory, while a flip-flop is purely combinational.
B. A latch samples input only on the rising edge, while a flip-flop is transparent during the high clock level.
C. A latch can be transparent during a clock level, while a flip-flop samples input only at a clock edge.
D. A latch is used only in ASICs, while a flip-flop is used only in FPGAs.

**Correct Answer: C**

**تحلیل گزینه‌ها:**

* **A غلط است:** هر دو حافظه دارند و sequential elements هستند.
* **B غلط است:** دقیقاً برعکس است؛ latch در یک سطح clock شفاف می‌شود، flip-flop روی edge نمونه‌برداری می‌کند.
* **C درست است:** **Level-triggered latch** در فاز شفافیت ورودی را عبور می‌دهد؛ **edge-triggered flip-flop** فقط روی لبهٔ clock مقدار را ثبت می‌کند.
* **D غلط است:** هم latch و هم flip-flop می‌توانند در طراحی‌های مختلف استفاده شوند؛ این تقسیم‌بندی ASIC/FPGA نیست.
