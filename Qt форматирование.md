# Конспект: Форматирование строк в Qt

## Основные классы для форматирования

## Итог по Qt форматированию

- **Основной метод**: `QString::arg()` для подстановки
- **Для чисел**: `QString::number()` или `QLocale::toString()`
- **Для дат**: `QDateTime::toString()` с форматами
- **Для производительности**: `QStringBuilder`, `reserve()`
- **Для перевода**: оборачивать в `tr()` или `translate()`
- **Qt 6**: улучшенная поддержка UTF-8, новые методы

```cpp
// Современный подход Qt 6
auto result = QString("User: %1 | Balance: %2 | Date: %3")
    .arg(userName)
    .arg(QLocale().toCurrencyString(balance))
    .arg(QDateTime::currentDateTime().toString(Qt::ISODate));
```

### 1. `QString` - основная строковая обертка
```cpp
#include <QString>

QString s1 = "Hello";                    // из const char*
QString s2 = QString::fromUtf8("Привет"); // явное преобразование
QString s3 = u"Unicode string"_qs;       // C++17 user-defined literal
```

### 2. `QStringBuilder` (оптимизация конкатенации)
```cpp
#include <QStringBuilder>

// Использует % вместо + для оптимизации
QString s = QString("A") % "B" % "C";  // Эффективная конкатенация
```

## Основные методы форматирования

### Форматирование аргументов: `arg()`
```cpp
// Подстановка позиционных аргументов
QString s = QString("%1 %2 %3").arg("Hello").arg(42).arg(3.14);
// "Hello 42 3.14"

// Форматирование чисел
QString::number(42);                    // "42"
QString::number(3.14159, 'f', 2);       // "3.14" (формат, точность)
QString::number(255, 16);               // "ff" (основание системы)

// Из чисел
int num = 42;
double dbl = 3.14159;
QString s1 = QString("Number: %1").arg(num);
QString s2 = QString("Pi: %1").arg(dbl, 0, 'f', 2); // "Pi: 3.14"

// С заполнением и выравниванием
QString("%1").arg(42, 5, 10, QChar('0')); // "00042"
// число, ширина, основание, заполнитель
```

### Форматирование с типами
```cpp
// Разные типы данных
QString("%1 %2 %3 %4")
    .arg("Text")           // строка
    .arg(42)              // целое
    .arg(3.14159, 0, 'f', 3) // дробное
    .arg(QDate::currentDate().toString(Qt::ISODate)); // дата

// Локализация чисел
QLocale locale(QLocale::German);
QString localized = locale.toString(1234.56, 'f', 2); // "1.234,56"
```

## Форматирование чисел

```cpp
// Преобразование в строку
QString s1 = QString::number(42);          // "42"
QString s2 = QString::asprintf("%d", 42);  // аналог sprintf

// Разные системы счисления
QString::number(255, 16).toUpper();  // "FF" (hex)
QString::number(255, 2);             // "11111111" (binary)
QString::number(255, 8);             // "377" (octal)

// Вещественные числа
QString::number(3.14159, 'f', 3);    // "3.142" (fixed)
QString::number(3.14159, 'e', 3);    // "3.142e+00" (scientific)
QString::number(3.14159, 'g', 3);    // "3.14" (shortest)
```

## Форматирование дат и времени

```cpp
#include <QDateTime>
#include <QLocale>

QDateTime now = QDateTime::currentDateTime();

// Стандартные форматы
now.toString(Qt::ISODate);        // "2024-01-15T14:30:00"
now.toString(Qt::TextDate);       // "Mon Jan 15 14:30:00 2024"
now.toString(Qt::RFC2822Date);    // для email

// Кастомный формат
now.toString("dd.MM.yyyy hh:mm:ss");  // "15.01.2024 14:30:00"
now.toString("dddd, MMMM d");         // "Monday, January 15"

// Локализованный вывод
QLocale locale(QLocale::Russian);
locale.toString(now, QLocale::LongFormat);
```

## Продвинутое форматирование

### Многоязычные строки
```cpp
// Qt Linguist / Translation system
QString tr("Hello %1").arg(userName);  // для перевода

// В контексте:
QCoreApplication::translate("MainWindow", "File %1").arg(fileName);
```

### Форматирование HTML
```cpp
QString html = QString("<b>%1</b><br><i>%2</i>")
    .arg("Bold text")
    .arg("Italic text");
```

### `QTextStream` - потоковое форматирование
```cpp
#include <QTextStream>

QString result;
QTextStream stream(&result);

stream << "Text: " << qSetFieldWidth(10) << left << "Hello"
       << "Number: " << fixed << qSetRealNumberPrecision(2) << 3.14159;

// Манипуляторы:
// qSetFieldWidth(), qSetPadChar(), left, right, center
// scientific, fixed, qSetRealNumberPrecision()
```

## Шаблоны и заполнители

### Использование `QStringLiteral` (для производительности)
```cpp
// Создает QString на этапе компиляции (если возможно)
auto s = QStringLiteral("Static string with %1").arg(variable);
```

### Многострочные строки
```cpp
QString multi = QStringLiteral(
    "This is a "
    "multi-line "
    "string"
);

// С разделителями
QStringList parts = {"A", "B", "C"};
QString joined = parts.join(", ");  // "A, B, C"
```

## Форматирование для вывода (qDebug, qInfo и др.)

```cpp
#include <QDebug>

// Простое форматирование
qDebug() << "Value:" << 42 << "Pi:" << 3.14159;

// Форматированный вывод
qDebug().noquote() << QString("Formatted: %1").arg(42);
qDebug().nospace() << "No" << "spaces";

// Qt 6.5+: поддержка std::format синтаксиса
qDebug("Value: {}, Pi: {:.2f}", 42, 3.14159);
```

## Оптимизация производительности

```cpp
// 1. QStringBuilder для конкатенации
QString s = QString("A") % "B" % "C";  // Лучше чем s +=

// 2. Резервирование памяти
QString s;
s.reserve(100);  // Избегаем переаллокаций
for (int i = 0; i < 100; ++i) {
    s.append(QString::number(i));
}

// 3. Использование QStringView (Qt 5.15+/6)
void processString(QStringView view) {
    // Работает без копирования строки
}
```

## Пример комплексного использования

```cpp
#include <QString>
#include <QDateTime>
#include <QLocale>

QString formatInvoice(const QString &customer, double amount, 
                      const QDate &date) {
    return QString(
        "<h3>Invoice</h3>"
        "<p>Date: %1</p>"
        "<p>Customer: %2</p>"
        "<p>Amount: <b>%3</b></p>"
    ).arg(
        date.toString("dd.MM.yyyy"),          // дата
        customer,                             // клиент
        QLocale().toCurrencyString(amount)    // сумма с валютой
    );
}

// Использование:
auto invoice = formatInvoice("John Doe", 1234.56, QDate::currentDate());
```

## Особенности Qt 6

1. **Изменения в кодировках**: По умолчанию UTF-8
   ```cpp
   // Qt 6: QString из char* интерпретируется как UTF-8
   QString s = "Привет";  // UTF-8 автоматически
   
   // Qt 5: требовалось явное преобразование
   QString s = QString::fromUtf8("Привет");
   ```

2. **Новые методы в Qt 6**:
   ```cpp
   QString s = "Hello World";
   s.chopped(3);          // Удалить N символов с конца
   s.first(5);            // Первые 5 символов
   s.last(5);             // Последние 5 символов
   s.sliced(3, 5);        // Подстрока с позиции 3 длиной 5
   ```

