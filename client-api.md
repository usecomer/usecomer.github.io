# Client API Modülü

> NameSpace app/common/models/GeneralOptions

Client API modülü içinde yer alan dosyalar frontend tarafındaki 
ajax ve form yapılarını kolaştıran bazı özellikler içerir.

Bu özelliklerden biri de **validateForm.js**; Client API tarafından gelen tüm sonuçları rahatlıkla yönetebilmeyi amaçlar. Yani API tartıdan bir JavaScript Function’ı rahatlıkla tetiklettirebiliriz. Ayrıca formlardaki validate yapısıyla uyumlu çalışmaktadır. Ekrana bir MessageBox basabiliriz. Ve bu yapının daha bir çok desteği mevcuttur.


##### Öncelikle API'de yeni bir controller ve action açmaktan bahsedelim.
## Controller ve Action Oluşturma
Controller ve Action oluşturma Yii2'nin standart yapısına yakın ilerlemektedir.
Bu durumu ayıran bir kaç nokta vardır.
- **Yeni controller eklerken** *app/common/modules/clientApi/controllers* dizinini kullanmalısınız.
Bazı **eski sürüm**lü projelerde *app/common/controllers/api* kullanılabilir. 
- *common\controllers\api\Controller* class'ına extend edilmelidir.
- Yii2'de de olduğu gibi yeni bir action'ın function adı **actionYeniBirAction** gibi public olarak eklenerek oluşturulur.
- Yii2'nin standart render method'undan farklı olarak aşaığıdaki gibi render edilir.
**renderJson** function'ı **array** ya da **object** bekler. 
```php
public function actionYeniBirAction()
{
    return $this->renderJson(["success" => true]);
}
```
- Bazı özel durumlarda **renderJson** yerine farklı teknikler serbest olarak
uygulanabilir. Örneğin; bir dosya indirme farklı tekniklerle uygulanabilir.
**Ama yine de renderJson yapısını bir çok aşamada kullanmanızı öneriyoruz.**


## renderJson nedir?
renderJson API içindeki sonuçların ajax ile view'e yansımasını kolaylaştıran bir function.
validateForm.js ile bütünleşik çalışır. Bu method bazı özel key'ler ile yönetilir.
Bunlar da sırasıyla şöyle;

#### (bool) success (Zorunlu!)
Eğer işlem başarılı ise karşısına **(boolean) true** bekler, eğer başarısız ise *(boolean) false** bekler. **Bu key kesinlikle her işlemde beklenir!** 

Örnek kullanımı aşağıdadır:
```php
return $this->renderJson(["success" => true]);
```

#### (string) messageBox
Eğer ekrana **toasts tipi mesaj** başmak isterseniz bu key'i kullanabilirsiniz. Bu key de diğer key'lerde olduğu gibi **success** key'ini bekler.
String içerir. 

Örnek kullanımı aşağıdadır:
```php
return $this->renderJson([
    "success" => true,
    "messageBox" => Yii::t('er', 'Ürün sepetinize başarıyla eklendi.'),
]);
```
```php
return $this->renderJson([
    "success" => false,
    "messageBox" => Yii::t('er', 'Ürün sepetinize eklenmeye çalışılırken bir hata aldık!'),
]);
```