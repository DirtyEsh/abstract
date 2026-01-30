**Callable (вызываемый объект)** — это объект в программировании, который можно "вызвать" как функцию с помощью оператора вызова (обычно круглые скобки `()` с аргументами).
## Callable в C++ — короткий, полный конспект

**Callable** — любой объект, к которому применим оператор вызова `()`.

```cpp
callable(args...);
```

---

## Виды callable (исчерпывающий список)

### 1. Обычная функция

```cpp
void f(int);
f(1);
```

---

### 2. Function pointer

```cpp
void f(int);
void (*p)(int) = f;
p(1);
```

Факт: это **значение**, а не объект.

---

### 3. Lambda

```cpp
int k = 3;
auto l = [k](int x) { return x + k; };
l(5);
```

Факт: лямбда — это **анонимный класс** с `operator()`.

---

### 4. Functor (функциональный объект)

```cpp
struct Mul {
    int k;
    int operator()(int x) const {
        return x * k;
    }
};
Mul m{3};
m(4);
```

---

### 5. Member function pointer

```cpp
struct A {
    void f(int) {}
};

void (A::*pm)(int) = &A::f;

A a;
(a.*pm)(3);
```

Факт: **не callable без объекта**.

---

### 6. `std::function`

```cpp
std::function<int(int)> f =
    [](int x){ return x * 2; };

f(3);
```

Факт: type-erasure, возможна аллокация.

---

## Обобщённый приём callable

### Шаблон (zero-cost)

```cpp
template<typename F>
void run(F&& f) {
    f(10);
}
```

Работает для **всех callable**.

---

### `std::invoke` (универсальный вызов)

```cpp
#include <functional>

std::invoke(f, args...);
```

Поддерживает:

- функции
    
- лямбды
    
- функторы
    
- member function pointer
    

---

## Проверка на callable

```cpp
#include <type_traits>

template<typename F>
concept Callable =
    requires(F f) {
        f(1);
    };
```

---

## const и operator()

```cpp
struct A {
    void operator()() const {}
};
```

Если `operator()` не `const` — нельзя вызывать на `const` объекте.

---

## Захваты и состояние

```cpp
auto f = [x = 10]() mutable {
    x++; // OK
};
```

- без `mutable` захват по значению — `const`
    

---

## Основные правила

1. **Любая лямбда = объект**
    
2. `std::function` — контейнер для callable
    
3. `operator()` может быть перегружен
    
4. lifetime захваченных данных критичен
    
5. шаблон + forwarding = лучший вариант
    

---

## Минимальный эталон

```cpp
template<typename F, typename... Args>
decltype(auto) call(F&& f, Args&&... args) {
    return std::invoke(
        std::forward<F>(f),
        std::forward<Args>(args)...
    );
}
```

Это максимум, что нужно знать про callable.