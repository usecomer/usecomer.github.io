# Hasone - Hasmany Kullanımı

> İki farklı tablo arasında bağlantı kurabilmek için model içinde kullanılacak functiondur.

## Örnek Kullanım Şekilleri

> Aşağıdaki tabloda getUser fonksiyonuyla bulunan modeldeki user_id ile user modelinin içindeki id eşlenmiştir.

```php
......
    /**
     * {@inheritdoc}
     */
    public function attributeLabels()
    {
        return [
            'user_id' => Yii::t('er', 'User ID'),
            'email' => Yii::t('er', 'Email'),
            'first_name' => Yii::t('er', 'First Name'),
            .......
               ];
    }
            
........
    public function getUser()
    {
        return $this->hasOne(User::className(), ['id' => 'user_id']);
    }
..........

```

