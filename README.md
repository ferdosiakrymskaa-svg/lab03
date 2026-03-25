

# Лабораторная работа №3: Введение в CMake

## Цель работы
Познакомиться с системой автоматизации сборки CMake, научиться создавать статические библиотеки и приложения, освоить основные команды CMake.

## Выполнение работы

### 1. Подготовка окружения

```bash
export GITHUB_USERNAME=ferdosiakrymskaa-svg
cd /home/ilyasov_ilya/ferdosiakrymskaa-svg/workspace
pushd .
source scripts/activate
```

Здесь я создал переменную с именем пользователя GitHub, перешел в рабочую директорию, сохранил текущий путь в стек и активировал скрипт окружения.

### 2. Клонирование репозитория

```bash
git clone https://github.com/ferdosiakrymskaa-svg/lab02.git projects/lab03
cd projects/lab03
git remote remove origin
git remote add origin https://github.com/ferdosiakrymskaa-svg/lab03.git
```

Склонировал предыдущую лабораторную работу в новую папку lab03, удалил старую привязку к удаленному репозиторию и добавил новый.

### 3. Ручная сборка статической библиотеки

```bash
g++ -std=c++11 -I./include -c sources/print.cpp
```

Скомпилировал исходный файл print.cpp в объектный файл. Ключ -std=c++11 задает стандарт языка, -I./include указывает путь к заголовочным файлам, -c означает только компиляцию без линковки.

```bash
ls print.o
```

Убедился, что объектный файл создался.

```bash
nm print.o | grep print
```

Посмотрел, какие символы (функции) есть в объектном файле. Увидел имена функций print.

```bash
ar rvs print.a print.o
```

Создал статическую библиотеку print.a из объектного файла print.o. Ключ r - добавить файл, v - подробный вывод, s - создать индекс.

```bash
file print.a
```

Проверил, что файл действительно является архивом.

### 4. Сборка и запуск примеров

```bash
g++ -std=c++11 -I./include -c examples/example1.cpp
g++ example1.o print.a -o example1
./example1 && echo
```

Скомпилировал первый пример, слинковал его со статической библиотекой и запустил. Программа вывела "hello".

```bash
g++ -std=c++11 -I./include -c examples/example2.cpp
nm example2.o
g++ example2.o print.a -o example2
./example2
cat log.txt && echo
```

Аналогично собрал второй пример, запустил его и проверил содержимое файла log.txt. Там тоже оказалось "hello".

### 5. Очистка

```bash
rm -rf example1.o example2.o print.o print.a example1 example2 log.txt
```

Удалил все временные файлы, созданные при ручной сборке.

### 6. Создание CMakeLists.txt

```bash
cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(print)
EOF

cat >> CMakeLists.txt <<EOF
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF

cat >> CMakeLists.txt <<EOF
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
EOF

cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF
```

Постепенно создал файл CMakeLists.txt. Сначала указал минимальную версию CMake и название проекта. Затем задал стандарт C++11. После добавил инструкцию для создания статической библиотеки из исходного файла print.cpp. В конце указал директорию с заголовочными файлами.

### 7. Сборка через CMake

```bash
cmake -H. -B_build
```

Запустил конфигурацию проекта. Ключ -H. указывает, где находится CMakeLists.txt, а -B_build задает директорию для сборки.

```bash
cmake --build _build
```

Запустил сборку. CMake сгенерировал Makefile и скомпилировал библиотеку.

### 8. Добавление исполняемых файлов в CMake

```bash
cat >> CMakeLists.txt <<EOF

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
EOF

cat >> CMakeLists.txt <<EOF

target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF
```

Добавил в CMakeLists.txt инструкции для создания исполняемых файлов example1 и example2, а также указал, что они должны линковаться с библиотекой print.

```bash
cmake --build _build
```

Пересобрал проект с обновленным CMakeLists.txt.

```bash
cmake --build _build --target print
cmake --build _build --target example1
cmake --build _build --target example2
```

Собрал отдельно библиотеку и каждый из примеров.

### 9. Проверка

```bash
ls -la _build/libprint.a
_build/example1 && echo
_build/example2
cat log.txt && echo
rm -rf log.txt
```

Проверил, что библиотека создалась, запустил примеры и убедился, что они работают.

### 10. Использование готового CMakeLists.txt

```bash
git clone https://github.com/tp-labs/lab03 tmp
mv -f tmp/CMakeLists.txt .
rm -rf tmp
```

Склонировал репозиторий с готовым CMakeLists.txt, заменил свой файл и удалил временную папку.

### 11. Установка проекта

```bash
cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
cmake --build _build --target install
tree _install
```

Собрал проект с указанием директории для установки. После сборки выполнил установку. Команда tree показала структуру установленных файлов.

### 12. Сохранение в Git

```bash
git add CMakeLists.txt
git commit -m "added CMakeLists.txt"
git push origin master
```

Добавил файл CMakeLists.txt в Git, закоммитил и отправил на GitHub.

---

## Домашнее задание

### Задание 1. Создание formatter_lib

В папке formatter_lib я создал файл CMakeLists.txt. В нем я указал минимальную версию CMake, название проекта, установил стандарт C++11 и добавил инструкцию для создания статической библиотеки из файла formatter.cpp. Также добавил путь к заголовочным файлам.

```cmake
cmake_minimum_required(VERSION 3.10)
project(formatter_lib)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter STATIC formatter.cpp)

target_include_directories(formatter PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
```

### Задание 2. Создание formatter_ex

В папке formatter_ex я создал CMakeLists.txt для библиотеки-расширения. Здесь нужно было подключить библиотеку formatter, поэтому я использовал target_link_libraries. Заголовки formatter нужны только при компиляции этой библиотеки, поэтому я указал PRIVATE.

```cmake
cmake_minimum_required(VERSION 3.10)
project(formatter_ex)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter_ex STATIC formatter_ex.cpp)

target_include_directories(formatter_ex PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_include_directories(formatter_ex PRIVATE ../formatter_lib)

target_link_libraries(formatter_ex formatter)
```

### Задание 3. Создание приложений

#### Приложение hello_world

Создал CMakeLists.txt в папке hello_world. Здесь нужно было создать исполняемый файл и подключить библиотеки formatter_ex и formatter. Также добавил пути к заголовкам обеих библиотек.

```cmake
cmake_minimum_required(VERSION 3.10)
project(hello_world)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(hello_world hello_world.cpp)

target_include_directories(hello_world PRIVATE 
    ../formatter_lib
    ../formatter_ex
)

target_link_libraries(hello_world formatter_ex formatter)
```

#### Библиотека solver_lib

Создал CMakeLists.txt в папке solver_lib. Здесь просто создается статическая библиотека из solver.cpp.

```cmake
cmake_minimum_required(VERSION 3.10)
project(solver_lib)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(solver STATIC solver.cpp)

target_include_directories(solver PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
```

#### Приложение solver

Создал CMakeLists.txt в папке solver. Здесь нужно было подключить три библиотеки: solver, formatter_ex и formatter. Я назвал исполняемый файл solver_app, чтобы не было конфликта имен с библиотекой solver.

```cmake
cmake_minimum_required(VERSION 3.10)
project(solver_app)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(solver_app equation.cpp)

target_include_directories(solver_app PRIVATE 
    ../solver_lib
    ../formatter_lib
    ../formatter_ex
)

target_link_libraries(solver_app solver formatter_ex formatter)
```

#### Главный CMakeLists.txt

В корневой папке homework создал главный CMakeLists.txt, который подключает все поддиректории.

```cmake
cmake_minimum_required(VERSION 3.10)
project(FormatterProject)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(formatter_lib)
add_subdirectory(formatter_ex)
add_subdirectory(solver_lib)
add_subdirectory(hello_world)
add_subdirectory(solver)
```

### Сборка и проверка

```bash
cd /home/ilyasov_ilya/ferdosiakrymskaa-svg/workspace/projects/lab03/homework
rm -rf build
mkdir build && cd build
cmake ..
make
```

Сначала я удалил старую папку build, если она была. Затем создал новую, перешел в нее и запустил конфигурацию CMake. После успешной конфигурации выполнил сборку командой make.

**Результат сборки:**
```
[ 20%] Built target formatter
[ 40%] Built target formatter_ex
[ 60%] Built target solver
[ 80%] Built target hello_world
[100%] Built target solver_app
```

```bash
./hello_world/hello_world
```

**Вывод:**
```
-------------------------
hello, world!
-------------------------
```

```bash
echo "1 -3 2" | ./solver/solver_app
```

**Вывод:**
```
-------------------------
x1 = 1.000000
-------------------------
-------------------------
x2 = 2.000000
-------------------------
```

### Отправка на GitHub

```bash
cd /home/ilyasov_ilya/ferdosiakrymskaa-svg/workspace/projects/lab03/homework
git add .
git commit -m "add CMakeLists.txt for all components"
git push origin main
```

Добавил все новые файлы в Git, создал коммит и отправил изменения на GitHub.

## Выводы

В ходе выполнения лабораторной работы я научился:

1. Собирать статические библиотеки вручную с помощью g++ и ar
2. Создавать CMakeLists.txt для статических библиотек
3. Указывать зависимости между библиотеками с помощью target_link_libraries
4. Понимать разницу между PUBLIC и PRIVATE при добавлении директорий с заголовками
5. Собирать проекты с помощью CMake (команды cmake и make)
6. Устанавливать проект с помощью CMAKE_INSTALL_PREFIX
7. Использовать Git для сохранения изменений


