[[Qt форматирование]]

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