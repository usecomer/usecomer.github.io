# noCache Yapısı

>Yapılacak Olan İşlemler

* Controller İşlemleri - Aynı Controller içinde hemen alt tarafına örnekteki gibi action oluşturulur.

```php
    public static function noCache_actionImages($params) //Bu parametre asıl controller'dan giden parametrelerdir.
    {
        $id = (int)(Yii::$app->request->get('id'));
        $modelUserFavoriteProduct_user = UserFavoriteProduct::getUser($id);
        UserFavoriteProduct::favoriteStatus2HtmlSvg($modelUserFavoriteProduct_user);
        self::loadData(".product.images", ".comer-product-favorite > .btn", $modelUserFavoriteProduct_user);
        return [
            'usedNoCache' => "", //bu şekilde return edilir
        ];
    }

```

* View işlemleri - Kod başlangıç alanında ```php echo $usedNoCache;``` bu alan echo edilmelidir.