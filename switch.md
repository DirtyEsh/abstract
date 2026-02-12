Короткий **рид-бук по `switch`**. Только нужное.

---

## `switch`

### Синтаксис

```cpp
switch (expr) {
case value1:
    // код
    break;
case value2:
    // код
    break;
default:
    // код
}
```

---

## Что можно в `expr`

- целочисленные типы
    
- `enum` / `enum class`
    
- `char`
    

❌ нельзя: `string`, `float`, `double`

---

## `case`

```cpp
case 1:
case 2:
    // общий код
    break;
```

- значения **константы**
    
- вычисляются на этапе компиляции
    

---

## `break`

- останавливает `switch`
    
- без `break` → **fallthrough**
    

---

## `fallthrough`

```cpp
case 1:
    foo();
    [[fallthrough]];
case 2:
    bar();
    break;
```

---

## `default`

- срабатывает, если ни один `case` не подошёл
    
- можно ставить **где угодно**
    

---

## `enum class`

```cpp
enum class State { Idle, Run, Stop };

switch (state) {
case State::Idle: break;
case State::Run:  break;
case State::Stop: break;
}
```

---

## Выжимка

- `switch` → много `if ==`
    
- `case` — только константы
    
- `break` обязателен
    
- `enum class` — лучший кандидат