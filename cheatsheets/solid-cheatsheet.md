SOLID
===

Эти принципы, когда применяются вместе, предназначены для повышения вероятности того, что программист создаст систему, которую будет легко поддерживать и расширять в течение долгого времени. Принципы SOLID — это руководства, которые могут применяться во время работы над программным обеспечением для удаления «кода с запашком» предписывая программисту выполнять рефакторинг исходного кода, пока тот не станет разборчиво написанным и расширяемым. Это часть общей стратегии гибкой и адаптивной разработки.

## Принцип единственной ответственности (Single responsibility)

> На каждый объект должна быть возложена одна единственная обязанность

Для этого проверяем, сколько у нас есть причин для изменения класса — если больше одной, то следует разбить данный класс.
### Пример на  JS
```
//Функция ниже считает только сумму и больше ничего
const calcTotal = (items) => items.reduce((sum, val)=>sum+val, 0);
//А эта функция выполняет сразу две задачи — считает площадь и периметр прямоугольника:
const calculateAreaAndPerimeter = (rectangle) => {
  const area = rectangle.width * rectangle.height;
  const perimeter = 2 * (rectangle.width + rectangle.height);
  return { area, perimeter };
}
//Разделим на функции с одной ответственностью:
const calculateArea = (rectangle) => rectangle.width * rectangle.height;
const calculatePerimeter = (rectangle) => 2 * (rectangle.width + rectangle.height);
```
### Пример на  React
```
import React, { useState } from "react";

function ProfileForm({ user }) {
  const [name, setName] = useState(user.name);
  const [email, setEmail] = useState(user.email);

  function handleSubmit(event) {
    event.preventDefault();
    // Обнови имя пользователя и емэил на сервере 
  }

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Name:
        <input type="text" value={name} onChange={e => setName(e.target.value)} />
      </label>
      <label>
        Email:
        <input type="email" value={email} onChange={e => setEmail(e.target.value)} />
      </label>
      <button type="submit">Update Profile</button>
    </form>
  );
}
export default ProfileForm;
```
Какую задачу решает этот компонент?
1. Отображает форму для редактирования профиля пользователя
2. Обновляет имя и электронную почту пользователя на сервере
Так как в ответе на поставленный вопрос у нас получилось два пункта, а не один — мы нарушили принцип единой ответственности. Перепишем код:

```
import React, { useState } from "react";
import UpdateProfileButton from "./UpdateProfileButton";

function ProfileForm({ user, onUpdateProfile }) {
  const [name, setName] = useState(user.name);
  const [email, setEmail] = useState(user.email);

  return (
    <form>
      <label>
        Name:
        <input type="text" value={name} onChange={e => setName(e.target.value)} />
      </label>
      <label>
        Email:
        <input type="email" value={email} onChange={e => setEmail(e.target.value)} />
      </label>
      <UpdateProfileButton onUpdateProfile={() => onUpdateProfile({ name, email })} />
    </form>
  );
}
export default ProfileForm;
```
Теперь у нас есть два компонента, каждый из которых отвечает за свою задачу:

1. **ProfileForm** — выводит форму
2. **UpdateProfileButton** — отправляет данные из формы на сервер


## Принцип открытости/закрытости (Open-closed)

> Функция или класс должны быть открыты для расширения, но не для модификации.

Открыт для расширения - мы можем расширить то, что делает модуль.
Закрыт для модификации - расширение поведения модуля не приводит к изменению исходного кода модуля.
### Пример на  JS
Предположим, у нас есть функция, которая использует определенную логику для расчета скидки:
```
const applyDiscountToCart = (cart, discount) => {
  for (let item of cart.items) {
    if (discount.type === "percentage") {
      item.price = item.price * (1 - discount.amount / 100);
    } else if (discount.type === "fixed") {
      item.price = item.price - discount.amount;
    }
  }
}
```
Если мы захотим добавить новые виды скидок — придется менять код функции `applyDiscountToCart`. Это нарушит принцип OCP. Вместо этого, вынесем логику в отдельные функции:
```const applyPercentageDiscount = (itemPrice, discountAmount) => {
  return itemPrice * (1 - discountAmount / 100);
}

const applyFixedDiscount = (itemPrice, discountAmount) => {
  return Math.max(0, itemPrice - discountAmount);
}

const applyDiscountToCart = (cart, discountApplier) => {
  for (let item of cart.items) {
    item.price = discountApplier(item.price, cart.discount.amount);
  }
  return cart;
}
```
Теперь функция `applyDiscountToCart` принимает в качестве второго параметра не размер скидки, а функцию, которая расчитывает размер скидки исходя из собственной логики.
```
const cart = {
  items: [{ name: "стул жидкий", price: 50 }],
  discount: { amount: 10 },
};

const cartDiscounted = applyDiscountToCart(cart, applyPercentageDiscount);
console.log(cartDiscounted.items[0].price); // 45
```
### Пример на React
Рассмотрим пример компонента, который выводит заголовок в зависимости от типа животного из пропса:
```
import React from "react";

export default function AnimalSound(props) {
  const { animal } = props;
  
  switch (animal) {
    case "dog":
      return <h1>Woof!</h1>;
    case "cat":
      return <h1>Meow!</h1>;
    case "cow":
      return <h1>Moo!</h1>;
    case "duck":
      return <h1>Quack!</h1>;
    default:
      return <h1>Unknown animal!</h1>;
  }
}
```
Каждый раз, когда нам потребуется выводить новый звук для другого животного, потребуется изменять код компонента и добавлять новое утверждение case. Это нарушает принцип открытости/закрытости.

Посмотрим, как мы можем отрефакторить этот компонент:
```
import React from "react";

const animalSounds = {
  dog: "Woof!",
  cat: "Meow!",
  cow: "Moo!",
  duck: "Quack!",
};

export default function AnimalSound(props) {
  const { animal } = props;
  
  const sound = animalSounds[animal] || "Unknown animal!";
  
  return <h1>{sound}</h1>;
}
```
Мы создали объект `animalSounds`, который содержит названия животных в качестве ключей и различные звуки в качестве значений. Мы используем этот объект, чтобы динамически подставлять ключ, который получаем во входных параметров и получать соответствующее значение звука из объекта. Это делает компонент открытым для расширения, потому что мы можем добавлять новые звуки животных в объект `animalSounds`, при этом исходный код компонента не меняется.

## Принцип подстановки Барбары Лисков (Liskov substitution)

> Объекты в программе могут быть заменены их наследниками без изменения свойств программы

Представьте, что у вас есть башня из блоков Lego. Если вы уберете один блок и замените его другим блоком той же формы и размера, башня будет стоять и выглядеть так же.

Если у вас есть класс, который используется в программе, вы должны иметь возможность заменить его подклассом (более конкретным типом класса), и программа все равно будет работать правильно, без ошибок или неожиданного поведения.
### Пример на JS
Рассмотрим классический пример с прямоугольником и квадратом. Квадрат — частный случай прямоугольника. Высота и ширина прямоугольника могут быть любыми, а для квадрата они равны.

Создадим два класса — более общий для прямоугольника, и частный для квадрата. Напишем функцию, которая выводит в консоль площадь фигуры, которую мы передаем в качестве аргумента.
```
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  constructor(size) {
    super(size, size);
  }
}

const printArea = (rectangle) => {
  console.log(rectangle.getArea());
}

const rectangle = new Rectangle(5, 10);
const square = new Square(5);

printArea(rectangle); // Output: 50
printArea(square); // Output: 25
```
Функция printArea соответствует принципу подстановки Лисков, т.к. она одинаково корректно отработает как для базового класса прямоугольника, так и для частного класса квадрата.
### Пример на React
В качестве примера снова возьму компонент с кнопкой:
```
function Button({ color }) {
  const styles = {
    padding: '10px',
    backgroundColor: color,
    borderRadius: '5px',
    boxShadow: '2px 2px 5px rgba(0, 0, 0, 0.25)',
  };

  return <button style={styles}>Click me!</button>;
}
```
В этом компоненте мы отрисовываем кнопку с заданными стилями, а цвет этой кнопки меняется в зависимости от значения входного параметра color.

Этот компонент нарушает принцип подстановки Лисков, т.к. не позволяет принимать дополнительные входные параметры для более специфических случаев. В частности, для такого компонента необходим обработчик события нажатия на кнопку.

Перепишем компонент:
```
function Button({ color }) {
  const styles = {
    padding: '10px',
    backgroundColor: color,
    borderRadius: '5px',
    boxShadow: '2px 2px 5px rgba(0, 0, 0, 0.25)',
  };

  const handleClick = useCallback(() => {
    console.log('Button clicked!');
  }, []);

  return (
    <button style={styles} onClick={handleClick}>
      Click me!
    </button>
  );
}
```
Теперь он в большей степени соответствует принципу подстановки Лисков. Но мы можем сделать его еще более гибким и реиспользуемым:
```
function Button({ color, onClick, children, ...restProps }) {
  const styles = {
    padding: '10px',
    backgroundColor: color,
    borderRadius: '5px',
    boxShadow: '2px 2px 5px rgba(0, 0, 0, 0.25)',
  };

  const handleClick = useCallback(() => {
    if (onClick) {
      onClick();
    }
  }, [onClick]);

  return (
    <button style={styles} onClick={handleClick} {...restProps}>
      {children}
    </button>
  );
}
```
Мы добились ряда улучшений:

1. Принимаем колбэк функцию onClick для обработки событий во входных параметрах
2. Добавили children для вставки любого контента, в том числе дополнительных html-элементов в кнопку
3. Добавили ...restProps который позволяет применять любые другие дополнительные параметры к элементу кнопки (className, style, aria-label и т.д.)
Благодаря этим изменениям, мы сделали компонент Button еще более гибким и пере-используемым. Это значительно упрощает использование компонента в различных контекстах и для различных случаев применения.

## Принцип разделения интерфейса (Interface segregation)

> Много специализированных интерфейсов лучше, чем один универсальный

Проверяем, насколько много интерфейс содержит методов и насколько разные функции накладываются на эти методы, и если необходимо — разбиваем интерфейсы.
### Пример на JS
Возьмем в качестве примера класс животного:
```
class Animal {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  
  eat() {
    // общее пищевое поведение
  }
  
  fly() {
    // общее поведение полета
  }
}
```
Этот пример нарушает принцип разделения интерфейсов. Каждое животное имеет пищевое поведение, но далеко не каждое может летать. Вместо этого, лучше разделить классы на более специфические.
```
class Animal {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  eat() {
    // общее пищевое поведение
  }
}

class FlyingAnimal extends Animal {
  fly() {
    // общее поведение полета
  }
}

class Bird extends FlyingAnimal {
  chirp() {
    // общее поведение щебетания 
  }
}

class Bat extends FlyingAnimal {
  echolocate() {
    // общее поведение эхолокации
  }
}

```
### Пример на React 
Возьмем более сложный и приближенный к рабочей задаче пример. Для наглядности добавлю TypeScript. У нас есть компонент, который выводит информацию о пользователе:
```
interface UserData {
  id: string;
  name: string;
  email: string;
  // ...
}

interface UserDashboardProps {
  userData: UserData;
}

function UserDashboard(props: UserDashboardProps) {
  const { userData } = props;

  return (
    <div>
      <h1>User Dashboard</h1>
      <p>ID: {userData.id}</p>
      <p>Name: {userData.name}</p>
      <p>Email: {userData.email}</p>
      {/* display other user data */}
    </div>
  );
}
```
Пока все хорошо. Но нам нужно откуда-то брать данные для этого компонента. Эти данные лежат на сервере. Нужно написать запрос. Как это сделать, чтобы не нарушить принцип разделения интерфейсов? Вынести в отдельный компонент:
```
import { useState, useEffect } from 'react';
// ...
interface UserDataLoader {
  loadUserData(id: string): Promise<UserData>;
}

function UserDataLoaderImpl(): UserDataLoader {
  function loadUserData(id: string): Promise<UserData> {
    return fetch(`https://example.com/users/${id}`)
      .then(response => {
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        return response.json();
      })
      .catch(error => {
        console.error('Error loading user data:', error);
        throw error;
      });
  }

  return { loadUserData };
}

// ...
```
1. Функция `loadUserData` принимает id пользователя в качестве аргумента, делает стандартный get-запрос на сервер, и возвращает результат или обрабатывает ошибку
2. Функция `UserDataLoaderImpl` — обертка, которая возвращает функцию `loadUserData`
Теперь перед нами еще одна проблема — как правильно добавить этот функционал? Всегда ли нам нужно грузить данные с сервера при использовании `UserDashboard` или в каких-то случаях мы просто передадим данные пользователя в качестве входных параметров? Скорее второе. Как это реализовать? Создать прослойку в виде компонента-контейнера:
```
interface UserDashboardContainerProps {
  userId: string;
}

function UserDashboardContainer(props: UserDashboardContainerProps) {
  const [userData, setUserData] = useState<UserData | null>(null);

  useEffect(() => {
    const userDataLoader = UserDataLoaderImpl();
    userDataLoader.loadUserData(props.userId)
      .then(data => setUserData(data))
      .catch(error => console.error('Error loading user data:', error));
  }, [props.userId]);

  if (!userData) {
    return <div>Loading...</div>;
  }

  return <UserDashboard userData={userData} />;
}
```
Что делает этот промежуточный компонент:

Принимает в качестве аргумента `id` пользователя
Хранит состояние `userData`, по-умолчанию оно `null`
При маунте и изменении входного параметра использует асинхронную функцию `loadUserData`,
при успешной подгрузке данных с сервера обновляет состояние `userData`
Когда `userData` содержит данные, рендерится компонент `UserDashboard`, который принимает данные о пользователе в качестве входных параметров
Собираем всё вместе:
```
import { useState, useEffect } from 'react';

interface UserData {
  id: string;
  name: string;
  email: string;
  // ...
}

interface UserDataLoader {
  loadUserData(id: string): Promise<UserData>;
}

function UserDataLoaderImpl(): UserDataLoader {
  function loadUserData(id: string): Promise<UserData> {
    return fetch(`https://example.com/users/${id}`)
      .then(response => {
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        return response.json();
      })
      .catch(error => {
        console.error('Error loading user data:', error);
        throw error;
      });
  }

  return { loadUserData };
}

interface UserDashboardProps {
  userData: UserData;
}

function UserDashboard(props: UserDashboardProps) {
  const { userData } = props;

  return (
    <div>
      <h1>User Dashboard</h1>
      <p>ID: {userData.id}</p>
      <p>Name: {userData.name}</p>
      <p>Email: {userData.email}</p>
      {/* display other user data */}
    </div>
  );
}

interface UserDashboardContainerProps {
  userId: string;
}

function UserDashboardContainer(props: UserDashboardContainerProps) {
  const [userData, setUserData] = useState<UserData | null>(null);

  useEffect(() => {
    const userDataLoader = UserDataLoaderImpl();
    userDataLoader.loadUserData(props.userId)
      .then(data => setUserData(data))
      .catch(error => console.error('Error loading user data:', error));
  }, [props.userId]);

  if (!userData) {
    return <div>Loading...</div>;
  }

  return <UserDashboard userData={userData} />;
}
```


## Принцип инверсии зависимостей (Dependency Invertion)

> Модули высокого уровня не должны зависеть от модулей низкого уровня

Детали реализации низкоуровневых модулей не должны быть открыты для высокоуровневых модулей, а изменения в низкоуровневых модулях не должны влиять на высокоуровневые модули.

### Пример на JS
Возьмем модуль, который содержит логику работы с платежными системами PayPal и Stripe:
```
class PaymentProcessor {
  constructor(paymentGateway) {
    this.paymentGateway = paymentGateway;
  }

  processPayment(amount) {
    if (this.paymentGateway.type === 'stripe') {
      // Обработай платеж Stripe
      return this.paymentGateway.processStripePayment(amount);
    } else if (this.paymentGateway.type === 'paypal') {
      // Обработай платеж PayPal
      return this.paymentGateway.processPayPalPayment(amount);
    } else {
      throw new Error('Unsupported payment gateway');
    }
  }
}

class StripePaymentGateway {
  constructor(apiKey) {
    this.type = 'stripe';
    this.apiKey = apiKey;
  }

  processStripePayment(amount) {
    // Обработай платеж используя Stripe API
    console.log(`Processing Stripe payment of $${amount} with API key ${this.apiKey}`);
  }
}

class PayPalPaymentGateway {
  constructor(username, password) {
    this.type = 'paypal';
    this.username = username;
    this.password = password;
  }

  processPayPalPayment(amount) {
    // Обработай платеж используя PayPal API
    console.log(`Processing PayPal payment of $${amount} with username ${this.username} and password ${this.password}`);
  }
}

const stripeGateway = new StripePaymentGateway('my-stripe-api-key');
const paymentProcessor = new PaymentProcessor(stripeGateway);
paymentProcessor.processPayment(100);
```
Что тут происходит:

У нас есть два платежных шлюза (`Stripe`, `Paypal`) и процессор для обработки платежных шлюзов (`PaymentProcessor`)
В классе платежного шлюза описана логика обработки платежа с учетом особенностей API — для `Stripe` нужен ключ, а для `PayPal` логин и пароль
Процессор принимает экземпляр платежного шлюза и вызывает метод обработки платежа в зависимости от типа экземпляра (`Stripe` или `Paypal`)
Проблема в том, что `PaymentProcessor` класс зависит от конкретной имплементации платежного шлюза — проверяет тип внутри метода processPayment. Это делает код негибким, его сложно менять или расширять в будущем.

Чтобы отрефакторить код с учетом DIP (принципа инверсии зависимостей), можем сделать, чтобы метод processPayment в процессоре просто вызывал соответствующий метод в экземпляре платежного шлюза, а конкретный платежный шлюз наследуется из более общего класса `PaymentGateway`:
```class PaymentProcessor {
  constructor(paymentGateway) {
    this.paymentGateway = paymentGateway;
  }

  processPayment(amount) {
    this.paymentGateway.processPayment(amount);
  }
}

class PaymentGateway {
  processPayment(amount) {
    throw new Error('processPayment method not implemented');
  }
}

class StripePaymentGateway extends PaymentGateway {
  constructor(apiKey) {
    super();
    this.type = 'stripe';
    this.apiKey = apiKey;
  }

  processPayment(amount) {
    // Process payment using Stripe API
    console.log(`Processing Stripe payment of $${amount} with API key ${this.apiKey}`);
  }
}

class PayPalPaymentGateway extends PaymentGateway {
  constructor(username, password) {
    super();
    this.type = 'paypal';
    this.username = username;
    this.password = password;
  }

  processPayment(amount) {
    // Process payment using PayPal API
    console.log(`Processing PayPal payment of $${amount} with username ${this.username} and password ${this.password}`);
  }
}

const stripeGateway = new StripePaymentGateway('my-stripe-api-key');
const paymentProcessor = new PaymentProcessor(stripeGateway);
paymentProcessor.processPayment(100);
```
Теперь мы получили два высокоуровневых класса — `PaymentProcessor` и `PaymentGateway`.

1. `PaymentGateway` определяет общую абстракцию, которую наследуют частные низкоуровневые классы `StripePaymentGateway` и `PayPalPaymentGateway`
2. `PaymentProcessor` тоже верхнуровневый класс, так как зависит от `PaymentGateway`, а не специфических реализаций платежных шлюзов
3. `StripePaymentGateway` и `PayPalPaymentGateway` реализуют интерфейс `PaymentGateway`, который определяет метод `processPayment`.

Теперь код стал более гибким и расширяемым, и соответствует принципу DIP. Мы легко можем добавлять сколько угодно специфических платежных шлюзов, не трогая верхнуровневые классы.

### Пример на React
```import React, { useState, useEffect } from 'react';

const UsersList = () => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    const getUsers = async () => {
      const response = await fetch('/api/users');
      const data = await response.json();
      setUsers(data);
    };
    getUsers();
  }, []);

  const addUser = async (newUser) => {
    const response = await fetch('/api/users', {
      method: 'POST',
      body: JSON.stringify(newUser),
      headers: {
        'Content-Type': 'application/json'
      }
    });
    const data = await response.json();
    setUsers([...users, data]);
  }

  return (
    <div>
      <h1>Users</h1>
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
      <form onSubmit={event => {
        event.preventDefault();
        const newUser = {name: event.target.name.value};
        addUser(newUser);
        event.target.reset();
      }}>
        <label htmlFor="name">Add User:</label>
        <input type="text" id="name" />
        <button type="submit">Submit</button>
      </form>
    </div>
  );
};

export default UsersList;
```
Чтобы следовать DIP, нужно сделать компонент `UserList` зависимым от абстракций вместо конкретных имплементаций. Можем вынести отдельно логику для получения/отправки данных на сервер, и подгружать в компонент эту абстракцию.

Интерфейсы:
```
interface UserRepository {
  fetchUsers: () => Promise<User[]>;
  addUser: (newUser: User) => Promise<User>;
}

interface User {
  id: number;
  name: string;
}
```
ApiUserRepository с логикой обмена данными с сервером:
```
class ApiUserRepository implements UserRepository {
  async fetchUsers() {
    const response = await fetch('/api/users');
    const data = await response.json();
    return data;
  }

  async addUser(newUser) {
    const response = await fetch('/api/users', {
      method: 'POST',
      body: JSON.stringify(newUser),
      headers: {
        'Content-Type': 'application/json'
      }
    });
    const data = await response.json();
    return data;
  }
}

```
Импортируем эту логику в наш компонент:
```
import React, { useState, useEffect } from 'react';
import { ApiUserRepository } from './user-repository';

const userRepository = new ApiUserRepository();

const UsersList = () => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    const getUsers = async () => {
      const data = await userRepository.fetchUsers();
      setUsers(data);
    };
    getUsers();
  }, []);

  const addUser = async (newUser) => {
    const data = await userRepository.addUser(newUser);
    setUsers([...users, data]);
  }

  return (
    <div>
      <h1>Users</h1>
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
      <form onSubmit={event => {
        event.preventDefault();
        const newUser = {name: event.target.name.value};
        addUser(newUser);
        event.target.reset();
      }}>   
      </div>)
}
```
