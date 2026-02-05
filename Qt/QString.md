[[Qt форматирование]]
[[QString и сортировка регистрами]]
`std::string stdStr = "Hello World";==

// Из const char*
QString qstr1 = QString::fromStdString(stdStr);

// Или напрямую
QString qstr2 = QString::fromStdString(stdStr);`==
`#include <QString>`
`int number = 42;`



`// Простое преобразование`
`QString str = QString::number(number);`

`// С указанием системы счисления`
`QString strHex = QString::number(number, 16); // "2a"`
`QString strBin = QString::number(number, 2);  // "101010"`

`// С указанием ширины и заполнения`
`QString strPadded = QString::number(number).rightJustified(5, '0'); // "00042"`



из QString в std::string

**`QString::toStdString()`:**

```
cpp

QString qstr = "Привет";
std::string stdstr = qstr.toStdString();  // UTF-8
```

**Или явно указать кодировку:**

```
cpp

std::string str1 = qstr.toUtf8().constData();    // UTF-8
std::string str2 = qstr.toLatin1().constData();  // Latin-1 (только англ.)
```

