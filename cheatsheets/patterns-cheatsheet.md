Паттерны проектирования
===
Паттерны проектирования - это Б А З А. Паттерны представляют типичные решения  часто повторяющихся проблем. Они представляют не конкретный класс или функцию, а только концепцию для разработки своего решения.

Все примеры будут реализованы на React как самая популярная JS-библиотека


# Базовые паттерны
## Одиночка 
**Одиночка** — это порождающий паттерн проектирования,
который гарантирует, что у класса есть только один
экземпляр, и предоставляет к нему глобальную точку
доступа.

![Singletron Img](/assets/singleton2.png)

### Пример 
Создаем класс-счетчик `Counter`.<br>У этого класса объявлены следующие поля:
- `getInstance` - возвращает экземпляр класса
- `getConut` - возвращает текущее значение счетчика
- `increment` - увеличение счетчика
- `decrement` - уменьшение счетчика
 
 ```
 let instance;
let counter = 0;

class Counter {
  constructor() {
    if (instance) {
      throw new Error("You can only create one instance!");
    }
    instance = this;
  }

  getInstance() {
    return this;
  }

  getCount() {
    return counter;
  }

  increment() {
    return ++counter;
  }

  decrement() {
    return --counter;
  }
}

const singletonCounter = Object.freeze(new Counter());
export default singletonCounter;
```
Пусть у нас есть следующие файлы
- `counter.js`: содержит singletonCounter
- `redButton.js`: Красная нопка, которая импортирует счетчик и меняет его
- `blueButton.js`: Синяя нопка, которая импортирует счетчик и меняет его

![Img Singleton](/assets/singleton.png)

Когда мы вызываем метод инкрементации счетчика в красной или синей кнопки, меняется значение в счетчике, следовательно и в красной и синей кнопке получат новое значение счетчика 

### Summary
Во многом паттерн является оверхед и иногда глобальная доступность объекта может создавать неконтролируемые сайд-эффекты

## Прокси-паттерн 
Паттерн, позволяющий обернуть объект дополнительной логикой на получение или изменение его свойств.
### Пример 
Реализуем объект `person`
```
const person = {
  name: "John Doe",
  age: 42,
  nationality: "American",
};
```
Вместо того, чтобы обращаться к объекту напрямую используем объект [Proxy](https://learn.javascript.ru/proxy?ysclid=lrb7zt2ro8757531210) 
```
const personProxy = new Proxy(person, {});
```
Второй аргумент у конструктора прокси это хендлер который позволяет реализовать различные воздействия на объект. Самые банальные `set` и `get`
- `get`: Получение значение свойства объекта
- `set` изменение состояния объекта
 ```
 const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    console.log(`The value of ${prop} is ${obj[prop]}`);
  },
  set: (obj, prop, value) => {
    console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
    obj[prop] = value;
  },
});
```
### Summary
Прокси-объекты полезны для различной валидации или логировании
Cтоит помнить, что чрезмерное использование прокси-объекта или выполнение сложных операций при каждом вызове метода обработчика может легко негативно сказаться на производительности вашего приложения.
## Паттерн-прототип
Паттерн Prototype — это полезный способ совместного использования свойств многими объектами одного типа. Prototype — это нативный объект JavaScript, и к нему могут обращаться объекты через цепочку prototype.
### Пример
Cоздаем класс `Dog`. 
```
class Dog {
  constructor(name) {
    this.name = name;
  }

  bark() {
    return `Woof!`;
  }
}

const dog1 = new Dog("Daisy");
const dog2 = new Dog("Max");
const dog3 = new Dog("Spot");
```
Все свойства попределенные в классе автоматически добавляются в прототип
```
console.log(Dog.prototype);
// constructor: ƒ Dog(name, breed) bark: ƒ bark()

console.log(dog1.__proto__);
// constructor: ƒ Dog(name, breed) bark: ƒ bark()
```
Вместо того, чтобы каждый раз определять свойства объекта, можно добавить свойство к прототипу
```
class SuperDog extends Dog {
  constructor(name) {
    super(name);
  }

  fly() {
    return "Flying!";
  }
}
```
![Prototype-pattern2](/assets/prototype2.png)
### Summary
Данный прототип позволяет проще получать и наследовать свойства других объектов. Цепочка наследований уменьшает и количество кода, и объем занимаемой памяти.
## Паттерн наблюдателя
При реализации паттерна наблюдателя определяются:
- `observers` - объекты, подписанные на другой объект, которые получают данные через подписку
- `observable` - объект, который щарит данные для своих подписчиков

`observable` объект обычно содержит следующие поля:

- `observers`: список подписчиков
- `subscribe()`: метод, который добавляет функцию-коллбек в массив подписчиков
- `unsubscribe()`: удаление из массива подписчиков
- `notify()`: уведомление всех подписчиков
  ![Observable](/assets/observable-example.png)
### Пример
```
class Observable {
  constructor() {
    this.observers = [];
  }

  subscribe(func) {
    this.observers.push(func);
  }

  unsubscribe(func) {
    this.observers = this.observers.filter((observer) => observer !== func);
  }

  notify(data) {
    this.observers.forEach((observer) => observer(data));
  }
}
```
### Summary
Полезный паттерн, повсеместно  используемый.
На базе этого паттерна реализована библиотека [RxJS](https://rxjs-dev.firebaseapp.com/)
## Посредник
Паттерн посредника позволяет наладить общение компонентов через единую точкку-объект или функцию.
**Вместо:**
![Bad connection of objects](/assets//bad-connection.png)
**Получается это**
![Good connection of objects](/assets//good-connection.png)
Вместо того, чтобы позволять каждому объекту напрямую взаимодействовать с другими объектами, что приводит к связи "многие ко многим", запросы объекта обрабатываются посредником. Посредник обрабатывает этот запрос и отправляет его туда, куда должен.
### Пример
Примером данного паттерна групповой чат. Пользователи не хотят общаться напрямую, они отправляют сообщения в групповой чат
```
class ChatRoom {
  logMessage(user, message) {
    const time = new Date();
    const sender = user.getName();

    console.log(`${time} [${sender}]: ${message}`);
  }
}

class User {
  constructor(name, chatroom) {
    this.name = name;
    this.chatroom = chatroom;
  }

  getName() {
    return this.name;
  }

  send(message) {
    this.chatroom.logMessage(this, message);
  }
}
```

# Паттерны рендеринга
# Паттерны производительности