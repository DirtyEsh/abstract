Спокойно. Исправляю. **КЛАСС**, не `struct`. Сухо и по делу.
[[перегрузки операторов]]

---

# Ультимативный конспект: инициализация **class** (включая `template<class T>`)

## 1. Класс БЕЗ конструктора

```cpp
class A {
public:
    int x;
    int y;
};
```

⚠️ Это **НЕ aggregate** (в отличие от `struct`, где `public` по умолчанию).

### Что МОЖНО

```cpp
A a;        // ❌ x,y НЕ инициализированы (мусор)
A a{};      // ✅ x=0, y=0  (value-initialization)
```

### Что НЕЛЬЗЯ

```cpp
A a{1, 2};  // ❌ aggregate init НЕ работает
A a(1, 2);  // ❌ нет конструктора
```

---

## 2. Класс С конструктором

```cpp
class A {
public:
    int x;
    int y;

    A(int a, int b) : x(a), y(b) {}
};
```

### Корректно

```cpp
A a(1, 2);      // direct-init
A a{1, 2};     // list-init (предпочтительно)
A a = A(1, 2); // copy-init
```

---

## 3. Default member initialization

```cpp
class A {
public:
    int x = 0;
    int y{1};
};
```

```cpp
A a;    // x=0, y=1
A b{};  // x=0, y=1
```

Если есть ctor — ctor **перекрывает** значения.

---

## 4. Шаблонный класс БЕЗ конструктора

```cpp
template<class T>
class Box {
public:
    T value;
};
```

### ВАЖНО

```cpp
Box<int> a;     // ❌ value НЕ инициализирован
Box<int> b{};   // ✅ value = int{}
```

**Правило:** шаблоны → всегда `{}`

---

## 5. Шаблонный класс С конструктором

```cpp
template<class T>
class Box {
public:
    T value;
    explicit Box(T v) : value(v) {}
};
```

```cpp
Box<int> a(5);
Box<int> b{5};
```

---

## 6. CTAD (C++17+)

```cpp
Box b{5};   // T = int
```

Работает **только если есть ctor**  
(для `class` без ctor — не работает)

---

## 7. Value-init vs default-init (критично)

```cpp
Box<int> a;    // ❌ UB при использовании
Box<int> b{};  // ✅ безопасно
```

---

## Ультимативная таблица

|Ситуация|Правильно|
|---|---|
|class без ctor|`T obj{}`|
|class с ctor|`T obj{...}`|
|template class|**ВСЕГДА `{}`**|
|хочу нули|`{}`|
|`T obj;`|почти всегда баг|

---

## Жёсткие правила (запомнить)

1. **`class` ≠ `struct`** (aggregate ломается)
    
2. `{}` — основной синтаксис
    
3. `T obj;` — мусор, если не POD с ctor
    
4. В шаблонах — только `{}`
    
5. CTAD работает **только через ctor**
    

Если дальше — можем разобрать:

- `explicit` и как он ломает инициализацию
    
- `initializer_list` vs обычный ctor
    
- copy / move init именно для `class`