---
id: 215
title: پردازش توزیع شده و غیر همزمان تصویر در MongoDB با Scala
date: 2014-02-11T00:24:38+00:00
author: امیر کریمی
layout: post
guid: https://dev-frame.amirkarimi.me/?p=215
permalink: '/2014/02/%d9%be%d8%b1%d8%af%d8%a7%d8%b2%d8%b4-%d8%aa%d9%88%d8%b2%db%8c%d8%b9-%d8%b4%d8%af%d9%87-%d9%88-%d8%ba%db%8c%d8%b1-%d9%87%d9%85%d8%b2%d9%85%d8%a7%d9%86-%d9%81%d8%a7%db%8c%d9%84-%d8%af%d8%b1-mongodb-scal/'
views:
  - "1137"
categories:
  - فناوری
tags:
  - MongoDB
  - Scala
  - اسکالا
  - تجربه
  - مانگو
---
<a href="http://www.mongodb.org/" target="_blank">MongoDB</a> فایل‌های بزرگتر از ۱۶ مگابایت را به صورت تکه تکه شده ذخیره می‌کند. به هر کدام از این تکه‌ها یک Chunk می‌گویند. قوانینی که برای ذخیره و بازیابی فایل در MongoDB وضع شده <a href="http://docs.mongodb.org/manual/core/gridfs/" target="_blank">GridFS</a> نام دارد که توسط درایورهای MongoDB پیاده‌سازی می‌شود.

وقتی می‌خواهیم یک فایل را از MongoDB بخوانیم، فایل به صورت Chunkهایی از آرایه‌های بایت ارائه می‌شوند (Chunks of Arrays of Bytes). مثلاً یک فایل ۱۰۰ مگا بایتی در ۱۰۰ قطعه ۱ کیلوبایتی ارائه می‌شود.

چرا سازندگان روانی MongoDB این قانون را وضع کردند؟ MongoDB یک بانک اطلاعات است که از اول برای توزیع فیزیکی طراحی شده و ممکن است لازم باشد یک فایل بزرگ در بین ماشین‌های مختلف توزیع شود. و توزیع یک فایل ۱۰۰ مگابایتی سخت از توزیع ۱۰۰ قطعه یک کلیو بایتی است.

اگر هم نخواهیم فایل را در Mongo ذخیره کنیم و فقط مسیر فایل را در بانک ذخیره کنیم آن وقت اگر بخواهیم برنامه‌ را به صورت توزیع شده روی چند سرور اجرا کنیم مشکلات زیادی در Sync کردن فولدری که فایل‌ها را در آن‌ها نگهداری می‌کنیم خواهیم داشت. توجه کنید که ممکن است Sync دو طرفه باشد. اگر هم بخواهیم یک فولدر مرکزی Share شده برای فایل‌ها بگذاریم که دیگر سیستممان توزیع شده نیست!

تا اینجا خیلی مسئله پیچیده‌ای نبود. نکته مهم اینجاست که در برنامه‌نویسی غیر همزمان در هنگام خواندن یک فایل از Mongo ما نمی‌توانیم صبر کنیم تا تمام قطعات برسند و بعد آنها را به هم بچسبانیم و ارائه دهیم یا حرکتی روی آن‌ها بزنیم. بلکه با رسیدن هر قطعه به صورت Event-driven به ما اطلاع داده می‌شود و باید یک جوری قطعه‌ها را به هم بچسبانیم یا (از آن بهتر) روی همان قطعه کوچک کار کنیم و همان را به مصرف کننده پاس دهیم.

<span style="line-height: 1.5em;">خوشبختانه در چارچوب Play امکانی فراهم شده که اجازه می‌دهد همانطور که MongoDB قطعه، قطعه فایل‌ها را به دست ما می‌رساند ما هم آنها را دست به دست به دست Browser کاربر نهایی برسانیم. کل ماجرا با جزئیاتش پیچیده‌ است ولی با کمک ویژگی‌های Scala واقعاً به راحتی انجام می‌شود. مفهومی وجود دارد به نام </span><a style="line-height: 1.5em;" href="http://mandubian.com/2012/08/27/understanding-play2-iteratees-for-normal-humans/" target="_blank">Iteratee</a><span style="line-height: 1.5em;"> (درکش کمی مشکل است ولی واقعاً کاربردی است) که با کمک آن این‌ قبیل کارها راحتتر انجام می‌شود.</span>

اما یک مشکل دیگر وجود دارد؛ مثلاً فرض کنید یک فایل تصویری در MongoDB داریم که می‌خواهیم در لحظه (بعد از درخواست Browser) سایز آن را تغییر دهیم (از آن Thumbnail بسازیم). قبلاً اکثر ما بارها در #C آن را انجام دادیم و کتابخانه‌های مکفی برای اینکار وجود دارند. فایل تصویر را می‌خوانیم، (در قالب Stream یا آرایه‌ای از بایت) به کتابخانه Image Resizer محبوبمان می‌سپاریم و تصویر Resize شده را تحویل می‌گیریم بعد آن را با HTTP به سمت Browser می‌فرستیم. جداً کار سختی نیست. مثل شکل زیر:

<p style="text-align: center;">
  <a href="/wp-content/uploads/2014/02/Screenshot-from-2014-02-11-001506.png"><img class="size-full wp-image-220 aligncenter" alt="پردازش خطی" src="/wp-content/uploads/2014/02/Screenshot-from-2014-02-11-001506.png" width="478" height="138" srcset="/wp-content/uploads/2014/02/Screenshot-from-2014-02-11-001506.png 478w, /wp-content/uploads/2014/02/Screenshot-from-2014-02-11-001506-300x86.png 300w" sizes="(max-width: 478px) 100vw, 478px" /></a>
</p>

ولی من نمی‌خواهم یک فایل کامل را به یک کتابخانه بدهم تا آن را تبدیل به یک تصویر کوچکتر کند. همانطور که گفتم نمی‌توانم (نباید) صبر کنم تا فایل کامل شود و بعد به سراغ Resize کردن بروم. بلکه وقتی اولین قطعه از فایل (که ممکن است مثلاً ۱ کیلوبایت باشد) به دستم برسد باید شروع به محاسبات تغییر اندازه کنم و منتظر قطعه بعدی نباشم و تا همینجای کار را برای مصرف کننده ارسال کنم. به این ترتیب هم حافظه کمتری مصرف می‌کنم هم در زمان ترافیک بالا خیلی خیلی کمتر از منابع سیستم استفاده خواهم کرد. ولی این واقعاً پیچیده است؛ در مایه‌های فیلم‌های عملی تخیلی! مثل شکل زیر:

<p style="text-align: center;">
  <a href="/wp-content/uploads/2014/02/Screenshot-from-2014-02-11-002029.png"><img class="aligncenter size-full wp-image-227" alt="پردازش قطعه قطعه" src="/wp-content/uploads/2014/02/Screenshot-from-2014-02-11-002029.png" width="543" height="156" srcset="/wp-content/uploads/2014/02/Screenshot-from-2014-02-11-002029.png 543w, /wp-content/uploads/2014/02/Screenshot-from-2014-02-11-002029-300x86.png 300w" sizes="(max-width: 543px) 100vw, 543px" /></a>
</p>

نمی‌دانم الگورتیمی وجود دارد که بتواند به صورت تکه تکه (نه از نظر تصویر بلکه از نظر محتوی فایل) یک فایل تصویری را Resize کند؟ اصلاً نمی‌دانم فرمت‌های تصویری مرسوم (Jpeg، Gif و &#8230;) قابلیت پردازش به این شکل را دارند یا نه؟

ولی سعی کردم؛ در <a href="https://groups.google.com/forum/#!forum/reactivemongo" target="_blank">گروه ReactiveMongo</a> یک <a href="https://groups.google.com/forum/#!topic/reactivemongo/IlezQssBtOI" target="_blank">پست</a> ارسال کردم وتقاضای کمک کردم. اگر این کار ممکن باشد تاثیر به شدت زیادی در Preformance وب سایت‌هایی که با این روش ساخته می‌شوند خواهند داشت و پاسخگویی سرور به تعداد بالای کاربر به شدت افزایش پیدا می‌کند.

هرچند پردازشی که روی تصویر انجام می‌شود ساده است ولی توزیع پذیری و کارایی بالای این روش قابل تعمیم است و قابل تصور نیست که چه کارهایی می‌توان با آن انجام داد.

<span style="line-height: 1.5em;">برای حسن ختام، </span><a style="line-height: 1.5em;" href="https://gist.github.com/gre/4058734" target="_blank">این یک قطعه کد بر روی چارچوب Play و زبان Scala است</a> <span style="line-height: 1.5em;">که یک فایل Zip را در همان زمان تولید، خرد خرد به کلاینت ارسال می‌کند (صبر نمی‌کند که اول فایل ایجاد شود و در آخر آن را ارسال کند).</span>

تا قبل از مهاجرت به Scala صحبت در مورد این روش پرداش اطلاعات واقعاً یک کار بسیار سخت یا نشدنی به نظر می‌رسید ولی الان حتی اگر این کارها به راحتی قابل انجام نباشد حداقل قابل لمس شده.

_توضیح: تصاویر این پست توسط نرم‌افزار <a href="http://www.libreoffice.org" target="_blank">LibreOffice</a> Draw و با بی دقتی بسیار اما به راحتی ساخته شده است. لطفاً اگر از Microsoft Visio استفاده می‌کنید و نمی‌توانید آن‌ را بخرید (کپی رایت را رعایت نمی‌کنید)، از LibreOffice Draw استفاده کنید._[  
](/wp-content/uploads/2014/02/Screenshot-from-2014-02-11-002029.png)