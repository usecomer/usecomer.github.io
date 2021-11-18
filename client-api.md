# Client API Modülü

Client API modülü içinde yer alan dosyalar frontend tarafındaki 
ajax ve form yapılarını kolaylaştıran bazı özellikler içerir.

Bu özelliklerden biri de **validateForm.js**; Client API tarafından gelen tüm sonuçları rahatlıkla yönetebilmeyi amaçlar. Yani API tartıdan bir JavaScript Function’ı rahatlıkla tetiklettirebiliriz. Ayrıca formlardaki validate yapısıyla uyumlu çalışmaktadır. Ekrana bir MessageBox basabiliriz. Ve bu yapının daha bir çok desteği mevcuttur.



> Öncelikle API'de yeni bir controller ve action açmaktan bahsedelim.
## Controller ve Action Oluşturma
Controller ve Action oluşturma Yii2'nin standart yapısına yakın ilerlemektedir.
Bu durumu ayıran bir kaç nokta vardır.
- **Yeni controller eklerken** *app/common/modules/clientApi/controllers* dizinini kullanmalısınız.
Bazı **eski sürüm**lü projelerde *app/common/controllers/api* kullanılabilir. 
- *common\controllers\api\Controller* class'ına extend edilmelidir.
- Yii2'de de olduğu gibi yeni bir action'ın function adı **actionYeniBirAction** gibi public olarak eklenerek oluşturulur.
- Yii2'nin standart render method'undan farklı olarak aşağıdaki gibi render edilir.
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

#### success (Zorunlu!) (bool) 
Eğer işlem başarılı ise karşısına **(boolean) true** bekler, eğer başarısız ise **(boolean) false** bekler. **Bu key kesinlikle her işlemde beklenir!** 

Örnek kullanımı aşağıdadır:
```php
return $this->renderJson(["success" => true]);
```

#### messageBox (string)
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

#### redirect (string)
Bu key ile yönlendirmeler yapılır. Örneğin ürün listesinde bir ürünü 
sepete eklettik ve kullanıcıyı sepet sayfasına yönlendirmek istersek 
kullanabiliriz.

Örnek kullanımı aşağıdadır:
```php
return $this->renderJson([
    "success" => false,
    "redirect" => SeoUrl::toInController("cart"),
]);
```

#### reload (bool)
Bu key kullanıcının bulunduğu sayfayı yeniler. Yenileme işlemi AJAX olduğu için bazı durumlarda 
sağlıklı çalışmayabilir. Bu yöntem kullanılıyorsa iyice **test etmek gerekli**dir.

Örnek kullanımı aşağıdadır:
```php
return $this->renderJson([
    "success" => false,
    "reload" => true,
]);
```

#### globalLiveData (array)
Bu yapıyı daha iyi anlayabilmek için **Global Live Data** yapısını bilmeniz gerekmektedir.

Global Live Data'yı burada da rahatlıkla kullanabilirsiniz. Bu yapının temel amacı sepet adedi gibi anlık değişebilecek 
ve her zaman live olarak beklediğimiz verileri yönetmek için kullanılır.

Örnek kullanımı aşağıdadır:
```php
return $this->renderJson([
    "success" => false,
    'globalLiveData' => ['cart' => ['count' => Cart::countUser()]]
]);
```

#### callFunctions (array)
Eğer bu API ile client tarafında bir veya daha fazla JavaScript Function'ı tetiklemek
isterseniz bu key tam size göre. Bu yapıyı anlamak biraz zor gelebilir ama gerçekten çok
kolay ve bazı durumlarda kurtarıcı rolü büyüktür. Bu key çoklu olarak farklı işlemleri yapabilecek
kabiliyettedir.

> Bu yapıyı anlayabilmek birden fazla örnek ile pekiştirelim.

Basit örnek kullanımı aşağıdadır:

Mesela API'den sonuç geldikten sonra aşağıdaki gibi basit bir JavaScript çalıştırmak istiyorsanız.
```javascript
alert("Hello COMER!");
```
Aşağıdaki gibi kodu yazmak gereklidir.
```php
return $this->renderJson([
    "success" => true,
    "callFunctions" => [
        [
            "name" => "alert", // JavaScript Function'ın adı,
            "params" => Yii::t('er', 'Hello COMER!') // JavaScript Function'ın alacağı ilk parametre; array ya da string olabilir,
        ]   
    ],
]);
```

Karmaşık örnek kullanımı aşağıdadır:

Mesela API'den sonuç geldikten sonra aşağıdaki gibi daha kapsamlı bir JavaScript çalıştırmak istiyorsanız.
Bu senaryoda ürün sepete eklendikten sonra custom bir JavaScript Function'ı çalıştırmak amaçlanmıştır.

Daha önceden block içinde bir JavaScript Function'ı oluşturduğumuzu varsayalım.
```javascript
function comerAddToCart(params){
    console.log(params); // bir object basacaktır.
}
```
Aşağıdaki gibi kodu yazmak gereklidir.
```php
return $this->renderJson([
    "success" => true,
    "callFunctions" => [
        [
            "name" => "comerAddToCart", // JavaScript Function'ın adı,
            "params" => [
                "product" => [
                    "id" => "123",
                    "category" => "Beyaz Eşyalar",
                    "name" => "XPLUS Bulaşık Makinesi",
                    "brand" => "Arçelik",
                ],
                "amount" => 1,
                "price" => 10.20,
                "currency" => "USD"
            ]
        ]   
    ],
]);
```


> Form alanlarına özel bazı özellikler de aşağıda belirtilmiştir. Bu key'ler her zaman HTML Form yapısı bekler.

#### message (string) || (array)
Form'ların üst kısmında alert tipinde mesaj göstermek için bu key'i kullanabilirsiniz.

Örnek kullanımı aşağıdadır:
```php
return $this->renderJson([
    "success" => false,
    "message" => Yii::t('er', 'Üzgünüz, bu işlemi şu an gerçekleştiremiyoruz. Lütfen daha sonra tekrar deneyin.'),
]);
```

#### errors (array)
Form input'larına ajax validate yapabilmek için kullanabilirsiniz. 
İki tip kullanım tekniği var;
- Form içinde tek model validate edilirken.
- Form içinde birden fazla model'i validate ederken.
 
Tek model için örnek kullanımı aşağıdadır:
```php
return $this->renderJson([
    "success" => false,
    "errors" => $this->getValidate($model),
]);
```

Multi model için örnek kullanımı aşağıdadır:
```php
return $this->renderJson([
    "success" => false,
    "errors" => $this->getValidateMultiple($model),
]);
```

#### formReset : false (bool)
Form'u response sonunda temizlemek isterseniz bu key size göre. 

Varsayılan olarak **(boolean) false** döner.
 
Örnek kullanımı aşağıdadır:
```php
return $this->renderJson([
    "success" => true,
    "formReset" => true,
]);
```



