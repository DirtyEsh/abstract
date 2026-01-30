**В Qt есть несколько способов:**

## **1. `qDebug()` (основной)**
```cpp
#include <QDebug>

qDebug() << "Сообщение" << variable << anotherVar;
// Вывод: Сообщение value1 value2

qDebug("Форматирование: %d %s", number, text.c_str());
```

## **2. `qInfo()`, `qWarning()`, `qCritical()`, `qFatal()`**
```cpp
qInfo() << "Информация";
qWarning() << "Предупреждение";  // жёлтый текст
qCritical() << "Ошибка";          // красный текст
// qFatal() - выводит и завершает программу
```

## **3. С кастомной настройкой**
```cpp
qDebug().noquote() << "Без кавычек";
qDebug().nospace() << "Без" << "пробелов";

// Цветной вывод (на некоторых терминалах)
qDebug() << "\033[32mЗелёный текст\033[0m";
```

## **4. В файл**
```cpp
#include <QFile>
#include <QTextStream>

QFile file("log.txt");
if (file.open(QIODevice::WriteOnly | QIODevice::Append)) {
    QTextStream stream(&file);
    stream << QDateTime::currentDateTime().toString() 
           << ": " << message << "\n";
}
```

## **5. В Release-сборке**
По умолчанию `qDebug()` не выводится. Чтобы включить:
```cpp
// В начале main()
qputenv("QT_LOGGING_RULES", "*.debug=true");
// Или
#ifndef QT_NO_DEBUG_OUTPUT
    qDebug() << "Только в Debug";
#endif
```

## **Пример использования:**
```cpp
void someFunction() {
    QString name = "John";
    int age = 25;
    
    qDebug() << "User:" << name << "Age:" << age;
    qDebug() << QString("Name: %1, Age: %2").arg(name).arg(age);
    
    if (age < 0) {
        qWarning() << "Invalid age!";
    }
}
```

**В Qt Creator** логи появляются в панели **"Application Output"** (внизу).

**Короче:** Используй `qDebug() << данные;` для всего.