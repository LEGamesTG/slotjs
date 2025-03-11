<h1 align="center">LUCK</h1>

<p align="center">
    🎰 Слот машина, мобильное SPA-приложение, созданное с использованием JavaScript, CSS-переменных и эмодзи!
</p>
<p align="center">
    🔊 Лучше с включенным звуком и вибрацией.
</p>

<br />

Запуск (Разработка)
-------------------

Взгляните на `package.json`, скрипты говорят сами за себя, но в `90%` случаев достаточно выполнить `npm install` и `npm start` :D

Проект будет доступен по адресу http://localhost:8080/slotjs/.

<br />

Сообщение о проблеме
--------------------

Не стесняйтесь сообщать о любых проблемах, которые вы найдете на этом сайте/проекте, создав новую issue в этом репозитории. Пожалуйста, убедитесь, что проблема еще не была сообщена другим пользователем как Issue или не указана в [файле TODO](./TODO).

<br />

Детали реализации, ограничения и возможные улучшения
----------------------------------------------------

### Не случайный

Вместо генерации случайных чисел для определения, выиграет ли игрок в автомате, этот автомат работает более предсказуемо. Способность пользователя остановить его в нужный момент действительно влияет на его шансы выиграть, что, как мне кажется, делает игру более увлекательной и справедливой. Представьте, например, напряжение, когда осталось всего один символ до джекпота, и все зависит от вас:

<p align="center">
    <img src="https://media2.giphy.com/media/xT5LMBHU0riscTRfXO/giphy.gif?cid=3640f6095c31f9a143544144495694c3" alt="Гомер Симпсон играет в рулетку" />
</p>

### Групповое вращение

Мы могли бы вращать контейнер барабанов (`.sm__reelsContainer`) вместо самих барабанов (`.sm__reel`), чтобы нам нужно было вращать только один элемент с помощью JS и еще один с CSS-анимацией (тот, который останавливается, если есть):

<p align="center">
    <img src="https://media.giphy.com/media/qqtvGYCjDNwac/giphy.gif" alt="Гомер Симпсон вращается на стуле" />
</p>

Основное преимущество этого подхода в том, что основное действие обновления вращения будет выполнять одинаковую работу независимо от количества барабанов, что потенциально может снизить и сделать более предсказуемым время выполнения метода `tick`.

Однако его недостатки заключаются в том, что у нас меньше контроля над вращением, тогда как при текущем подходе мы можем иметь разные скорости и даже ускорения для каждого барабана, и, что самое важное, когда мы останавливаем барабан, нам нужно переместить его за пределы контейнера, изменяя DOM, что сделает действие остановки медленнее.

Поскольку вращение барабанов в настоящее время работает лучше (на уровне около `60 кадров в секунду` даже с замедлением CPU в `6 раз`), чем их остановка (обычно занимает около `2 кадров`), в основном из-за дорогостоящей операции `Recalculate Style`, которую вызывают динамически создаваемые анимации при остановке барабана, я решил не реализовывать этот подход. Ну, знаете, _работает — не трогай_.

Если бы в этой игре было больше барабанов, и это вызывало бы проблемы с производительностью на некоторых устройствах, мы могли бы провести тест производительности, чтобы сравнить оба подхода.

### Вращение с анимациями

Можно было бы использовать анимацию вращения вместо ручного расчета вращения, что может быть немного более производительным, чем текущий подход.

Однако, когда мы останавливаем барабан, нам нужно будет определить его угол из матрицы вращения и убедиться, что переход между анимацией вращения и остановки будет плавным, тогда как сейчас мы всегда знаем текущий угол всех барабанов. Также у нас будет меньше контроля над вращением, тогда как при текущем подходе мы можем иметь разные скорости и даже ускорения для каждого барабана.

### Эффект увеличения

Эффект увеличения немного медленный в Firefox и размытый на некоторых устройствах iOS, тогда как в Chrome влияние на производительность есть, но оно не так заметно. Было бы проще и гораздо более производительно во всех браузерах показывать увеличенное изображение текущей комбинации над игровым автоматом.

В любом случае, я подозреваю, что проблема на iOS связана с тем, что масштабирование выполняется с использованием GPU, но после этого не выполняется перерасчет, поэтому мы получаем увеличенное растровое изображение вместо нового, сгенерированного с использованием увеличенного приложения, которое должно выглядеть более четким, как в других браузерах.

Это может быть исправимо с помощью трюка `filter: none` или `-webkit-filter: blur(0px)`, но так как у меня нет устройства iOS для тестирования, я не реализовал никакого исправления.

### Эффект размытия

Каждая ячейка в барабане повторяется несколько раз в зависимости от доступного пространства между одной ячейкой и следующей, с эффектом размытия и уменьшением `opacity`, чтобы анимация выглядела более плавной и без пробелов между ячейками, но при этом было очевидно, где находится реальная ячейка (не размытая).

Однако, похоже, что `filter: blur(Npx)` не использует GPU в Firefox, что вызывает серьезные проблемы с производительностью. Поэтому этот эффект не используется в Firefox, где изменяется только `opacity`.

<br />

Другие идеи
-----------

### Специальные символы

Мы могли бы добавить специальные символы, которые дают случайный бонус: мгновенный приз, множители, замедление, символ-джокер... и использовать центр игрового автомата для отображения этого.

### Виртуальные комнаты

Мы могли бы группировать игроков в "виртуальные комнаты" в соответствии с некоторыми параметрами, такими как время, которое они тратят на остановку барабана, их средний процент выигрышей..., убедившись, что они находятся на схожем уровне навыков, и большая часть денег, которые они тратят, будет идти в джекпот этой комнаты, а небольшой процент — на комиссию.

Также мы могли бы использовать WebSockets, чтобы они все знали о выигрышах других в этой комнате, чтобы поддерживать их вовлеченность в игру / соревнование.

<br />

Текущая работа
--------------

Это просто проект выходного дня, который я создал некоторое время назад, поэтому он не будет часто обновляться. Однако, вы можете найти текущую работу в [файле TODO](./TODO).

<br />
