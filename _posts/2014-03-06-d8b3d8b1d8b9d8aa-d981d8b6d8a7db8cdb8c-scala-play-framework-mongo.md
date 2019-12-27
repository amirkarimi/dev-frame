---
id: 272
title: سرعت فضایی Scala و Play Framework و Mongo
date: 2014-03-06T00:00:30+00:00
author: امیر کریمی
layout: post
guid: http://www.dev-frame.com/?p=272
permalink: '/2014/03/%d8%b3%d8%b1%d8%b9%d8%aa-%d9%81%d8%b6%d8%a7%db%8c%db%8c-scala-play-framework-mongo/'
views:
  - "8919"
categories:
  - دسته‌بندی نشده
  - فناوری
tags:
  - Linux
  - MongoDB
  - Open Source
  - Performance
  - Scala
  - اسکالا
  - تجربه
  - خبر
  - لینوکس
  - مانگو
---
<p style="text-align: center;">
  <a href="/wp-content/uploads/2014/03/so_happy_small1.jpg"><img class="size-full wp-image-298 aligncenter" alt="so_happy_small" src="/wp-content/uploads/2014/03/so_happy_small1.jpg" width="300" height="174" /></a>
</p>

<p style="text-align: right;">
  از شعف در پوست خود نمی‌گنجم!
</p>

برای پروژه اخیر اسکالا یک سرور مجازی بر روی یک سرور مجازی دیگر راه انداختم. چون سرور اصلی مجازی بود VMWare جواب نداد و از VirtualBox و مد Binary Translation که کندترین حالت مجازی سازی است استفاده کردم. ۱ گیگابایت رم و ۴۰ گیگابایت هارد به آن اختصاص دادم. یک سرور ۳۲ بیتی اوبتو ۱۲.۰۴ روی آن نصب کردم. بعد جاوا، MongoDB، Play Framework و بقیه. و خلاصه پروژه را رویش بالا آوردم.

یک سرور Git هم برای خودکار سازی انتشار برنامه بر روی سرور نصب کردم و با یک اسکریپت ساده برنامه را بر روی سرور Publish می‌کنم.

سرعت اجرا و پاسخگویی برنامه واقعاً عجیب است! یک لحظه شک کردم که شاید دارم با نسخه روی سیستم خودم کار می‌کنم. به یکی از بچه‌ها هم زنگ زدم و خواستم سایت را چک کند. او هم از سرعت بالای سایت تعجب کرد. همه چیز عالی و با سرعت و دقت کار می‌کند **در حالی که فقط حدود ۱۰۰ مگابایت از رم سرور مصرف شده بود با فقط یک CPU  تک هسته‌ای ۳۲ بیتی که دو مرتبه مجازی سازی شده! **

<span style="line-height: 1.5em;">رکوردهای یکی از جداول را زیادتر کردم تا به تعداد بیشتر از ۲۶۰،۰۰۰ رکورد رسید و کلی اطلاعات دیگر به سیستم اضافه شد. بعد از چند ساعت کار کردن با سرور، رم مصرفی کل سیستم (به جز cache) به ۴۴۰ مگابایت رسید ولی باز هم باورد کردنی نیست. یعنی خود لینوکس، جاوا، برنامه‌ها، بانک اطلاعات و خلاصه <strong>همه چیز</strong>، فقط ۴۰۰ مگابایت!!!</span>

<p style="text-align: center;">
  <a href="/wp-content/uploads/2014/03/Screenshot-from-2014-03-06-004236.png"><img class="size-full wp-image-287 aligncenter" alt="Screenshot from 2014-03-06 00:42:36" src="/wp-content/uploads/2014/03/Screenshot-from-2014-03-06-004236.png" width="515" height="80" srcset="/wp-content/uploads/2014/03/Screenshot-from-2014-03-06-004236.png 515w, /wp-content/uploads/2014/03/Screenshot-from-2014-03-06-004236-300x46.png 300w" sizes="(max-width: 515px) 100vw, 515px" /></a>
</p>

البته قبلاً با ۱ میلیون رکورد روی سیستم خودم (که آن هم لینوکس است) تست کرده بودم و باز هم سرعت باور نکردنی بود! ولی فکر نمی‌کردم با چنین سرور ضعیفی چنین پاسخی بگیرم.

همین تعداد رکورد را در SQL Server دارم. در حالتی که سر سرور خلوت است فقط ۸۰۰ مگابایت توسط SQL Server **به تنهایی** مصرف می‌شود! همیشه مجبور بودم یک سرور ویندوزی با حداقل ۴ گیگابایت رم تهیه کنم و برای SQL Server حتما از نسخه Standard یا Enterprise (نسخه‌های دزدی) استفاده کنم تا بتواند بیشتر از ۱ گیگابایت رم را استفاده کند و سرعت خوبی داشته باشد. خود ویندوز و دات نت و بقیه برنامه‌ها هم که بقیه رم را مصرف می‌کردند و تقریبا چیزی باقی نمی‌ماند.

<p style="text-align: center;">
  <a href="/wp-content/uploads/2014/03/Screenshot-from-2014-03-06-005007.png"><img class="size-full wp-image-289 aligncenter" alt="Screenshot from 2014-03-06 00:50:07" src="/wp-content/uploads/2014/03/Screenshot-from-2014-03-06-005007.png" width="451" height="120" srcset="/wp-content/uploads/2014/03/Screenshot-from-2014-03-06-005007.png 451w, /wp-content/uploads/2014/03/Screenshot-from-2014-03-06-005007-300x79.png 300w" sizes="(max-width: 451px) 100vw, 451px" /></a>
</p>

متاسفانه بخاطر مسائل امنیتی تا کامل شدن کار فعلاً نمی‌توانم لینک وب سایت را منتشر کنم ولی به زودی این کار را خواهم کرد.

پیشنهاد می‌کنم اگر تجربه‌ی کار با لینوکس را ندارید حتماً در اولین فرصت شروع کنید.