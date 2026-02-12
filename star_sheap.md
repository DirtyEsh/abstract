## 1. Внутри класса (рекомендуется)

```cpp
// Автоматическая генерация всех операторов сравнения (C++20)
auto operator<=>(const MyClass&) const = default;

// Или вручную:
auto operator<=>(const MyClass& other) const {
    // сравниваем поля в порядке приоритета
    if (auto cmp = field1 <=> other.field1; cmp != 0) return cmp;
    if (auto cmp = field2 <=> other.field2; cmp != 0) return cmp;
    return field3 <=> other.field3;
}
```

## 2. Снаружи класса (если нужно)

```cpp
// Объявить внутри класса как friend
class MyClass {
    int x, y;
public:
    friend auto operator<=>(const MyClass&, const MyClass&);
};

// Определить снаружи
auto operator<=>(const MyClass& a, const MyClass& b) {
    if (auto cmp = a.x <=> b.x; cmp != 0) return cmp;
    return a.y <=> b.y;
}
```

## 3. Минимальный рабочий пример

```cpp
#include <compare>

struct Point {
    int x, y;
    
    // Всё что нужно - один оператор
    auto operator<=>(const Point&) const = default;
};

// Теперь доступны ВСЕ сравнения: ==, !=, <, >, <=, >=
```

## 4. Конкретные сигнатуры для разных типов

```cpp
// Для чисел (strong_ordering)
std::strong_ordering operator<=>(const MyInt&) const;

// Для строк без учета регистра (weak_ordering)  
std::weak_ordering operator<=>(const MyString&) const;

// Для float/double с NaN (partial_ordering)
std::partial_ordering operator<=>(const MyFloat&) const;
```

**Главное:**
- `const` в конце обязательно
- Возвращай `auto` или конкретный тип сравнения
- Если все поля уже умеют `<=>`, делай `= default`
- `operator==` сгенерируется сам, но можно переопределить отдельно для скорости

**Пример в 3 строки:**
```cpp
class Data {
    int a, b;
public:
    auto operator<=>(const Data&) const = default; // всё, готово
};
```

Работает с C++20 и выше. Компилятор сам сгенерит все 6 операторов сравнения из одного `<=>`.

---
