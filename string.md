---

---
в Qt ->[[QString]]
----------------


## Основные моменты
#includ `<string>`
- Управляет памятью автоматически
- Поддерживает RAII
[[ Основные escape-последовательности]]
[[Форматирование]]
## Литералы (C++11 и выше)
```cpp
using namespace std::string_literals;
auto str = "Hello"s;        // std::string
auto wstr = L"Hello"s;      // std::wstring
auto u8str = u8"Hello"s;    // std::u8string (C++20)
auto ustr = u"Hello"s;      // std::u16string
auto Ustr = U"Hello"s;      // std::u32string
```

## Основные методы

### Создание и инициализация
```cpp
std::string s1;                // пустая строка
std::string s2("Hello");       // из C-строки
std::string s3(5, 'a');        // "aaaaa"
std::string s4(s2);            // копирование
std::string s5 = "Hello";      // оператор присваивания
```

### Доступ к элементам
```cpp
s[i]        // без проверки границ
s.at(i)     // с проверкой (выбрасывает std::out_of_range)
s.front()   // первый символ
s.back()    // последний символ
s.data()    // указатель на данные (C++11: гарантированно null-terminated)
s.c_str()   // указатель на C-строку
```

### Информация
```cpp
s.size() / s.length()  // текущая длина
s.empty()              // true если строка пуста
s.capacity()           // выделенная память
s.max_size()           // максимально возможный размер
```

### Модификация
```cpp
s.clear()               // очистка
s.push_back('c')        // добавить символ в конец
s.pop_back()            // удалить последний символ (C++11)
s.append("text")        // добавить в конец
s += "text"             // оператор добавления
s.insert(pos, "text")   // вставка
s.erase(pos, count)     // удаление
s.replace(pos, count, "new")  // замена
s.resize(new_size)      // изменить размер
s.reserve(capacity)     // зарезервировать память
s.shrink_to_fit()       // уменьшить capacity до size (C++11)
```

### Поиск
```cpp
s.find("sub")           // первое вхождение
s.rfind("sub")          // последнее вхождение
s.find_first_of("abc")  // первый из символов
s.find_last_of("abc")   // последний из символов
s.find_first_not_of("abc")
s.find_last_not_of("abc")
// Все возвращают std::string::npos при отсутствии
```

### Подстроки и сравнение
```cpp
s.substr(pos, count)    // извлечь подстроку
s.compare(other)        // сравнение (0 если равны, <0 если меньше, >0 если больше)
s.starts_with("pre")    // начинается с (C++20)
s.ends_with("post")     // заканчивается на (C++20)
```

### Ввод/вывод
```cpp
std::getline(std::cin, s);     // чтение строки с пробелами
std::cout << s;                // вывод
```

## Итераторы
```cpp
// Все стандартные итераторы
auto begin = s.begin();      // итератор на начало
auto end = s.end();          // итератор за конец
auto rbegin = s.rbegin();    // реверсный итератор
auto rend = s.rend();

// Константные версии
auto cbegin = s.cbegin();
auto cend = s.cend();

// Использование с алгоритмами
std::sort(s.begin(), s.end());
std::find(s.begin(), s.end(), 'a');
```

## Полезные функции
```cpp
std::to_string(42)        // число → строка
std::stoi("42")           // строка → int (stol, stoll, stoul, stof, stod)
std::to_chars / from_chars // эффективное преобразование (C++17)
```

## Замечания
- Строки изменяемы, но операции могут вызывать реаллокацию
- Используйте `reserve()` для предотвращения многократных реаллокаций
- `operator[]` не проверяет границы, `at()` — проверяет
- Для конкатенации предпочтительнее `+=` и `append()`, а не `+` (создает временные объекты)