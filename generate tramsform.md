Супер, делаем **сжатый рид-бук**. Только синтаксис и что куда.

---

## `std::generate`

```cpp
#include <algorithm>
```

### Синтаксис

```cpp
std::generate(first, last, gen);
```

### Аргументы

- `first` — начало диапазона
    
- `last` — конец диапазона
    
- `gen` — **функция без аргументов**, возвращает значение
    

### Пример (лямбда)

```cpp
std::generate(v.begin(), v.end(),
    []() {
        return 42;
    });
```

---

## `std::transform`

```cpp
#include <algorithm>
```

### 1️⃣ Один диапазон → один результат

```cpp
std::transform(in_first, in_last, out_first, op);
```

### Аргументы

- `in_first`, `in_last` — вход
    
- `out_first` — куда писать
    
- `op` — **функция (T) → U**
    

### Пример

```cpp
std::transform(a.begin(), a.end(), b.begin(),
    [](int x) {
        return x * 2;
    });
```

---

### 2️⃣ Два диапазона → один результат

```cpp
std::transform(first1, last1, first2, out_first, op);
```

### Аргументы

- первый диапазон
    
- второй диапазон
    
- выход
    
- `op` — **функция (T1, T2) → U**
    

### Пример

```cpp
std::transform(a.begin(), a.end(), b.begin(), c.begin(),
    [](int x, int y) {
        return x + y;
    });
```

---

## Мини-выжимка

```
generate  → []() → value
transform → [](x) → value
transform → [](x, y) → value
```

- `generate` — **игнорирует старые значения**
    
- `transform` — **преобразует существующие**
    
- выходной диапазон должен быть **готов**
    

Если хочешь — следующим логично дать `std::iota` или `std::accumulate` в таком же формате.