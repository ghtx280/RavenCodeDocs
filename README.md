## СЛУЖБОВІ ЗМІННІ:

Повний текст команди `srting`
```js
$content // '!foo 1 2 3'
```

Поточна викликана команда `srting`
```js
$cmd // 'foo'
```

Параметри які йдуть після команди `array`
```js
$args // ['1', '2', '3']
```

Дані про користувача `object`
```js
$member
```

Дані про канал `object`
```js
$channel
```

Дані про сервер `object`
```js
$guild
```

Дані про поточне повідомлення `object`
```js
$message
```



## ФУНКЦІЇ:

### $import
Імпортує дані з іншого файлу та опціонально передає параметри   
!!! Застереження. При імпорті файлу, весь код який там є, виконається.
Рекомендується використовувати лише функції та змінні, щоб не було проблем

```js
// action1
let { foo } = $import("action2", { num: 100 })

foo // 200
```

```js
// action2
return {
    foo: $params.num * 2
}
```



### $random
Отримати випадкове число чи елемент масиву
```js
$random(10) // Від 0 до 10
$random(5, 15) // Від 5 до 15
$random([1, 2, 3, 4, 5]) // З масиву
$random(['a', 'b', 'c'], [20, 50, 30]) // З ймовірністю у %
```

### $range
Створити проміжок, тобто масив, зручно використовувати в for of
```js
$range(5) // [1, 2, 3, 4, 5]
$range(2, 6) // [2, 3, 4, 5, 6]
$range(1, 10, 2) // [1, 3, 5, 7, 9]
```





## СХОВИЩЕ

Сховище являє собою просту базу данних {ключ: значення}
Ми можемо 


Отримати дані про поточного користувача
```js
await $store.member('money').get() // 900
```

Отримати загальні дані
```js
await $store.global('money').get() // 300
```

Зміна даних (**Далі однаково для member та global**)
```js
await $store.global('money').set(500) // оновити значення (якщо такого поля немає то воно створиться) 
await $store.global('money').set(null) // видалити значення (якщо такого поля немає поверне false) 
```

### Операція зміни чисел
```js
// додати до поточного значення
await $store.global('money').inc(50) // Якщо було 500 то стане 550

// відняти від поточного значення
await $store.global('money').dec(50) // Якщо було 500 то стане 450
```

### Зміна масиву
```js
// додати нове значення до масиву 'roles'
await $store.global('roles').push('admin')

// видалити значення 'admin' з масиву 'roles'
await $store.global('roles').delete('admin')

// замінити 'admin' на 'user' у масиві 'roles'
await $store.global('roles').replace('admin', 'user')

// перевірка, чи є 'user' у масиві 'roles', повертне true або false
await $store.global('roles').has('user')

```


### !!! mention знаходиться в розробці, тому поки недоступний

Отримати дані про згаданого користувача за індексом
```js
await $store.mention(0, 'money').get() // 800
await $store.mention(1, 'money').get() // 500
```

Оновити дані згаданого користувача
```js
await $store.mention(0, 'money').set(200) // true якщо оновилося успішно
await $store.mention(1, 'money').set(400) // false якщо помилка оновилося успішно
```

Отримати дані кожного згаданого користувача згідно з масивом (лише для читання)
```js
await $store.mention(['money', 'money']).get() // [200, 400]
```

