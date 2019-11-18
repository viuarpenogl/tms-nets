ИНСТРУКЦИЯ
==========

## Описание

Данный репозиторий содержит в себе реализацию генератора участков (t, s)-последовательностей с основанием 2, созданного на основе
алгоритма, предложенного Гаральдом Нидеррайтером в 1987 году в работе "Low-Discrepancy and Low-Dispersion Sequences".

## Ветви репозитория

  * **master** - главная ветвь; в ней располагается последняя стабильная версия проекта;
  * **development** - ветвь, содержащая самые актуальные наработки;
  * **gh-pages** - техническая ветвь, хранящая в себе файлы документации;
  * **nets-tests** - неактуальная, рассматривается к удалению.

Объединение **gh-pages** с другими ветвями, а также самостоятельное изменение её содержимого не допускается.

## Документация

Документация к стабильной версии программы доступна по адресу: https://jointpoints.github.io/tms-nets/ (на английском языке),
генерируется автоматически с помощью утилиты Doxygen при каждом изменении в ветви **master**.

## Технические требования

Для компиляции и сборки программы, использующей данный генератор, необходимо иметь:

  * компилятор, поддерживающий C++17;
  * 64-битный процессор с 2 и более ядрами;
  * (желательно) утилиту CMake версии 3.8 и выше.

## Использование генератора

Для использования генератора потребуются файлы `niederreiter2.hpp`, `irrpoly/checker.hpp`, `irrpoly/gf.hpp`, `irrpoly/polynomial.hpp`,
`irrpoly/polynomialgf.hpp`. В своём исходном коде пропишите строку

    #include "<your/path/to/our/files/>niederreiter2.hpp"

Генератор представлен шаблонным классом `Nied2Generator`. Сигнатуры конструкторов:

1.

    Nied2Generator<typename UIntType, uint32_t NBITS>(uint32_t const dim, bool const in_parallel)

  * `UIntType` - тип целочисленных переменных, который следует использовать при генерации; **значение по умолчанию** - `uint64_t`;
  * `NBITS` - число доступных бит в `UIntType` для хранения промежуточных результатов расчётов, **максимальное значение** - `sizeof(UIntType) * 8 - 1`; **значение по умолчанию** - `63`;
  * `dim` - размерность пространства;
  * `in_parallel` - указывает, следует ли генерировать неприводимые многочлены последовательно (`false`) или параллельно (`true`); **значение по умолчанию** - `false`.

Этот конструктор следует использовать, если требуется сгенерировать максимально плотно распределённые точки.

2.

    Nied2Generator<typename UIntType, uint32_t NBITS>(std::vector<uint32_t> const &degrees_of_irred)

  * `UIntType` - тип целочисленных переменных, который следует использовать при генерации; **значение по умолчанию** - `uint64_t`;
  * `NBITS` - число доступных бит в `UIntType` для хранения промежуточных результатов расчётов, **максимальное значение** - `sizeof(UIntType) * 8 - 1`; **значение по умолчанию** - `63`;
  * `degrees_of_irred` - вектор, задающий степени, которые следует использовать при генерации неприводимых многочленов; для *i*-ой компоненты будет сгенерирован многочлен `degrees_of_irred[i-1]`-ой степени при i от 1 до `degrees_of_irred.size()`.

Этот конструктор следует использовать, есил требуется вручную настроить плотность по каждой компоненте.

Примеры использования генератора с исчерпывающими комментариями можно найти в `main.cpp`. Для сборки примера возпользуйтесь файлом `CMakeLists` и утилитой CMake.

## Тестирование

Папка `tests` содержит функции тестирования корректности работы алгоритма. Пример их использования можно найти в `tests/main.cpp`. Для запуска тестов понадобятся файлы `niederreiter2.hpp`, `irrpoly/checker.hpp`, `irrpoly/gf.hpp`, `irrpoly/polynomial.hpp`,
`irrpoly/polynomialgf.hpp`, `tests/tests.hpp`, `tests/tests_routines.hpp`, `tests/tests_routines.cpp`. В своём исходном коде пропишите строку

    #include "<your/path/to/our/files/>tests/tests.hpp"

В скором времени для тестов планируется завести отдельный `CMakeLists`.

Используемые тесты:

1.

    void nied2_check_definition(Nied2Generator<UIntType, NBITS> *generator, uint64_t amount)

Этот тест проверяет, являются ли `amount` точек, сгенерированные генератором `generator`, (t, m, s)-сетью в классическом смысле Нидеррайтера. Тест может давать ложноположительные результаты, если генерируемые точки не уникальны.

2.

    void nied2_check_uniqueness(Nied2Generator<UIntType, NBITS> *generator, uint64_t amount)

Этот тест в процессе разработки и адаптации под новую объектно-ориентированную архитектуру проекта.