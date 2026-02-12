[[Итератор]]
[[Предикат]]
[[Компаратор ]]
[[Рандом]]
[[Сравнение диапазонов]]
[[повторяющиеся символы]]
Ниже — **наведённый порядок, ничего не потеряно, логика выровнена, Markdown аккуратный**. Я добавил минимальные связки и исправил неточности, но смысл оставил 1-в-1.

---

# Алгоритмы поиска и подсчёта в C++

```cpp
#include <algorithm>   // find, find_if, count, count_if,
                       // min, max, min_element, max_element
#include <numeric>     // accumulate
```

---

## `std::find` и `std::find_if`

## 📌 `std::find`

### Что делает

Ищет **первое вхождение конкретного значения** в диапазоне.

### Синтаксис

```cpp
template<class InputIt, class T>
InputIt find(InputIt first, InputIt last, const T& value);
```

### Параметры

- `first` — начало диапазона
    
- `last` — конец диапазона (**не включается**)
    
- `value` — искомое значение
    

### Возвращает

- Итератор на найденный элемент
    
- `last`, если элемент не найден
    

### Примеры

```cpp
std::vector<int> v = {1, 2, 3, 4, 5};

auto it = std::find(v.begin(), v.end(), 3);
if (it != v.end()) {
    std::cout << *it; // 3
}
```

```cpp
int arr[] = {10, 20, 30};
auto ptr = std::find(std::begin(arr), std::end(arr), 20);
```

```cpp
std::string s = "hello";
auto pos = std::find(s.begin(), s.end(), 'l'); // первый 'l'
```

---

## 📌 `std::find_if`

### Что делает

Ищет **первый элемент**, для которого предикат возвращает `true`.

### Синтаксис

```cpp
template<class InputIt, class UnaryPredicate>
InputIt find_if(InputIt first, InputIt last, UnaryPredicate p);
```

### Параметры

- `first`, `last` — диапазон
    
- `p` — предикат (`bool p(const T&)`)
    

### Возвращает

- Итератор на первый подходящий элемент
    
- `last`, если ничего не найдено
    

### Примеры

```cpp
std::vector<int> v = {1, 3, 5, 7, 9};

auto it = std::find_if(v.begin(), v.end(),
    [](int x) { return x > 4; });
// *it == 5
```

#### Функция

```cpp
bool isEven(int x) { return x % 2 == 0; }
auto it = std::find_if(v.begin(), v.end(), isEven);
// it == v.end()
```

#### Функтор

```cpp
struct GreaterThan {
    int limit;
    bool operator()(int x) const { return x > limit; }
};

auto it = std::find_if(v.begin(), v.end(), GreaterThan{6});
// *it == 7
```

---

## `std::count` и `std::count_if`

## 📌 `std::count`

### Что делает

Считает, **сколько элементов равны заданному значению**.

```cpp
template<class InputIt, class T>
typename std::iterator_traits<InputIt>::difference_type
count(InputIt first, InputIt last, const T& value);
```

---

## 📌 `std::count_if`

### Что делает

Считает, **сколько элементов удовлетворяют условию**.

```cpp
template<class InputIt, class UnaryPredicate>
typename std::iterator_traits<InputIt>::difference_type
count_if(InputIt first, InputIt last, UnaryPredicate p);
```

---

## 🚀 Универсальный пример

```cpp
struct Person {
    std::string name;
    int age;
    bool is_active;
};

std::vector<Person> people = {
    {"Alice", 25, true},
    {"Bob", 30, false},
    {"Charlie", 25, true},
    {"David", 40, true},
    {"Eve", 25, false}
};
```

### `count`

```cpp
std::vector<int> ages;
for (const auto& p : people)
    ages.push_back(p.age);

int count_25 = std::count(ages.begin(), ages.end(), 25);
// 3
```

### `count_if`

```cpp
int active = std::count_if(people.begin(), people.end(),
    [](const Person& p) { return p.is_active; });

int older_30 = std::count_if(people.begin(), people.end(),
    [](const Person& p) { return p.age > 30; });
```

---

## ⚠️ Нюансы

### Возвращаемый тип

```cpp
auto c = std::count(v.begin(), v.end(), 5);
```

### Пользовательские типы (`count`)

```cpp
struct Point { int x, y; };

bool operator==(const Point& a, const Point& b) {
    return a.x == b.x && a.y == b.y;
}
```

### Производительность

- Всегда **O(n)**
    
- Нет раннего выхода
    
- Для `map / set` есть `.count()`
    

---

## C++20 Ranges

```cpp
#include <ranges>

int c1 = std::ranges::count(people, 25, &Person::age);
int c2 = std::ranges::count_if(people,
    [](const Person& p) { return p.age > 30; });
```

---

## `std::accumulate`

```cpp
#include <numeric>
```

### Суммирование чисел

```cpp
std::vector<int> v = {100, 210, 134};
int sum = std::accumulate(v.begin(), v.end(), 0);
```

### Символы → строка

```cpp
std::vector<char> chars = {'A','l','g','o'};
std::string s = std::accumulate(
    chars.begin(), chars.end(), std::string{});
```

### Строки

```cpp
std::vector<std::string> v{"Hello", ", ", "Algorithms", "!"};
std::string result =
    std::accumulate(v.begin(), v.end(), std::string{});
```

---

## Минимум и максимум

### Значения

```cpp
std::max(4, 5);         // 5
std::min(-7, 3);        // -7
auto [mn, mx] = std::minmax(14, 6);
```

### В диапазоне

```cpp
std::vector<int> nums = {1, 2, 5, 6, 7, 3};

auto it = std::max_element(nums.begin(), nums.end());
```

- `min_element`
    
- `max_element`
    
- `minmax_element`
    

---

# `std::reverse` — выжимка

## Что делает

Разворачивает диапазон **на месте**.

```cpp
`#include <algorithm>  std::reverse(first, last);`
```

---

## Как работает

- Меняет порядок элементов: первый ↔ последний, второй ↔ предпоследний
    
- **Ничего не возвращает**
    
- Работает за `O(n)`
    
- Дополнительной памяти не требует
## Итоговая выжимка

- **Ищешь первый элемент** → `find / find_if`
    
- **Считаешь количество** → `count / count_if`
    
- **Агрегация значений** → `accumulate`
    
- **Минимум / максимум** → `min(_element)` / `max(_element)`
    
- **Ranges (C++20)** — короче и чище
    

Если хочешь — следующим логично разобрать:  
`any_of / all_of / none_of` или `transform`.


Принял. Тогда **идеальный короткий формат** — вот так:

---

## `std::is_sorted`

```cpp
#include <algorithm>
```

### Базовый вариант

```cpp
bool ok = std::is_sorted(first, last);
```

Проверка по `operator<` (по возрастанию).

> Дальше — по аналогии с другими алгоритмами (`comp`, лямбды, и т.д.).
> 

Ок, **короткий рид-бук**, в том формате, который тебе зашёл.  
Без объяснений, без воды.

---

## `std::unique`

```cpp
#include <algorithm>
```

### Базовый вариант

```cpp
auto it = std::unique(first, last);
```

- **НЕ удаляет**
    
- сдвигает дубликаты в конец
    
- возвращает итератор на новый логический конец
    

### Правильное использование

```cpp
auto it = std::unique(v.begin(), v.end());
v.erase(it, v.end());
```

⚠️ Убирает **только соседние** дубликаты  
→ обычно перед этим делают `sort`

---

## `std::sort`

```cpp
#include <algorithm>
```

### Базовый вариант

```cpp
std::sort(first, last);
```

Сортировка по `operator<` (по возрастанию).

> Дальше — по аналогии с другими алгоритмами (компаратор, лямбда).

---

## `erase` (метод контейнера)

```cpp
v.erase(it);              // удалить один элемент
v.erase(first, last);     // удалить диапазон
```

- реально удаляет элементы
    
- сдвигает хвост
    
- меняет `size()`
    

---

## `erase + unique` (классика)

```cpp
v.erase(std::unique(v.begin(), v.end()), v.end());
```

---

## `std::remove`

```cpp
#include <algorithm>
```

### Базовый вариант

```cpp
auto it = std::remove(first, last, value);
```

- **НЕ удаляет**
    
- сдвигает “удалённые” в конец
    

### Правильно

```cpp
v.erase(std::remove(v.begin(), v.end(), x), v.end());
```

---

## `std::remove_if`

```cpp
auto it = std::remove_if(first, last, pred);
```

```cpp
v.erase(std::remove_if(v.begin(), v.end(),
    [](int x){ return x < 0; }), v.end());
```

---

## Мини-шпаргалка (1 взгляд)

```
sort        → сортирует
unique      → сдвигает дубликаты
remove      → сдвигает элементы
erase       → реально удаляет

unique/remove → ВСЕГДА + erase
```

---

## Если ещё накинуть (того же уровня)

- `std::reverse` — разворот диапазона
    
- `std::is_sorted` — проверка сортировки
    
- `std::count / count_if` — подсчёт
    
- `std::find / find_if` — поиск
    

Если хочешь — дальше можем сделать **одну страницу “ТОП-10 алгоритмов STL, которые реально нужны”** в таком же формате.