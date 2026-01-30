`explicit`
# Конспект: `explicit` в C++

## Что это?
`explicit` — это спецификатор, который **запрещает неявные преобразования** и **неявное создание объектов** для конструкторов и операторов преобразования.

## 1. Для конструкторов (самое частое использование)

### Без `explicit` (опасно!)
```cpp
class String {
public:
    String(int size) {  // Конструктор из int
        buffer = new char[size];
        this->size = size;
    }
    // ... другие методы ...
};

void printString(String s) {
    // ...
}

int main() {
    String s1 = 10;     // ОК: неявное преобразование int → String
    String s2(10);      // ОК: явный вызов
    
    printString(100);   // ОПАСНО! Создается String(100) неявно
    printString('A');   // Еще хуже: char → int → String
    
    return 0;
}
```

### С `explicit` (безопасно!)
```cpp
class String {
public:
    explicit String(int size) {  // Теперь только явное создание
        buffer = new char[size];
        this->size = size;
    }
    // ... другие методы ...
};

void printString(String s) {
    // ...
}

int main() {
    // String s1 = 10;     // ОШИБКА: нельзя неявно преобразовать
    String s2(10);          // ОК: явный вызов
    String s3 = String(10); // ОК: явное создание
    
    // printString(100);   // ОШИБКА: нельзя неявно преобразовать
    printString(String(100)); // ОК: явное преобразование
    
    return 0;
}
```

## 2. Для операторов преобразования (C++11 и выше)

### Без `explicit`
```cpp
class SmartBool {
    bool value;
public:
    operator bool() const {  // Неявное преобразование
        return value;
    }
};

SmartBool sb;
if (sb) {            // ОК: неявное преобразование SmartBool → bool
    // ...
}

int x = sb;          // ОПАСНО! SmartBool → bool → int
```

### С `explicit` (C++11)
```cpp
class SmartBool {
    bool value;
public:
    explicit operator bool() const {  // Только явное преобразование
        return value;
    }
};

SmartBool sb;
if (sb) {            // ОК: в if разрешено (контекстное преобразование)
    // ...
}

// int x = sb;       // ОШИБКА: нельзя неявно преобразовать
bool b = static_cast<bool>(sb);  // ОК: явное преобразование
```

## 3. Когда использовать `explicit`?

### ✅ Обязательно использовать:
```cpp
// 1. Конструкторы с одним параметром (кроме копирования)
class File {
public:
    explicit File(const std::string& path); // путь к файлу
};

// 2. Конструкторы, которые могут быть вызваны неочевидно
class Buffer {
public:
    explicit Buffer(size_t size); // размер буфера
};

// 3. Операторы bool (чтобы избежать неожиданных преобразований)
class PtrWrapper {
public:
    explicit operator bool() const;
};
```

### ❌ Можно не использовать:
```cpp
// 1. Конструкторы копирования и перемещения
class MyClass {
public:
    MyClass(const MyClass& other); // не explicit
    MyClass(MyClass&& other);      // не explicit
};

// 2. Многоаргументные конструкторы (до C++11)
// (хотя в C++11 и они могут быть неявными при {} инициализации)
```

## 4. Примеры из стандартной библиотеки

```cpp
// std::vector
std::vector<int> v1(10);      // 10 нулей - explicit конструктор
// std::vector<int> v2 = 10;  // ОШИБКА: explicit запрещает

// std::unique_ptr
std::unique_ptr<int> p1(new int(42)); // OK
// std::unique_ptr<int> p2 = new int(42); // ОШИБКА

// std::string_view (C++17)
std::string_view sv1("hello");      // OK - конструктор explicit?
// std::string_view sv2 = "hello";  // Зависит от реализации
```

## 5. Особенности в C++11 и выше

### `explicit` с `= delete` и `= default`
```cpp
class NonCopyable {
public:
    explicit NonCopyable() = default;
    explicit NonCopyable(const NonCopyable&) = delete; // Можно, но избыточно
};
```

### `explicit` с несколькими параметрами (C++11)
```cpp
class Vec3 {
public:
    // В C++11: предотвращает неявное создание через {}
    explicit Vec3(int x, int y, int z) : x(x), y(y), z(z) {}
    
private:
    int x, y, z;
};

Vec3 v1{1, 2, 3};      // OK: direct initialization
// Vec3 v2 = {1, 2, 3}; // ОШИБКА: copy-list-initialization
```

### Конструкторы по умолчанию (редко)
```cpp
class Logger {
public:
    explicit Logger() = default; // Зачем? Чтобы запретить Logger l = {};
};
```

## 6. Правило: Почти всегда `explicit`

**Google C++ Style Guide**: Делайте все конструкторы с одним параметром `explicit`, кроме копирования/перемещения.

**C++ Core Guidelines**:
- C.46: По умолчанию делайте одноаргументные конструкторы `explicit`
- C.164: Избегайте неявных операторов преобразования

## 7. Практические примеры

### Пример 1: Безопасный указатель
```cpp
template<typename T>
class SafePtr {
    T* ptr;
public:
    explicit SafePtr(T* p = nullptr) : ptr(p) {}
    explicit operator bool() const { return ptr != nullptr; }
    
    // Без explicit было бы опасно:
    // SafePtr<int> p = new int(5); // ОШИБКА - хорошо!
    // if (p) ... // OK - в if разрешено
    // int x = p; // ОШИБКА - хорошо!
};
```

### Пример 2: Идентификатор
```cpp
class UserId {
    int id;
public:
    explicit UserId(int id) : id(id) {
        if (id <= 0) throw std::invalid_argument("Invalid ID");
    }
    int get() const { return id; }
};

void processUser(UserId id) {
    // Гарантировано получили валидный ID
}

// processUser(123);      // ОШИБКА: нужна явная проверка
// processUser(UserId(123)); // OK: валидация выполнена
processUser(UserId(123));    // Явное создание
```

### Пример 3: Единицы измерения
```cpp
class Meter {
    double value;
public:
    explicit Meter(double m) : value(m) {}
    double toMeters() const { return value; }
};

class Kilometer {
    double value;
public:
    explicit Kilometer(double km) : value(km) {}
    operator Meter() const { return Meter(value * 1000); }
};

void printDistance(Meter m) {
    std::cout << m.toMeters() << " m\n";
}

// printDistance(5.0);           // ОШИБКА: какой единицы?
printDistance(Meter(5.0));        // OK: 5 метров
printDistance(Kilometer(2.0));    // OK: неявное Kilometer → Meter разрешено
                                  // но Kilometer(2.0) → Meter явно
```

## Итог

| Ситуация | `explicit` | Результат |
|----------|------------|-----------|
| `Class obj = value;` | Нет | Разрешено (неявное преобразование) |
| `Class obj = value;` | Да | **Запрещено** |
| `Class obj(value);` | Любой | Разрешено (явный вызов) |
| `func(value)` где ждет `Class` | Нет | Разрешено (неявное создание) |
| `func(value)` где ждет `Class` | Да | **Запрещено** |
| `if(obj)` где `operator bool()` | Да | Разрешено (специальное правило для bool) |

**Главное правило**: Используйте `explicit` для конструкторов с одним параметром (кроме копирования/перемещения) и для операторов преобразования, чтобы избежать неожиданных преобразований типов.