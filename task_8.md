---
title: "Практическая работаv №8: Объектно-ориентированное программирование в Dart"
discipline: "Разработка мобильных приложений"
tags:
  [
    dart,
    oop,
    flutter,
    constructors,
    inheritance,
    mixins,
    abstract-classes,
    sealed-classes,
  ]
---

# Практическая работа: Объектно-ориентированное программирование в Dart (Flutter Architecture)

> [!NOTE]
> **Цель работы:** Глубоко освоить объектно-ориентированную модель языка Dart. Изучить проектирование классов, генеративные, именованные, фабричные и константные конструкторы, списки инициализации, механизм неявных интерфейсов, одиночное наследование, концепцию миксинов (`mixin`), а также современные модификаторы классов (`sealed`, `base`, `interface`) для построения масштабируемой архитектуры приложений на Flutter.

---

## 📋 Содержание

1. [Архитектура ООП в Dart: всё есть объект](#1-архитектура-ооп-в-dart-всё-есть-объект)
2. [Тема 1. Классы, инкапсуляция, методы и геттеры/сеттеры](#тема-1-классы-инкапсуляция-методы-и-геттерысеттеры)
3. [Тема 2. Все виды конструкторов: Named, Initializer List, Factory, Const](#тема-2-все-виды-конструкторов-named-initializer-list-factory-const)
4. [Тема 3. Наследование (extends), полиморфизм и super](#тема-3-наследование-extends-полиморфизм-и-super)
5. [Тема 4. Абстрактные классы, неявные интерфейсы (implements) и Sealed-классы](#тема-4-абстрактные-классы-неявные-интерфейсы-implements-и-sealed-классы)
6. [Тема 5. Миксины (Mixins): композиция поведения через with и on](#тема-5-миксины-mixins-композиция-поведения-через-with-и-on)
7. [50 Смешанных заданий по ООП (Mobile Architecture Focus)](#50-смешанных-заданий-по-ооп)

---

## 1. Архитектура ООП в Dart: всё есть объект

В Dart любая сущность является объектом — экземпляром класса, наследующегося от корневого типа `Object?` (или `Object` для non-nullable значений). Даже числа, функции и `null` являются полноправными объектами.

```
                            ┌────────────────┐
                            │    Object?     │
                            └───────┬────────┘
                                    │
                                    ▼
                            ┌────────────────┐
                            │     Object     │
                            └───────┬────────┘
            ┌───────────────────────┼───────────────────────┐
            ▼                       ▼                       ▼
    ┌──────────────┐        ┌──────────────┐        ┌──────────────┐
    │ Widget (UI)  │        │  Repository  │        │ num, String  │
    └──────────────┘        └──────────────┘        └──────────────┘
```

### Ключевые особенности ООП в Dart

- **Отсутствие ключевых слов public/private/protected:** Видимость ограничивается библиотекой (файлом). Префикс подчеркивания `_` делает класс, поле или метод приватным для текущего файла.
- **Единое наследование классов:** Класс может наследоваться только от одного суперкласса (`extends`).
- **Множественные неявные интерфейсы:** Любой класс неявно определяет интерфейс, содержащий все его публичные поля и методы (`implements`).
- **Миксины (`with`):** Механизм повторного использования кода в нескольких несвязанных иерархиях без проблем множественного наследования (Diamond Problem).

---

## Тема 1. Классы, инкапсуляция, методы и геттеры/сеттеры

### Структура класса

Класс объединяет переменные состояния (поля) и функции, оперирующие этими данными (методы).

### Пример кода

```dart
class BankAccount {
  final String id;
  String owner;

  // Приватное поле (доступно только внутри данного .dart файла)
  double _balance = 0.0;

  BankAccount({required this.id, required this.owner});

  // Геттер для безопасного чтения баланса
  double get balance => _balance;

  // Сеттер с бизнес-валидацией
  set balance(double value) {
    if (value < 0) {
      throw ArgumentError("Баланс счета не может быть отрицательным");
    }
    _balance = value;
  }

  // Бизнес-метод с возвратом логического статуса
  bool withdraw(double amount) {
    if (amount > 0 && _balance >= amount) {
      _balance -= amount;
      return true;
    }
    return false;
  }

  void deposit(double amount) {
    if (amount > 0) {
      _balance += amount;
    }
  }

  // Переопределение метода строкового представления
  @override
  String toString() => "Счет #$id ($owner): ${_balance.toStringAsFixed(2)} ₽";
}

void main() {
  var account = BankAccount(id: "ACC-40817", owner: "Иван Иванов");
  account.deposit(5000);
  account.withdraw(1200);
  print(account); // Вызывает toString()
}
```

### Задания для закрепления

1. **Инкапсулированный термостат:** Создайте класс `Thermostat` с приватным полем `_temperatureCelsius`. Реализуйте геттер температуры в градусах Фаренгейта и сеттер с ограничением диапазона от -50 до +100 °C.

<img width="1920" height="1080" alt="2026-10-30_08-42-26" src="https://github.com/user-attachments/assets/6e3d184b-c5f3-4ac3-8fb0-aa929ddbdc81" />

```dart
class Thermostat {

  double _temperatureCelsius = 0.0;


  double get temperatureFahrenheit => (_temperatureCelsius * 9 / 5) + 32;


  set temperatureCelsius(double value) {
    if (value < -50 || value > 100) {
      throw ArgumentError('Температура должна быть в диапазоне от -50 до +100 °C. Получено: $value');
    }
    _temperatureCelsius = value;
  }
}

void main() {
  var thermostat = Thermostat();
  thermostat.temperatureCelsius = 25.0;
  print('Температура в Фаренгейтах: ${thermostat.temperatureFahrenheit}°F'); // 77.0°F
  
}
```

2. **Счетчик шагов с защитой от скручивания:** Разработайте класс `Pedometer`, где текущие шаги можно только увеличивать через метод `addSteps(int count)`, а уменьшать нельзя.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1bd99980-4428-45b8-b208-9ee6342c2311" />

```dart
class Pedometer {
  int _steps = 0;

 
  int get steps => _steps;

  
  void addSteps(int count) {
    if (count <= 0) {
      
      throw ArgumentError('Количество добавляемых шагов должно быть больше нуля.');
    }
    _steps += count;
  }
}

void main() {
  var pedometer = Pedometer();
  pedometer.addSteps(100);
  pedometer.addSteps(50);
  print('Пройдено шагов: ${pedometer.steps}'); // 150
  
  
}
```

3. **Модель книги с расчетом времени чтения:** Класс `Book` содержит поля названия и числа страниц. Добавьте вычисляемый геттер `estimatedReadingHours`, считающий время из расчета 1 страница в 2 минуты.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5722e02d-f1e3-44b6-94f1-ffddd97d21f8" />

```dart
class Book {
  final String title;
  final int pages;

  Book(this.title, this.pages);


  double get estimatedReadingHours => (pages * 2) / 60;
}

void main() {
  var book = Book('Dart для профи', 300);
  print('Книга: "${book.title}"');

  print('Примерное время чтения: ${book.estimatedReadingHours} часов'); 
}
```

4. **Валидатор пароля в сеттере:** Класс `UserCredentials` с приватным паролем `_passwordHash`. Сеттер принимает сырую строку, проверяет ее длину (\(\ge 8\) символов) и сохраняет псевдо-хэш.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3f004263-1f5c-48ca-8fcb-9608c5804cf1" />

```dart
import 'dart:convert';

class UserCredentials {
  String? _passwordHash;

  String? get passwordHash => _passwordHash;

  
  set password(String rawPassword) {
    if (rawPassword.length < 8) {
      throw ArgumentError('Пароль должен содержать минимум 8 символов.');
    }
    
   
    _passwordHash = base64Encode(utf8.encode(rawPassword));
  }
}

void main() {
  var user = UserCredentials();
  user.password = 'supersecret123';
  print('Сохраненный хэш: ${user.passwordHash}');
  
  
}
```

5. **Статический счетчик экземпляров:** Добавьте в класс `DeviceSession` статическое поле `static int activeSessionsCount`, которое увеличивается при создании каждого нового объекта.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0def82b8-a749-4eda-9810-543b23cd2482" />

```dart
class DeviceSession {
  
  static int activeSessionsCount = 0;

  
  DeviceSession() {
  
    activeSessionsCount++;
  }
}

void main() {
  print('Начальное количество сессий: ${DeviceSession.activeSessionsCount}'); 
  
  var session1 = DeviceSession();
  var session2 = DeviceSession();
  var session3 = DeviceSession();
  
  print('Количество сессий после создания трех объектов: ${DeviceSession.activeSessionsCount}');
}
```

---

## Тема 2. Все виды конструкторов: Named, Initializer List, Factory, Const

Dart предоставляет одну из самых выразительных систем конструкторов среди современных языков программирования.

### Сравнительная таблица конструкторов

| Тип конструктора         | Синтаксис                              | Назначение                                   | Flutter-контекст                          |
| :----------------------- | :------------------------------------- | :------------------------------------------- | :---------------------------------------- |
| **Генеративный**         | `User(this.name, this.age);`           | Быстрое связывание параметров с полями       | Инициализация State-объектов              |
| **Именованный**          | `User.anonymous() : ...;`              | Несколько способов создания объекта          | `EdgeInsets.all()`, `Color.fromARGB()`    |
| **Список инициализации** | `User(...) : _id = uuid(), super(...)` | Вычисление полей до тела конструктора        | Валидация `assert`, инициализация `final` |
| **Константный**          | `const User(this.name);`               | Создание объектов во время компиляции        | Неизменяемые виджеты (`const Text()`)     |
| **Фабричный**            | `factory User.fromJson(...)`           | Возврат кэшированного экземпляра или подтипа | Парсинг REST API, синглтоны               |

### Пример кода

```dart
class UserModel {
  final int id;
  final String name;
  final String role;
  final DateTime createdAt;

  // 1. Основной генеративный конструктор с именованными аргументами
  const UserModel({
    required this.id,
    required this.name,
    this.role = "User",
    required this.createdAt,
  });

  // 2. Именованный конструктор: Гостевой пользователь
  UserModel.guest()
      : id = 0,
        name = "Гость",
        role = "Guest",
        createdAt = DateTime.now(); // Список инициализации

  // 3. Фабричный конструктор: Парсинг JSON ответа сервера
  factory UserModel.fromJson(Map<String, dynamic> json) {
    if (!json.containsKey("id") || !json.containsKey("name")) {
      throw FormatException("Некорректный JSON пользователя");
    }
    return UserModel(
      id: json["id"] as int,
      name: json["name"] as String,
      role: json["role"] as String? ?? "User",
      createdAt: DateTime.parse(json["createdAt"] as String),
    );
  }

  // 4. Фабричный конструктор с кэшированием (Паттерн Singleton/Multiton)
  static final Map<int, UserModel> _cache = {};
  factory UserModel.cached(int id, String name) {
    return _cache.putIfAbsent(
      id,
      () => UserModel(id: id, name: name, createdAt: DateTime.now()),
    );
  }
}
```

### Задания для закрепления

1. **Именованные конструкторы цвета:** Создайте класс `AppColor` с конструкторами `AppColor.fromHex(String hex)` и `AppColor.fromRGBO(int r, int g, int b, double o)`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1b24f2b5-8808-4f5d-847d-14e11400e3d5" />

```dart
class AppColor {
  String? hex;
  int? r, g, b;
  double? o;

  AppColor.fromHex(this.hex);
  AppColor.fromRGBO(this.r, this.g, this.b, this.o);
}
```

2. **Константная точка на плоскости:** Создайте класс `const Point2D(this.x, this.y)`. Создайте два идентичных константных объекта и убедитесь, что `identical(p1, p2) == true`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/45554526-b15c-4d64-a834-a8cf30409f51" />

```dart
class Point2D {
  final double x;
  final double y;

  const Point2D(this.x, this.y);
}

void main() {
  const p1 = Point2D(1.0, 2.0);
  const p2 = Point2D(1.0, 2.0);
  print(identical(p1, p2) == true); // true
}
```

3. **Список инициализации с assert:** Создайте класс `Rectangle` с полями ширины и высоты. В списке инициализации добавьте проверку `assert(width > 0 && height > 0)`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b7ec7d46-3ae6-48fc-bf01-c2fadd89cf7c" />

```dart
class Rectangle {
  final double width;
  final double height;

  Rectangle(this.width, this.height)
      : assert(width > 0 && height > 0);
}
```

4. **Фабричный синглтон сетевого клиента:** Реализуйте класс `ApiClient` с фабричным конструктором `factory ApiClient()`, гарантированно возвращающим один и тот же приватный экземпляр.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d0cfddb2-a6b3-4f16-afb0-3327c07364f8" />

```dart
class ApiClient {
  static final ApiClient _instance = ApiClient._internal();

  factory ApiClient() {
    return _instance;
  }

  ApiClient._internal();
}
```

5. **Редирект конструктора (Redirecting constructor):** Напишите класс `Circle(this.radius)` и именованный конструктор `Circle.unit() : this(1.0);`, перенаправляющий вызов на основной.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4b2b764b-078f-4cc8-bebf-acb6811e5af0" />

```dart
class Circle {
  final double radius;

  Circle(this.radius);
  
  Circle.unit() : this(1.0);
}
```

---

## Тема 3. Наследование (extends), полиморфизм и super

Наследование позволяет создавать иерархии типов, переиспользуя логику базовых классов.

```
                     ┌────────────────────────┐
                     │   UiWidget (Базовый)   │
                     ├────────────────────────┤
                     │ + id: String           │
                     │ + render(): void       │
                     └───────────┬────────────┘
                                 │ extends
         ┌───────────────────────┴───────────────────────┐
         ▼                                               ▼
┌──────────────────┐                           ┌──────────────────┐
│   TextWidget     │                           │   ButtonWidget   │
├──────────────────┤                           ├──────────────────┤
│ + text: String   │                           │ + onClick: Void  │
│ + render(): void │                           │ + render(): void │
└──────────────────┘                           └──────────────────┘
```

### Пример кода

```dart
// Базовый класс компонента интерфейса
abstract class BaseScreen {
  final String routeName;
  final bool requiresAuth;

  BaseScreen({required this.routeName, this.requiresAuth = false});

  void onInit() {
    print("[$routeName] Инициализация базовых ресурсов экрана");
  }

  void render();
}

// Дочерний класс экрана настроек
class SettingsScreen extends BaseScreen {
  final bool isDarkModeEnabled;

  SettingsScreen({required this.isDarkModeEnabled})
      : super(routeName: "/settings", requiresAuth: true); // Вызов конструктора предка

  @override
  void onInit() {
    super.onInit(); // Вызов базовой логики
    print("[SettingsScreen] Загрузка локальных предпочтений темы");
  }

  @override
  void render() {
    print("Отрисовка экрана настроек (Темная тема: $isDarkModeEnabled)");
  }
}
```

### Задания для закрепления

1. **Иерархия банковских карт:** Создайте базовый класс `Card` и наследников `DebitCard` (без овердрафта) и `CreditCard` (с лимитом овердрафта).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3f0ea649-f797-4317-93ec-e390254193ab" />

```dart
class Card {
  double balance;

  Card(this.balance);

  void withdraw(double amount) {
    if (amount <= balance) {
      balance -= amount;
      print('Успешно снято $amount. Баланс: $balance');
    } else {
      print('Недостаточно средств.');
    }
  }
}

class DebitCard extends Card {
  DebitCard(double balance) : super(balance);
}

class CreditCard extends Card {
  double overdraftLimit;

  CreditCard(double balance, this.overdraftLimit) : super(balance);

  @override
  void withdraw(double amount) {
    if (amount <= balance + overdraftLimit) {
      balance -= amount;
      print('Успешно снято $amount. Баланс: $balance');
    } else {
      print('Превышен лимит овердрафта.');
    }
  }
}
```

2. **Полиморфный список виджетов:** Создайте список `List<BaseScreen>` и в цикле вызовите метод `render()` для каждого элемента независимо от конкретного подтипа.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/62ccdc74-4d6c-4289-b5ef-1e0a938143da" />

```dart
abstract class BaseScreen {
  void render();
}

class HomeScreen extends BaseScreen {
  @override
  void render() {
    print('Rendering HomeScreen');
  }
}

class ProfileScreen extends BaseScreen {
  @override
  void render() {
    print('Rendering ProfileScreen');
  }
}

void main() {
  List<BaseScreen> screens = [
    HomeScreen(),
    ProfileScreen(),
  ];

  for (var screen in screens) {
    screen.render();
  }
}
```

3. **Использование super-параметров (Dart 2.17+):** Перепишите конструктор наследника с использованием компактного синтаксиса `SubClass({required super.id, super.title});`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/80dc0494-e067-4686-a51d-e3633a712607" />

```dart
class Parent {
  final int id;
  final String? title;

  Parent({required this.id, this.title});
}

class SubClass extends Parent {
  SubClass({required super.id, super.title});
}

```

4. **Ковариантный параметр метода (`covariant`):** Покажите пример сужения типа аргумента в переопределенном методе подкласса с помощью ключевого слова `covariant`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ff9a3078-8353-475b-9e27-d6e56fe218f4" />

```dart
class Animal {
  void interact(Animal other) {
    print('Животное взаимодействует с животным');
  }
}

class Dog extends Animal {
  @override
  void interact(covariant Dog other) {
    print('Собака взаимодействует с собакой');
  }
}
```

5. **Предотвращение переопределения через `@nonVirtual`:** Создайте метод критической проверки лицензии в базовом классе и запретите его изменение в наследниках.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/16ed52a1-e367-4394-b1f3-5ca7af62bef2" />

```dart
import 'package:meta/meta.dart';

class BaseProduct {
  @nonVirtual
  void verifyLicense() {
    print('Лицензия проверена. Доступ разрешен.');
  }
}

class ProProduct extends BaseProduct {
  // Попытка переопределить метод verifyLicense() 
  // вызовет ошибку на этапе анализа кода.
}


```

---

## Тема 4. Абстрактные классы, неявные интерфейсы (implements) и Sealed-классы

### Разница между `extends` и `implements`

| Критерий         | `extends` (Наследование)   | `implements` (Реализация интерфейса)                |
| :--------------- | :------------------------- | :-------------------------------------------------- |
| **Количество**   | Строго один класс          | Множество классов через запятую                     |
| **Тела методов** | Наследуются по умолчанию   | **Не наследуются!** Обязательно переопределить всё  |
| **Поля**         | Наследуются поля и геттеры | Поля воспринимаются как контракты геттеров/сеттеров |

### Sealed-классы (Изолированные иерархии в Dart 3+)

Модификатор `sealed` гарантирует, что все возможные наследники класса объявлены **в том же самом файле**. Это позволяет компилятору проверять исчерпываемость выражений `switch` (Exhaustiveness checking) без необходимости ветки `default`.

### Пример кода (Архитектура состояния экрана / MVI / BLoC)

```dart
// Изолированное состояние экрана каталога товаров
sealed class CatalogState {}

class CatalogLoadingState extends CatalogState {}

class CatalogDataState extends CatalogState {
  final List<String> products;
  CatalogDataState(this.products);
}

class CatalogErrorState extends CatalogState {
  final String errorMessage;
  CatalogErrorState(this.errorMessage);
}

// Рендерер UI с исчерпывающим сопоставлением с шаблонами
String renderStateUi(CatalogState state) {
  // Компилятор гарантирует, что рассмотрены ВСЕ возможные состояния!
  return switch (state) {
    CatalogLoadingState() => "Показ спиннера загрузки...",
    CatalogDataState(:final products) => "Каталог: найдено ${products.length} товаров",
    CatalogErrorState(:final errorMessage) => "Ошибка: $errorMessage (Показать кнопку повтора)",
  };
}
```

### Задания для закрепления

1. **Контракт локального кэша:** Объявите интерфейс `StorageService` с методами `save(String key, String val)`, `read(String key)`, `delete(String key)`. Реализуйте класс `MemoryStorageService` через `implements`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c4bd8c64-abae-446a-8a31-f58e65833eee" />

```dart
abstract interface class StorageService {
  void save(String key, String val);
  String? read(String key);
  void delete(String key);
}

class MemoryStorageService implements StorageService {
  final Map<String, String> _storage = {};

  @override
  void save(String key, String val) {
    _storage[key] = val;
  }

  @override
  String? read(String key) {
    return _storage[key];
  }

  @override
  void delete(String key) {
    _storage.remove(key);
  }
}
```

2. **Множественная реализация интерфейсов:** Создайте класс `SmartTv`, реализующий сразу два несвязанных интерфейса: `Displayable` и `NetworkConnectable`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/02c5cec7-c0b8-4c80-88d0-3c969dbad3d1" />

```dart
abstract interface class Displayable {
  void display();
}

abstract interface class NetworkConnectable {
  void connect();
}

class SmartTv implements Displayable, NetworkConnectable {
  @override
  void display() {}

  @override
  void connect() {}
}
```

3. **Sealed-иерархия авторизации:** Создайте `sealed class AuthState` с наследниками `Unauthenticated`, `Authenticating`, `Authenticated(User user)` и `AuthFailed(String reason)`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/706747bf-0c4b-41c0-a4ed-59f2e1f848c2" />

```dart
class User {}

sealed class AuthState {}

class Unauthenticated extends AuthState {}

class Authenticating extends AuthState {}

class Authenticated extends AuthState {
  final User user;
  Authenticated(this.user);
}

class AuthFailed extends AuthState {
  final String reason;
  AuthFailed(this.reason);
}
```

4. **Модификатор `base class`:** Создайте класс `base class Engine`, гарантирующий, что сторонние библиотеки смогут только наследовать его (`extends`), но не смогут реализовать его как чистый интерфейс (`implements`).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/dd195c7f-1586-4577-ace6-d5663591033b" />

```dart
base class Engine {
  void start() {}
}
```

5. **Модификатор `interface class`:** Создайте чистый интерфейсный контракт, исключающий прямое наследование реализации вне родной библиотеки.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a5a83b7f-4b79-4109-89ad-9ef70e5f52f6" />

```dart
interface class DeviceContract {
  void initialize() {}
}
```

---

## Тема 5. Миксины (Mixins): композиция поведения через with и on

Миксин (`mixin`) — это блок кода, содержащий поля и методы, который можно подмешать в класс без использования классического наследования.

### Синтаксис и правила

- Объявляется ключевым словом `mixin Name { ... }`.
- Подмешивается к классу оператором `with Name`.
- Миксины не могут объявлять генеративные конструкторы (их нельзя создать через `new` или `Name()`).
- Ограничение применимости: `mixin Name on TargetClass` означает, что подмешать данный миксин можно **только** в класс, являющийся наследником `TargetClass`.

### Пример кода

```dart
// 1. Простой миксин логирования жизненного цикла
mixin LoggingMixin {
  void logAction(String action) {
    print("[LOG - ${DateTime.now().toIso8601String()}] Действие: $action");
  }
}

// 2. Миксин валидации с ограничением на класс формы
abstract class FormElement {
  String get tag;
}

mixin ValidationMixin on FormElement {
  bool validateNotNull(dynamic value) {
    if (value == null) {
      print("Элемент $tag не прошел проверку: поле обязательно!");
      return false;
    }
    return true;
  }
}

// 3. Класс поля ввода, объединяющий базовый класс и оба миксина
class EmailInput extends FormElement with LoggingMixin, ValidationMixin {
  @override
  final String tag = "EmailField";

  void onUserSubmitted(String? email) {
    logAction("Пользователь нажал Submit");
    if (validateNotNull(email)) {
      logAction("Email валиден: $email");
    }
  }
}
```

### Задания для закрепления

1. **Миксин сериализации в JSON:** Создайте миксин `JsonPrintable`, добавляющий любому классу метод `printAsJson()`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5bee927d-3804-4909-8d9e-3c4715e2775c" />

```dart
import 'dart:convert';

mixin JsonPrintable {
  void printAsJson() {
    print(jsonEncode(this));
  }
}
```

2. **Ограничение миксина на ChangeNotifier:** Создайте миксин `AutoDisposer on ChangeNotifier`, добавляющий флаг `bool isDisposed` и проверяющий его в методах.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/bed1e1a8-1b56-4d66-8f1b-6e0e8cb8f4a5" />

```dart
class ChangeNotifier {
  void notifyListeners() {}
  void dispose() {}
}

mixin AutoDisposer on ChangeNotifier {
  bool isDisposed = false;

  @override
  void notifyListeners() {
    if (!isDisposed) {
      super.notifyListeners();
    }
  }

  @override
  void dispose() {
    isDisposed = true;
    super.dispose();
  }
}
```

3. **Аналитический трекер кликов:** Напишите миксин `AnalyticsTracker` с методом `trackEvent(String eventName)` и примените его к классу кнопки `CustomButton`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1b4109ec-5d37-4c02-9c21-bbbe2c428b4f" />

```dart
mixin AnalyticsTracker {
  void trackEvent(String eventName) {
    print('Tracked event: $eventName');
  }
}

class CustomButton with AnalyticsTracker {
  void onClick() {
    trackEvent('button_clicked');
  }
}
```

4. **Порядок линеаризации миксинов:** Создайте класс с двумя миксинами, реализующими одинаковый метод `execute()`. Покажите на практике, метод какого миксина выполнится первым (принцип правого приоритета).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b39e49ab-1ac1-4b2f-b300-58dc702cd6ae" />

```dart
mixin MixinA {
  void execute() => print('MixinA');
}

mixin MixinB {
  void execute() => print('MixinB');
}

class MixedClass with MixinA, MixinB {}

void main() {
  MixedClass().execute();
}
```

5. **Миксин кэширования вычислений:** Создайте миксин `MemoizationMixin`, сохраняющий результаты тяжелых математических функций в словарь по строковому ключу аргументов.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/dfea7ef7-5f51-4356-ac8e-e77b88e244b5" />

```dart
mixin MemoizationMixin {
  final Map<String, dynamic> _cache = {};

  dynamic memoize(String key, dynamic Function() computation) {
    if (_cache.containsKey(key)) {
      return _cache[key];
    }
    final result = computation();
    _cache[key] = result;
    return result;
  }
}

class MathSolver with MemoizationMixin {
  int heavyCalculation(int n) {
    return memoize('heavy_$n', () {
      int sum = 0;
      for (int i = 0; i < n * 1000000; i++) {
        sum += i;
      }
      return sum;
    });
  }
}
```

---

## 50 Смешанных заданий по ООП

Задания моделируют архитектурные сценарии коммерческих Flutter-приложений (State Management, Networking, UI Layouts, Analytics, Security).

### Блок 1: Модели данных, DTO и конструкторы

1. **Неизменяемая карточка товара (Immutable Product):** Создайте константную модель товара с обязательными полями, расчетом цены со скидкой и валидацией неотрицательной стоимости в списке инициализации.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6384daec-22e0-44fc-8e83-0d53d235b506" />

```dart
// --- Задача 1: Неизменяемая карточка товара (Immutable Product) ---
class Product {
  final String id;
  final String name;
  final double price;
  final double discountPercent;

  const Product({
    required this.id,
    required this.name,
    required this.price,
    this.discountPercent = 0.0,
  }) : assert(price >= 0, 'Цена не может быть отрицательной');

  double get discountedPrice => price * (1 - discountPercent / 100);
}
```

2. **Парсер профиля пользователя с безопасными дефолтами:** Реализуйте конструктор `User.fromJson(Map<String, dynamic> json)` с обработкой отсутствующих полей и защитой от `TypeError`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/85001cd4-67b3-4f60-85d5-26b951442bb8" />

```dart
class User {
  final String name;
  final int age;

  User({required this.name, required this.age});

  factory User.fromJson(Map<String, dynamic> json) {
    return User(
      name: json['name'] is String ? json['name'] as String : 'Unknown',
      age: json['age'] is int ? json['age'] as int : 0,
    );
  }
}
```

3. **Multiton локалей приложения:** Спроектируйте класс `AppLocale` с фабричным конструктором, возвращающим единственный экземпляр для каждого языка: `AppLocale("ru")`, `AppLocale("en")`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9cda8fc7-929c-4b93-a48f-1bf47ade35ac" />

```dart
class AppLocale {
  final String languageCode;
  static final Map<String, AppLocale> _cache = {};

  factory AppLocale(String languageCode) {
    return _cache.putIfAbsent(
      languageCode,
      () => AppLocale._internal(languageCode),
    );
  }

  AppLocale._internal(this.languageCode);
}
```

4. **Модель геоточки с парсингом строки:** Класс `GeoPoint` с именованным конструктором `GeoPoint.fromString("55.751244, 37.618423")`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6dff07ee-a3cd-4b2b-8cf3-cecd731c7bc5" />

```dart
class GeoPoint {
  final double latitude;
  final double longitude;

  GeoPoint(this.latitude, this.longitude);

  factory GeoPoint.fromString(String pointString) {
    final parts = pointString.split(',');
    if (parts.length != 2) {
      throw FormatException('Неверный формат координат');
    }
    return GeoPoint(
      double.parse(parts[0].trim()),
      double.parse(parts[1].trim()),
    );
  }
}
```

5. **Конструктор визитки из сущности БД:** Напишите фабричный конструктор `ContactCard.fromDatabaseRecord(Map<String, Object?> row)`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2781d707-68cd-47fd-acb6-19510a75f839" />

```dart
class ContactCard {
  final int id;
  final String name;
  final String phone;

  ContactCard({required this.id, required this.name, required this.phone});

  factory ContactCard.fromDatabaseRecord(Map<String, Object?> row) {
    return ContactCard(
      id: row['id'] as int? ?? 0,
      name: row['name'] as String? ?? 'Без имени',
      phone: row['phone'] as String? ?? 'Нет телефона',
    );
  }
}
```

6. **Клонирование с изменением полей (copyWith):** Добавьте метод `UserModel copyWith({String? name, String? avatarUrl})` для неизменяемой модели.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e79531c1-0375-423b-bf8d-415342f9284c" />

```dart
class UserModel {
  final String name;
  final String? avatarUrl;

  const UserModel({required this.name, this.avatarUrl});

  UserModel copyWith({String? name, String? avatarUrl}) {
    return UserModel(
      name: name ?? this.name,
      avatarUrl: avatarUrl ?? this.avatarUrl,
    );
  }
}

```

7. **Конструктор диапазона дат:** Класс `DateRange(this.start, this.end)` со списком инициализации, выбрасывающим `AssertionError`, если дата окончания раньше даты начала.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/018eb260-97de-469f-800d-c7f9e9e38160" />

```dart
class DateRange {
  final DateTime start;
  final DateTime end;

  DateRange(this.start, this.end)
      : assert(!end.isBefore(start), 'Дата окончания не может быть раньше даты начала');
}
```

8. **DTO банковской транзакции:** Класс `TransactionDto` с именованными конструкторами для пополнения (`TransactionDto.deposit`), списания (`TransactionDto.withdrawal`) и перевода (`TransactionDto.transfer`).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/17ac56c0-85d1-4b44-a180-270a52bf2005" />

```dart
class TransactionDto {
  final String type;
  final double amount;
  final String? fromAccount;
  final String? toAccount;

  TransactionDto._({
    required this.type,
    required this.amount,
    this.fromAccount,
    this.toAccount,
  });

  factory TransactionDto.deposit(double amount, String account) {
    return TransactionDto._(type: 'DEPOSIT', amount: amount, toAccount: account);
  }

  factory TransactionDto.withdrawal(double amount, String account) {
    return TransactionDto._(type: 'WITHDRAWAL', amount: amount, fromAccount: account);
  }

  factory TransactionDto.transfer(double amount, String from, String to) {
    return TransactionDto._(
      type: 'TRANSFER',
      amount: amount,
      fromAccount: from,
      toAccount: to,
    );
  }
}

```

9. **Парсер конфигурации окружения (Environment):** Фабричный конструктор `EnvConfig.fromEnvironment()`, читающий флаги через `const String.fromEnvironment()`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f4d1464a-ec05-4ba7-9764-3d63f9aa80c7" />

```dart
class EnvConfig {
  final String apiUrl;
  final bool isProduction;

  const EnvConfig._({required this.apiUrl, required this.isProduction});

  factory EnvConfig.fromEnvironment() {
    const String url = String.fromEnvironment('API_URL', defaultValue: 'https://api.dev');
    const bool isProd = bool.fromEnvironment('IS_PROD', defaultValue: false);
    
    return const EnvConfig._(apiUrl: url, isProduction: isProd);
  }
}
```

10. **Кастомный оператор эквивалентности:** Переопределите оператор `==` и метод `hashCode` для класса `CartItem` на основе совпадения полей `productId` и `selectedSize`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0232113f-536f-497d-a462-8664361e1f24" />

```dart
class CartItem {
  final String productId;
  final String selectedSize;

  CartItem({required this.productId, required this.selectedSize});

  @override
  bool operator ==(Object other) =>
      identical(this, other) ||
      other is CartItem &&
          runtimeType == other.runtimeType &&
          productId == other.productId &&
          selectedSize == other.selectedSize;

  @override
  int get hashCode => Object.hash(productId, selectedSize);
}
```

### Блок 2: Наследование и иерархия UI-компонентов

11. **Иерархия системных уведомлений:** Базовый класс `InAppNotification` (таймаут, заголовок) и наследники `SuccessNotification`, `WarningNotification`, `CriticalAlertNotification` с разным цветом и иконкой.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6ffbe7bc-da54-41cf-8a30-9338a7044ddd" />

```dart
abstract class InAppNotification {
  final Duration timeout;
  final String title;

  InAppNotification(this.timeout, this.title);

  String get color;
  String get icon;
  void show();
}

class SuccessNotification extends InAppNotification {
  SuccessNotification(Duration timeout, String title) : super(timeout, title);

  @override
  String get color => 'Зеленый';
  @override
  String get icon => 'Галочка';
  @override
  void show() => print('[$color][$icon] $title (Таймаут: ${timeout.inSeconds}с)');
}

class WarningNotification extends InAppNotification {
  WarningNotification(Duration timeout, String title) : super(timeout, title);

  @override
  String get color => 'Оранжевый';
  @override
  String get icon => 'Внимание';
  @override
  void show() => print('[$color][$icon] $title (Таймаут: ${timeout.inSeconds}с)');
}

class CriticalAlertNotification extends InAppNotification {
  CriticalAlertNotification(Duration timeout, String title) : super(timeout, title);

  @override
  String get color => 'Красный';
  @override
  String get icon => 'Ошибка';
  @override
  void show() => print('[$color][$icon] $title (Таймаут: ${timeout.inSeconds}с)');
}

void main() {
  SuccessNotification(Duration(seconds: 3), 'Успешный вход в систему').show();
  WarningNotification(Duration(seconds: 5), 'Низкий заряд батареи').show();
  CriticalAlertNotification(Duration(seconds: 10), 'Потеряно соединение с сервером').show();
}

```

12. **Компоненты диалоговых окон:** Базовый класс `BaseDialog` с шаблоном отрисовки шапки и подвала, и наследник `ConfirmActionDialog` с кнопками «Подтвердить» и «Отмена».

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9b4b71f7-0d4a-4e48-baf2-7765f2a14d7a" />

```dart
class BaseDialog {
  void renderHeader() => print('--- Стандартная шапка ---');
  void renderFooter() => print('--- Стандартный подвал ---');
  
  void build() {
    renderHeader();
    print('Тело базового диалога');
    renderFooter();
  }
}

class ConfirmActionDialog extends BaseDialog {
  final String title;
  ConfirmActionDialog(this.title);

  @override
  void renderHeader() {
    print('=== $title ===');
  }

  @override
  void renderFooter() {
    print('[ Подтвердить ] [ Отмена ]');
  }
}

void main() {
  var dialog = ConfirmActionDialog('Удалить аккаунт?');
  dialog.build();
}
```

13. **Иерархия медиаплееров:** Базовый класс `MediaPlayer` (методы `play()`, `pause()`, `seek()`) и специализированные наследники `AudioPlayer` и `HlsVideoPlayer`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c314b5a7-b66c-48d2-b16f-d11667e1e45a" />

```dart
abstract class MediaPlayer {
  void play();
  void pause();
  void seek(Duration position);
}

class AudioPlayer extends MediaPlayer {
  @override
  void play() => print('Аудио: Воспроизведение начато');
  @override
  void pause() => print('Аудио: Пауза');
  @override
  void seek(Duration position) => print('Аудио: Перемотка на $position');
}

class HlsVideoPlayer extends MediaPlayer {
  @override
  void play() => print('HLS Видео: Воспроизведение начато');
  @override
  void pause() => print('HLS Видео: Пауза');
  @override
  void seek(Duration position) => print('HLS Видео: Перемотка на $position');
}

void main() {
  var audio = AudioPlayer();
  audio.play();
  audio.seek(Duration(minutes: 2, seconds: 30));
  
  print('---');
  
  var video = HlsVideoPlayer();
  video.play();
  video.pause();
}
```

14. **Модели сенсоров смартфона:** Базовый класс `HardwareSensor` и подклассы `AccelerometerSensor` (оси X, Y, Z) и `BarometerSensor` (давление в гПа).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/533df341-ebcf-4942-a321-df888baaf716" />

```dart
abstract class HardwareSensor {
  String get name;
  void readData();
}

class AccelerometerSensor extends HardwareSensor {
  double x = 0.0;
  double y = 0.0;
  double z = 0.0;

  @override
  String get name => 'Акселерометр';

  @override
  void readData() {
    print('$name: X=$x, Y=$y, Z=$z');
  }
}

class BarometerSensor extends HardwareSensor {
  double pressureHpa = 1013.25;

  @override
  String get name => 'Барометр';

  @override
  void readData() {
    print('$name: Давление = $pressureHpa гПа');
  }
}

void main() {
  var accel = AccelerometerSensor();
  accel.x = 1.2;
  accel.y = -0.5;
  accel.z = 9.8;
  accel.readData();

  var baro = BarometerSensor();
  baro.pressureHpa = 1005.5;
  baro.readData();
}
```

15. **Система тарифов подписки:** Базовый класс `SubscriptionPlan` и наследники `MonthlyPlan`, `AnnualDiscountPlan` (скидка 20%) и `FamilyPlan` (множитель цены за участников).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6cab4cf8-796f-43c5-aadb-eca9d2b2a668" />

```dart
abstract class SubscriptionPlan {
  final double basePrice;
  SubscriptionPlan(this.basePrice);
  
  double calculatePrice();
}

class MonthlyPlan extends SubscriptionPlan {
  MonthlyPlan(double basePrice) : super(basePrice);

  @override
  double calculatePrice() => basePrice;
}

class AnnualDiscountPlan extends SubscriptionPlan {
  AnnualDiscountPlan(double basePrice) : super(basePrice);

  @override
  double calculatePrice() => basePrice * 12 * 0.8; // Скидка 20%
}

class FamilyPlan extends SubscriptionPlan {
  final int memberCount;
  final double memberMultiplier;

  FamilyPlan(double basePrice, this.memberCount, {this.memberMultiplier = 0.5}) : super(basePrice);

  @override
  double calculatePrice() {
    if (memberCount <= 1) return basePrice;
    return basePrice + (basePrice * memberMultiplier * (memberCount - 1));
  }
}

void main() {
  print('Месячная подписка: \$${MonthlyPlan(10.0).calculatePrice().toStringAsFixed(2)}');
  print('Годовая со скидкой 20%: \$${AnnualDiscountPlan(10.0).calculatePrice().toStringAsFixed(2)}');
  print('Семейная (3 человека): \$${FamilyPlan(10.0, 3).calculatePrice().toStringAsFixed(2)}');
}

```

16. **Слои графического редактора:** Базовый класс `CanvasLayer` (прозрачность, видимость, z-index) и подклассы `VectorLayer`, `RasterBitmapLayer`, `TextLayer`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/831643ab-1c55-42f1-8f85-4e8c4ad8dfd7" />

```dart
class CanvasLayer {
  double opacity;
  bool isVisible;
  int zIndex;

  CanvasLayer({this.opacity = 1.0, this.isVisible = true, this.zIndex = 0});

  void render() => print('Отрисовка базового слоя (Прозрачность: $opacity, Видимость: $isVisible, Z: $zIndex)');
}

class VectorLayer extends CanvasLayer {
  @override
  void render() => print('Отрисовка векторного слоя');
}

class RasterBitmapLayer extends CanvasLayer {
  @override
  void render() => print('Отрисовка растрового слоя');
}

class TextLayer extends CanvasLayer {
  @override
  void render() => print('Отрисовка текстового слоя');
}

void main() {
  var vector = VectorLayer()..zIndex = 1;
  var raster = RasterBitmapLayer()..zIndex = 2;
  var text = TextLayer()..zIndex = 3;
  
  vector.render();
  raster.render();
  text.render();
}
```

17. **Иерархия платежных шлюзов:** Базовый класс `PaymentGateway` с защищенным методом логирования и наследники `SbpGateway` (Система быстрых платежей) и `BankCardGateway`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cbef6700-a877-4c5f-a8fe-2042ddd52865" />

```dart
abstract class PaymentGateway {
  void _log(String message) {
    print('[LOG]: $message');
  }

  void pay(double amount) {
    _log('Инициализация платежа на сумму \$$amount');
  }
}

class SbpGateway extends PaymentGateway {
  @override
  void pay(double amount) {
    super.pay(amount);
    _log('Платеж через СБП успешно выполнен.');
  }
}

class BankCardGateway extends PaymentGateway {
  @override
  void pay(double amount) {
    super.pay(amount);
    _log('Платеж банковской картой успешно выполнен.');
  }
}

void main() {
  SbpGateway().pay(1500.0);
  print('---');
  BankCardGateway().pay(300.0);
}
```

18. **Экраны онбординга:** Базовый экран обучающего тура с кнопкой пропуска и конкретные шаги с уникальными иллюстрациями.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ef612ed8-d00a-4daf-928f-7d63843ec866" />

```dart
abstract class OnboardingScreen {
  void skip() => print('Онбординг пропущен.');
  void next() => print('Переход к следующему шагу...');
}

class ConcreteOnboardingStep extends OnboardingScreen {
  final String title;
  final String illustrationUrl;

  ConcreteOnboardingStep({required this.title, required this.illustrationUrl});

  @override
  void next() {
    print('Шаг: "$title", Иллюстрация: $illustrationUrl');
    super.next();
  }
}

void main() {
  var step1 = ConcreteOnboardingStep(
    title: 'Добро пожаловать!',
    illustrationUrl: 'assets/welcome.png',
  );
  step1.next();

  print('---');
  
  var step2 = ConcreteOnboardingStep(
    title: 'Настройте профиль',
    illustrationUrl: 'assets/profile.png',
  );
  step2.skip();
}
```

19. **Кастомная кнопка с состояниями:** Базовый класс `WidgetButton` и подкласс `AsyncLoadingButton`, отображающий индикатор загрузки при активном асинхронном действии.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9e079611-398e-4d7a-9332-65ad8e99367b" />

```dart
class WidgetButton {
  final String label;
  WidgetButton(this.label);

  Future<void> onPressed() async {
    print('Кнопка "$label" нажата.');
  }
}

class AsyncLoadingButton extends WidgetButton {
  bool isLoading = false;

  AsyncLoadingButton(String label) : super(label);

  @override
  Future<void> onPressed() async {
    if (isLoading) return;

    isLoading = true;
    print('Кнопка "$label": [Показан индикатор загрузки]');
    
    await Future.delayed(const Duration(milliseconds: 800)); 
    
    isLoading = false;
    print('Кнопка "$label": [Индикатор скрыт]');
    await super.onPressed();
  }
}

void main() async {
  var asyncBtn = AsyncLoadingButton('Отправить данные');
  print('Вызов кнопки...');
  await asyncBtn.onPressed();
}

```

20. **Иерархия логгеров приложения:** Базовый класс `AppLogger` и подклассы `ConsoleLogger` (форматированный вывод в терминал) и `FileCrashLogger` (запись на диск).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/63026b4d-0adf-4cfa-b7ec-3cb6e5f8248f" />

```dart
abstract class AppLogger {
  void log(String message);
}

class ConsoleLogger extends AppLogger {
  @override
  void log(String message) {
    print('[КОНСОЛЬ]: $message');
  }
}

class FileCrashLogger extends AppLogger {
  @override
  void log(String message) {
    print('[ЗАПИСЬ В ФАЙЛ crash_logs.txt]: $message');
  }
}

void main() {
  var consoleLogger = ConsoleLogger();
  var fileLogger = FileCrashLogger();

  consoleLogger.log('Приложение успешно запущено');
  fileLogger.log('Необработанное исключение: NullPointerException');
}

```

### Блок 3: Абстрактные контракты, интерфейсы и репозитории

21. **Репозиторий каталога товаров (Repository Pattern):** Объявите интерфейс `ProductRepository` с методами `Future<List<Product>> getAll()`, `Future<Product> getById(int id)` и создайте две реализации: `NetworkProductRepository` и `MockProductRepository`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/20234f63-0a30-4fc3-a761-ae3691860ff8" />

```dart
class Product {
  final int id;
  final String name;
  
  Product(this.id, this.name);
  
  @override
  String toString() => 'Product(id: $id, name: $name)';
}

abstract class ProductRepository {
  Future<List<Product>> getAll();
  Future<Product> getById(int id);
}

class NetworkProductRepository implements ProductRepository {
  @override
  Future<List<Product>> getAll() async {
    await Future.delayed(Duration(milliseconds: 500)); // Имитация сети
    return [Product(1, 'Сетевой Товар 1'), Product(2, 'Сетевой Товар 2')];
  }

  @override
  Future<Product> getById(int id) async {
    await Future.delayed(Duration(milliseconds: 200));
    return Product(id, 'Сетевой Товар $id');
  }
}

class MockProductRepository implements ProductRepository {
  final List<Product> _mockData = [
    Product(10, 'Тестовый Товар А'),
    Product(20, 'Тестовый Товар Б'),
  ];

  @override
  Future<List<Product>> getAll() async => _mockData;

  @override
  Future<Product> getById(int id) async {
    return _mockData.firstWhere(
      (p) => p.id == id, 
      orElse: () => Product(-1, 'Не найдено'),
    );
  }
}

void main() async {
  print('--- Network Repository ---');
  ProductRepository networkRepo = NetworkProductRepository();
  print(await networkRepo.getAll());
  print(await networkRepo.getById(5));

  print('\n--- Mock Repository ---');
  ProductRepository mockRepo = MockProductRepository();
  print(await mockRepo.getAll());
}

```

22. **Интерфейс биометрической аутентификации:** Спроектируйте интерфейс `BiometricAuthService` с методами `canAuthenticate()` и `authenticate()`. Напишите реализацию-заглушку для тестов.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a6a34b39-a478-41b6-9d17-223e4f135123" />

```dart
abstract class BiometricAuthService {
  bool canAuthenticate();
  Future<bool> authenticate();
}

class MockBiometricAuthService implements BiometricAuthService {
  @override
  bool canAuthenticate() {
    // Заглушка: всегда возвращаем true, что устройство поддерживает биометрию
    return true;
  }

  @override
  Future<bool> authenticate() async {
    // Имитация процесса сканирования отпечатка/лица
    await Future.delayed(Duration(seconds: 1));
    return true; // Успешная аутентификация
  }
}

void main() async {
  BiometricAuthService authService = MockBiometricAuthService();
  
  if (authService.canAuthenticate()) {
    print('Биометрия доступна. Запуск сканирования...');
    bool isSuccess = await authService.authenticate();
    print('Результат аутентификации: ${isSuccess ? "Успешно" : "Отклонено"}');
  } else {
    print('Биометрия недоступна на устройстве.');
  }
}
```

23. **Шифрование пользовательских данных:** Интерфейс `CryptoService` с методами `encrypt(String plainText)` и `decrypt(String cipherText)`. Реализуйте класс `AesCryptoService`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/67b73c56-f016-496c-a66b-fa610e962aba" />

```dart
import 'dart:convert';

abstract class CryptoService {
  String encrypt(String plainText);
  String decrypt(String cipherText);
}

class AesCryptoService implements CryptoService {
  // Для примера используем Base64 как обратимое "шифрование". 
  // В реальном проекте здесь используется пакет pointycastle или encrypt.
  @override
  String encrypt(String plainText) {
    return base64Encode(utf8.encode('AES_ENCRYPTED_$plainText'));
  }

  @override
  String decrypt(String cipherText) {
    try {
      String decoded = utf8.decode(base64Decode(cipherText));
      return decoded.replaceFirst('AES_ENCRYPTED_', '');
    } catch (e) {
      return 'Ошибка расшифровки';
    }
  }
}

void main() {
  CryptoService crypto = AesCryptoService();
  
  String originalText = 'Секретные данные пользователя';
  print('Оригинал: $originalText');

  String encrypted = crypto.encrypt(originalText);
  print('Зашифровано: $encrypted');

  String decrypted = crypto.decrypt(encrypted);
  print('Расшифровано: $decrypted');
}

```

24. **Контракт аналитического провайдера:** Интерфейс `AnalyticsProvider` с методом `logEvent(String name, Map<String, Object> params)`. Реализуйте множественную отправку через класс `CompositeAnalyticsProvider`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/07a3ac23-494f-4ea2-a18a-a1189fa90a6c" />

```dart
abstract class AnalyticsProvider {
  void logEvent(String name, Map<String, Object> params);
}

class ConsoleAnalyticsProvider implements AnalyticsProvider {
  final String name;
  ConsoleAnalyticsProvider(this.name);

  @override
  void logEvent(String name, Map<String, Object> params) {
    print('[$this.name] Отправлено событие: $name, Параметры: $params');
  }
}

class CompositeAnalyticsProvider implements AnalyticsProvider {
  final List<AnalyticsProvider> _providers;

  CompositeAnalyticsProvider(this._providers);

  @override
  void logEvent(String name, Map<String, Object> params) {
    // Множественная отправка: перебираем всех провайдеров и вызываем у них метод
    for (var provider in _providers) {
      provider.logEvent(name, params);
    }
  }
}

void main() {
  var firebaseProvider = ConsoleAnalyticsProvider('Firebase');
  var amplitudeProvider = ConsoleAnalyticsProvider('Amplitude');

  var composite = CompositeAnalyticsProvider([firebaseProvider, amplitudeProvider]);

  composite.logEvent('button_click', {
    'screen': 'home',
    'button_id': 'buy_now',
  });
}
```

25. **Интерфейс геолокатора:** Спроектируйте контракт получения координат устройства с поддержкой стрима обновлений позиции `Stream<GeoPoint> get positionStream`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/775757f4-918c-4398-b8e8-5d75656e676a" />

```dart
class GeoPoint {
  final double latitude;
  final double longitude;

  GeoPoint(this.latitude, this.longitude);

  @override
  String toString() => 'GeoPoint($latitude, $longitude)';
}

abstract class GeolocationService {
  Stream<GeoPoint> get positionStream;
}

class MockGeolocationService implements GeolocationService {
  @override
  Stream<GeoPoint> get positionStream async* {
    // Имитация потока обновлений координат
    for (int i = 1; i <= 3; i++) {
      await Future.delayed(Duration(seconds: 1));
      yield GeoPoint(55.751244 + i * 0.001, 37.618423 + i * 0.001);
    }
  }
}

void main() async {
  GeolocationService geoService = MockGeolocationService();
  
  print('Начинаем отслеживание координат...');
  
  // Слушаем поток
  await for (GeoPoint point in geoService.positionStream) {
    print('Новая позиция: $point');
  }
  
  print('Отслеживание завершено.');
}

```

26. **Служба локальных уведомлений:** Интерфейс `NotificationSender` с методами отправки мгновенных и отложенных системных пушей.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6edc5e3b-8017-4fe0-82a0-7ea82e87ff68" />

```dart
abstract class NotificationSender {
  void sendImmediate(String title, String body);
  void sendDelayed(String title, String body, Duration delay);
}

class MockNotificationSender implements NotificationSender {
  @override
  void sendImmediate(String title, String body) {
    print('МГНОВЕННО: [$title] $body');
  }

  @override
  void sendDelayed(String title, String body, Duration delay) {
    print('ЗАПЛАНИРОВАНО через ${delay.inSeconds} сек.: [$title] $body');
    // В реальном приложении здесь вызывался бы планировщик локальных пушей
  }
}

void main() {
  NotificationSender sender = MockNotificationSender();

  sender.sendImmediate('Вход выполнен', 'Добро пожаловать в приложение!');
  
  sender.sendDelayed(
    'Напоминание', 
    'Не забудьте проверить задачи.', 
    Duration(hours: 1),
  );
}
```

27. **Контракт синхронизатора баз данных:** Абстрактный класс `DataSyncEngine` с абстрактными методами `fetchRemoteDelta()` и `applyLocalChanges()`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e6cf6dbf-a8a9-4753-bbf1-fc84456ec3f2" />

```dart
abstract class DataSyncEngine {
  Future<void> fetchRemoteDelta();
  Future<void> applyLocalChanges();
}

class MySyncEngine implements DataSyncEngine {
  @override
  Future<void> fetchRemoteDelta() async {
    print('1. Загрузка изменений с сервера...');
    await Future.delayed(Duration(seconds: 1));
    print(' Изменения загружены.');
  }

  @override
  Future<void> applyLocalChanges() async {
    print('2. Применение локальных изменений к БД...');
    await Future.delayed(Duration(milliseconds: 500));
    print(' Изменения применены.');
  }
}

void main() async {
  DataSyncEngine syncEngine = MySyncEngine();
  
  print('Старт синхронизации баз данных...');
  await syncEngine.fetchRemoteDelta();
  await syncEngine.applyLocalChanges();
  print('Синхронизация завершена успешно.');
}
```

28. **Провайдер сетевого статуса:** Интерфейс `ConnectivityService` с геттером `bool isOnline` и стримом событий потери интернета.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f8316623-f4cf-440d-b4bf-2b633fbf96f7" />

```dart
abstract class ConnectivityService {
  bool get isOnline;
  Stream<bool> get onConnectivityChanged; // Поток потери/восстановления интернета
}

class MockConnectivityService implements ConnectivityService {
  @override
  bool get isOnline => true; // Текущий статус

  @override
  Stream<bool> get onConnectivityChanged async* {
    print('Мониторинг сети запущен...');
    await Future.delayed(Duration(seconds: 1));
    yield false; // Потеря интернета
    await Future.delayed(Duration(seconds: 2));
    yield true; // Восстановление
  }
}

void main() async {
  ConnectivityService connectivity = MockConnectivityService();
  
  print('Текущий статус сети (онлайн): ${connectivity.isOnline}');

  // Слушаем события изменения
  await for (bool status in connectivity.onConnectivityChanged) {
    if (status) {
      print('Событие: Интернет восстановлен!');
    } else {
      print('Событие: Интернет потерян!');
    }
  }
}
```

29. **Хранилище ключ-значение (KeyValueStorage):** Спроектируйте интерфейс с обобщенными методами `T? read<T>(String key)` и `Future<void> write<T>(String key, T value)`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/73265e0e-14ff-4ec9-8cec-3beb93951f26" />

```dart
abstract class KeyValueStorage {
  T? read<T>(String key);
  Future<void> write<T>(String key, T value);
}

class MemoryKeyValueStorage implements KeyValueStorage {
  final Map<String, dynamic> _storage = {};

  @override
  T? read<T>(String key) {
    if (_storage.containsKey(key)) {
      return _storage[key] as T?;
    }
    return null;
  }

  @override
  Future<void> write<T>(String key, T value) async {
    // Имитация асинхронной записи (например, на диск)
    await Future.delayed(Duration(milliseconds: 100));
    _storage[key] = value;
  }
}

void main() async {
  KeyValueStorage storage = MemoryKeyValueStorage();

  await storage.write<String>('username', 'Ivan');
  await storage.write<int>('age', 25);

  String? user = storage.read<String>('username');
  int? age = storage.read<int>('age');
  bool? isAdmin = storage.read<bool>('isAdmin');

  print('Имя пользователя: $user');
  print('Возраст: $age');
  print('Админ: $isAdmin (null, так как не записывали)');
}
```

30. **Интерфейс экспортера отчетов:** Контракт `ReportExporter` с реализациями `PdfReportExporter` и `ExcelReportExporter`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/22a3a818-7d8f-4f74-b5bf-62e77f2773ce" />

```dart
abstract class ReportExporter {
  void exportReport(String reportName);
}

class PdfReportExporter implements ReportExporter {
  @override
  void exportReport(String reportName) {
    print('Генерация документа PDF: $reportName.pdf');
    print('Форматирование шрифтов и страниц завершено.');
  }
}

class ExcelReportExporter implements ReportExporter {
  @override
  void exportReport(String reportName) {
    print('Генерация таблицы Excel: $reportName.xlsx');
    print('Создание сводных таблиц и графиков завершено.');
  }
}

void main() {
  ReportExporter pdfExporter = PdfReportExporter();
  ReportExporter excelExporter = ExcelReportExporter();

  pdfExporter.exportReport('Квартальный_отчет_2023');
  print('---');
  excelExporter.exportReport('Финансовые_показатели');
}
```

### Блок 4: Миксины и повторное использование функционала

31. **Миксин дебаунса событий (Anti-Spam Click):** Миксин `DebounceMixin`, предотвращающий повторный вызов одного и того же действия чаще чем раз в 500 мс.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/247e245b-edb0-47d9-9386-d0db18164b61" />

```dart
mixin DebounceMixin {
  final Map<String, DateTime> _lastExecutionTimes = {};

  bool executeDebounced(String actionKey, Duration debounceDuration, void Function() action) {
    final now = DateTime.now();
    final lastTime = _lastExecutionTimes[actionKey];

    if (lastTime == null || now.difference(lastTime) > debounceDuration) {
      _lastExecutionTimes[actionKey] = now;
      action();
      return true; // Действие выполнено
    }
    return false; // Заблокировано дебаунсом
  }
}

class ClickHandler with DebounceMixin {
  void onButtonClicked() {
    bool executed = executeDebounced('button_click', Duration(milliseconds: 500), () {
      print('Кнопка нажата и обработана: ${DateTime.now().millisecondsSinceEpoch}');
    });

    if (!executed) {
      print('Клик проигнорирован (защита от спама)');
    }
  }
}

void main() async {
  var handler = ClickHandler();

  print('Быстрые клики:');
  handler.onButtonClicked(); // Выполнится
  handler.onButtonClicked(); // Заблокируется
  handler.onButtonClicked(); // Заблокируется

  print('\nЖдем 600 мс...');
  await Future.delayed(Duration(milliseconds: 600));
  
  handler.onButtonClicked(); // Снова выполнится
}

```

32. **Миксин восстановления состояния экрана (RestorationMixin):** Миксин, автоматически сохраняющий текстовые поля ввода при сворачивании приложения.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b8672fdc-dad0-44f9-ba2a-20abd51a0ac0" />

```dart
mixin RestorationMixin {
  final Map<String, String> _savedState = {};
  final Map<String, dynamic> textFields = {'username': '', 'email': ''};

  // Вызывается при сворачивании приложения
  void onAppPaused() {
    _savedState.clear();
    textFields.forEach((key, value) {
      if (value is String && value.isNotEmpty) {
        _savedState[key] = value;
      }
    });
    print('[System] Состояние текстовых полей сохранено: $_savedState');
  }

  // Вызывается при восстановлении приложения
  void onAppResumed() {
    if (_savedState.isNotEmpty) {
      _savedState.forEach((key, value) {
        textFields[key] = value;
      });
      print('[System] Состояние восстановлено из кэша.');
    }
  }
}

class LoginScreen with RestorationMixin {
  void userTypes() {
    textFields['username'] = 'admin_user';
    textFields['email'] = 'admin@example.com';
    print('Пользователь ввел текст: $textFields');
  }
}

void main() {
  var screen = LoginScreen();
  
  screen.userTypes();
  
  print('\n--- Сворачиваем приложение ---');
  screen.onAppPaused();
  
  // Имитация очистки памяти/пересоздания экрана
  screen.textFields['username'] = ''; 
  screen.textFields['email'] = '';
  print('Поля после очистки: ${screen.textFields}');

  print('\n--- Разворачиваем приложение ---');
  screen.onAppResumed();
  print('Поля после восстановления: ${screen.textFields}');
}
```

33. **Миксин проверки сетевого соединения:** Миксин `NetworkAwareMixin`, внедряющий в презентер экрана проверку доступности интернета перед выполнением любого запроса.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0e62b5fe-f8dc-4ec2-8ccc-3c6ce6464dcc" />

```dart
mixin NetworkAwareMixin {
  // Имитация проверки наличия сети
  bool get isNetworkAvailable => true; 

  Future<void> performNetworkRequest(String endpoint) async {
    print('Проверка сети перед запросом к $endpoint...');
    
    if (!isNetworkAvailable) {
      print('ОШИБКА: Нет подключения к интернету. Запрос отменен.');
      return;
    }

    print('Сеть в порядке. Выполняется запрос...');
    await Future.delayed(Duration(seconds: 1)); // Имитация запроса
    print('Данные успешно получены с $endpoint');
  }
}

class UserPresenter with NetworkAwareMixin {
  Future<void> loadUserData() async {
    await performNetworkRequest('https://api.example.com/user');
  }
}

void main() async {
  var presenter = UserPresenter();
  await presenter.loadUserData();
}
```

34. **Миксин форматирования цен и валют:** Миксин `CurrencyFormatterMixin`, предоставляющий готовый метод преобразования чисел в красивый денежный формат.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d35562d2-027d-4f34-a6fa-f42377aada03" />

```dart
mixin CurrencyFormatterMixin {
  String formatCurrency(double amount, {String symbol = '\$', String separator = ' '}) {
    // Округляем до 2 знаков после запятой
    String formatted = amount.toStringAsFixed(2);
    
    // Разделяем целую и дробную части
    List<String> parts = formatted.split('.');
    String integerPart = parts[0];
    String fractionalPart = parts[1];
    
    // Форматируем целую часть (добавляем разделители тысяч)
    String result = '';
    int count = 0;
    for (int i = integerPart.length - 1; i >= 0; i--) {
      result = integerPart[i] + result;
      count++;
      if (count % 3 == 0 && i != 0) {
        result = separator + result;
      }
    }
    
    return '$symbol$result.$fractionalPart';
  }
}

class ProductViewModel with CurrencyFormatterMixin {
  void displayPrice() {
    double price = 1234567.891;
    print('Цена товара: ${formatCurrency(price, symbol: '₽', separator: ' ')}');
    
    double smallPrice = 500.5;
    print('Цена со скидкой: ${formatCurrency(smallPrice, symbol: '\$', separator: ',')}');
  }
}

void main() {
  var viewModel = ProductViewModel();
  viewModel.displayPrice();
}

```

35. **Миксин таймера автообновления:** Миксин `PeriodicRefreshMixin`, запускающий периодический опрос сервера каждые 30 секунд при активном экране.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2c79da01-cbc1-4870-8e6c-2179c88291e3" />

```dart
import 'dart:async';

mixin PeriodicRefreshMixin {
  Timer? _refreshTimer;
  int _requestCount = 0;

  void onScreenActive() {
    print('Экран активен. Запуск автообновления каждые 30 секунд.');
    _refreshTimer?.cancel();
    // Для демонстрации используем 2 секунды вместо 30
    _refreshTimer = Timer.periodic(Duration(seconds: 2), (timer) { 
      _pollServer();
    });
  }

  void onScreenInactive() {
    print('Экран неактивен. Остановка автообновления.');
    _refreshTimer?.cancel();
    _refreshTimer = null;
  }

  void _pollServer() {
    _requestCount++;
    print('Опрос сервера #$_requestCount (раз в 30 сек)...');
  }
}

class DashboardScreen with PeriodicRefreshMixin {}

void main() async {
  var screen = DashboardScreen();
  
  screen.onScreenActive();
  
  // Ждем 5 секунд, чтобы увидеть несколько срабатываний
  await Future.delayed(Duration(seconds: 5));
  
  screen.onScreenInactive();
}
```

36. **Миксин валидации паспортных данных:** Миксин `PassportValidationMixin` с методами строгой проверки серии, номера и кода подразделения.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b87005d6-7dd5-4486-8a83-499f532f9d5e" />

```dart
mixin PassportValidationMixin {
  bool validateSeries(String series) {
    // Серия паспорта: ровно 4 цифры
    return RegExp(r'^\d{4}$').hasMatch(series);
  }

  bool validateNumber(String number) {
    // Номер паспорта: ровно 6 цифр
    return RegExp(r'^\d{6}$').hasMatch(number);
  }

  bool validateDepartmentCode(String code) {
    // Код подразделения: формат XXX-XXX (цифры)
    return RegExp(r'^\d{3}-\d{3}$').hasMatch(code);
  }
}

class PassportForm with PassportValidationMixin {
  void submitForm(String series, String number, String code) {
    bool isSeriesValid = validateSeries(series);
    bool isNumberValid = validateNumber(number);
    bool isCodeValid = validateDepartmentCode(code);

    print('Серия ($series): ${isSeriesValid ? "Валидна" : "ОШИБКА"}');
    print('Номер ($number): ${isNumberValid ? "Валиден" : "ОШИБКА"}');
    print('Код ($code): ${isCodeValid ? "Валиден" : "ОШИБКА"}');
    
    if (isSeriesValid && isNumberValid && isCodeValid) {
      print('Форма успешно отправлена!');
    } else {
      print('Пожалуйста, исправьте ошибки в форме.');
    }
  }
}

void main() {
  var form = PassportForm();
  
  print('--- Тест 1: Валидные данные ---');
  form.submitForm('4510', '123456', '770-001');
  
  print('\n--- Тест 2: Невалидные данные ---');
  form.submitForm('45A0', '123', '77-0001');
}
```

37. **Миксин анимации затухания (FadeAnimationController):** Подмешиваемый контроллер управления прозрачностью элемента от 0.0 до 1.0.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/239a9ef1-84da-4e03-b044-62e3c89b937a" />

```dart
mixin FadeAnimationController {
  double _opacity = 1.0;
  
  double get opacity => _opacity;

  void fadeOut() {
    _opacity = 0.0;
    print('Анимация: Элемент скрыт (opacity: $_opacity)');
  }

  void fadeIn() {
    _opacity = 1.0;
    print('Анимация: Элемент показан (opacity: $_opacity)');
  }

  void setCustomOpacity(double value) {
    if (value >= 0.0 && value <= 1.0) {
      _opacity = value;
      print('Анимация: Установлена прозрачность (opacity: $_opacity)');
    } else {
      print('Ошибка: Прозрачность должна быть от 0.0 до 1.0');
    }
  }
}

class FadeWidget with FadeAnimationController {
  void render() {
    print('Отрисовка виджета с прозрачностью: $opacity');
  }
}

void main() {
  var widget = FadeWidget();
  
  widget.render();
  widget.fadeOut();
  widget.render();
  widget.setCustomOpacity(0.5);
  widget.render();
  widget.fadeIn();
}
```

38. **Миксин переключения темы:** Миксин, предоставляющий экрану быстрый доступ к текущей цветовой палитре (темная/светлая).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8a941668-7831-4600-b290-fbcaeec58abe" />

```dart
class AppColors {
  final String background;
  final String text;
  
  const AppColors(this.background, this.text);
}

mixin ThemeSwitcherMixin {
  bool _isDarkMode = false;

  static const AppColors _lightPalette = AppColors('#FFFFFF', '#000000');
  static const AppColors _darkPalette = AppColors('#121212', '#FFFFFF');

  AppColors get currentPalette => _isDarkMode ? _darkPalette : _lightPalette;
  
  bool get isDarkMode => _isDarkMode;

  void toggleTheme() {
    _isDarkMode = !_isDarkMode;
    print('Тема переключена. Текущий режим: ${_isDarkMode ? "Тёмный" : "Светлый"}');
  }
}

class SettingsScreen with ThemeSwitcherMixin {
  void showSettings() {
    print('Фон: ${currentPalette.background}, Текст: ${currentPalette.text}');
  }
}

void main() {
  var screen = SettingsScreen();
  
  print('--- Начальное состояние ---');
  screen.showSettings();

  print('\n--- Включаем темную тему ---');
  screen.toggleTheme();
  screen.showSettings();
}

```

39. **Миксин глубоких ссылок (DeepLinkHandlerMixin):** Миксин, парсящий входящие URI-схемы приложения и открывающий целевые экраны.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/90ec3162-2cac-48bb-96ca-35bc7ff8c960" />

```dart
mixin DeepLinkHandlerMixin {
  void handleDeepLink(String url) {
    print('Получена ссылка: $url');
    
    try {
      final uri = Uri.parse(url);
      
      if (uri.scheme != 'myapp') {
        print('Игнорирование: Неверная схема (ожидалась myapp)');
        return;
      }

      if (uri.host == 'profile') {
        print('Навигация: Открываем экран Профиля (ID: ${uri.queryParameters['id'] ?? "неизвестен"})');
      } else if (uri.host == 'product') {
        print('Навигация: Открываем экран Товара (ID: ${uri.pathSegments.isNotEmpty ? uri.pathSegments[0] : "неизвестен"})');
      } else {
        print('Навигация: Открываем главный экран (Маршрут не распознан)');
      }
    } catch (e) {
      print('Ошибка парсинга ссылки: $e');
    }
  }
}

class MainAppRouter with DeepLinkHandlerMixin {}

void main() {
  var router = MainAppRouter();
  
  router.handleDeepLink('myapp://profile?id=42');
  print('---');
  router.handleDeepLink('myapp://product/105');
  print('---');
  router.handleDeepLink('https://google.com');
}
```

40. **Миксин аудита действий администратора:** Подмешиваемый модуль, отправляющий критические действия модератора на выделенный сервер безопасности.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1cfe3016-cdb0-4787-ab9a-6c107ef8932e" />

```dart
mixin AdminAuditMixin {
  void sendToSecurityServer(String action, String adminId) {
    // Имитация отправки на сервер безопасности
    print('[SECURITY AUDIT] Отправка лога на сервер...');
    print('Администратор: $adminId');
    print('Действие: $action');
    print('Время: ${DateTime.now().toUtc().toIso8601String()}');
    print('Статус: Доставлено.');
  }
}

class ModeratorPanel with AdminAuditMixin {
  void banUser(String adminId, String targetUserId) {
    // Логика бана...
    print('Пользователь $targetUserId забанен.');
    // Отправка критического лога
    sendToSecurityServer('BAN_USER: $targetUserId', adminId);
  }

  void deletePost(String adminId, String postId) {
    // Логика удаления...
    print('Пост $postId удален.');
    // Отправка критического лога
    sendToSecurityServer('DELETE_POST: $postId', adminId);
  }
}

void main() {
  var panel = ModeratorPanel();
  
  print('--- Действие 1 ---');
  panel.banUser('admin_root', 'user_999');
  
  print('\n--- Действие 2 ---');
  panel.deletePost('admin_root', 'post_1234');
}
```

### Блок 5: Современные модификаторы классов и архитектурные стейты

41. **Sealed-состояние корзины интернет-магазина:** Создайте `sealed class CartState` с состояниями: `CartEmpty`, `CartLoading`, `CartLoaded(List<Item> items, double total)`, `CartCheckoutError(String error)`. Напишите функцию обработки состояний через исчерпывающий `switch`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/395a4e2c-2dbf-493a-9f67-e998fbac678b" />

```dart
class Item {
  final String name;
  Item(this.name);
}

sealed class CartState {}

class CartEmpty extends CartState {}
class CartLoading extends CartState {}

class CartLoaded extends CartState {
  final List<Item> items;
  final double total;
  CartLoaded(this.items, this.total);
}

class CartCheckoutError extends CartState {
  final String error;
  CartCheckoutError(this.error);
}

// Исчерпывающий switch
void handleCartState(CartState state) {
  switch (state) {
    case CartEmpty():
      print('Корзина пуста.');
    case CartLoading():
      print('Загрузка корзины...');
    case CartLoaded(items: final items, total: final total):
      print('Загружено ${items.length} товаров на сумму \$$total');
    case CartCheckoutError(error: final error):
      print('Ошибка оформления: $error');
  }
}

void main() {
  handleCartState(CartEmpty());
  handleCartState(CartLoading());
  handleCartState(CartLoaded([Item('Книга'), Item('Ручка')], 1500.50));
  handleCartState(CartCheckoutError('Недостаточно средств'));
}
```

42. **Sealed-иерархия сетевого ответа (Result Pattern):** Реализуйте `sealed class Result<T>` с подтипами `Success<T>(final T data)` и `Failure<T>(final Exception exception)`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/23d7e76f-b3dd-43b6-b8d7-cee2cc127629" />

```dart
sealed class Result<T> {}

class Success<T> extends Result<T> {
  final T data;
  Success(this.data);
}

class Failure<T> extends Result<T> {
  final Exception exception;
  Failure(this.exception);
}

Result<int> divide(int a, int b) {
  if (b == 0) {
    return Failure(Exception('Деление на ноль невозможно'));
  }
  return Success(a ~/ b);
}

void printResult(Result<int> result) {
  switch (result) {
    case Success(data: final data):
      print('Успех: $data');
    case Failure(exception: final ex):
      print('Ошибка: ${ex.toString()}');
  }
}

void main() {
  var res1 = divide(10, 2);
  printResult(res1);

  var res2 = divide(5, 0);
  printResult(res2);
}
```

43. **Архитектура UI-событий экрана логина:** Создайте `sealed class LoginEvent` с событиями `LoginEmailChanged(String email)`, `LoginPasswordChanged(String pass)`, `LoginSubmitted`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/372f33fe-bd10-472c-b2a4-5fdf618dd4db" />

```dart
sealed class LoginEvent {}

class LoginEmailChanged extends LoginEvent {
  final String email;
  LoginEmailChanged(this.email);
}

class LoginPasswordChanged extends LoginEvent {
  final String pass;
  LoginPasswordChanged(this.pass);
}

class LoginSubmitted extends LoginEvent {}

void processLoginEvent(LoginEvent event) {
  switch (event) {
    case LoginEmailChanged(email: final email):
      print('Валидация email: $email');
    case LoginPasswordChanged(pass: final pass):
      print('Проверка надежности пароля (длина: ${pass.length})');
    case LoginSubmitted():
      print('Отправка данных авторизации на сервер...');
  }
}

void main() {
  processLoginEvent(LoginEmailChanged('test@mail.com'));
  processLoginEvent(LoginPasswordChanged('12345678'));
  processLoginEvent(LoginSubmitted());
}
```

44. **Модификатор `final class` для безопасности криптоключа:** Объявите класс `final class EncryptionKey`, запретив любое его расширение или реализацию вне текущей библиотеки.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0a0ebd37-6270-43f8-9150-9dea23cb7edf" />

```dart
final class EncryptionKey {
  final String _keyValue;

  EncryptionKey(this._keyValue);

  void printKeyInfo() {
    print('Ключ длиной ${_keyValue.length} символов загружен в защищенную память.');
  }
}

void main() {
  var key = EncryptionKey('SuperSecretAES256Key');
  key.printKeyInfo();
  
  // Попытка сделать class MyKey extends EncryptionKey {} 
  // вызовет ошибку компиляции.
}
```

45. **Модификатор `interface class` для публичного API SDK:** Разработайте интерфейсный класс платежного терминала, запретив прямое наследование логики клиентами библиотеки.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cfa3c8d1-cbcf-4836-b0e0-ec5818f8879c" />

```dart
interface class PaymentTerminal {
  void connect() {
    print('SDK: Терминал подключен к сети.');
  }
  
  void processPayment(double amount) {
    print('SDK: Обработка платежа на сумму \$$amount');
  }
}

// Клиентский код реализует интерфейс
class MyCustomTerminal implements PaymentTerminal {
  @override
  void connect() {
    print('Мой терминал: Кастомное подключение...');
  }

  @override
  void processPayment(double amount) {
    print('Мой терминал: Оплата \$$amount проведена.');
  }
}

void main() {
  PaymentTerminal terminal = MyCustomTerminal();
  terminal.connect();
  terminal.processPayment(99.99);
}
```

46. **Исчерпывающий роутинг экрана через Sealed Route:** Спроектируйте `sealed class AppRoute` для типобезопасной навигации во Flutter.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/dc22e26a-b1d9-4b6b-b25b-90315e353129" />

```dart
sealed class AppRoute {}

class HomeRoute extends AppRoute {}
class CatalogRoute extends AppRoute {}

class ProductRoute extends AppRoute {
  final String productId;
  ProductRoute(this.productId);
}

// Типобезопасная навигация
void navigateTo(AppRoute route) {
  switch (route) {
    case HomeRoute():
      print('Переход на Главный экран');
    case CatalogRoute():
      print('Переход в Каталог');
    case ProductRoute(productId: final id):
      print('Переход на карточку товара с ID: $id');
  }
}

void main() {
  navigateTo(HomeRoute());
  navigateTo(ProductRoute('prod_12345'));
}
```

47. **Паттерн «Команда» (Command Pattern):** Создайте абстрактный класс `Command` с методом `execute()` и `undo()` для реализации функции отмены действий в редакторе текста.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/55b2d9cc-6db9-4fc3-ad5e-6fc4ff6354fb" />

```dart
abstract class Command {
  void execute();
  void undo();
}

class TextEditor {
  String text = '';
}

class AddTextCommand implements Command {
  final TextEditor editor;
  final String textToAdd;

  AddTextCommand(this.editor, this.textToAdd);

  @override
  void execute() {
    editor.text += textToAdd;
    print('Выполнено: добавлено "$textToAdd". Текст: "${editor.text}"');
  }

  @override
  void undo() {
    editor.text = editor.text.substring(0, editor.text.length - textToAdd.length);
    print('Отменено: удалено "$textToAdd". Текст: "${editor.text}"');
  }
}

void main() {
  var editor = TextEditor();
  var command = AddTextCommand(editor, 'Привет, мир!');
  
  command.execute();
  command.undo();
}
```

48. **Паттерн «Строитель» (Builder):** Реализуйте `NotificationBuilder` с цепочечными вызовами `.setTitle().setBody().setIcon().build()`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/47182861-0b1f-46f7-b0ca-ee3928fd7f9f" />

```dart
class Notification {
  final String title;
  final String body;
  final String icon;

  Notification(this.title, this.body, this.icon);

  @override
  String toString() => 'Push: [$icon] $title - $body';
}

class NotificationBuilder {
  String _title = '';
  String _body = '';
  String _icon = 'default_icon';

  NotificationBuilder setTitle(String title) {
    _title = title;
    return this;
  }

  NotificationBuilder setBody(String body) {
    _body = body;
    return this;
  }

  NotificationBuilder setIcon(String icon) {
    _icon = icon;
    return this;
  }

  Notification build() {
    return Notification(_title, _body, _icon);
  }
}

void main() {
  var pushNotification = NotificationBuilder()
      .setTitle('Новое сообщение')
      .setBody('Привет! Как дела?')
      .setIcon('chat_icon')
      .build();

  print(pushNotification);
}
```

49. **Паттерн «Наблюдатель» (Observer / EventBus):** Создайте легковесную шину событий с классами событий и методами подписки/отписки.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cbae2cff-b7aa-424d-b744-f826cfce84e3" />

```dart
abstract class AppEvent {}

class UserLoggedIn extends AppEvent {
  final String username;
  UserLoggedIn(this.username);
}

class ThemeChanged extends AppEvent {
  final bool isDark;
  ThemeChanged(this.isDark);
}

class EventBus {
  final Map<Type, List<Function>> _listeners = {};

  void subscribe<T extends AppEvent>(void Function(T) callback) {
    _listeners.putIfAbsent(T, () => []).add(callback);
  }

  void unsubscribe<T extends AppEvent>(void Function(T) callback) {
    _listeners[T]?.remove(callback);
  }

  void publish<T extends AppEvent>(T event) {
    if (_listeners.containsKey(T)) {
      for (var callback in _listeners[T]!) {
        callback(event);
      }
    }
  }
}

void main() {
  var bus = EventBus();

  // Подписчики
  void onLogin(UserLoggedIn event) => print('UI: Привет, ${event.username}!');
  void onTheme(ThemeChanged event) => print('UI: Тема изменена на ${event.isDark ? "темную" : "светлую"}');

  bus.subscribe<UserLoggedIn>(onLogin);
  bus.subscribe<ThemeChanged>(onTheme);

  // Генерация событий
  bus.publish(UserLoggedIn('admin'));
  bus.publish(ThemeChanged(true));

  // Отписка и проверка
  bus.unsubscribe<UserLoggedIn>(onLogin);
  print('--- После отписки ---');
  bus.publish(UserLoggedIn('guest')); // Ничего не выведет
}
```

50. **Комплексный архитектурный модуль заказа такси (Production Case):**
    - Спроектируйте `sealed class TaxiOrderState` (поиск машины, машина назначена, поездка, завершено, отменено).
    - Создайте класс заказа `TaxiOrder` с именованными фабричными конструкторами и списком инициализации.
    - Реализуйте миксины `LocationTrackingMixin` и `FareCalculationMixin`.
    - Напишите контроллер управления поездкой с исчерпывающим сопоставлением с шаблонами.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/07087fec-c319-46a6-a64c-a8d5abadae9f" />

```dart
// 1. Sealed State
sealed class TaxiOrderState {}

class SearchingState extends TaxiOrderState {}
class CarAssignedState extends TaxiOrderState {
  final String driverName;
  CarAssignedState(this.driverName);
}
class RidingState extends TaxiOrderState {}
class CompletedState extends TaxiOrderState {
  final double price;
  CompletedState(this.price);
}
class CancelledState extends TaxiOrderState {
  final String reason;
  CancelledState(this.reason);
}

// 2. Класс заказа с именованными конструкторами и списком инициализации
class TaxiOrder {
  final String id;
  final String pickup;
  final String destination;

  TaxiOrder._({
    required this.id, 
    required this.pickup, 
    required this.destination,
  });

  factory TaxiOrder.standard(String pickup, String dest) {
    return TaxiOrder._(id: 'STD-001', pickup: pickup, destination: dest);
  }

  factory TaxiOrder.express(String pickup, String dest) {
    return TaxiOrder._(id: 'EXP-999', pickup: pickup, destination: dest);
  }
}

// 3. Миксины
mixin LocationTrackingMixin {
  double lat = 0.0;
  double lng = 0.0;
  void updateLocation(double newLat, double newLng) {
    lat = newLat;
    lng = newLng;
    print('GPS: Координаты обновлены ($lat, $lng)');
  }
}

mixin FareCalculationMixin {
  double calculateFare(double distanceKm) {
    double baseFare = 150.0;
    double perKm = 20.0;
    return baseFare + (distanceKm * perKm);
  }
}

// 4. Контроллер
class TaxiController with LocationTrackingMixin, FareCalculationMixin {
  TaxiOrderState _currentState = SearchingState();

  void updateState(TaxiOrderState newState) {
    _currentState = newState;
    _handleState();
  }

  void _handleState() {
    // Исчерпывающее сопоставление с шаблонами
    switch (_currentState) {
      case SearchingState():
        print('Система: Ищем свободную машину...');
      case CarAssignedState(driverName: final name):
        print('Система: Машина найдена! Водитель: $name');
      case RidingState():
        print('Система: Вы в пути.');
      case CompletedState(price: final price):
        print('Система: Поездка завершена. Итоговая стоимость: \$${price.toStringAsFixed(2)}');
      case CancelledState(reason: final reason):
        print('Система: Заказ отменен. Причина: $reason');
    }
  }
}

void main() {
  var order = TaxiOrder.express('ул. Ленина, 1', 'Аэропорт');
  print('Создан заказ ${order.id} от ${order.pickup} до ${order.destination}');

  var controller = TaxiController();

  controller.updateState(SearchingState());
  controller.updateState(CarAssignedState('Иван'));
  
  controller.updateLocation(55.7558, 37.6173);
  controller.updateState(RidingState());
  
  double fare = controller.calculateFare(15.5); // 15.5 км
  controller.updateState(CompletedState(fare));
}
```

---
