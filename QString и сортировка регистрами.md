Ок. **Коротко, исчерпывающе, строго для конспекта.**

---

# Qt: сортировка строк и регистр

## Главное

В Qt **НЕ нужно** лезть в коды символов и `tolower`.  
Qt уже решает **регистр + локаль + Unicode**.

---

## Базовый инструмент — `QString::compare`

```cpp
QString::compare(a, b, Qt::CaseSensitive);    // с учётом регистра
QString::compare(a, b, Qt::CaseInsensitive);  // без учёта регистра
```

Возвращает:

- `< 0` → `a < b`
    
- `= 0` → равны
    
- `> 0` → `a > b`
    

### В `std::sort`

```cpp
std::sort(v.begin(), v.end(),
    [](const QString& a, const QString& b) {
        return QString::compare(a, b, Qt::CaseInsensitive) < 0;
    });
```

---

## Правильный вариант для UI — `QCollator`

```cpp
#include <QCollator>

QCollator coll;
coll.setCaseSensitivity(Qt::CaseInsensitive);

std::sort(v.begin(), v.end(),
    [&coll](const QString& a, const QString& b) {
        return coll.compare(a, b) < 0;
    });
```

### Что решает `QCollator`

- Unicode
    
- локаль
    
- регистр
    
- “человеческий” порядок
    

---

## Numeric mode (важно для файлов)

```cpp
coll.setNumericMode(true);
```

`file2 < file10` ✅  
без него: `file10 < file2` ❌

---

## Когда что использовать

|Задача|Инструмент|
|---|---|
|Просто сравнить строки|`QString::compare`|
|Сортировка UI|`QCollator`|
|Игнорировать регистр|`Qt::CaseInsensitive`|
|Учитывать регистр|`Qt::CaseSensitive`|

---

## Чего НЕ делать в Qt

❌ `std::tolower`  
❌ ручные ASCII-компараторы  
❌ `toLower()` в цикле для сортировки

---

## Финальная выжимка

- Qt **уже умеет** работать с регистром
    
- `QString::compare` — просто и достаточно
    
- `QCollator` — правильно и профессионально
    
- Регистр настраивается **флагом**, не кодом