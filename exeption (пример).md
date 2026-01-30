#include <iostream>
#include <string>
#include <stdexcept>

// 1. Генерация исключения
void riskyFunction(int value) {
    if (value < 0) {
        throw std::invalid_argument("Значение не может быть отрицательным");
    }
    if (value == 0) {
        throw "Деление на ноль!"; // Можно бросать любой тип
    }
}

// 2. Обработка исключений
int main() {
    try {
        riskyFunction(-5);
        std::cout << "Всё хорошо" << std::endl;
    }
    catch (const std::invalid_argument& e) {
        std::cerr << "Ошибка: " << e.what() << std::endl;
    }
    catch (const char* message) {
        std::cerr << "Сообщение: " << message << std::endl;
    }
    catch (...) { // Ловит любые исключения
        std::cerr << "Неизвестная ошибка!" << std::endl;
    }
    
    return 0;
}#include <iostream>
#include <string>
#include <stdexcept>

// 1. Генерация исключения
void riskyFunction(int value) {
    if (value < 0) {
        throw std::invalid_argument("Значение не может быть отрицательным");
    }
    if (value == 0) {
        throw "Деление на ноль!"; // Можно бросать любой тип
    }
}

// 2. Обработка исключений
int main() {
    try {
        riskyFunction(-5);
        std::cout << "Всё хорошо" << std::endl;
    }
    catch (const std::invalid_argument& e) {
        std::cerr << "Ошибка: " << e.what() << std::endl;
    }
    catch (const char* message) {
        std::cerr << "Сообщение: " << message << std::endl;
    }
    catch (...) { // Ловит любые исключения
        std::cerr << "Неизвестная ошибка!" << std::endl;
    }
    
    return 0;
}