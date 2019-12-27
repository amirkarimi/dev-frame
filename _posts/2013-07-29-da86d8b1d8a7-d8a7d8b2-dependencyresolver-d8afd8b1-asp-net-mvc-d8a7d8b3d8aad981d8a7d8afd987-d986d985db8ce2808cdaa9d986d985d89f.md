---
id: 19
title: چرا از DependencyResolver در ASP.NET MVC استفاده نمی‌کنم؟
date: 2013-07-29T07:39:36+00:00
author: امیر کریمی
layout: post
guid: http://dev-frame.com/post.aspx?id=3ff2b549-bed0-4f07-b9c9-884f1458ebcf
permalink: '/2013/07/%da%86%d8%b1%d8%a7-%d8%a7%d8%b2-dependencyresolver-%d8%af%d8%b1-asp-net-mvc-%d8%a7%d8%b3%d8%aa%d9%81%d8%a7%d8%af%d9%87-%d9%86%d9%85%db%8c%e2%80%8c%da%a9%d9%86%d9%85%d8%9f/'
views:
  - "1617"
categories:
  - فناوری
tags:
  - ASP.NET MVC
  - Dependency Injection
---
یکی از راه‌های استفاده از <a href="http://en.wikipedia.org/wiki/Dependency_injection" target="_blank">Dependency Injection</a> در ASP.NET MVC استفاده از متد <a href="http://msdn.microsoft.com/en-us/library/gg416564(v=vs.108).aspx" target="_blank">DependencyResolver.SetResolver</a> است. اما من هیچ وقت از آن استفاده نمی‌کنم. هر چند DependencyResolver از <a href="http://blog.ploeh.dk/2010/02/03/ServiceLocatorisanAnti-Pattern/" target="_blank">ضد الگوی Service Locator</a> استفاده می‌کند ولی دلیل من برای عدم استفاده از آن فقط این نیست.

بسیاری از DI Container ها از HTTP Context Lifetime پشتیبانی می‌کنند که فقط مختص محیط وب است؛ یعنی هر گاه HTTP Request آغاز می‌شود یک Container مسئول ایجاد اشیاء شده و تا انتهای اجرای HTTP Request باقی می‌ماند. بعد از اتمام HTTP Request تمامی اشیاء ایجاد شده که IDisposable باشند از بین خواهند رفت و سپس خود Container نابود می‌شود.

مهم‌ترین دلیل من برای استفاده نکردن از DependencyResolver، مدیریت Lifetime است. Lifetime مورد نظر من HTTP Request نیست بلکه Controller است. خوشبختانه در ایجاد Controller ها در ASP.NET MVC از Factory Pattern استفاده شده و با پیاده‌سازی <a href="http://msdn.microsoft.com/en-us/library/system.web.mvc.icontrollerfactory(v=vs.108).aspx" target="_blank">IControllerFactory</a> می‌توان Factory خود را داشت. من هم همین کار را در <a href="https://github.com/AmirKarimi/Xoqal" target="_blank">چارچوب زغال</a> انجام دادم و یک Factory‌ به نام <a href="https://github.com/AmirKarimi/Xoqal/blob/master/Source/Xoqal.Web.Mvc/UnityControllerFactory.cs" target="_blank">UnityControllerFactory</a> ایجاد کردم. که با دستور زیر می‌توان آن‌را در Global.asax.cs ثبت کرد:

<div class="wlWriterEditableSmartContent" id="scid:f32c3428-b7e9-4f15-a8ea-c502c7ff2e88:ce0aea07-d6b2-45f7-b395-2d9eaf6ccb10" style="float: none; margin: 0px; display: inline; padding: 0px;">
  <pre class="brush: c#;toolbar:false;">ControllerBuilder.Current.SetControllerFactory(new DefaultControllerFactory(myContainer));</pre>
</div>

Unity Container به صورت پیش‌فرض از HTTP Request Lifetime پشتیبانی نمی‌کند اما در عوض از Hierarchical Lifetime بهره می‌برد. هر Container در Unity می‌تواند Fork شده و از خودش زیر مجموعه‌هایی ایجاد کنند که دقیقاً مشخصات پدرشان را دارند اما دارای Lifetime جداگانه هستند. وقتی این Containerهای فرزند نابود می‌شوند تمام اشیایی که توسط خودشان ایجاد شده و دارای طول عمر Hierarchical باشند را هم با خود نابود خواهند کرد.

برای ثبت یک وابستگی با طول عمر Hierarchical Lifetime در Unity به شکل زیر عمل می‌شود:

<div class="wlWriterEditableSmartContent" id="scid:f32c3428-b7e9-4f15-a8ea-c502c7ff2e88:f455e049-1686-4bf1-a54e-267a46b899fd" style="float: none; margin: 0px; display: inline; padding: 0px;">
  <pre class="brush: c#;toolbar:false;">myContainer
    .RegisterType&lt;Data.IUserRepository, Data.UserRepository&gt;(
        new HierarchicalLifetimeManager())</pre>
</div>

اما چرا این حد کنترل Lifetime برایم مهم است؟

علاوه بر اینکه با استفاده از IControllerFactory ضد الگوی Service Locator را دور زده‌ایم و علاوه بر اینکه قدرت بیشتری در مدیریت Litetime خواهیم داشت علت دیگری هم وجود دارد؛ برای برنامه‌های رو میزی و موبایل نمی‌توان از HTTP Request Lifetime استفاده کرد و هر یک باید با توجه به نوع برنامه به صورت خاصی تنظیم شوند. مطمئناً می‌توان از Lifetime های دیگری بهره برد اما در اینجا پشتیبانی از ساختار Hierarchical در Unity کمک می‌کند تا برای انواع برنامه‌های وب، رومیزی و موبایل ساختار یکنواخت‌تری داشته باشیم. تصور کنید بخواهیم یک برنامه Tab-based رومیزی ایجاد کنیم که Lifetime مورد نظر ما یک Tab باشد. باز هم Hierarchical Lifetime کمک می‌کند.

چون یکی از اهداف اصلی چارچوب زغال ایجاد یک بستر یکنواخت برای انواع برنامه‌های وب، رومیزی و … است، <a href="https://github.com/AmirKarimi/Xoqal/blob/master/Source/Xoqal.Web.Mvc/UnityControllerFactory.cs" target="_blank">UnityControllerFactory</a> در زغال گنجانده شده است. امیدوارم در آینده در کنار پروژه‌های Xoqal.Web.Mvc و Xoqal.Presentation ، پروژه‌هایی مانند Xoqal.Xamarin و Xoqal.ServiceStack هم داشته باشیم و علاوه بر Unity از DI Container های دیگری هم پشتیبانی کنیم.

>   * <a href="http://www.xamarin.com/" target="_blank">Xamarin</a> یک شرکت نرم‌افزاری است که ابزارهایی برای برنامه‌نویسی بر روی پلتفرم‌های iOS و Andorid با استفاده از زبان `C#` در محیط `.NET` فراهم کرده است.
>   * <a href="http://www.servicestack.net/" target="_blank">Service Stack</a> یک کتابخانه بسیار قوی با معماری اصولی برای ایجاد وب سرویس‌های REST است.