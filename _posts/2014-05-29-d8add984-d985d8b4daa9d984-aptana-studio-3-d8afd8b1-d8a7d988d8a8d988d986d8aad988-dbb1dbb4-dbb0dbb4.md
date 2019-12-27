---
id: 541
title: حل مشکل Aptana Studio 3 در اوبونتو ۱۴.۰۴
date: 2014-05-29T09:06:13+00:00
author: امیر کریمی
layout: post
guid: https://dev-frame.amirkarimi.me/?p=541
permalink: '/2014/05/%d8%ad%d9%84-%d9%85%d8%b4%da%a9%d9%84-aptana-studio-3-%d8%af%d8%b1-%d8%a7%d9%88%d8%a8%d9%88%d9%86%d8%aa%d9%88-%db%b1%db%b4-%db%b0%db%b4/'
views:
  - "3512"
categories:
  - فناوری
tags:
  - Eclipse
  - Rails
  - Ruby
  - اکلیپس
  - روبی
  - ریلز
---
به تازگی Aptana Studio 3 را بر روی اوبونتو ۱۴.۰۴ نصب کردم که هر از چند گاهی بدون دلیل کرش می‌کند و بدون هیچ پیغامی بسته می‌شود. برای حل این مشکل یک راه حل ساده وجود دارد.

این کد را به انتهای فایل AptanaStudio3.ini اضافه کنید:

<pre class="brush: plain; title: ; notranslate" title="">-Dorg.eclipse.swt.browser.DefaultType=mozilla
</pre>

فایل AptanaStudio3.ini در ریشه فولد Aptana Studio قرار دارد.

منبع <a href="http://www.webupd8.org/2014/05/fix-aptana-studio-3-crashing-in-ubuntu.html" target="_blank">webupd8</a>