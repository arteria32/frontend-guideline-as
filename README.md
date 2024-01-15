# frontend-guideline-as
Гайдлайн по фронтенд разработке для малых команд в условиях больших неопределенностей. 

# Перед началом работы 
1. Любая задача **должна быть зафиксирована в таск-трекере**. Нельзя брать задачи из почты или задач из устной беседы.
2. Все задачи можно разделить на следующие группы:
   
| Тип задачи|Описание | Что надо на входе| Что требуется на  выходе|
| ------------- | ------------- |------------- | ------------- |
| Фича  | Новые функции системы  |1.[USM](https://practicum.yandex.ru/blog/chto-takoe-user-story-i-kak-napisat/) - чтобы понимать контекст задачи <br> 2. Достаточный перечень [Use Case сценариев](https://practicum.yandex.ru/blog/chto-takoe-use-case-kak-ih-napisat/) - для тестирования готового решения как разработчиком (написание модульных или e2e тестов), так и тестировщиком (ручной тест, e2e-тест) <br> 3. Если задача связана с бекендом, то описание поведения сервиса с **примерами рабочих контрактов** <br> 4. (Опционально) Если задача крупная (новый экран или новый модуль), то предварительно необходимо отрисовать макет в Figma |1. Модульный тест, покрывающий все сценарии описанные в Use Cases <br> 2. Расширенный список Use Cases с примерами реализации <br> 3. Внутренняя документация для других разработчиков <br> 4. Внутренняя документация для других разработчиков |
| Доработка | Все то, что пользователями не понравилось и они попросили переделать  |1. Обоснование изменений системы (_Что не понравилось ? Как надо ?_) - это позволит в дальнейшем прогнозировать поведение пользователей и их хотелки. <br> 2. Обсудить трудозатраты а Product Manager (PM)  и Team Lead (TL). Если задача меньше чем на день, то взять в текущий спринт, иначе - в беклог. |1. Обновленный модульный тест <br> 2. Обновленная документация|
| Баг | Если, что-то сломалось, то скорее всего побегут к фронту |1. Баг-репорт  | Сообщение или комментарий _Что было сломано ? Почему это было сломано ? Как этого не допустить в следующий раз ?_  |
| Рефакторинг | Если есть моменты, когда все задачи сделаны, а Бизнес-аналитики спят, то пара улучшить свой код | 1. Описание проблему <br> 2. Определить метрику выполнения работы (_Пример: Скорость загрузки системы, количество запросов, кол-во строк кода_) <br> 3. Откопать документацию и Use Case  |1. Информ. письмо о проделанной работы <br> 2. Зафиксировать бест практисы, применяемые при рефакторинге <br> 3. Детальный тест ( _в первую очередь непосредствнно разработчику, потому что тестировщику пофиг на задачи с рефакторингом_) |

> [!TIP]
> Если какого то пункта на входе не хватает-делай сам! туториалы есть в интернете.

3. Приоритет выполнения задач. Критичный баг=>Простая доработка=>Фича=>Некритичный баг=>Рефакторинг=>Cложная доработка. (_Критичность бага и сложность доработки определяется совместно с командой_)
4. Предварительная оценка трудозатрат. Если оценка не проставлена TL, то проставь ее самостоятельно.
   
# В процессе работы 
## Проектирование

> [!TIP]
> Сначал думай, потом делай !

При написании любой фичи постоянно надо **всегда** отвечать на вопросы: 

### 1. Зачем это делать ? (Уточнение ТЗ. Масштабирование и расширение)

> [!NOTE]
> Не верь никому и никогда !

**Проверка ТЗ на прочность:** Каждое условие, каждое "если" написанное в условии надо пробовать менять и оценивать возможность его изменения.Сегодня заказчик сказал А, а завтра Б, и если твое решение сильно не пострадает от этого, то это хорошее решение. 

**Определять связи и зависимости:** При прочтении ТЗ или Баг-репорта анализ зависимых модулей позволит более аккуратно вести разработку и более точно проводить последующее тестирование. Анализ проблем и требуемых изменений приложения позволит определить проблемые места в ПО и заложить их переработку и рефакторинг. (_Пример: Две задачи потребовали точечного изменения внешнего вида таблицы-пара сделать задачу на ее переработку_)

**Проблема пользователя важнее макета в фигме** Не надо бояться предлагать свои решения, даже если они не согласуются с макетом от дизайнеров. Решение со стороны разработчика зачастую будет более быстрым и эффективным и "легким", при это главное закрыть все боли пользователя (User Story)

**Разделяй и властвуй** Разделяй свою задачу на более мелкие задачи. Обезличивай мелкие задачи (это повысит шанс переиспользования компонентов) (_Пример: Если надо сделать "Таблицу для калькулятора", то сделай сначала просто таблицу, а потом уже настрой эту таблицу для калькулятора_)

### 2. Кто и что будет это делать ? Структура классов и функций.

Всегда надо помнить про ООП и SOLID:

[Шпаргалка SOLID](cheatsheets/solid-cheatsheet.md)

[Шпаргалка ООП](cheatsheets/oop-cheatsheet.md)

Чтобы не придумывать велосипед, вспоминай про патерны
[Шпаргалка по основным паттернам](cheatsheets/patterns-cheatsheet.md)

В рамках фронтенда делим все компоненты на следующие группы:
| Название |Описание | Характеристика | Когда применять |
| ------------- | ------------- |------------- | ------------- | 
| **Чистый компонент (Pure Component)** |Компоненты, которые при одних и тех же входных параметрах **всегда отображают одно и тоже** | 1. Исключить внутри компонента API запросы<br>2. Не использовать глобальное состояние внутри <br> 3. Не подключать и не использовать классы/сервисы, которые в себе что-то хранят и используются где-то еще  | 1. Визуальный компонент с отдельной логикой на отображение <br> 2. Форма или какая-то сложная кнопка (набор кнопок) с входящей колбек-функцией и только|
| **Контролируемый компонент (Controlled Component)** |**Контролируемый компонент** - это тот, который принимает свое текущее значение через props и уведомляет об изменениях с помощью колбеков, таких как onChange. Родительский компонент "управляет" им, обрабатывая обратный вызов и передавая новые значения в качестве props управляемому компоненту. |Наличие пропсов и колбеков. <br>**Пример:**<br>```<input type="text" value={value} onChange={handleChange} />``` |Почти всегда, если форма очень простая, то можно использовать и неконтролируемый |
| **Неконтролируемый компонент (Uncontrolled Component)** |**Неконтролируемые компоненты** - это те, которые хранят свое собственное состояние внутри, и вы запрашиваете DOM, используя ссылку, чтобы найти его текущее значение, когда вам это нужно. Это немного больше похоже на традиционный HTML. |Наличие пропсов и колбеков. <br>**Пример:**<br>```<input type="text" defaultValue="foo" ref={inputRef} />// Use `inputRef.current.value` to read the current value of <input>``` |Оч редко, лучше не использовать, но если форма простая, то можно  |
| **Компоненты высшего порядка (Higher-Order Component, HOC)** |**Компонент высшего порядка (Higher-Order Component, HOC)** — это один из продвинутых способов для повторного использования логики. Компоненты, которые принимают в качестве аргумета другие компоненты и выдает новый компонент| На вход подается набор компонентов |1. Переиспрользование функций <br>2. Какой-то конструктор страницы или модуля <br> 3. Обертки, прокси и другой доп. функционал  |
| **Компонент-переключатель (Switching Component)** |Компонент, который меняет отображение по какому-либо правилу | На вход подается некое условие или данные для определения этого условия | Когда есть условие на отоборажение компонентов|
| **Компоненты, отслеживающие состояния** |**Компонент с отслеживанием состояния** имеет состояние и жизненный цикл. Состояние, содержащее свойства компонента, может быть изменено только внутри компонента. Если можно избежать отслеживание состояния, то лучше избежать, чтобы избежать лишней сложности | 1. Хуки<br> 2. State<br> 3. Жизненные хуки|  Если есть какая-то параллельная логика или многовариантные сценарии
| **Компоненты без отслеживания состояния** |Если поведение компонента не зависит от его состояния, то это **компонент без сохранения состояния**. | Не отслеживает состояние| Простые линейные сценарии и алгоритмы
| **Парт (Part)** |Компонент, решающий большую часть задачи, используя ряд других компонентов. | Внутри парта заключен целостный бизнес-юнит| Объединение разных компонентов для объединения в бизнес-юнит (_Настройка(Парт) содержит разные блоки (компоненты)_)


## JS/TS
### Общие правила
1. Вместо `for(;;)` использовать `for ...of` или `forEach`.
2. Если есть одно условия, то необходимо использовать сокращенное условие
<br>**Bad:**
   <br>
   ```
   if (test) {
    // Perform something if test is true
    // …
    return;
    } else {
    // Perform something if test is false
    // …
    }
    ```
    **Good:**
    ```
    if (test) {
    // Perform something if test is true
    // …
    return;
    }
    // Perform something if test is false
    ```
1. При использовании `switch` не писать `break` после `return`
2. При отлавливании ошибок используется конструкцию `try...catch` и логируется ошибки в `catch`
3. Все классы называем через `PascalСase`, объекты-`camelCase`
4. Название переменных:
   1. Используйте короткие наименования (обычно 5-15 символов), без сложных сокращений
   2. Используйте релевантные названия переменных, они должны быть понятны любому разработчику и по возможности связаны с реальным объектом
   3. Обычные перменные называть `camelCase`, константы `NAMES_LIKE_THIS`
5. Определять сложные условия до условных операторов
    **Bad**
    ```
    if (user.firstName && user.lastname) {
    //do something
    }
    ```
    **Good:**
    ```
    const hasFullUserName = user.firstName && user.lastname
    if (hasFullUserName) {
    //do awesome something
    }
    ```   
6. Комментарии. Избегайте написания комментариев для объяснения кода. Используйте комментарии для ответа на вопрос “Почему?” вместо “Как?”. В некоторых случаях вы могли бы написать комментарий к коду: предупреждения, сложные выражения или разъяснение необычного решения.
7. Избегать сравнение со строчками напрямую
   
   **Bad**
    ```
    const MyComponent = (type) => {
    const text = type === 'correct' ? '😎' : '😢'
    return (
        <Emoji>
        {text}
        </Emoji>
    )
    }
    ```
    **Good:**
    ```
    const FEEDBACK = {
    CORRECT: 'correct',
    INCORRECT: 'incorrect',
    }
    const MyComponent = (type) => {
    const text = type === FEEDBACK.CORRECT ? '😎' : '😢' 
    return (
        <Emoji>
        {text}
        </Emoji>
    )
    }

    ```   
### Производительность
Необходимо отдавать предпочтение понятности и выразительности кода, а не производительности. Обычные функции врядли будут узким местом в вашем приложении (!При это совсем забывать про сложность алгоритмов не надо). Повышайте производительность за счет сжатия дополнительных файлов, правилам сборки, сокротить количество ререндера страницы.

**Bad**
```// bad (albeit way faster)
const arr = [1, 2, 3, 4];
const len = arr.length;
var i = -1;
var result = [];
while (++i < len) {
  var n = arr[i];
  if (n % 2 > 0) continue;
  result.push(n * n);
}
```
**Good:**
```
const arr = [1, 2, 3, 4];
const isEven = n => n % 2 == 0;
const square = n => n * n;
const result = arr.filter(isEven).map(square);
```
### Чистота функций и объектов
Используйте внешние зависимости и связи только если это необходимо. Старайтесь сделать функции чистыми. Это позволит избежать лишних мутаций

**Bad**
```
const merge = (target, ...sources) => Object.assign(target, ...sources);
merge({ foo: "foo" }, { bar: "bar" }); // => { foo: "foo", bar: "bar" }
```
**Good**
```
const merge = (...sources) => Object.assign({}, ...sources);
merge({ foo: "foo" }, { bar: "bar" }); // => { foo: "foo", bar: "bar" }
```
### Преобразование типов
Не бойтесь использовать преобразование типов, это часто сокращает количество кода

**Bad**
```
if (x === undefined || x === null) { ... }
```
**Good**
```
if (x == undefined) { ... }
```

### Правила типизации 
1. **Типы>Интерфейсов**. Все, что не касается классов и их наследия, использовать типы.
2. **Типизировать все, приходящее с бека**. Делать это можно с помощью Json2Ts (beshanoe.github.io)
3. **Не использовать Any (только в экстренных случаях), лучше использовать unknow.** Unknown – это рестриктивный тип, который был 
введён в TypeScript 3.0. Он более ограничен в сравнении с any и может избавить вас от ряда непредвиденных ошибок.
4. **Чаще использовать enum.** Где очевидно, что у поля может быть ограниченное количество вариантов-сразу писать енам.
5. **Структурировать типы с помощью nameSpace.** Пространства имён позволяют организовывать код и избегать коллизий в именовании его 
элементов. Пространства имён также позволяют предотвратить коллизии в именовании за счёт присваивания фрагменту кода 
уникального имени.
Там, где можно обобщить и типизировать, надо использовать generic. Дженерики позволяют писать более универсальный код, 
способный работать с любым типом. С их помощью можно написать одну функцию, класс либо интерфейс, работающие с несколькими 
типами.
Позволять типам "дышать". Надо обосновано делать поля необязательными. При расширении типов надо делать поля такими, 
расширение не должно аффектить предыдущих код. Пример: Есть задача "Надо сделать возможность скрывать карточки" - плохое 
решение сделать поле IsVis-т.к. оно скроет все что было, хорошее решение сделать поле isHide- то что было, оно и было, но можно 
теперь скрывать. 
Ну наверное можно и про декораторы что-то сказать и где-то использовать.
### Организация типов
1. Все, что приходит с API типизировать в корне проекта в отдельной папке **src/types/api**
2. Все, что не привязано к конкретным элементам, частям интерфейса, модулям, вещи которые являются верхнеуровневым (Пример: 
Модели, Событие, Установка) типизировать в корне проекта в отдельной папке **src/types/common**
3. Все, что нужно для фронта и только фронта, вещи, которые не выходят за пределы кода, (Пример: Контракт общения между 
компонентами, структура виджета, Системное уведомление, Схема пользователя), типизировать в отдельной папке **src/types/system**
4. Все, что не выходит за бизнес логику определенного модуля, (Пример: Конфиг для настройки модели, Счетчики статистики) 
типизировать в папке **<module-name>/types**
5. Все, что используется в рамках только одного компонента или функции типизировать непосредственно в файле компонента или функци

## HTML/CSS
### Семантика 
**Bad**
```
<div id=main>
  <div class=article>
    <div class=header>
      <h1>Blog post</h1>
      <p>Published: <span>21st Feb, 2015</span></p>
    </div>
    <p>…</p>
  </div>
</div>
```
**Good**
```
<main>
  <article>
    <header>
      <h1>Blog post</h1>
      <p>Published: <time datetime=2015-02-21>21st Feb, 2015</time></p>
    </header>
    <p>…</p>
  </article>
</main>

```

### БЭМ
Использовать не только как правила наименования классов, а как шпоргалку на разделение хтмл контента на модули, и стили на классы
[БЭМ](https://ru.bem.info/?ysclid=lrdnt4ce8h601236929)
### Общие рекомендации по CSS
1. Избегать хардкода цветов, различных растояний или размеров  
    **Bad**
    ```
    .button {
    color: #333;
    padding: 16px;
    }
    ```
    **Good**
    ```
    .button {
    color: var(--color-primary);
    padding: var(--space-sm);
    }

    ```
2. Не использовать ID никогда
    **Bad**
    ```
    #header { ... }
    #section { ... }
    ```
    **Good**
    ```
    .header { ... }
    .section { ... }
    ```
3.  Не определять стили напрямую в HTML/JSX
    **Bad**
    ```
    input[type="text"] { ... }
    header
    section
    ```
    **Good**
    ```
    .form-control { ... }
    .header { ... }
    .section { ... }
    ```
4. Избегайте вложенности элементов - это снижает проивзодительность и читаемость css
  **Bad**
    ```
   .navbar ul { ... }
    .navbar ul li { ... }
    .navbar ul li a { ... }
    ```
    **Good**
    ```
    .navbar { ... }
    .nav { ... }
    .nav__item { ... }
    .nav__link { ... }
    ```
5.  Избегайте перегрузку селекторов.
    **Bad**
    ```
    img[src$=svg], ul > li:first-child {
    opacity: 0;
    }
    ```
    **Good**
    ```
    [src$=svg], ul > :first-child {
    opacity: 0;
    }
    ```
6. Минимизируйте сочетания селекторов связанных с DOM. Иногда, лучше добавить лишний класс элементу, который вы хотите найти, чем искать его через три структурных селектора: псевдо-класс, потомок, комбинатор.
   **Bad**
    ```
    div:first-of-type :last-child > p ~ *
    ```
    **Good**
    ```
    div:first-of-type .info

    ```
7. Специфичность Не используйте значения и селекторы, которые нельзя переопределить. Минимизируйте использование id (идентификаторов) и избегайте правило `!important`
    **Bad**
    ```
    .bar {
    color: green !important;
    }
    .foo {
    color: red;
    }
    ```
    **Good**
    ```
    .foo {
    color: red;
    }
    .foo.bar {
    color: green;
    }
    ```

8. Переопределение стилей селекторов затрудняет отладку. Избегайте этого, если можете.
    **Bad**
    ```
    li {
    visibility: hidden;
    }
    li:first-child {
    visibility: visible;
    }
    ```
    **Good**
    ```
    li + li {
    visibility: hidden;
    }
    ```

9. Не дублируйте определения селекторов, стили которых и так могут передаваться по наследству.
    **Bad**
    ```
    div h1, div p {
    text-shadow: 0 1px 0 #fff;
    }
    ```
    **Good**
    ```
    div {
    text-shadow: 0 1px 0 #fff;
    }
    ```
## GIT
### Зачем правильно коммитить ? 
* Проще отслеживать изменения
* Легче сливать изменения
* Проще погружаться в чужой код
### Как правильно коммитить
1. Модульность - каждый коммит должен содержать только одну группу связанных изменений.
2. Коммить как можно чаще. Коммит должен быть минимально достаточным для воспроизведения инкремента. Частые коммиты позволяют проще отслеживать и легче вливать.
3. Название коммита. Кратко и ясно.<br>
3.1. Начало с большой буквы<br>
3.2. Не ставить точку в конце описания<br>
3.3. Начинать с глагола в форме инфинитива<br>
3.4. (опционально) Все коммиты на английском<br>
1. Отвечать на вопросы "что" и "почему", а не "как" ?
2. Не коммитить незаврешенные работы. Только в крайнем случае с указанием незавершенности в описании (_in-progress_)
![Commit Meme](/assets/mem1.jpg)
## Документация кода
...In coming
# После завершения работы
## Rewiew
### Общие рекомендации 
* Любой реквест должен быть рассмотрен и одобрен перед отправкой в прод. Каждый участник команды должен участвовать в этом процессе и помогать коллегам делать свой код лучше, ведь мы работаем над проектом вместе.
* Уведомления о новых реквестах на почту должны быть обязательно включены для всех репозиториев команды. Если вы увидели такое письмо, не надо сразу бежать смотреть, однако лучше это сделать в течении текущего/следующего дня. Время на ревью такое же рабочее время, оно есть у каждого. Ситуация, когда автор спамит в чат с просьбой посмотреть свой реквест - сигнал о том, что процесс хромает. Ставьте себе напоминания на конкретные реквесты, чтобы не забыть о них.
* Если вы плохо понимаете контекст задачи и логику в реквесте, то оцените просто качество кода, это может сделать каждый. Если у вас 
есть вопросы по логике или коду - пишите комменты, если вы видите критичные недоработки - жмите Request changes, не бойтесь этого 
* Если вы не видите проблемных моментов, однако чувствуете, что не понимаете логики - все равно поставьте Approve с комментарием в 
стиле - “У меня вопросов нет, но недостаточно экспертизы, чтобы оценить корректность логики”, так вы покажете, что уделили внимание 
и рассмотрели изменения в меру своих сил. Автор реквеста должен понимать этот момент и ждать Approve от человека, который 
сможет оценить контекст. Таким человеком может быть либо сотрудник с правами maintainer, либо разработчик, понимающий суть 
изменений.
* Постарайтесь понять контекст задачи и логику, но если контекст задачи слишком велик, то смотрите пункт выше. Рекомендую стараться 
вникать в контекст задач, потому что, возможно, что в будущем вам придется работать с этим реквестом. 
* Если вы открыли реквест и видите Approve от другого человека, все равно посмотрите код. Ваш коллега мог упустить из вида то, что 
увидите вы.
* Вы можете оставить комментарий, который не обязательно исправлять, под такие правила попадает всякого рода "вкусовщина". Для подобных комментариев можете поставить префикс, например, "пожелания".
### Чек-лист перед отправкой  на ревью 
**Проектирование:**
* Органично ли взаимодействуют компоненты системы друг с другом?
* Укладывается ли новый код в уже сложившуюся кодовую базу? (Например, новый кусок кода явно выбивается из общего стиля. У ревьюера сразу возникнет вопрос, чем вызвано такое нововведение, может быть здесь закрался неочевидный баг... и т.п.,)

**Функциональность:**

* Делает ли этот код то, что задумал разработчик? 
* Удобно ли использовать реализованные компоненты внутри текущей кодовой базы для других разработчиков?
* Если изменения затрагивают информационную безопасность. (Проверьте, что в открытый доступ не попадут "секреты")

**Сложность:**

* Насколько сложен написанный код? (Чем проще - тем понятнее, грубо говоря)
* А нужна ли вам сейчас преждевременная оптимизация кода? (Хорошая практика - не гнаться за преждевременной оптимизацией кода, главное хорошо и качественно решить текущую задачу)
* Не гонитесь решить будущую проблему, которой еще нет. (Так же хорошая практика не пытаться предусмотреть будущие проблемы, которые еще не возникали и может никогда и не возникнут)
  
**Тестирование:**

* Покрыт ли новый код тестами?
* Действительно ли тесты работают? (Например, тест проверяет, что функция возвращает int. Проверьте, что тест действительно упадет, если функция вернет, например, строку (str))
* Правильно ли разделены тесты между разными методами?

**Оформление:**

* Выбраны ли хорошие имена для переменных, функций, классов и других составляющих кода? (Они должны отображать свое 
назначение, но не должны быть слишком длинными)
* Написал ли разработчик понятные комментарии и нужны ли они вообще? 

### Чек-лист ревьюера:
* Код хорошо спроектирован.
* Предоставленный интерфейс удобен пользователям кода и клиентским приложениям, а в итоге — конечным пользователям.
* Учтены возможные проблемы параллелизма.
* Код не переусложнён.
* Код реализует ровно то, что нужно в данный момент.
* Код покрыт тестами.
* Тесты хорошо спроектированы.
* Выбраны понятные названия для переменных, функций, классов.
* Комментарии полезные и описывают, почему написан код, а не что он делает.
* Код оформлен в соответствии с принятыми стандартами
## Тесты
...In coming
