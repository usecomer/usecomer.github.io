# AJAX Yapısı
COMER içeride AJAX için eşsiz özellikler katmıştır. Size bunlardan bahseceğim.

> Bu yapı **[Client API Modülü](client-api.md)** ile uyumlu çalışmaktadır. API yapısındaki bir çok özelliği desteklemektedir. Bundan dolayı 
>**Client API Modülü** tarafındaki yapıyı bilmekte fayda vardır.

## ajax-get kullanımı

Bu method iki kullanıma sahip aslında ikisi arasında işlevsel olarak çok bir fark yok ama HTML'de ihtiyacınız olan duruma göre birini tercih edebilirsiniz.
Kullanım tekbikleri aşağıda:

**A** ile;
```php
echo Html::a('TIKLA', ['clientApi/controller/action'], ['class' => 'ajax-get']);
```

**BUTTON** ile;
```php
echo Html::button('TIKLA', ['class' => 'ajax-get', "data-href" => Url::to(['clientApi/controller/action'])]);
```

#### data-function ile kullanımı
Bu yapı ayrıca **Custom JS Function** yazmak isterseniz onu da desteklemektedir.

```php
echo Html::button('TIKLA', ['class' => 'ajax-get', "data-href" => Url::to(['clientApi/controller/action']), 'data-function' => 'myNewFunction']);
```

```javascript
window.myNewFunction = function (element, response){
    console.log(element, response);
};
```

Örnekte görüldüğü gibi ***myNewFunction*** adında bir function oluşturduk. 