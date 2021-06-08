## Laboratory work III

Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере **CMake**

```sh
$ open https://cmake.org/
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab03** на сервисе **GitHub**
- [x] 2. Ознакомиться со ссылками учебного материала
- [x] 3. Выполнить инструкцию учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
$ export GITHUB_USERNAME=tishchka
$ alias edit=subl
```

```sh
$ cd ${GITHUB_USERNAME}/workspace
# Запоминаем текущий каталог в виртуальном стеке каталогов
$ pushd .
$ source scripts/activate
```

```sh
# Клонируем репозиторий, созданный во 2-ой лабораторной
# в папку с лабораторной №3
$ git clone https://github.com/${GITHUB_USERNAME}/lab02.git projects/lab03
Cloning into 'projects/lab03'...
remote: Enumerating objects: 19, done.
remote: Counting objects: 100% (19/19), done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 19 (delta 1), reused 13 (delta 0), pack-reused 0
Unpacking objects: 100% (19/19), 3.00 KiB | 341.00 KiB/s, done.

# Переходим в созданную директорию
$ cd projects/lab03
# Удаляем ссылку на репозиторий 2-ой лабораторной
$ git remote remove origin
# Добывляем ссылку с именем origin на созданный репозиторий 3-ей лабораторной
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab03.git
```

```sh
# Компилируем и собираем файл sources/print.cpp по стандарту 11-ого года
$ g++ -std=c++11 -I./include -c sources/print.cpp
# Выводим файлы созданного во время компиляции файла print.o
$ ls print.o
print.o

# Печатаем список символов из объектного файла print.o
$ nm print.o | grep print
00000000000000a1 t _GLOBAL__sub_I__Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSo
0000000000000000 T _Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSo
000000000000002a T _Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSt14basic_ofstreamIcS2_E

# Создаем архив файла print.o
$ ar rvs print.a print.o
ar: creating print.a
a - print.o
# Выводим формат файла
$ file print.a
print.a: current ar archive

# Компилируем и собираем файл examples/example1.cpp по стандарту 11-ого года
$ g++ -std=c++11 -I./include -c examples/example1.cpp
# Выводим файлы созданного во время компиляции файла
$ ls example1.o
example1.o

# Запускаем исходный код в example1.o и print.a,
# указв в командной строке имя исполняемого файла, который получим на выходе
$ g++ example1.o print.a -o example1
# Запускаем исполняемый файл и выводим результат его работы
$ ./example1 && echo
hello
```

```sh
# Компилируем и собираем файл examples/example2.cpp по стандарту 11-ого года
$ g++ -std=c++11 -I./include -c examples/example2.cpp
# Печатаем список символов из объектного файла example2.o
$ nm example2.o
                 U __cxa_atexit
                 U __dso_handle
0000000000000000 V DW.ref.__gxx_personality_v0
                 U _GLOBAL_OFFSET_TABLE_
0000000000000173 t _GLOBAL__sub_I_main
                 U __gxx_personality_v0
0000000000000000 T main
                 U __stack_chk_fail
                 U _Unwind_Resume
0000000000000126 t _Z41__static_initialization_and_destruction_0ii
                 U _Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSt14basic_ofstreamIcS2_E
                 U _ZNSaIcEC1Ev
                 U _ZNSaIcED1Ev
                 U _ZNSt14basic_ofstreamIcSt11char_traitsIcEEC1EPKcSt13_Ios_Openmode
                 U _ZNSt14basic_ofstreamIcSt11char_traitsIcEED1Ev
                 U _ZNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEC1EPKcRKS3_
                 U _ZNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEED1Ev
                 U _ZNSt8ios_base4InitC1Ev
                 U _ZNSt8ios_base4InitD1Ev
0000000000000000 r _ZStL19piecewise_construct
0000000000000000 b _ZStL8__ioinit

# Запускаем исходный код в example2.o и print.a,
# указв в командной строке имя исполняемого файла, который получим на выходе
$ g++ example2.o print.a -o example2
# Запускаем созданный исполняемый файл, 
# в результате чего создается текстовый файл log.txt
$ ./example2
# Выводим содержимое созданного файла
$ cat log.txt && echo
hello
```

```sh
# Удаляем все созданные во время компиляции файлы
$ rm -rf example1.o example2.o print.o
$ rm -rf print.a
$ rm -rf example1 example2
$ rm -rf log.txt
```

```sh
# Создаем CMakeLists.txt и записываем в него следующие команды
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4) # Минимальная версия cmake для работы
project(print) # Имя проекта
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
set(CMAKE_CXX_STANDARD 11) # Устанавливаем стандарт C++, требуемый для работы
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
# Добавляем файл /sources/print.cpp в текущую директорию в качестве статичной библиотеки
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include) # Включаем директорию include
EOF
```

```sh
# Генерируем файлы сборки в директорию build и настраиваем их
$ cmake -H. -B_build
-- The C compiler identification is GNU 9.3.0
-- The CXX compiler identification is GNU 9.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/gndavydov/GNDavydov/workspace/projects/lab03/_build

# Запускаем сборку проекта
$ cmake --build _build
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print

```

```sh
$ cat >> CMakeLists.txt <<EOF
# Добавляем исполняемые файлы example1.cpp, example2.cpp
add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
# Связываем исполняемые файлы с библиотекой print
target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF
```

```sh
# Запускаем сборку проекта
$ cmake --build _build
-- Configuring done
-- Generating done
-- Build files have been written to: /home/tishchka/tishchka/workspace/projects/lab03/_build
[ 33%] Built target print
Scanning dependencies of target example2
[ 50%] Building CXX object CMakeFiles/example2.dir/examples/example2.cpp.o
[ 66%] Linking CXX executable example2
[ 66%] Built target example2
Scanning dependencies of target example1
[ 83%] Building CXX object CMakeFiles/example1.dir/examples/example1.cpp.o
[100%] Linking CXX executable example1
[100%] Built target example1

# Запускаем сборку библиотеки
$ cmake --build _build --target print
[100%] Built target print

# Запускаем сборку исполняемых файлов
$ cmake --build _build --target example1
[ 50%] Built target print
[100%] Built target example1

$ cmake --build _build --target example2
[ 50%] Built target print
[100%] Built target example2
```

```sh
$ ls -la _build/libprint.a
-rw-rw-r-- 1 tishchka tishchka 3286 Mar 19 10:00 _build/libprint.a

$ _build/example1 && echo
hello
$ _build/example2
$ cat log.txt && echo
hello
$ rm -rf log.txt
```

```sh
# Клонируем репозиторий по данной ссылки в директорию tmp
$ git clone https://github.com/tp-labs/lab03 tmp
Cloning into 'tmp'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 91 (delta 0), reused 0 (delta 0), pack-reused 88
Unpacking objects: 100% (91/91), 1.03 MiB | 31.00 KiB/s, done.

# Перемещаем файл CMakeLists.txt из данной директории в рабочую
$ mv -f tmp/CMakeLists.txt .
# Удаляем созданную директорию tmp
$ rm -rf tmp
```

```sh
# Выводим содержимое файла CMakeLists.txt в терминал
$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)

project(print)

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME}
      RUNTIME DESTINATION bin
    )
  endforeach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
endif()

install(TARGETS print
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)

$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
-- Configuring done
-- Generating done
-- Build files have been written to: /home/gndavydov/GNDavydov/workspace/projects/lab03/_build

# Запускаем сборку install
$ cmake --build _build --target install
Install the project...
-- Install configuration: ""
-- Installing: /home/tishchka/tishchka/workspace/projects/lab03/_install/lib/libprint.a
-- Installing: /home/tishchka/tishchka/workspace/projects/lab03/_install/include
-- Installing: /home/tishchka/tishchka/workspace/projects/lab03/_install/include/print.hpp
-- Installing: /home/tishchka/tishchka/workspace/projects/lab03/_install/cmake/print-config.cmake
-- Installing: /home/tishchka/tishchka/workspace/projects/lab03/_install/cmake/print-config-noconfig.cmake

# Выводим дерево зависимостей
$ tree _install
_install
├── cmake
│   ├── print-config.cmake
│   └── print-config-noconfig.cmake
├── include
│   └── print.hpp
└── lib
    └── libprint.a
```

```sh
# Отправляем изменения на сервер
$ git add CMakeLists.txt
$ git commit -m"added CMakeLists.txt"
$ git push origin main
```

## Report

```sh
$ popd
$ export LAB_NUMBER=03
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

Представьте, что вы стажер в компании "Formatter Inc.".
### Задание 1
Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.

### Задание 2
У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш 
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки 
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
* *hello_world*, которое использует библиотеку *formatter_ex*;
* *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.

**Удачной стажировки!**

## Links
- [Основы сборки проектов на С/C++ при помощи CMake](https://eax.me/cmake/)
- [CMake Tutorial](http://neerc.ifmo.ru/wiki/index.php?title=CMake_Tutorial)
- [C++ Tutorial - make & CMake](https://www.bogotobogo.com/cplusplus/make.php)
- [Autotools](http://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)
- [CMake](https://cgold.readthedocs.io/en/latest/index.html)

```
Copyright (c) 2015-2021 The ISC Authors
```
