ООП
===
Объектно-ориентированное программирование — это методология программирования, в которой все важные вещи представлены объектами, каждый из которых является экземпляром определенного класса, а классы образуют иерархию наследования.
## Объекты, классы
**Класс** — это своеобразный «чертёж», по которому
строятся **объекты** — экземпляры этого класса.
![Classes VS Objects](/assets/objects-classes-examples.png)

- По соглашению имена классов объявляются с первой заглавной буквы и прописываются в CamelCase. Ключевое слово class создаёт константу, исключая возможность её дальнейшего переопределения.
- Классы всегда должны содержать метод-конструктор, который служит для будущего инстанцирования самого класса. В JS конструктор – это простая функция, возвращающая объект. Единственная особенность здесь в том, что при вызове с ключевым словом new она присваивает свой прототип в виде прототипа возвращаемого объекта.
- Ключевое слово this указывает на сам класс и служит для определения его свойств внутри конструктора.
- Методы можно добавлять простым определением имени функции и её исполняемого кода.
## Принципы ООП
![All Principles OOP](/assets/all-principles-oop.png)
### Абстракция
**Абстракция** — это модель некоего объекта или явления
реального мира, откидывающая незначительные детали, не
играющие существенной роли в данном контексте.
### Инкапсуляция
**Инкапсуляция** — это способность объектов скрывать часть
своего состояния и поведения от других объектов,
предоставляя внешнему миру только определённый
интерфейс взаимодействия с собой.

Допустим, нам нужно, чтобы класс Alien имел свойство birthYear и использовал его для выполнения метода howOld, но мы не хотим, чтобы это свойство было доступно вне самого объекта.
```
class Alien extends Enemy {
    #birthYear // Сначала нужно объявить закрытое свойство, используя в начале его имени символ '#'

    constructor (name, phrase, power, speed, birthYear) {
        super(name, phrase, power, speed)
        this.species = "alien"
        this.#birthYear = birthYear // Затем внутри функции конструктора мы присваиваем его значение
    }
    fly = () => console.log("Zzzzzziiiiiinnnnnggggg!!")
    howOld = () => console.log(`I was born in ${this.#birthYear}`) // и используем его в соответствующем методе
}
    
// Привычным образом выполняем инстанцирование
const alien1 = new Alien("Ali", "I'm Ali the alien!", 10, 50, 10000)
```
После этого можно обращаться к методу howOld так:
```
alien1.howOld() // вывод: "I was born in 10000"
```
Если же мы попробуем обратиться к нему напрямую, то получим ошибку. При этом закрытое свойство во время вывода объекта в консоль отображаться не будет.
```
console.log(alien1.#birthYear) // Выбрасывает ошибку
```

![Incapsulation](/assets/incapsulation.png)
### Наследование 
**Наследование** – это возможность создавать классы на основе других классов. С помощью этого принципа можно определять родительский класс (с нужными свойствами и методами), а затем дочерний класс, который будет наследовать от родителя все свойства и методы.

```
class User {

	#password;

	constructor(email, password) {
		this.email = email;
		this.#password = password;
	}

	login(email, password) {
		if (email === this.email && password === this.#password) {
			console.log('Login Successfully');
		} else {
			console.log('Authentication Failed!!');
		}
	}

	resetPassword(newPassword) {
		this.#password = newPassword;
	}

	logout() {
		console.log('Logout Successfully');
	}
}

class Author extends User {

	#numOfPost;
	
	constructor(email, password) {
		super(email, password);
		this.#numOfPost = 0;
	}

	createPost(content) {
		// add content to your DB. :)
		this.#numOfPost++;
	}
	
	getNumOfPost() {
		return this.#numOfPost;
	}
	
}

class Admin extends User {

	constructor(email, password) {
		super(email, password);
	}
	
	removeUser(userId) {
		// remove this userId from your DB.
		console.log('User Removed successfully.');
	}
	
}

const user = new Author('user@gmail.com', 'password:)');
user.login('user@gmail.com', 'password:)');
user.createPost('Leo Tolstoy is the Russian author');
console.log(user.getNumOfPost()); // 2
const admin = new Admin('admin@gmail.com', 'password');
admin.login('admin@gmail.com', 'password');
admin.resetPassword('newpassword');
admin.login('admin@gmail.com', 'newpassword');
admin.removeUser(2);
```
 В приведенном выше примере классы `Author` и `Admin` наследуют свойство класса `User` с помощью ключевых слов `extends` и `super`.

Ключевое слово `extends` используется для установления отношений родитель-ребенок между двумя классами. В первом случае `Author` становится подклассом, а `User` – родительским классом.

Подкласс имеет доступ ко всем публичным и защищенным членам суперкласса. Кроме того, он может иметь свои собственные свойства и методы. Так мы можем достичь повторного использования через наследование.

Ключевое слово `super` – это специальное ключевое слово. Вызов `super` в конструкторе дочернего класса вызывает конструктор родительского. Так мы инициализируем свойства в классах `Author` и `Admin`.

### Полиморфизм
**Полиморфизм** — это способность программы выбирать
различные реализации, при вызове операций с одним и
тем же названием
![Polymorphism](/assets/polymorphism-example.png)

Полиморфизм уменьшает дублирование участков кода. Существуют два типа использования полиморфизма:

- Полиморфизм во время компиляции
- Полиморфизм во время выполнения
  
**Перегрузка функций** – это тип полиморфизма во время компиляции. Этот тип создает более одной функции с одинаковым именем и разными параметрами или типами. Но перегрузка функций не поддерживается в JavaScript, потому что при создании функций с одинаковыми именами JavaScript переопределит последнюю функцию.

**Переопределение методов** – это тип полиморфизма во время выполнения. Например, используется для переопределения методов родительского класса в дочернем классе.

```
class User {
  constructor(email, password) {
    this.email = email;
    this.password = password;
  }

  login(email, password) {
    if (email === this.email && password === this.password) {
      console.log('Login Successfully');
    } else {
      console.log('Authentication Failed!!');
    }
  }
}

class Author extends User {
  #numOfPost;

  constructor(email, password) {
    super(email, password);
    this.#numOfPost = 0;
  }

  createPost(content) {
    this.#numOfPost++;
  }

  getNumOfPost() {
    return this.#numOfPost;
  }
}

class Admin extends User {
  constructor(email, password) {
    super(email, password);
  }

  login(email, password) {
		const isAdminValid = true; 
		// делаем проверку на валидность администратора
		// например 2-ух факторная идентификация
    if (email === this.email && password === this.password && isAdminValid == true) {
      console.log('Admin Login Successfully');
    } else {
      console.log('Authentication Failed!!');
    }
  }

  removeUser(userId) {
    console.log('User Removed successfully.');
  }
}

const author= new Author('author@gmail.com', 'password:)');
author.login('author@gmail.com', 'password:)'); // Login Successfully

const admin= new Admin('admin@gmail.com', 'password');
admin.login('admin@gmail.com', 'password'); // Admin Login Successfully
```
В примере `Author` и `Admin` наследуются от класса `User`. Оба класса имеют метод `login`, который принадлежит `User`. Только для администратора необходимо поставить дополнительный уровень проверки. Это достигается с помощью переопределения метода `login` и добавления дополнительных проверок. Просто перезаписываем название. Таким образом, можно достичь полиморфизма в JavaScript.

