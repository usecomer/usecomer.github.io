# Controller

> Fonksiyonların kullanılacağı asıl alan burasıdır.

> Yeni bir controller yaratıldığında aşağıdaki konuma bunu eklemeyi unutma

```php
use backend\modules\rbac\filters\AccessControl;

```

```php
 'access' => [
                'class' => AccessControl::className(),
            ],
```

```php
    public function behaviors()
    {
        return [
            'access' => [
                'class' => AccessControl::className(),
            ],
            'verbs' => [
                'class' => VerbFilter::className(),
                'actions' => [
                    'delete' => ['POST'],
                ],
            ],
        ];
    }
```

## Parametre Aktarımı

Comer içinde bazı durumlarda başka controller içindeki parametre verilerini başka alanlara aktarmak gerekebilir. Biz
bunu Pazarlama araçları entegreasyonlarında yoğun bir şekilde kullanıyoruz. Örneğin bir controllerdaki function 'un
içindeki array' e ulaşmak için yapacağımız işlem şu şekildedir.

`FaqController.php`

```php 
$items=['comer','e-ticaret','e-ihracat'];
    
   Yii::$app->params[self::PARAM_KEY] = $items; 
```

Burada sadece dikkat etmeniz gereken şey kullanılacak parametre hiyerarşiye uygun olarak functiondan sonra gelmelidir.
Bu şekilde

```php 
  const PARAM_KEY = 'faqs'; 
  ```

Controller' da sabit olarak tanımımızı yapıyoruz.
> Burada dikkat etmemiz gereken en önemli şey sabit değerinin başka hiç bir yerde kullanılmamış olmasına dikkat ediniz.

Bunlardan sonra çağırma aşaması kaldı.

```php
$items = Yii::$app->params[FaqController::PARAM_KEY];
```

Yukarıdaki kodumuzu yazdırdığımız her yerde $items arrayimiz dönecektir.
> Tekrar hatırlatma: Bu çağırma işlemi function çağırıldıktan sonra gerçekleştirilmelidir.

## Parametre

Controller'da basit bir şekilde parametre işleme şu şekildedir:

```php
    public function actionIndex($pending = false) //parametre belirtiniz
    {
        $searchModel = new StockAlarmSearch();
        /*
         * Parametreyi manuel listelet
         */
        if ($pending) {
            BackendFilterCache::deleteAllFilter($searchModel);
            $searchModel->status = StockAlarm::STATUS_PENDING;
        }
        $dataProvider = $searchModel->search(Yii::$app->request->post());
        /*
         * parametre filtrelendikten sonra redirect et
         */
        if ($pending) {
            return $this->redirect(Url::current(['pending' => null]));
        }

        return $this->render('index', [
            'searchModel' => $searchModel,
            'dataProvider' => $dataProvider,
        ]);
    }
```

Controller'da üstteki örneğe göre daha kompleks parametre işleme şu şekildedir:

```php
    public function actionIndex($out_of_product = false, $editable_view = null)
    {
        $searchModel = new ProductChildSearch([
        ]);
        /**
         * Stoksuz olanları manuel listelet
         */
        if ($out_of_product) {
            BackendFilterCache::deleteAllFilter($searchModel);
            $searchModel->stock = '0';
            $searchModel->filterNumberCondition['stock'] = '=';
            $searchModel->is_active = 1;
        }
        $dataProvider = $searchModel->search(Yii::$app->request->post());

        /**
         * Stoksuzların filtresini ekledikten sonra redirect et
         */
        if ($out_of_product) {
            return $this->redirect(Url::current(['out_of_product' => null]));
        }

      ..........

```

> Bu işlemlerden sonra filtrelemede sıkıntı yaşıyorsanız kod hiyerarşisini kontrol ediniz.

## Panel Üzerinden Controller ve Sayfa Oluşturma

* Panel üzerinden "Kontrolör Haritası"na gidiniz.
* Yeni controller oluşturunuz.
* Panel üzerinden sayfalar-> sayfa oluştura gidiniz.
* Sayfa tipinde oluşturduğunuz controller'i seçiniz

> Sistem otomatik işlemezse

```
vagrant ssh
```

Projeninizin dosya yoluna gidin

```
php yii seo-url/one controller-adı
```

```
php yii queue/run
```

## noCache Yapısı

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

* View işlemleri - Kod başlangıç alanında ```echo $usedNoCache;``` bu alan echo edilmelidir.

## Comer Event

Process oluşturmak ve kullanmak için yapılması gerekenler:

> Controllerda yapılacak olan işlemler


```php
    public function actionRollbackProcess($id)
    {
        $model = $this->findModel($id); //modeli bul
        $model->rollbackProcess($id); //rollback işlemini başlat
        return $this->redirect(['nereye yönlendirilecek']);

    } 
```

>Modelde yapılacak olan işlemler

```php
    public function rollbackProcess($id)
    {
        $model = self::findOne($id); //modeli bul
        if (!$model) { //model yoksa
            return false; //false döndür
        }
        $modelNew = new self(); //yeni model oluştur
        $modelNew->user_id = $model->user_id; //yeni modelin user_id'sini eski modelin user_id'si ile eşitle
        $modelNew->save(); //yeni modeli kaydet
        return true; //true döndür
    }
```

>Frontendde yapılacak olan işlemler

```php
Html::a('<span class="glyphicon glyphicon-repeat rollback-button"></span>', ['rollback-process', 'id' => $model->id], [
                            'title' => Yii::t('er', 'İşlemi Geri Al'),
                            'data' => [
                                'confirm' => Yii::t('er', 'İşlemi geri almak istediğinize emin misiniz?'),
                                'method' => 'post',
                            ],
                        ])
```
