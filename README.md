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
<br>
### 1. Зачем это делать ?. Уточнение ТЗ. Масштабирование и расширение

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



### 3. Как это делать ? Общие правила написания кода, файловая структура 

Чет из функционалки взять, файловая структура, комменты 
## JS
## HTML/CSS
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
4. Отвечать на вопросы "что" и "почему", а не "как" ?
5. Некоммитить незаврешенные работы. Только в крайнем случае с указанием незавершенности в описании (_in-progress_)
![Commit Meme](/assets/mem1.jpg)
# После завершения работы
## Rewiew
## Тесты
