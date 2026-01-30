**`setCurrentRow()` — метод для QListWidget, QTableWidget и т.д.**

## **Что делает:**

Выбирает (выделяет) строку в списке/таблице по индексу.

## **Пример:**

cpp

```
QListWidget *listWidget = new QListWidget;
listWidget->addItem("Item 1");
listWidget->addItem("Item 2");
listWidget->addItem("Item 3");

listWidget->setCurrentRow(1); // Выделит "Item 2" (индекс с 0)
```