[[Итератор]]
finde() finde_if()
timate Guide: std::find и std::find_if
📌 std::find
Что делает:
Ищет первое вхождение значения в диапазоне.

Синтаксис:
```
cpp
template< class InputIt, class T >
InputIt find( InputIt first, InputIt last, const T& value );
```
Что принимает:
first - итератор на начало диапазона

last - итератор на конец диапазона (не включается)

value - значение для поиска

Что возвращает:
Итератор на найденный элемент, если найден

last (конец диапазона), если не найден

Примеры:
```
cpp
std::vector<int> v = {1, 2, 3, 4, 5};

// Поиск значения
auto it = std::find(v.begin(), v.end(), 3);
if (it != v.end()) {
    std::cout << "Найден: " << *it; // 3
}

// Поиск в массиве
int arr[] = {10, 20, 30};
int* ptr = std::find(std::begin(arr), std::end(arr), 20);

// Поиск в строке
std::string s = "hello";
auto pos = std::find(s.begin(), s.end(), 'l'); // первый 'l'
```
---

📌 std::find_if
Что делает:
Ищет первый элемент, удовлетворяющий условию (предикату).

Синтаксис:
```
cpp
template< class InputIt, class UnaryPredicate >
InputIt find_if( InputIt first, InputIt last, UnaryPredicate p );
```
Что принимает:
first, last - итераторы диапазона

p - предикат (функция/лямбда, возвращающая bool)

Что возвращает:
Итератор на первый элемент, где p(*it) == true

last, если ни один элемент не подходит

Примеры:
```
cpp
std::vector<int> v = {1, 3, 5, 7, 9};

// Лямбда
auto it = std::find_if(v.begin(), v.end(), 
    [](int x) { return x > 4; }); // первый >4
// *it = 5

// Функция
bool isEven(int x) { return x % 2 == 0; }
auto it2 = std::find_if(v.begin(), v.end(), isEven);
// it2 == v.end() (нет четных)

// Функтор
struct GreaterThan {
    int limit;
    bool operator()(int x) const { return x > limit; }
};
auto it3 = std::find_if(v.begin(), v.end(), GreaterThan{6});
// *it3 = 7
```
---

# **`std::count` и `std::count_if` — кратко**

## **📌 `std::count`**
```cpp
// Синтаксис
template< class InputIt, class T >
typename iterator_traits<InputIt>::difference_type
count( InputIt first, InputIt last, const T& value );

// Возвращает: количество элементов равных value
```

## **📌 `std::count_if`**
```cpp
// Синтаксис  
template< class InputIt, class UnaryPredicate >
typename iterator_traits<InputIt>::difference_type
count_if( InputIt first, InputIt last, UnaryPredicate p );

// Возвращает: количество элементов где predicate возвращает true
```

## **🚀 Один пример на все случаи:**
```cpp
#include <algorithm>
#include <vector>
#include <iostream>

struct Person {
    std::string name;
    int age;
    bool is_active;
};

int main() {
    std::vector<Person> people = {
        {"Alice", 25, true},
        {"Bob", 30, false},
        {"Charlie", 25, true},
        {"David", 40, true},
        {"Eve", 25, false}
    };

    // 1. count - конкретное значение (обычно для простых типов)
    std::vector<int> ages;
    for (const auto& p : people) ages.push_back(p.age);
    
    int count_25 = std::count(ages.begin(), ages.end(), 25);
    // count_25 = 3 (Alice, Charlie, Eve)
    
    // 2. count_if - с условием (лямбда, функция, функтор)
    
    // а) Количество активных пользователей
    int active_count = std::count_if(people.begin(), people.end(),
        [](const Person& p) { return p.is_active; });
    // active_count = 3 (Alice, Charlie, David)
    
    // б) Количество людей старше 30
    int older_30 = std::count_if(people.begin(), people.end(),
        [](const Person& p) { return p.age > 30; });
    // older_30 = 1 (David)
    
    // в) Сложное условие (активные и младше 30)
    int young_active = std::count_if(people.begin(), people.end(),
        [](const Person& p) { 
            return p.is_active && p.age < 30; 
        });
    // young_active = 2 (Alice, Charlie)
    
    // 3. Для строк (поиск символов)
    std::string text = "hello world";
    int count_l = std::count(text.begin(), text.end(), 'l');
    // count_l = 3
    
    // 4. С функтором (состояние)
    class AgeChecker {
        int min_age;
    public:
        AgeChecker(int age) : min_age(age) {}
        bool operator()(const Person& p) const {
            return p.age >= min_age;
        }
    };
    
    int adults = std::count_if(people.begin(), people.end(), 
                               AgeChecker(18));
    // adults = 5 (все совершеннолетние)
    
    std::cout << "25-летних: " << count_25 << "\n"
              << "Активных: " << active_count << "\n"
              << "Старше 30: " << older_30 << "\n"
              << "Молодых активных: " << young_active;
    
    return 0;
}
```

## **⚠️ Нюансы использования:**

### **1. Возвращаемый тип:**
```cpp
// size_t или ptrdiff_t (обычно auto)
auto count = std::count(v.begin(), v.end(), 5);
// или
std::ptrdiff_t count = std::count(v.begin(), v.end(), 5);
```

### **2. Для пользовательских типов:**
```cpp
struct Point { int x, y; };
bool operator==(const Point& a, const Point& b) {
    return a.x == b.x && a.y == b.y;
}

std::vector<Point> points = {{1,2}, {3,4}, {1,2}};
int same_points = std::count(points.begin(), points.end(), 
                             Point{1, 2});
// same_points = 2
```

### **3. Производительность:**
- **O(n)** — всегда проходит по всему диапазону
- **Нет раннего выхода** (в отличие от `find`)
- Для `std::set`/`std::map` есть встроенный метод `.count()`

### **4. Пустой диапазон:**
```cpp
std::vector<int> empty;
int zero = std::count(empty.begin(), empty.end(), 42);
// zero = 0 (безопасно)
```

### **5. C++20 Ranges (короче):**
```cpp
#include <ranges>
int count = std::ranges::count(people, 25, &Person::age);
int count_if = std::ranges::count_if(people, 
    [](const Person& p) { return p.age > 30; });
```

## **💡 Когда использовать:**
- **`count`** — сколько раз встречается **конкретное значение**
- **`count_if`** — сколько элементов **удовлетворяют условию**
- **Если нужно найти первый** — используй `find`/`find_if`
- **Если нужно просто проверить наличие** — `any_of`/`none_of`

---

**`std::accumulate`**, то код станет компактнее. Чтобы его использовать, кроме библиотеки `algorithm`, надо подключить библиотеку
 #includ  <**numeric**> .
 Алгоритм `std::accumulate` принимает на вход *указатели на начало* и *конец контейнера*, а третьим параметром — *начальное значение*, получится так:

```
std::vector<int> first_programer = {100, 210, 134, 89, 256};
int sum_first_programer = std::accumulate(
    first_programer.begin(), // Начало диапазона.
    first_programer.end(),   // Конец диапазона.
    0                        // Начальное значение.
);

std::vector<int> second_programer = {670, 110, 578, 321, 83};
int sum_second_programer = std::accumulate(
    second_programer.begin(),
    second_programer.end(), 
    0
```

еще и символф:

```
`std::vector<char> source_line =` 
    `{'A', 'l', 'g', 'o', 'r', 'i', 't', 'h', 'm', 's'};`
`std::string new_line = std::accumulate(`
    `source_line.begin(),` 
    `source_line.end(),` 
    `std::string{}        // Начинаем с пустой строки.`
`);`
`std::cout << new_line.c_str() << std::endl;`
`// Будет выведено: Algorithms.`
```

c векторами, например строк и всего, у чего определена opiratir+:
```
std::vector<std::string> v_str{"Hello", ", ", "Algorithms", "!"};
std::string ss = std::accumulate(v_str.begin(), v_str.end(), std::string{});
std::cout << ss.c_str() << std::endl;
// Будет выведено: Hello, Algorithms!
```
### Ищем минимум и максимум

Для реализации поиска максимальных и минимальных значений в стандартной библиотеке есть несколько алгоритмов, которые решают эту задачу. Их можно разделить на две группы:

1. `std::min`, `std::max`, `std::minmax`. Принимают элементы и возвращают искомое значение:
    
    ```
    int max_int = std::max(4, 5);  // 5.
    int min_int = std::min(-7, 3); // -7.
    auto [min, max] = std::minmax(14, 6);  // min = 6, max = 14.
    auto [min2, max2] = std::minmax(
        {-0.4, 1e+5, 2.});  // min2 = -0.4, max2 = 1e+5. 
    ```
    
2. `std::min_element`, `std::max_element`, `std::minmax_element`. Принимают диапазон как пару итераторов и возвращают итератор на искомый элемент:
    
    ```
    // Позиции:              0  1  2  3  4  5  6  7
    std::vector<int> nums = {1, 2, 5, 6, 7, 3, 7, 1};
    auto it = std::max_element(nums.begin(), nums.end());
    std::cout << "Максимум на позиции " << (it - nums.begin()) 
              << ", значение: " << (*it) << std::endl;
    // Напечатает:
    // Максимум на позиции 4, значение: 7. 
    ```
    
    Алгоритм `max_element` возвратил итератор на максимальный элемент вектора. Аналогично работают `min_element`, `minmax_element`.
    