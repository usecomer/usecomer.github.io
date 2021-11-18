# ActiveForm
Yii2'nin içinde yer alan ActiveForm yapısından extend ederek yeni bir yapı oluşturduk.
Yii2'deki tüm özellikleri desteklemesiyle birlikte içeride performans ve yazılım
tarafında süreci kolaylaştırmak için bazı çalışmalar yapılmıştır. Block'lar içinde 
eğer ActiveForm kullanılacaksa kesinlikle bu yapı kullanılmalıdır. 
**Kullanılmadığı durumlarda live ortamda formun çalışmama durumu yaşanabilir!**

> Bu yapı **[Client API Modülü](client-api.md)** ile uyumlu çalışmaktadır. API yapısındaki bir çok özelliği desteklemektedir. Bundan dolayı 
 **[Client API Modülü](client-api.md)**  tarafındaki yapıyı bilmekte fayda vardır.

#### Bağlantı
```php
use common\components\_extends\ActiveForm;
```

Eğer bir Block'un Client API'si varsa bu durumda form içinde action belirtilmezse mevcut Block'un controller ve action
kombininde API'e bağlanmayı deneyecektir. 

Örnek: 

```php
use common\components\_extends\ActiveForm;

$form = ActiveForm::begin([
    "modelBlock" => $_modelBlock,
]);

ActiveForm::end();
```

Yukarıdaki view dosyası **form/account/view.php** olarak düşünün. Bu parçacıktan çağrılan ActiveForm otomatik olarak
**clientApi/form/account** 'u action olarak varsayar.