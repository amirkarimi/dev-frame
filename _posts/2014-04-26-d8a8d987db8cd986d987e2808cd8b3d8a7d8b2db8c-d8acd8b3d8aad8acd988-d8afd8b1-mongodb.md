---
id: 455
title: بهینه‌سازی جستجو در MongoDB
date: 2014-04-26T18:32:16+00:00
author: امیر کریمی
layout: post
guid: https://dev-frame.amirkarimi.me/?p=455
permalink: '/2014/04/%d8%a8%d9%87%db%8c%d9%86%d9%87%e2%80%8c%d8%b3%d8%a7%d8%b2%db%8c-%d8%ac%d8%b3%d8%aa%d8%ac%d9%88-%d8%af%d8%b1-mongodb/'
views:
  - "1290"
categories:
  - فناوری
tags:
  - MongoDB
  - Performance
  - مانگو
---
در ساعات اوج، هر جستجو در کتب نمایشگاه تا حتی چند ثانیه طول می‌کشید. مشکل از Regular Expression بود.

من برای جستجو در مثلا عنوان کتاب از کوئری زیر استفاده می‌کردم:

<pre class="brush: jscript; title: ; notranslate" title="">{ "title": /متن جستجو/i }

</pre>

چون Regular Expression یک زبان کامل است امکان استفاده از اندیس‌ها در این حالت وجود ندارد و یک کوئری ساده روی چند صد هزار رکورد ممکن است تا ۱ ثانیه طول بکشد.

البته این کوئری قابلیت استفاده از اندیس‌ها را دارد:

<pre class="brush: jscript; title: ; notranslate" title="">{ "title": /^متن جستجو/i }

</pre>

ولی فقط عناوینی را بر می‌گردانند که با متن جستجو شروع شده باشند. معمولا ما در جستجو‌ها دنبال چنین حالتی نیستیم و فقط می‌خواهیم عبارت مورد نظرمان در هر جای متن که بود پیدا شود. پس این حالت هم پاسخگو نیست.

یک راه دیگر می‌ماند و آن استفاده از کلمه‌های کلیدی است. به این ترتیب که ابتدا همه رکوردهای موجود را بررسی کرده و از هر فیلد یک سری کلمه کلیدی استخراج کرد و آن‌ها را برای آن رکورد ذخیره کرد.

من اسکریپت زیر را برای اینکار نوشتم

<pre class="brush: jscript; title: ; notranslate" title="">function getKeywords(field) {
    if (field) {
        return field.toString().toLowerCase()
            .replace(/,|،|\(|\)|\[|\]|\"|\'/g, " ")
            .match(/[^ ]+/g);
    }
}

db.books.find().forEach(function(doc){
    doc.keywords = {};
    doc.keywords.title = getKeywords(doc.title)
    doc.keywords.subject = getKeywords(doc.subject)
    doc.keywords.author = getKeywords(doc.author)
    doc.keywords.translator = getKeywords(doc.translator)
    doc.keywords.publisher = getKeywords(doc.publisher)
    doc.keywords.distributor = getKeywords(doc.distributor)

    db.books.save(doc);
});

db.books.ensureIndex({"keywords.title":1})
db.books.ensureIndex({"keywords.subject":1})
db.books.ensureIndex({"keywords.author":1})
db.books.ensureIndex({"keywords.translator":1})
db.books.ensureIndex({"keywords.publisher":1})
db.books.ensureIndex({"keywords.distributor":1})

</pre>

با اجرای آن از هر فیلد یک آرایه استخراج می‌شود که به صورت جداگانه ذخیره کردم و در در آخر هم برای هر یک از این آرایه‌ها اندیس‌گذاری کردم.

حالا مثلاً کتابهایی که در عنوان آن‌ها عبارت جاوا وجود داشته باشد با کوئری زیر برگردانده می‌شوند

<pre class="brush: jscript; title: ; notranslate" title="">{ "keywords.title": "java" }

</pre>

اما زمان اجرای آن تقریبا برابر صفر است! بی نهایت سریعتر از Regular Expression عمل می‌کند. فقط لازم است که در هنگام جستجو عبارت مورد نظر کاربر را به درستی تقسیم کرد تا به نتیجه مورد نظرش برسد.

مثلاً اگر کاربر عبارت «شعر فارسی» را جستجو کند نباید همین عبارت عیناً در کوئری قرار گیرد بلکه باید به دو عبارت «شعر» و «فارسی» تقسیم شده و با دستور زیر جستجو شود

<pre class="brush: jscript; title: ; notranslate" title="">{ "keywords.subject": { $all: ["شعر", "فارسی"] } }

</pre>

فقط در این حالت ترتیب مهم نیست و هر عبارتی که در آن «شعر» و «فارسی» باشد جزو نتایج جستجو است.

نکته اینکه اگر می‌خواستم تنها روی یک فیلد چنین امکانی داشته باشم خود Mongo امکانش را از طریق <a href="http://docs.mongodb.org/manual/core/index-text/" target="_blank">اندیس متنی </a>می‌داد.

باید به این نکته هم توجه کرد که تعداد مراجعات به وب سایت نمایشگاه کتاب انقدر زیاد است که حتی سیستم آمارگیری که استفاده می‌کردم به مشکل خورد! به هر حال حالا سرعت جستجو نشان دهنده قدرت واقعی تکنلوژی استفاده شده است.

حالا لود سیستم که به ۶ هم رسیده بود به حالت اول بازگشت ولی مقدار رم مصرفی بیشتر شده که طبیعی است:

<p style="text-align: center;">
  <a href="/wp-content/uploads/2014/04/systemload-2014-04-26-192959.png"><img class="size-full wp-image-467 aligncenter" alt="systemload 2014-04-26 19:29:59" src="/wp-content/uploads/2014/04/systemload-2014-04-26-192959.png" width="498" height="68" srcset="/wp-content/uploads/2014/04/systemload-2014-04-26-192959.png 498w, /wp-content/uploads/2014/04/systemload-2014-04-26-192959-300x40.png 300w" sizes="(max-width: 498px) 100vw, 498px" /></a>
</p>

<span style="line-height: 1.5em;">لطفاً امتحان کنید و نظر بدید: </span><a style="line-height: 1.5em;" href="http://www.tibf.ir/fa/book" target="_blank">http://www.tibf.ir/fa/book</a>