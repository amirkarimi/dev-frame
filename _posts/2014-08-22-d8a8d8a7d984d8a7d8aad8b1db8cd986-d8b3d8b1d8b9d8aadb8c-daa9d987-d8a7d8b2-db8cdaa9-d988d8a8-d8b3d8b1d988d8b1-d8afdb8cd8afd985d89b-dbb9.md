---
id: 610
title: بالاترین سرعتی که از یک وب سرور دیدم؛ ۹ میلی ثانیه!
date: 2014-08-22T16:09:57+00:00
author: امیر کریمی
layout: post
guid: http://www.dev-frame.com/?p=610
permalink: '/2014/08/%d8%a8%d8%a7%d9%84%d8%a7%d8%aa%d8%b1%db%8c%d9%86-%d8%b3%d8%b1%d8%b9%d8%aa%db%8c-%da%a9%d9%87-%d8%a7%d8%b2-%db%8c%da%a9-%d9%88%d8%a8-%d8%b3%d8%b1%d9%88%d8%b1-%d8%af%db%8c%d8%af%d9%85%d8%9b-%db%b9/'
views:
  - "16444"
categories:
  - فناوری
tags:
  - Performance
  - Play Framework
  - Scala
  - کارایی
---
بعضی وقت‌ها سرعت وب سرور‌ها را به صورت تفننی بررسی می‌کنم. یک مقایسه جالب هم بین Django، Rails و Play Framework انجام داده‌ام که در آینده منتشر خواهم کرد. اما این بار یک سرعت باور نکردنی از وب سرور Play Framework دیدم.

این یک برنامه است که با Play Framework 2.3 و زبان Scala نوشته شده، در این صفحه یک رکورد از بانک اطلاعاتی MongoDB دریافت و یک صفحه Html ساده در خروجی رندر می‌شود.

<p style="text-align: center;">
  <a href="/wp-content/uploads/2014/08/Screenshot-from-2014-08-22-164232.png"><img class="aligncenter  wp-image-611" alt="Screenshot from 2014-08-22 16:42:32" src="/wp-content/uploads/2014/08/Screenshot-from-2014-08-22-164232.png" width="541" height="74" srcset="/wp-content/uploads/2014/08/Screenshot-from-2014-08-22-164232.png 901w, /wp-content/uploads/2014/08/Screenshot-from-2014-08-22-164232-300x41.png 300w" sizes="(max-width: 541px) 100vw, 541px" /></a>
</p>

<p style="text-align: center;">
  <p style="text-align: right;">
    همانطور که در تصویر قابل مشاهده است تمام این عملیات تنها در ۹ میلی ثانیه انجام می‌شود. نکته جالب اینجاست که این وب سرور Develop است و در زمان توسعه نیز روی همین وب سرور (Netty) کار می‌کنم. هر چند این صفحه به صورت Local باز شده اما باز هم ۹ میلی ثانیه زمان بسیار کمیست.
  </p>
  
  <p style="text-align: right;">
    این کار را با وب سرور‌هایی که به صورت روزمره با آن‌ها سر و کار دارید انجام دهید، فقط کافیست در کروم یا فایرفاکس دکمه F12 را بزنید و به قسمت Network رفته و صفحه را رفرش کنید.
  </p>