# مستندات پلاگین بازار برای گودو انجین
زاهنمای پیاده سازی پلاگین بازار برای گودو انجین

## دریافت پلاگین
جهت دریافت پلاگین به [این](https://gdpars.sellfile.ir/prod-2174966-%D9%BE%D9%84%D8%A7%DA%AF%DB%8C%D9%86+%D9%BE%D8%B1%D8%AF%D8%A7%D8%AE%D8%AA+%D8%AF%D8%B1%D9%88%D9%86+%D8%A8%D8%B1%D9%86%D8%A7%D9%85%D9%87+%D8%A7%DB%8C+%D8%A8%D8%A7%D8%B2%D8%A7%D8%B1.html) صفحه مراجه کنید.

## نصب و راه اندازی
ابتدا فایل را از زیپ خارج کرده و بر اساس نسخه گودوی خود فایل ها را در محل پروژه ی خود کپی کنید.

## اتصال به بازار
برای اتصال به بازار، باید از فانکشن `start_setup` موجود در کلاس `Payment` استفاده کنید:

گودو 3:
```gdscript
const PUBLIC_KEY = ""

onready var payment = Payment.new(PUBLIC_KEY)

func _ready():
	...
	payment.start_setup()
	payment.connect("connection_succeed",self,"connection_succeed",[],CONNECT_ONESHOT)
	payment.connect("connection_failed",self,"connection_failed",[],CONNECT_ONESHOT)
	payment.connect("disconnected",self,"disconnected",[],CONNECT_ONESHOT)

func connection_succeed():
	...

func connection_failed(error : String):
	...

func disconnected():
	...
```
گودو 4:
```gdscript
const PUBLIC_KEY = ""

@onready var payment = Payment.new(PUBLIC_KEY)

func _ready():
	...
	payment.start_setup()
	payment.connection_succeed.connect(connection_succeed,CONNECT_ONE_SHOT)
	payment.connection_failed.connect(connection_failed,CONNECT_ONE_SHOT)
	payment.disconnected.connect(disconnected,CONNECT_ONE_SHOT)

func connection_succeed():
	...

func connection_failed(error : String):
	...

func disconnected():
	...
```
فانکشن `start_setup` سه سیگنال دارد:

سیگنال `connection_succeed` وقتی صدا زده می‌شود که اتصال به بازار برقرار شود.

سیگنال `connection_failed` وقتی صدا زده می‌شود که اتصال به بازار برقرار نشود.

سیگنال `disconnected` وقتی صدا زده می‌شود که طبق رویدادی ارتباط برنامه شما با بازار قطع شود.

همان‌طور که می‌بینید، در `connection_failed` به از `error` دسترسی دارید که به کمک آن می‌توان متوجه مشکلی که باعث عدم اتصال برنامه شما به بازار شده است شد.

## شروع روند خرید یک محصول
برای شروع روند خرید یک محصول، باید از فانکشن `purchase_product` از کلاس `Payment` روند خرید محصول مورد نظر را شروع کنید:

گودو 3:

```gdscript
	payment.purchase_product("product_id")
	payment.connect("purchase_succeed",self,"purchase_succeed",[],CONNECT_ONESHOT)
	payment.connect("purchase_flow_began",self,"purchase_flow_began",[],CONNECT_ONESHOT)
	payment.connect("failed_to_begin_flow",self,"failed_to_begin_flow",[],CONNECT_ONESHOT)
	payment.connect("purchase_canceled",self,"purchase_canceled",[],CONNECT_ONESHOT)
	payment.connect("purchase_failed",self,"purchase_failed",[],CONNECT_ONESHOT)

func purchase_succeed(info : PurchaseInfo):
	...

func purchase_flow_began():
	...

func failed_to_begin_flow(error : String):
	...

func purchase_canceled():
	...

func purchase_failed(error : String):
	...
```
گودو 4:
```gdscript
	payment.purchase_product("product_id")
	payment.purchase_succeed.connect(purchase_succeed,CONNECT_ONE_SHOT)
	payment.purchase_flow_began.connect(purchase_flow_began,CONNECT_ONE_SHOT)
	payment.failed_to_begin_flow.connect(failed_to_begin_flow,CONNECT_ONE_SHOT)
	payment.purchase_canceled.connect(purchase_canceled,CONNECT_ONE_SHOT)
	payment.purchase_failed.connect(purchase_failed,CONNECT_ONE_SHOT)

func purchase_succeed(info : PurchaseInfo):
	...

func purchase_flow_began():
	...

func failed_to_begin_flow(error : String):
	...

func purchase_canceled():
	...

func purchase_failed(error : String):
	...
```

به جای مقدار `product_id` باید مقدار شناسه محصولی که قصد خرید آن را دارید را جایگزین کنید. برای دریافت مقدار شناسه محصول مورد نظر، به پیشخان توسعه‌دهندگان مراجعه کنید و در صفحه اطلاعات برنامه، در تب «پرداخت درون برنامه‌ ای» یک محصول جدید بسازید و شناسه‌ای برای آن در نظر بگیرید. یا این که می‌توانید، شناسه محصول‌هایی که پیش از این ساخته‌اید را جایگزین مقدار `product_id` کنید.

فانکشن `purchase_product` دارای سیگنال های زیر می باشد:

سیگنال `purchase_flow_began` وقتی صدا زده می‌شود که فرایند هدایت کاربر به صفحه خرید بازار به درستی انجام شود.

سیگنال `failed_to_begin_flow` وقتی صدا زده می‌شود که فرایند هدایت کاربر به صفحه خرید بازار به درستی انجام نشود.

همانطور که می‌بینید، در `failed_to_begin_flow` به از `error` دسترسی دارید که به کمک آن می‌توان متوجه مشکلی که در شروع فرایند خرید شده است شد.

سیگنال `purchase_succeed` وقتی صدا زده می‌شود که خرید با موفقیت انجام شود، که در این صورت از طریق `Purchaseinfo` می‌توانید به اطلاعات خرید دست پیدا کنید. توجه کنید که در صورت موفقیت‌آمیز بودن خرید، بسیار پیشنهاد می‌شود که از طریق `API` پرداخت بازار، خودتان صحت پرداخت را نیز بررسی کنید.

سیگنال `purchase_canceled` وقتی صدا زده می‌شود که کاربر از خرید منصرف شود.

سیگنال `purchase_failed` وقتی صدا زده می‌شود که مشکلی در خرید وجود داشته باشد. این مشکل می‌تواند از جنس صحت نداشتن اطلاعات خرید و مشکلات مشابه باشد. در این‌صورت به کمک `error` موجود در این سیگنال می‌توانید از اشکال به‌وجود آمده مطلع شوید.

### شروع روند اشتراک یک محصول
برای اشتراک یک محصول، دقیقا مثل شروع خرید یک محصول عمل کنید و به جای استفاده از فانکشن `purchase_product` از فانکشن `subscribe_product` موجود در کلاس `Payment` استفاده کنید.

### مصرف کردن خرید کاربر
وقتی کاربر یکی از محصولات دیجیتالی برنامه شما را خریداری می‌کند، مالک یک نسخه محصول مورد نظر می‌شود و در صورتی که شما کاربر را مجددا به صفحه پرداخت هدایت کنید، 

کاربر نمی‌تواند دوباره خرید خود را انجام دهد و با اطلاعات خرید قبلی به برنامه شما باز می‌گردد (`purchase_succeed`). اما شما می‌توانید برای محصول مورد نظر خود تاریخ مصرف و یا شرایط خاصی را در نظر بگیرید تا خرید مصرف شود. در این صورت کاربر می‌تواند مجددا خرید خود را انجام دهد. برای مصرف کردن یک خرید از فانکشن `consume_product` موجود در کلاس `Payment` استفاده کنید:

گودو 3:
```gdscript
	payment.consume_product("purchase_token")
	payment.connect("consume_succeed",self,"consume_succeed",[],CONNECT_ONESHOT)
	payment.connect("consume_failed",self,"consume_failed",[],CONNECT_ONESHOT)

func consume_succeed():
	...

func consume_failed(error : String):
	...
```
گودو 4:
```gdscript
	payment.consume_product("purchase_token")
	payment.consume_succeed.connect(consume_succeed,CONNECT_ONE_SHOT)
	payment.consume_failed.connect(consume_failed,CONNECT_ONE_SHOT)

func consume_succeed():
	...

func consume_failed(error : String):
	...
```
همان‌طور که می‌بینید، `consume_product` دو سیگنال دارد:

سیگنال `consume_succeed` وقتی صدا زده می‌شود که عملیات مصرف کردن خرید با موفقیت انجام شود.

سیگنال `consume_failed` وقتی صدا زده می‌شود که عملیات مصرف کردن خرید انجام نشود، در این شرایط شما می‌توانید با استفاده از `error` موجود در این سیگنال متوجه مشکل موجود شوید.

توجه کنید به‌جای مقدار `purchase_token` در نمونه کد بالا، باید مقدار واقعی توکن خریدی که می‌خواهید مصرف شود را قرار دهید. دسترسی به توکن خرید، از طریق کلاس `PurchaseInfo` امکان‌پذیر است. وقتی کاربر خرید انجام می‌دهد، پولکی به شما یک `PurchaseInfo` در `purchase_succeed` می‌دهد. همچنین می‌توانید تمام خریدهای کاربر که در برنامه شما انجام داده است را از طریق فانکشن `get_purchased_products` بدست بیاورید که در این صورت، پولکی به شما لیستی از `PurchaseInfo` می‌دهد که از این طریق نیز می‌توان به توکن خرید دسترسی پیدا کرد.

> **توجه: محصولات اشتراکی را نمی‌توانید مصرف کنید.**

> **توجه: به این نکته توجه داشته باشید که قبل از کار با `consume_product` باید مطمئن شوید که کاربر وارد حساب بازار خود شده است، در غیر این‌صورت `consume_failed` صدا زده می‌شود.**

## دریافت خریدهای مربوط به پرداخت درون برنامه ای کاربر در برنامه شما
شما می‌توانید با استفاده از فانکشن `get_purchased_products` موجود در کلاس `Payment` از خریدهای کاربر که در برنامه شما انجام داده است، مطلع شوید:

گودو 3:
```gdscript
	payment.get_purchased_products()
	payment.connect("purchased_query_succeed",self,"purchased_query_succeed",[],CONNECT_ONESHOT)
	payment.connect("purchased_query_failed",self,"purchased_query_failed",[],CONNECT_ONESHOT)

func purchased_query_succeed(items : Array):
   ...

func purchased_query_failed(error : String):
   ...
```
گودو 4:
```gdscript
func _ready():
	payment.get_purchased_products()
	payment.purchased_query_succeed.connect(purchased_query_succeed,CONNECT_ONE_SHOT)
	payment.purchased_query_failed.connect(purchased_query_failed,CONNECT_ONE_SHOT)

func purchased_query_succeed(items : Array[PurchaseInfo]):
   ...

func purchased_query_failed(error : String):
   ...
```
همانطور که می‌بینید، `get_purchased_products` دارای دو سیگنال می باشد:

سیگنال `purchased_query_succeed` وقتی صدا زده می‌شود که ارتباط با بازار به خوبی برقرار شود و مشکلی در دریافت لیست خریدها وجود نداشته باشد. در این صورت، با استفاده از `items` که لیستی از `PurchaseInfo` است می‌توانید به خریدهای کاربر دسترسی پیدا کنید.
سیگنال `purchased_query_failed` وقتی صدا زده می‌شود که ارتباط با بازار برقرار نشود یا مشکلی در دریافت لیست خریدها وجود داشته باشد. در این صورت، می‌توانید با استفاده از `error` موجود در این سیگنال متوجه مشکل به‌وجود آمده شوید.

> **توجه: به این نکته توجه داشته باشید قبل از کار با `get_purchased_products` باید مطمئن شوید که کاربر وارد حساب بازار خود شده است، در غیر این‌صورت `purchased_query_failed` صدا زده می‌شود.**

## دریافت اشتراک‌های کاربر در برنامه شما
شما می‌توانید با استفاده از فانکشن `get_subscribed_products` موجود در کلاس `Payment` از محصولاتی که کاربر اشتراک آن‌ها را در برنامه شما خریداری کرده، مطلع شوید:

گودو 3:
```gdscript
	payment.get_subscribed_products()
	payment.connect("subscribed_query_succeed",self,"subscribed_query_succeed",[],CONNECT_ONESHOT)
	payment.connect("subscribed_query_failed",self,"subscribed_query_failed",[],CONNECT_ONESHOT)

func subscribed_query_succeed(items : Array):
	...

func subscribed_query_failed(error : String):
	...
```
گودو 4:
```gdscript
	payment.get_subscribed_products()
	payment.subscribed_query_succeed.connect(subscribed_query_succeed,CONNECT_ONE_SHOT)
	payment.subscribed_query_failed.connect(subscribed_query_failed,CONNECT_ONE_SHOT)

func subscribed_query_succeed(items : Array[PurchaseInfo]):
	...

func subscribed_query_failed(error : String):
	...
```
همانطور که می‌بینید، `get_subscribed_products` دارای دو سیگنال می باشد:

سیگنال `subscribed_query_succeed` وقتی صدا زده می‌شود که ارتباط با بازار به خوبی برقرار شود و مشکلی در دریافت لیست اشتراک‌ها وجود نداشته باشد که در این صورت، با استفاده از `items` که لیستی از `PurchaseInfo` است می‌توانید به اشتراک‌های کاربر دسترسی پیدا کنید.

سیگنال `subscribed_query_failed` وقتی صدا زده می‌شود که ارتباط با بازار برقرار نشود یا مشکلی در دریافت لیست اشتراک‌ها وجود داشته باشد، که در این شرایط، می‌توانید با استفاده از `error` موجود در این سیگنال متوجه مشکل به‌وجود آمده شوید.

> **توجه: قبل از کار با `get_subscribed_products` باید مطمئن شوید که کاربر وارد حساب بازار خود شده است، در غیر این‌صورت `subscribed_query_failed` صدا زده می‌شود.**
