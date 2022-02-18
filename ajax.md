# AJAX Yapısı
COMER içeride AJAX için eşsiz özellikler katmıştır. Size bunlardan bahseceğim.

> Bu yapı **[Client API Modülü](client-api.md)** ile uyumlu çalışmaktadır. API yapısındaki bir çok özelliği desteklemektedir. Bundan dolayı 
> **[Client API Modülü](client-api.md)**  tarafındaki yapıyı bilmekte fayda vardır.

## ajax-get kullanımı

Bu method iki kullanıma sahip, aslında ikisi arasında işlevsel olarak çok bir fark yok ama HTML'de ihtiyacınız olan duruma göre birini tercih edebilirsiniz.
Kullanım teknikleri aşağıda:

**A** ile;
```php
echo Html::a('TIKLA', ['clientApi/controller/action'], ['class' => 'ajax-get']);
```

**BUTTON** ile;
```php
echo Html::button('TIKLA', ['class' => 'ajax-get', "data-href" => Url::to(['clientApi/controller/action'])]);
```
### ajax-get için detaylı anlatım

```php
<?= Html::a($province,\yii\helpers\Url::to(['/clientApi/component/store-finder','province'=>$province ]), ['class' => 'icon-with-text-subtitle ajax-get','data-function'=> 'deneme',]) ?>```

> province kısmı foreachten gelen datadır, data function oluşturduğumuz functiondur örneği aşağıdadır.


```js
<script>
    window.deneme = function (a,b) {
        let jsonlar = <?= \yii\helpers\Json::encode($countryList) ?>;
        console.log(a,b)
        console.log(jsonlar)
        $.each( jsonlar, function( key, value ) {
            $("h2").html(jsonlar[key][value]);
            console.log(key,value)
        });
    };
</script>
```
> Aşağıdaki örnek clientApi tarafındaki actionumuzdur.
```php
    public function actionStoreFinder($province)
    {

        if (Yii::$app->request->get('province')) {
            return $this->renderJson([
                'success' => true,
                "messageBox"=> $province
            ]);

        }
        return $this->renderJson([
            'success' => true,
            "messageBox"=> "selam"

        ]);
    }

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

Örnekte görüldüğü gibi ***myNewFunction*** adında bir function oluşturduk. Bu yeni function'ın başında "**window.**" olmak zorunda yoksa bir hata alırsınız.