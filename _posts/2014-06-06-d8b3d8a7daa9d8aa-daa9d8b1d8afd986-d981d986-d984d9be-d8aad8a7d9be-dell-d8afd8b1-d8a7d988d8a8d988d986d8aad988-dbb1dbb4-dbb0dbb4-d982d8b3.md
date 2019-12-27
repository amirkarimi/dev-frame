---
id: 575
title: 'ساکت کردن فن لپ تاپ DELL در اوبونتو ۱۴.۰۴ &#8211; قسمت ۲'
date: 2014-06-06T19:54:21+00:00
author: امیر کریمی
layout: post
guid: http://www.dev-frame.com/?p=575
permalink: '/2014/06/%d8%b3%d8%a7%da%a9%d8%aa-%da%a9%d8%b1%d8%af%d9%86-%d9%81%d9%86-%d9%84%d9%be-%d8%aa%d8%a7%d9%be-dell-%d8%af%d8%b1-%d8%a7%d9%88%d8%a8%d9%88%d9%86%d8%aa%d9%88-%db%b1%db%b4-%db%b0%db%b4-%d9%82%d8%b3/'
views:
  - "10621"
categories:
  - فناوری
tags:
  - Fan
  - Linux
  - Ubuntu
  - اوبونتو
  - فن
  - لینوکس
---
نصب پیش‌فرض i8k که در قسمت قبل این پست گفتم باعث بالا رفتن حرارت لپ‌تاپم شد. چون تنظیمات پیشفرض آن تا حدود دمای ۷۰ درجه را عادی تلقی می‌کند.

برای تنظیم اینکه فن با چه سرعتی در چه بازه حرارتی کار کند اول باید فایل `/etc/i8kmon.conf` را ایجاد کنیم:

<pre class="brush: bash; title: ; notranslate" title="">sudo gedit /etc/i8kmon.conf
</pre>

و داخل آن می‌توانیم i8k را کانفیگ کنیم که یک کانفیگ نمونه به شکل زیر است:

<pre class="brush: plain; title: ; notranslate" title="">
{% highlight config %}
# Run as daemon, override with --daemon option
set config(daemon)      0

# Automatic fan control, override with --auto option
set config(auto)        1

# Report status on stdout, override with --verbose option
set config(verbose) 1

# Status check timeout (seconds), override with --timeout option
set config(timeout) 20

# Temperature thresholds: {fan_speeds low_ac high_ac low_batt high_batt}
set config(0)   \{\{-1 0\}  -1  40  -1  40\}
set config(1)   \{\{-1 1\}  30  60  30  60\}
set config(2)   \{\{-1 2\}  53  128  53  128\}

# end of file
{% endhighlight %}

دقت شود که اگر لپ‌تاپ مجهز به دو فن باشد باید تنظیمات متفاوتی را استفاده کرد.

مهمترین قسمت این تنظیمات خطوط ۱۵ تا ۱۷ هستند که به ترتیب زیر عمل می‌کند:

  * در صورتی که دمای CPU زیر ۴۰ درجه باشد فن خاموش می‌شود
  * اگر دما زیر ۶۰ درجه باشد فن با سرعت ۱ به کار می‌افتد
  * اگر دما بالای ۶۰ درجه باشد فن با سرعت ۲ به کار می‌افتد

حالا با ریست کردن سرویس i8kmon با دستور زیر و یا ریست کردن سیستم فن‌ها بیشتر کار می‌کنند و سیستم خنک‌تر می‌مانند:

<pre class="brush: bash; title: ; notranslate" title="">sudo service i8kmon restart
</pre>

منبع: <a href="http://askubuntu.com/questions/281478/fan-speed-in-ubuntu-pwmconfig-no-pwm-capable-sensor-modules-installed" target="_blank">Ask Ubuntu</a>