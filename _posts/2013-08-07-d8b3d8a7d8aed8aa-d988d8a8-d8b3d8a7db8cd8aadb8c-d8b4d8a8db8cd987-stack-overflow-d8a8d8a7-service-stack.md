---
id: 18
title: ساخت وب سایتی شبیه Stack Overflow با Service Stack
date: 2013-08-07T20:49:31+00:00
author: امیر کریمی
layout: post
guid: http://dev-frame.com/post.aspx?id=9e7089f1-12fd-4201-8269-e314e0893d75
permalink: '/2013/08/%d8%b3%d8%a7%d8%ae%d8%aa-%d9%88%d8%a8-%d8%b3%d8%a7%db%8c%d8%aa%db%8c-%d8%b4%d8%a8%db%8c%d9%87-stack-overflow-%d8%a8%d8%a7-service-stack/'
views:
  - "1449"
categories:
  - فناوری
tags:
  - Service Stack
---
یکی از نمونه‌هایی که در سایت Service Stack برای یادگیری برنامه‌نویسان و نمایش قدرت و سادگی این چارچوب قرارداده شده، یک برنامه تحت وب تک صفحه‌ای (Singe Page Application) است که سعی کرده Stack Overflow را شبیه سازی کند.

<a href="http://www.servicestack.net/RedisStackOverflow/" target="_blank">این برنامه وبی نمونه</a> که بر روی سرور لینوکس و وب سرور nginx بارگذاری شده تنها از یک فایل `HTML` و یک فایل #`C` تشکیل شده. برای ذخیره سازی داده‌ها هم از Redis (که یک بانک اطلاعاتی NoSQL است) استفاده شده!