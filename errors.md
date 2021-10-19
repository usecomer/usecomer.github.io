# Hatalar

>Karşılaşabileceğiniz bazı hatalar burada belirtilmiştir


## Block'um Sayfalar Sayfasına Düşmüyor

Eklediğiniz block sayfalar sayfasında belirtiğiniz controller içinde görünmüyorsa yapmanız gerekenler

* Sayfalar sayfasında yapıyı güncelle butonuna tıklayın ve uyarı düşmezse bir sonraki adımı takip edin

* View yolunun doğru olduğundan emin olun, eğer hatalı view yoluna sahipseniz düzeltme işlemini yapın ve birinci maddeyi tekrar uygulayın değilse bir sonraki aşamaya geçin

* Action'unuzu doğru oluşturduğunuzdan ve isimlendirme kurallarına uygun olduğundan emin olun örnek action oluşturma şekli aşağıdadır. Sorun buradaysa düzeltme işlemini yapıp birinci maddeyi tekrar uygulayın.

```php
    public function actionFilterSort()
    {
        $productFilter = $this::productFilter();

        return $this->render('filterSort/view',['productFilter'=>$productFilter]);

    }

```

## "An error occurred while handling another error... Hatası

> Oluşturduğunuz actionda isimlendirme kuralına dikkat ediniz

```php
    public function actionFilterSort() //camel case kullanıma dikkat ediniz
    {
        $productFilter = $this::productFilter();

        return $this->render('filterSort/view',['productFilter'=>$productFilter]);

    }

```

## Composer Update Hatası

```
 [Composer\DependencyResolver\SolverProblemsException]                                                                       
  Problem 1                                                                                                                   
      - guzzlehttp/guzzle 7.4.x-dev requires symfony/deprecation-contracts ^2.2 -> no matching package found.                 
      - paquettg/php-html-parser dev-master requires guzzlehttp/guzzle ^7.0 -> satisfiable by guzzlehttp/guzzle[7.4.x-dev].   
      - Installation request for paquettg/php-html-parser dev-master -> satisfiable by paquettg/php-html-parser[dev-master].  
                                                                                                                              
  Potential causes:                                                                                                           
   - A typo in the package name                                                                                               
   - The package is not available in a stable-enough version according to your minimum-stability setting                      
     see <https://getcomposer.org/doc/04-schema.md#minimum-stability> for more details.                                       
   - It's a private package and you forgot to add a custom repository to find it                                              
                                                                                                                              
  Read <https://getcomposer.org/doc/articles/troubleshooting.md> for further common problems.                                 
```

>Composer güncellenirken böyle bir hata alıyorsanız `app/composer.json` path yolundaki dosyanızdaki minimum stablity alanıyla ya da php sürümünüzle alakalı olabilir eğer hatanın minimum stabilty ile alakalı olduğunu düşünüyorsanız bu satırı ve composer.json ın üstündeki vendoru `app/vendor` silip tekrar composer'i update ediniz.