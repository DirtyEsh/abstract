синус

```cpp
std::vector<int16_t> signal;
signal.reserve(48000);

const double amplitude = 15000;
const int wave_length = 200;

for (int i = 0; i < wave_length; ++i) {
    // Далее формула синусоиды с заданной амплитудой и длиной волны.
    double value = amplitude * sin(2 * i * std::numbers::pi / wave_length);
    signal.push_back(static_cast<int16_t>(value));
}

ReplicateSignal(signal, 48000);
```

убывающий синус
```cpp
void ApplyDecay(std::vector<int16_t>& signal, size_t max_length) {
    // Не забудем уменьшить длину.
    size_t decay_length = std::min(signal.size(), max_length);

    // Используем цикл с убывающим счётчиком.
    for(size_t i = decay_length; i > 0; --i) {
        int16_t& sample = signal[signal.size() - i];
        // Умножим сигнал на убывающую величину.
        // Здесь она вычисляется.
        double factor = double(i) / decay_length;
        sample = static_cast<int16_t>(sample * factor);
    }
}

// ...
// main()

// Сделаем затухание длиной в 25 миллисекунд.
ApplyDecay(signal, 48000 / 1000 * 25);
```
функциональный метод
```CPP
std::vector<int16_t> signal;
signal.reserve(48000);

const double amplitude = 15000;
const int wave_length = 200;

// Используем mutable-лямбда функцию. Она может менять захваченные значения.
// В данном случае меняем i, который инициализируем в 
// квадратных скобках: i = 0.
auto generator = [=, i = 0]() mutable {
    // Возвратим значение. Алгоритм generate_n добавит его
    // в вектор.
    return amplitude * sin(2 * i++ * std::numbers::pi / wave_length);
};

std::generate_n(std::back_inserter(signal), wave_length, generator);
```

[[generate tramsform]]
### generate
Попробуем применить функциональный стиль к заполнению сигнала. Для этого нам пригодятся алгоритмы `generate` и `transform`. Первый имеет брата-близнеца — `generate_n`. Он использует функциональный объект, чтобы заполнить требуемый диапазон. Функциональный объект вызывается многократно и результаты этих вызовов используются для заполнения контейнера:

```cpp
std::vector<int16_t> signal;
signal.reserve(48000);

const double amplitude = 15000;
const int wave_length = 200;

// Используем mutable-лямбда функцию. Она может менять захваченные значения.
// В данном случае меняем i, который инициализируем в 
// квадратных скобках: i = 0.
auto generator = [=, i = 0]() mutable {
    // Возвратим значение. Алгоритм generate_n добавит его
    // в вектор.
    return amplitude * sin(2 * i++ * std::numbers::pi / wave_length);
};

std::generate_n(std::back_inserter(signal), wave_length, generator); 
```

### transform
 тоже записывает значения, но работает по-другому. Он читает элементы из одного диапазона, применяет к ним указанную функцию и записывает результат функции в другой диапазон. `transform` похож на `copy`, но добавляет применение функции.

Теперь используем `transform`, чтобы применить затухание:

```CPP
void ApplyDecay(std::vector<int16_t>& signal, size_t max_length) {
    size_t decay_length = std::min(signal.size(), max_length);
    
    auto transformer = [=, i = decay_length](int16_t source) mutable {
        double factor = double(i--) / decay_length;
        return static_cast<int16_t>(source * factor);
    };

    auto start_pos = signal.end() - decay_length;
    std::transform(start_pos, signal.end(), start_pos, transformer);
} 
```

меандр:
Так это будет выглядеть в коде:

```
const int wave_length = 200;
for (size_t offset = wave_length;
    offset < signal.size(); 
    offset += wave_length) {
    
    // Копируем первые wave_length элементов в позицию offset.
    std::copy_n(meander.begin(), wave_length, signal.begin() + offset);
} 
```

Такой цикл содержит ошибку, приводящую к проблеме, которую не всегда легко обнаружить. Последнее копирование может выйти за пределы вектора. При частоте дискретизации 48 000 этого не произойдёт, так как `wave_length` укладывается в размере вектора целое число раз. Но если уменьшить вектор, например, до 44 100 элементов, то программа войдёт в состояние неопределённости.

В условии цикла проверили: `offset < signal.size()`, а затем заполнили 200 элементов, начиная с `offset`. Условие гарантирует, что в векторе есть один свободный элемент, но не гарантирует, что их 200. Никаких дополнительных проверок `copy_n` не осуществляет.

Решить проблему можно двумя способами:

1. Будем проверять, что в векторе ещё есть 200 элементов:
    
    ```
     for (size_t offset = wave_length;
          // Меняем условие цикла. Обратите внимание на <=.
          offset + wave_length <= signal.size(); 
          offset += wave_length) {
         
         std::copy_n(meander.begin(), wave_length, signal.begin() + offset);
     }
      
    ```
    затухание
```cpp
    void ApplyDecay(std::vector<int16_t>& signal, size_t max_length) {
    // Не забудем уменьшить длину.
    size_t decay_length = std::min(signal.size(), max_length);

    // Используем цикл с убывающим счётчиком.
    for(size_t i = decay_length; i > 0; --i) {
        int16_t& sample = signal[signal.size() - i];
        // Умножим сигнал на убывающую величину.
        // Здесь она вычисляется.
        double factor = double(i) / decay_length;
        sample = static_cast<int16_t>(sample * factor);
    }
}

// ...
// main()

// Сделаем затухание длиной в 25 миллисекунд.
ApplyDecay(signal, 48000 / 1000 * 25);
```
```cpp
void ApplyDecay(std::vector<int16_t>& signal, size_t max_length) {
    size_t decay_length = std::min(signal.size(), max_length);
    
    auto transformer = [=, i = decay_length](int16_t source) mutable {
        double factor = double(i--) / decay_length;
        return static_cast<int16_t>(source * factor);
    };

    auto start_pos = signal.end() - decay_length;
    std::transform(start_pos, signal.end(), start_pos, transformer);
}
```