این فایل قابل قبول است. اسمش در لیست تو به‌صورت **04-PCB_security.pdf** آمده و محتوای فایل هم دقیقاً **PCB Security Basics** است، پس تدریس شده و ادامه می‌دهیم. 

# گام ۱: آموزش مفهومی و مفصل

## Deep Explanation — PCB Security Basics

### 1. PCB چیست و چرا از نظر امنیتی مهم است؟

**PCB / Printed Circuit Board**

 همان برد فیزیکی‌ای است که قطعات الکترونیکی روی آن نصب می‌شوند و از طریق مسیرهای فلزی به هم وصل می‌شوند. مثلاً مادربرد لپ‌تاپ، برد داخل گوشی، برد داخل روتر، برد کنترلر ماشین لباس‌شویی، یا برد یک دستگاه IoT همگی PCB هستند. PCB معمولاً یک بستر غیررسانا دارد، اغلب از جنس **FR4 / Flame Retardant 4**، و مسیرهای رسانای آن معمولاً از **Copper** ساخته می‌شوند. کار اصلی PCB این است که قطعات را مکانیکی نگه دارد و از نظر الکتریکی به هم وصل کند. 

از دید امنیت سخت‌افزار، PCB نقطه‌ای است که سیستم از حالت «چیپ مجرد» به یک دستگاه واقعی تبدیل می‌شود. یعنی حتی اگر خود microcontroller، memory یا accelerator امن باشد، مسیرهای روی PCB ممکن است اطلاعات حساس را آشکار کنند. مهاجم لازم نیست همیشه داخل chip را reverse engineer کند؛ گاهی کافی است probe را روی یک trace بگذارد، ولتاژ را اندازه بگیرد، یا با **Oscilloscope** و **Logic Analyzer** سیگنال‌ها را بخواند. این همان تفاوت امنیت chip-level و board-level است.

تشبیه ساده: فرض کن یک ساختمان خیلی امن داری، ولی سیم‌های دوربین‌ها و قفل‌های الکترونیکی از بیرون دیوار قابل دسترسی‌اند. حتی اگر خود قفل قوی باشد، کسی که به سیم‌ها دسترسی دارد می‌تواند سیگنال‌ها را شنود کند، تغییر دهد یا تزریق کند. در PCB هم همین است. پروتکل‌هایی مثل **UART، I2C، SPI، JTAG** برای توسعه و ارتباط قطعات مفیدند، اما اگر محافظت نشوند، به attacker یک راه مستقیم برای دیدن یا کنترل سیستم می‌دهند.

فصل می‌گوید اکثر PCBها protection جدی ندارند. یعنی کاربر یا attacker با دسترسی فیزیکی می‌تواند برد را probe کند، voltages را اندازه بگیرد، و signals را تحلیل کند. این برای سیستم‌های embedded خطرناک است، چون اطلاعاتی مثل firmware logs، debug interface، sensor data، memory traffic یا حتی secret-related operations ممکن است روی همین خطوط حرکت کنند. پس امنیت PCB یعنی کاهش امکان دسترسی، خواندن، تغییر یا سوءاستفاده از ارتباطات و مسیرهای فیزیکی روی board.

---

### 2. انواع PCB و Viaها

در فایل سه نوع اصلی PCB آمده است: **Single-sided PCB، Double-sided PCB، Multilayer PCB**. در **Single-sided PCB** فقط یک طرف معمولاً برای قطعات و سمت دیگر برای routing استفاده می‌شود. این ساده‌ترین و ارزان‌ترین نوع است، اما برای سیستم‌های پیچیده محدودیت شدید دارد. در **Double-sided PCB** هر دو طرف برد برای قرار دادن قطعات و routing استفاده می‌شوند. این انعطاف بیشتری می‌دهد، ولی همچنان بسیاری از مسیرها از بیرون قابل مشاهده یا قابل probe هستند.

در **Multilayer PCB** چندین لایه داخلی داریم. قطعات ممکن است در دو سمت برد باشند، routing هم روی لایه‌های خارجی و داخلی انجام شود. این نوع برای سیستم‌های پیچیده لازم است، چون تعداد قطعات و مسیرها زیاد است. از نظر امنیتی هم multilayer board مهم است، چون می‌توان signalهای حساس را داخل لایه‌های میانی برد قرار داد تا probe کردن آن‌ها سخت‌تر شود. البته این امنیت مطلق نیست؛ attacker حرفه‌ای می‌تواند board را لایه‌برداری کند، ولی هزینه و سختی حمله بالا می‌رود.

در ساختار PCB، مفهوم **Via** خیلی مهم است. **PTH / Plated Through Hole** یا via سوراخی است که داخل آن metallization دارد و برای اتصال الکتریکی بین لایه‌ها استفاده می‌شود. در مقابل **Non-PTH hole** فلزکاری ندارد و بیشتر مکانیکی است. در برد چندلایه، چند نوع via داریم: **Through-hole via** که از top تا bottom می‌رود، **Blind via** که یک لایه خارجی را به یک لایه داخلی وصل می‌کند، و **Buried via** که فقط دو لایه داخلی را به هم وصل می‌کند. 

از نظر security، via و layer انتخاب مهمی است. وقتی مسیر حساس روی سطح board باشد، attacker ساده‌تر آن را لمس می‌کند. وقتی مسیر از **Buried via** و internal layer عبور کند، probe کردن سخت‌تر می‌شود. به همین دلیل یکی از good practices بعداً این است که سیم‌های حساس داخل board قرار بگیرند، مخصوصاً با packageهایی مثل **BGA / Ball Grid Array** 

که pinها زیر chip هستند و دسترسی مستقیم به آن‌ها سخت‌تر است.

---

### 3. Packageها: DIP، SOP، QFP، QFN، BGA

قطعات روی PCB با packageهای مختلف نصب می‌شوند. **DIP / Dual In-line Package** قدیمی‌تر و through-hole است؛ پایه‌ها از سوراخ‌های برد عبور می‌کنند و لحیم می‌شوند. این برای آموزش و تعمیر آسان است، ولی از نظر امنیتی ضعیف‌تر است، چون پایه‌ها بزرگ و قابل دسترسی‌اند. مهاجم راحت‌تر می‌تواند probe وصل کند یا pinها را دنبال کند.

در **SMD / Surface Mounting Device**

، قطعه روی سطح board نصب می‌شود. نمونه‌ها شامل **SOP، QFP، QFN، BGA** هستند. **SOP** شبیه DIP است ولی کوچک‌تر و سطحی. **QFP** پایه‌ها را در چهار طرف دارد. **QFN** پایه‌های بسیار کوچک در پایین یا لبه پایینی دارد و soldering دستی را سخت می‌کند. **BGA** پیچیده‌ترین مورد این بخش است: پایه‌ها به شکل توپ‌های رسانا زیر chip قرار دارند. همین باعث می‌شود دسترسی مستقیم به pinها بسیار سخت‌تر شود.

از نظر امنیتی، **BGA** معمولاً بهتر از packageهایی مثل DIP یا QFP است، چون پایه‌ها بیرون و در دسترس نیستند. اگر یک خط حساس مثل **JTAG TDI/TDO** یا bus مربوط به memory از زیر BGA وارد layer داخلی شود، attacker نمی‌تواند به‌سادگی probe را روی pin بگذارد. البته باز هم مطلقاً امن نیست؛ با X-ray، decapsulation، microprobing یا board rework حرفه‌ای می‌شود حمله کرد، اما هزینه بالا می‌رود.

پس در امتحان باید بدانی package فقط مسئلهٔ manufacturing نیست؛ روی attack surface هم اثر دارد. هرچه pinها بزرگ‌تر، بیرونی‌تر و ساده‌تر باشند، attack آسان‌تر است. هرچه routing داخلی‌تر، package فشرده‌تر و دسترسی فیزیکی سخت‌تر باشد، امنیت فیزیکی بالاتر می‌رود، البته با هزینه و پیچیدگی بیشتر.

---

### 4. Design Flow

 در PCB و نقش Gerber File

برای طراحی PCB، اول باید اندازه board و **Design Rules** مثل minimum spacing، width مسیرها، فاصله‌ها و لایه‌ها مشخص شود. بعد components روی board قرار می‌گیرند؛ این مرحله **Placement** است. سپس مسیرهای اتصال کشیده می‌شوند؛ این مرحله **Routing** است. در نهایت خروجی طراحی به شکل **Gerber File** تولید می‌شود. فایل Gerber یک فرمت صنعتی، معمولاً ASCII، است که اطلاعات layout، geometry، layers، connections و بخش‌های لازم برای manufacturing را به کارخانه می‌دهد. 

نکتهٔ امنیتی اینجاست: **Gerber File** اگر لو برود، تقریباً نقشهٔ فیزیکی board لو رفته است. attacker می‌تواند بفهمد کدام trace کجا می‌رود، کدام pin به کدام chip وصل است، JTAG کجاست، memory bus کجاست، و کدام مسیرها احتمالاً حساس‌اند. پس PCB security فقط دربارهٔ board ساخته‌شده نیست؛ دربارهٔ حفاظت از design files هم هست.

ابزارهای طراحی مثل **Allegro، Eagle، Altium، KiCAD** برای PCB Design استفاده می‌شوند. در طراحی واقعی باید part number و package دقیق قطعات را بدانیم، چون footprint هر قطعه فرق دارد. اگر package اشتباه باشد، board ممکن است اصلاً قابل مونتاژ نباشد یا routing اشتباه شود. از دید امنیتی، footprint و placement هم مهم‌اند، چون تعیین می‌کنند آیا attacker به pins و traces دسترسی دارد یا نه.

به زبان ساده، PCB Design Flow مثل طراحی نقشهٔ خیابان‌های یک شهر است. قطعات ساختمان‌ها هستند، traces خیابان‌ها هستند، viaها تونل‌ها یا پل‌ها بین طبقات‌اند، و Gerber File نقشهٔ نهایی شهر است. اگر نقشه به دست مهاجم برسد، پیدا کردن مسیرهای حساس بسیار آسان‌تر می‌شود.

---

### 5. Serial vs Parallel Transmission

داده روی PCB می‌تواند به‌صورت **Serial Transmission** یا **Parallel Transmission** منتقل شود. در **Serial Transmission** بیت‌ها روی یک wire و در زمان‌های مختلف ارسال می‌شوند. اگر N بیت داریم، معمولاً N cycle لازم است. مزیت این روش این است که تعداد wire و pin کم می‌شود؛ مدار کوچک‌تر و ارزان‌تر می‌شود و routing ساده‌تر است. عیب آن latency بیشتر و throughput محدودتر است.

در **Parallel Transmission** چندین بیت هم‌زمان روی چند wire جداگانه منتقل می‌شوند. مثلاً برای 8 بیت، 8 wire داریم و همهٔ بیت‌ها هم‌زمان ارسال می‌شوند. این سریع‌تر است، اما pin و wire بیشتری می‌خواهد. روی PCB پیچیده‌تر است و از نظر امنیتی هم سطح حمله بیشتری ایجاد می‌کند، چون attacker می‌تواند چندین line را probe کند و bus کامل را ببیند.

در serial communication معمولاً از دو نوع register استفاده می‌شود: **PISO / Parallel-In Serial-Out** در transmitter و **SIPO / Serial-In Parallel-Out** در receiver. در transmitter، داده چندبیتی ابتدا parallel وارد PISO می‌شود، سپس bit-by-bit ارسال می‌شود. در receiver، SIPO بیت‌ها را یکی‌یکی می‌گیرد و بعد از N cycle دوباره دادهٔ parallel را بازسازی می‌کند. 

این دقیقاً مثل این است که بخواهی یک کلمه را یا یک‌باره با چند نفر هم‌زمان بفرستی، یا حرف‌به‌حرف با یک نفر. روش اول سریع‌تر ولی نیازمند کانال‌های بیشتر است؛ روش دوم ساده‌تر و کم‌هزینه‌تر، ولی کندتر است. برای sensorهایی مثل temperature و humidity که throughput بالا لازم ندارند، serial protocolها معمولاً کافی‌اند.

---

### 6. Asynchronous vs Synchronous Serial Links

در **Asynchronous Protocols**،

clock

 بین transmitter و receiver مشترک نیست. فقط data line ارسال می‌شود و receiver خودش timing را با شروع هر character تنظیم می‌کند. نمونهٔ مهم آن **UART / Universal Asynchronous Receiver/Transmitter**
 
  است. در UART معمولاً line در حالت idle برابر logic 1 است. برای شروع transmission، transmitter line را از high به low می‌برد؛ این **falling edge** همان **Start Bit** 
  
  را نشان می‌دهد. receiver با دیدن آن می‌فهمد character جدید شروع شده است. 

بعد از start bit، data bits ارسال می‌شوند. در پایان، transmitter line را برای حداقل یک bit time در logic 1 نگه می‌دارد؛ این **Stop Bit** است. stop bit به receiver فرصت می‌دهد پایان character را بفهمد و برای character بعدی آماده شود. گاهی UART یک **Parity Bit** هم دارد تا خطای ساده در انتقال تشخیص داده شود. اما parity security نیست؛ فقط error detection ساده است.

در **Synchronous Protocols**، clock مشترک بین transmitter و receiver وجود دارد. یعنی حداقل دو wire لازم داریم: data و clock. Receiver دقیقاً می‌داند چه زمانی باید sample کند. نمونه‌های مهم: **I2C / Inter-Integrated Circuit** و **SPI / Serial Peripheral Interface**.
 در این پروتکل‌ها data معمولاً در frame یا transaction سازمان‌دهی می‌شود، نه characterهای مستقل مثل UART.

از نظر امنیتی، asynchronous و synchronous هر دو قابل حمله‌اند. UART ساده و رایج است و روی بردها زیاد به‌عنوان debug port باقی می‌ماند. I2C و SPI برای ارتباط chip-to-chip استفاده می‌شوند و اگر attacker به SDA/SCL یا MOSI/MISO/SCLK دسترسی پیدا کند، می‌تواند sensorها، memoryها یا peripherals را شنود یا گاهی کنترل کند.

---

### 7. Simplex، Half-duplex، Full-duplex و اصطلاحات bus

در **Simplex Link**، ارتباط فقط در یک جهت است. یک طرف همیشه transmitter است و طرف دیگر همیشه receiver. در **Half-duplex Link** هر دو طرف می‌توانند ارسال و دریافت کنند، اما نه هم‌زمان. یعنی در هر لحظه فقط یک جهت فعال است. در **Full-duplex Link** دو طرف می‌توانند هم‌زمان ارسال و دریافت کنند، ولی wire و pin و logic بیشتری لازم است. 

اصطلاحات **Master** و **Slave** هم در پروتکل‌های بردی زیاد می‌آیند. **Master** دستگاهی است که transfer را شروع می‌کند، clock را تولید می‌کند و transaction را مدیریت می‌کند. **Slave** دستگاهی است که توسط master address یا select می‌شود. در **Multi-master** چند master ممکن است بخواهند bus را کنترل کنند. در این حالت **Arbitration**

 لازم است تا فقط یک master در هر لحظه کنترل bus را داشته باشد.

**Synchronization**

 یعنی هماهنگ‌کردن clock یا timing بین دستگاه‌ها. در synchronous links این کار با clock مشترک انجام می‌شود. در asynchronous links، receiver از start bit و timing داخلی برای sampling استفاده می‌کند. این مفاهیم خیلی تستی‌اند، چون گزینه‌ها معمولاً master/slave و transmitter/receiver را با هم قاطی می‌کنند.

نکتهٔ مهم: **Transmitter/Receiver** نقش مربوط به جهت داده است، اما **Master/Slave** نقش مربوط به کنترل transaction است. مثلاً در I2C read، master ممکن است receiver داده باشد، اما همچنان master است چون transaction و clock را کنترل می‌کند.

---

### 8. UART

**UART**

 یک serial asynchronous protocol است. transmitter از **PISO Register** استفاده می‌کند: دادهٔ parallel را load می‌کند، start bit را می‌فرستد، سپس بیت‌ها را shift می‌کند، و در پایان stop bit را نگه می‌دارد. receiver از **SIPO Register** و logic کنترلی استفاده می‌کند: line را monitor می‌کند، falling edge start bit را detect می‌کند، data bits را با timing مناسب sample می‌کند، و stop bit را بررسی می‌کند. 

UART

 ساده است و به همین دلیل روی embedded boards زیاد دیده می‌شود. مشکل امنیتی همین سادگی است. خیلی از دستگاه‌ها UART debug console دارند که بعد از تولید هم روی board باقی می‌ماند. اگر attacker به TX/RX/GND دسترسی پیدا کند، ممکن است boot logs، shell access، firmware messages یا configuration sensitive information ببیند.

UART

 معمولاً clock wire جدا ندارد، پس دو طرف باید baud rate سازگار داشته باشند. اگر baud rate اشتباه باشد، receiver بیت‌ها را غلط sample می‌کند. start bit برای synchronization
 
  موقتی هر character استفاده می‌شود. stop bit هم باید وجود داشته باشد تا receiver characterها را جدا کند.

در امتحان، UART را با I2C و SPI قاطی نکن. UART asynchronous است، clock مشترک ندارد، character-based است، و line idle معمولاً high است. I2C و SPI synchronous هستند و clock را master تولید می‌کند.

---

### 9. I2C

**I2C**

 یک serial synchronous protocol دو سیمه است. دو line اصلی دارد: **SDA / Serial Data Line** و **SCL / Serial Clock Line**. Clock توسط master تولید می‌شود. I2C معمولاً برای ارتباط چند chip روی فاصله کوتاه استفاده می‌شود، مثل اتصال microcontroller به sensors، EEPROM، RTC یا peripheralهای کم‌سرعت. فایل speed modeها را هم آورده: **Standard mode = 100 kHz، Fast mode = 400 kHz، Fast mode plus = 1 MHz**. 

I2C

 معمولاً **open-drain** است. یعنی دستگاه‌ها line را فعالانه به 0 می‌کشند، ولی 1 شدن line با pull-up resistor انجام می‌شود. به همین دلیل transition از 1 به 0 سریع‌تر است، اما transition از 0 به 1 کندتر است. این نکته در تست‌ها می‌آید: open-drain یعنی دستگاه‌ها 1 را drive نمی‌کنند؛ line را release می‌کنند تا pull-up آن را 1 کند.

در I2C، bus وقتی idle است که SDA و SCL هر دو logic 1 باشند. **Start Condition** وقتی است که SDA از 1 به 0 برود در حالی که SCL مقدار 1 دارد. در فایل یک جمله دربارهٔ SCL=0

 آمده، اما نمودار و تعریف استاندارد I2C نشان می‌دهد start/stop زمانی معتبرند که SCL high است. برای امتحان مفهومی معمولاً این را این‌طور حفظ کن: **START: SDA falling while SCL high** و **STOP: SDA rising while SCL high**
 
 . در خود شکل‌های صفحه ۶ و ۷ نیز start و stop با SCL high نشان داده شده‌اند. 

I2C byte-oriented

 است. master اول address slave را می‌فرستد، معمولاً 7 بیت address و 1 بیت **R/W**. بعد slave باید **ACK** بدهد. ACK یعنی receiver در clock نهم SDA را low می‌کند. اگر SDA low نشود، یعنی **NACK**؛ slave ممکن است حاضر نباشد یا درست کار نکند. بعد از آن byteهای data منتقل می‌شوند و هر byte باید acknowledge شود. در پایان، master **Stop Condition** صادر می‌کند. 

---

### 10. SPI

**SPI** 

هم serial synchronous است، اما نسبت به I2C سیم‌های بیشتری دارد و معمولاً full-duplex است. interface اصلی آن چهار pin دارد: **SCLK، MOSI، MISO، SS/CS**.
**SCLK**

 clock است که master تولید می‌کند.
**MOSI / Master Out Slave In**

 داده از master به slave است.
**MISO / Master In Slave Out** 

داده از slave به master است.
**SS/CS / Slave Select یا Chip Select**

 برای انتخاب slave استفاده می‌شود. 

در SPI single-slave، master مستقیماً clock و MOSI و CS را به slave می‌دهد و MISO را از slave می‌گیرد. در multi-slave دو روش داریم: **Independent** و **Daisy Chain**. در Independent، master clock و MOSI را با همه share می‌کند، اما برای هر slave یک CS جدا دارد. مشکل این روش scalability است، چون تعداد CSها باید از قبل کافی باشد. اگر slave زیاد شود، pin زیاد لازم داریم.

در **Daisy Chain**، MOSI فقط به slave اول می‌رود، خروجی slave اول به ورودی slave دوم وصل می‌شود و همین‌طور ادامه دارد. این ساختار مثل یک shift register بزرگ عمل می‌کند. master تعداد زیادی بیت می‌فرستد و داده در زنجیره حرکت می‌کند. این scalableتر است، اما latency و مدیریت داده پیچیده‌تر می‌شود.

از نظر امنیتی، SPI مهم است چون خیلی از flash memoryها، sensors، displays و secure elements از SPI استفاده می‌کنند. اگر attacker به **MOSI/MISO/SCLK/CS** دسترسی داشته باشد، ممکن است firmware flash را dump کند، traffic را sniff کند یا command تزریق کند. برای همین routing خطوط SPI حساس باید محافظت شود.

---

### 11. JTAG و چرا خطرناک است؟

**JTAG / Joint Test Action Group**

 یک serial protocol برای testing، debugging و programming است و بر اساس استاندارد **IEEE 1149.1** شناخته می‌شود. JTAG بسیار قدرتمند است چون به **Boundary Scan Chain** دسترسی دارد. یعنی می‌تواند به flip-flopها، register file و configuration registers داخل microcontroller یا IC دسترسی پیدا کند. همین قدرت، آن را از نظر امنیتی خطرناک می‌کند. 

JTAG

 از **TAP / Test Access Port** استفاده می‌کند. TAP معمولاً شامل یک controller کوچک، یک **Instruction Register / IR** و مجموعه‌ای از **Data Registers / DRs** است. interface 
 
 
 اصلی JTAG شامل این سیگنال‌هاست: **TCK / Test Clock، TMS / Test Mode Select، TDI / Test Data Input، TDO / Test Data Output** و گاهی **TRST / Test Reset**. TMS روی rising edge 
 
 
 از TCK مسیر state machine را تعیین می‌کند. 

در JTAG، داده‌ها و instructionها serial وارد می‌شوند. می‌توان registerها را خواند، نوشت، execution را متوقف کرد، state میکروکنترلر را دید و بعد execution را ادامه داد. برای developer عالی است؛ برای attacker هم عالی است، اگر باز و بدون authentication رها شود. مثلاً اگر attacker بتواند از طریق JTAG program counter را تغییر دهد، memory را بخواند یا debug mode را فعال کند، عملاً کنترل عمیق روی دستگاه دارد.

پس JTAG معمولاً یکی از خطرناک‌ترین interfaceها در PCB security است. در محصول نهایی یا باید disable شود، یا با authentication، fuse، lock mechanism، secure debug، encryption 


یا physical protection محدود شود. داشتن JTAG header باز روی board تقریباً دعوت‌نامه برای attacker است.

---

### 12. TAP Controller FSM و Public/Private Instructions

**TAP Controller**


 یک **FSM / Finite State Machine** با 16 state دارد. این stateها عمدتاً به دو مسیر تقسیم می‌شوند: مسیر **Data Register / DR** و مسیر **Instruction Register / IR**. عملیات‌های اصلی شامل **RESET، RUN-TEST، SCAN-DR، SCAN-IR** هستند. هر scan هم سه مرحله مهم دارد: **CAPTURE، SHIFT، UPDATE**. در CAPTURE مقدار گرفته می‌شود، در SHIFT
 
 
  بیت‌ها serial جابه‌جا می‌شوند، و در UPDATE مقدار جدید اعمال می‌شود. 

در بخش instructionها، JTAG چند دستور public/standard دارد. **BYPASS** mandatory است و TDI را تقریباً مستقیم با یک flip-flop واسط به TDO وصل می‌کند. **IDCODE** recommended


 است و device identifier را می‌دهد. **SAMPLE** mandatory است و boundary scan registers را به TDI/TDO وصل می‌کند تا بتوان محتویات را از بیرون دید. 

اما قسمت حساس‌تر **Private Instructions** است. JTAG اجازه می‌دهد vendor یا designer instructionهای اختصاصی تعریف کند. این می‌تواند مفید باشد، مثلاً برای test داخلی یا debug عمیق‌تر. اما اگر این private instructionها محافظت نشوند، attacker می‌تواند از آن‌ها برای دسترسی به بخش‌هایی استفاده کند که نباید بیرون باشند.

پس در طراحی امن، private instructionها باید key/password/authentication داشته باشند، یا در محصول نهایی غیرفعال شوند. صرف اینکه یک دستور private است، به معنی secure بودن آن نیست؛ security by obscurity کافی نیست.

---

### 13. Good Practices برای PCB Security

فایل چند practice اصلی پیشنهاد می‌کند. اول **Encryption and authenticated techniques**، مخصوصاً برای جلوگیری از leakage در interfaceهایی مثل JTAG. دوم **Private instructions** همراه با key یا password، برای جلوگیری از unauthorized access. سوم **Physical access restriction** یعنی دسترسی به pinهای IC و debug headerها سخت شود. چهارم **Internal wiring** یعنی خطوط حساس داخل برد قرار بگیرند؛ مثلاً با BGA و multilayer board. پنجم **Obfuscation Techniques** مثل **Permutation Networks** و **Dummy ICs**. 

**Permutation Network**



 یعنی بین دو component یک بلوک قابل برنامه‌ریزی بگذاریم که ارتباط درست را فقط با key مناسب برقرار کند. بدون key، connections اشتباه‌اند و سیستم درست کار نمی‌کند یا دادهٔ معنی‌دار منتقل نمی‌شود. این روش مخصوصاً وقتی مفید است که یکی از ICها programmable باشد. اگر permutation block خودش جدا و قابل reverse engineering باشد، امنیت کمتر می‌شود؛ بهتر است داخل programmable IC پنهان شود. 

**Dummy ICs**


 یعنی قطعات یا مسیرهای اضافی که دادهٔ واقعی تولید نمی‌کنند، بلکه random یا dummy signals تولید می‌کنند تا attacker گیج شود. هدف این است که attacker نتواند به‌راحتی تشخیص دهد کدام trace واقعی و کدام trace فریب‌دهنده است. عیبش واضح است: هزینه، پیچیدگی و power consumption را زیاد می‌کند. 

جمع‌بندی امنیتی این فصل: PCB security یعنی پنهان‌کردن و محافظت از critical pins و critical traces، محدودکردن debug interfaceها، استفاده از multilayer/BGA/internal routing، encrypt/authenticate کردن ارتباطات حساس، و سخت‌تر کردن reverse engineering با obfuscation. به‌خصوص **JTAG** باید جدی گرفته شود، چون دسترسی عمیق به داخل microcontroller می‌دهد.

---

# گام ۲: خلاصه تستی و مقایسه‌ای

## Bilingual Cheat-Sheet

## PCB Basics

| Keyword              | نکتهٔ تستی                                                           |
| -------------------- | -------------------------------------------------------------------- |
| **PCB**              | بردی که قطعات را نگه می‌دارد و آن‌ها را electrically connect می‌کند  |
| **FR4**              | مادهٔ رایج insulating layer                                          |
| **Copper**           | مادهٔ رایج metallization                                             |
| **Single-sided PCB** | ساده، routing محدود                                                  |
| **Double-sided PCB** | دو طرف برای component/routing                                        |
| **Multilayer PCB**   | لایه‌های داخلی؛ مناسب سیستم پیچیده و امنیت بیشتر                     |
| **Gerber File**      | فایل ASCII صنعتی شامل layout/geometry/connections برای manufacturing |

---

## Via Types

| Type                          | معنی                                    |
| ----------------------------- | --------------------------------------- |
| **PTH / Plated Through Hole** | سوراخ فلزکاری‌شده، اتصال الکتریکی       |
| **Non-PTH**                   | سوراخ بدون metallization، بیشتر مکانیکی |
| **Through-hole via**          | اتصال top به bottom                     |
| **Blind via**                 | اتصال external layer به internal layer  |
| **Buried via**                | اتصال بین دو internal layer             |

---

## Packages

| Package | ویژگی امنیتی/فنی                                                |
| ------- | --------------------------------------------------------------- |
| **DIP** | قدیمی، through-hole، probe آسان                                 |
| **SOP** | SMD، کوچک‌تر از DIP                                             |
| **QFP** | pins در چهار طرف                                                |
| **QFN** | pins کوچک در پایین/لبه، soldering سخت‌تر                        |
| **BGA** | pins زیر chip، probe سخت‌تر، مناسب‌تر برای hiding critical pins |

---

## Serial vs Parallel

| Feature     | **Serial Transmission**       | **Parallel Transmission** |
| ----------- | ----------------------------- | ------------------------- |
| Wires       | کم                            | زیاد                      |
| ارسال N بیت | N cycles                      | هم‌زمان                   |
| Latency     | بیشتر                         | کمتر                      |
| Complexity  | کمتر                          | بیشتر                     |
| Example use | sensors, low-throughput links | memory/data buses         |

---

## PISO / SIPO

| Register | سمت         | نقش                                            |
| -------- | ----------- | ---------------------------------------------- |
| **PISO** | Transmitter | Parallel data را load می‌کند و serial می‌فرستد |
| **SIPO** | Receiver    | serial bits را می‌گیرد و parallel data می‌سازد |

---

## Async vs Sync Serial

| Feature      | **Asynchronous**                     | **Synchronous**                           |
| ------------ | ------------------------------------ | ----------------------------------------- |
| Shared clock | ندارد                                | دارد                                      |
| Example      | **UART**                             | **I2C, SPI**                              |
| Unit         | character-based                      | frame/transaction-based                   |
| Sampling     | receiver خودش timing را تنظیم می‌کند | clock مشترک زمان sampling را تعیین می‌کند |
| Wires        | data کافی است، معمولاً TX/RX         | data + clock                              |

---

## Link Types

| Link                | معنی                                 |
| ------------------- | ------------------------------------ |
| **Simplex**         | فقط یک جهت                           |
| **Half-duplex**     | دو جهت، اما نه هم‌زمان               |
| **Full-duplex**     | دو جهت هم‌زمان                       |
| **Master**          | آغازکننده transfer، تولیدکننده clock |
| **Slave**           | device addressed by master           |
| **Arbitration**     | انتخاب master در multi-master bus    |
| **Synchronization** | هماهنگ‌سازی timing/clock             |

---

## UART

| Concept            | نکته                                                         |
| ------------------ | ------------------------------------------------------------ |
| **UART**           | Universal Asynchronous Receiver/Transmitter                  |
| **Idle line**      | معمولاً logic 1                                              |
| **Start bit**      | falling edge، line از 1 به 0                                 |
| **Data bits**      | بعد از start bit ارسال می‌شوند                               |
| **Stop bit**       | line حداقل یک bit time روی 1                                 |
| **Parity bit**     | optional error detection                                     |
| **Security issue** | debug console، logs، shell یا firmware info ممکن است لو برود |

---

## I2C

| Concept            | نکته                           |
| ------------------ | ------------------------------ |
| **I2C**            | Inter-Integrated Circuit       |
| **Wires**          | **SDA + SCL**                  |
| **Communication**  | Half-duplex                    |
| **Clock**          | توسط Master تولید می‌شود       |
| **Open-drain**     | 1→0 سریع، 0→1 کندتر با pull-up |
| **Standard mode**  | 100 kHz                        |
| **Fast mode**      | 400 kHz                        |
| **Fast mode plus** | 1 MHz                          |
| **Address phase**  | 7-bit address + R/W bit        |
| **ACK**            | SDA low در clock نهم           |
| **NACK**           | SDA high در clock نهم          |

---

## SPI

| Concept                     | نکته                                     |
| --------------------------- | ---------------------------------------- |
| **SPI**                     | Serial Peripheral Interface              |
| **Communication**           | Full-duplex                              |
| **Wires**                   | SCLK, MOSI, MISO, SS/CS                  |
| **MOSI**                    | Master Out Slave In                      |
| **MISO**                    | Master In Slave Out                      |
| **SS/CS**                   | انتخاب slave                             |
| **No standard**             | پیاده‌سازی‌ها می‌توانند متفاوت باشند     |
| **Independent multi-slave** | هر slave یک CS جدا                       |
| **Daisy chain**             | slaves مثل shift register زنجیره می‌شوند |

---

## JTAG

| Concept      | نکته                                                      |
| ------------ | --------------------------------------------------------- |
| **JTAG**     | Joint Test Action Group                                   |
| **Standard** | IEEE 1149.1                                               |
| **Use**      | testing, debugging, programming                           |
| **Risk**     | دسترسی به boundary scan، registers، microcontroller state |
| **TAP**      | Test Access Port                                          |
| **IR**       | Instruction Register                                      |
| **DR**       | Data Registers                                            |
| **TCK**      | Test Clock                                                |
| **TMS**      | Test Mode Select                                          |
| **TDI**      | Test Data Input                                           |
| **TDO**      | Test Data Output                                          |
| **TRST**     | optional asynchronous reset                               |

---

## TAP FSM

| Concept             | نکته                                            |
| ------------------- | ----------------------------------------------- |
| **16 states**       | FSM اصلی JTAG                                   |
| **Two paths**       | DR path و IR path                               |
| **Main operations** | RESET, RUN-TEST, SCAN-DR, SCAN-IR               |
| **Scan phases**     | CAPTURE, SHIFT, UPDATE                          |
| **TMS**             | روی rising edge از TCK مسیر FSM را تعیین می‌کند |

---

## JTAG Instructions

| Instruction              | وضعیت        | نقش                                      |
| ------------------------ | ------------ | ---------------------------------------- |
| **BYPASS**               | Mandatory    | TDI به TDO با یک FF واسط                 |
| **IDCODE**               | Recommended  | device identifier                        |
| **SAMPLE**               | Mandatory    | اتصال boundary scan registers به TDI/TDO |
| **Private Instructions** | User-defined | خطرناک اگر بدون authentication باشند     |

---

## Good Practices

| Practice                        | هدف                                       |
| ------------------------------- | ----------------------------------------- |
| **Encryption + Authentication** | جلوگیری از information leakage            |
| **Secure JTAG**                 | جلوگیری از unauthorized debug access      |
| **Physical access restriction** | سخت‌کردن probe کردن                       |
| **BGA**                         | پنهان‌کردن pins زیر chip                  |
| **Multilayer board**            | انتقال critical traces در internal layers |
| **Permutation Network**         | اتصال درست فقط با key                     |
| **Dummy IC / Dummy traces**     | گیج‌کردن attacker                         |
| **Obfuscation**                 | سخت‌کردن reverse engineering              |

---

# گام ۳: تله‌های امتحانی

## Pitfalls

### 1. PCB

 فقط mechanical support نیست

PCB

 فقط قطعات را نگه نمی‌دارد؛ مسیرهای electrical connection را هم فراهم می‌کند. از نظر امنیتی همین traces می‌توانند leak point باشند.

### 2. Non-PTH

 را با via اشتباه نگیر

**PTH / Via**

 metallization
 
  دارد و برای اتصال الکتریکی استفاده می‌شود.
**Non-PTH** 

فلزکاری ندارد و بیشتر mechanical است.

### 3. Multilayer PCB 

امنیت مطلق نمی‌دهد

Multilayer probing 


را سخت‌تر می‌کند، اما attacker حرفه‌ای هنوز می‌تواند reverse engineering انجام دهد. یعنی risk را کم می‌کند، حذف نمی‌کند.

### 4. Serial

 همیشه سریع‌تر نیست

Serial wire


 کمتر دارد، اما برای N بیت معمولاً N cycle لازم دارد. پس latency بیشتر می‌شود. گزینه‌ای که بگوید serial همیشه از parallel سریع‌تر است، غلط است.

### 5. UART clock 

مشترک ندارد

**UART** 

asynchronous 

است. Receiver از start bit و baud rate خودش برای sampling استفاده می‌کند. گزینه‌ای که بگوید UART همیشه clock line مشترک دارد، غلط است.

### 6. Start bit

 در UART یعنی falling edge

UART line 


در حالت idle معمولاً high است. شروع character با رفتن line از 1 به 0 مشخص می‌شود.

### 7. Master 

همیشه transmitter نیست

در I2C read، master داده را دریافت می‌کند، اما هنوز master است چون transaction و clock را کنترل می‌کند.

### 8. I2C


 و SPI هر دو synchronous هستند، ولی مثل هم نیستند

**I2C:** 


دو wire، half-duplex، address + ACK
**SPI:**


 چهار wire، full-duplex، slave select، no standard

### 9. ACK



 در I2C یعنی SDA low

در clock نهم، receiver باید SDA را low کند. اگر low نشود، **NACK** است.

### 10. Open-drain



 یعنی 1 را active drive نمی‌کنیم

در I2C، دستگاه‌ها line را به 0 می‌کشند. 1 شدن با pull-up انجام می‌شود؛ برای همین 0→1 کندتر است.

### 11. SPI independent multi-slave scalable 




نیست

چون برای هر slave یک CS جدا لازم دارد. Daisy chain scalableتر است، ولی مثل shift register بزرگ عمل می‌کند.

### 12. JTAG


 فقط برای test بی‌خطر نیست

JTAG 


می‌تواند registerها، state، execution و debug access را کنترل کند. باز گذاشتن JTAG در محصول نهایی خطر جدی است.

### 13. TMS


 و TCK را قاطی نکن

**TCK**

 clock
 
  است.
**TMS**

 state
 
  بعدی TAP controller را تعیین می‌کند، معمولاً sampled روی rising edge از TCK.

### 14. BYPASS 



یعنی مدار اصلی را تغییر نمی‌دهد

**BYPASS**


 TDI 
 
 را تقریباً مستقیم به TDO وصل می‌کند. برای زنجیره‌های JTAG مفید است، ولی کاری با عملکرد اصلی IC نمی‌کند.

### 15. Private Instruction



 به‌خودی‌خود secure نیست

Private


 بودن یعنی standard عمومی نیست؛ اما بدون key/password/authentication هنوز قابل سوءاستفاده است.

### 16. Dummy IC


 امنیت را رایگان نمی‌کند

**Dummy ICs**


 attacker 
 
 
 را گیج می‌کنند، اما cost، power و complexity را زیاد می‌کنند.

---

# گام ۴: خودآزمایی

## 100% English Quiz

### Question 1

**Which statement best describes the security risk of exposed PCB communication interfaces such as UART, I2C, SPI, and JTAG?**

A. They only affect mechanical robustness and cannot expose digital information.
B. They improve interoperability, but they can also expose signals that attackers may probe, analyze, or manipulate.
C. They are secure by default because they are standardized protocols.
D. They are used only during PCB manufacturing and disappear after assembly.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** این interfaceها digital signals حمل می‌کنند و می‌توانند information leakage یا control access ایجاد کنند.
* **B درست است:** پروتکل‌های استاندارد کار integration را ساده می‌کنند، اما اگر روی PCB قابل دسترسی باشند، attacker می‌تواند آن‌ها را probe یا manipulate کند.
* **C غلط است:** استاندارد بودن یعنی interoperability، نه security.
* **D غلط است:** UART، I2C، SPI و JTAG معمولاً روی محصول نهایی هم باقی می‌مانند، مگر عمداً حذف یا غیرفعال شوند.

---

### Question 2

**Which comparison between I2C and SPI is correct?**

A. I2C normally uses SDA and SCL and is half-duplex, while SPI typically uses SCLK, MOSI, MISO, and SS/CS and supports full-duplex communication.
B. I2C requires one chip-select line per slave, while SPI uses addressing and ACK bits on a two-wire bus.
C. SPI is asynchronous, while I2C is asynchronous but character-based like UART.
D. I2C and SPI both require only one data wire and no clock line.

**Correct Answer: A**

**تحلیل گزینه‌ها:**

* **A درست است:** **I2C** دو line اصلی **SDA/SCL** دارد و half-duplex است؛ **SPI** معمولاً چهار line دارد و full-duplex است.
* **B غلط است:** این توصیف برعکس است. I2C address + ACK دارد؛ SPI معمولاً SS/CS برای انتخاب slave دارد.
* **C غلط است:** هر دو synchronous هستند. UART asynchronous و character-based است.
* **D غلط است:** هر دو clock line دارند؛ I2C از SCL و SPI از SCLK استفاده می‌کند.

---

### Question 3

**Why is JTAG considered powerful but security-sensitive in embedded systems?**

A. Because it only reads the PCB temperature and cannot access internal registers.
B. Because it provides access to the boundary-scan chain and can read or modify internal registers and device state.
C. Because it is a wireless protocol used for long-range communication.
D. Because it prevents all debugging operations after manufacturing.

**Correct Answer: B**

**تحلیل گزینه‌ها:**

* **A غلط است:** JTAG فقط temperature یا اطلاعات سطحی نمی‌خواند؛ می‌تواند به boundary scan و registers دسترسی بدهد.
* **B درست است:** JTAG برای testing/debugging قدرتمند است، اما همین دسترسی به registerها و internal state آن را security-sensitive می‌کند.
* **C غلط است:** JTAG یک wired serial debug/test interface است، نه wireless.
* **D غلط است:** برعکس؛ JTAG برای debug و test طراحی شده است. مشکل وقتی است که در محصول نهایی بدون محافظت باقی بماند.
