# lab03
Представьте, что вы стажер в компании "Formatter Inc.".
## Задание 1

Вам поручили перейти на систему автоматизированной сборки CMake. Исходные файлы находятся в директории formatter_lib. В этой директории находятся файлы для статической библиотеки formatter. Создайте CMakeList.txt в директории formatter_lib, с помощью которого можно будет собирать статическую библиотеку formatter.

Файл CMakeLists.txt в директории formatter_lib

```
cmake_minimum_required(VERSION 3.4)
project(formatter_lib)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_library(formatter_lib STATIC ${CMAKE_CURRENT_SOURCE_DIR}/formatter.cpp)
```

Собираем

```
$ cmake -B build
$ cmake --build build
```


##Задание 2

У компании "Formatter Inc." есть перспективная библиотека, которая является расширением предыдущей библиотеки. Т.к. вы уже овладели навыком созданием CMakeList.txt для статической библиотеки formatter, ваш руководитель поручает заняться созданием CMakeList.txt для библиотеки formatter_ex, которая в свою очередь использует библиотеку formatter.

Файл CMakeLists.txt в директории Formatter_ex_lib

```
cmake_minimum_required(VERSION 3.4)
project(formatter_ex_lib)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib formatter_lib_dir)

add_library(formatter_ex_lib STATIC ${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex.cpp)

target_include_directories(formatter_ex_lib PUBLIC
${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib
)

target_link_libraries(formatter_ex_lib formatter_lib)
```

Собираем

```
$ cmake -B build
$ cmake --build build
```



##Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек. Чтобы продемонстрировать как работать с библиотекой formatter_ex, вам необходимо создать два CMakeList.txt для двух простых приложений:

- hello_world, которое использует библиотеку formatter_ex;

Файл CMakeLists.txt в директории hello_world_application

```
cmake_minimum_required(VERSION 3.4)

project(hello_world)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib formatter_ex_lib_dir)

add_executable(hello_world ${CMAKE_CURRENT_SOURCE_DIR}/hello_world.cpp)

target_include_directories(hello_world PUBLIC
${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib
)

target_link_libraries(hello_world formatter_ex_lib)
```

Собираем

```
$ cmake -B build
$ cmake --build build
```

Запускаем

```
$ build/hello_world
```

![VirtualBox_kali-linux-2023 1-virtualbox-amd64_30_05_2023_17_36_28](https://github.com/luckydog228/lab03/assets/128289395/8f92f2a1-2341-45ff-a9cd-6b1c55aa537e)


- solver, приложение которое испольует статические библиотеки formatter_ex и solver_lib.

Диреткория solver_lib

Исправленный файл solver.cpp

```
#include "solver.h"

#include <cmath>
#include <stdexcept>

void solve(float a, float b, float c, float& x1, float& x2)
{
    float d = (b * b) - (4 * a * c);

    if (d < 0)
    {
        throw std::logic_error{"error: discriminant < 0"};
    }

    x1 = (-b - sqrtf(d)) / (2 * a);
    x2 = (-b + sqrtf(d)) / (2 * a);
}
```

CMakeLists.txt

```
cmake_minimum_required(VERSION 3.4)
project(solver_lib)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_library(solver_lib ${CMAKE_CURRENT_SOURCE_DIR}/solver.cpp)
```

Директория solver_application

CMakeLists.txt

```
cmake_minimum_required(VERSION 3.4)
project(solver)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib formatter_ex_lib_dir)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../solver_lib solver_lib_dir)
add_executable(solver ${CMAKE_CURRENT_SOURCE_DIR}/equation.cpp)
target_include_directories(formatter_ex_lib PUBLIC
${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib
${CMAKE_CURRENT_SOURCE_DIR}/../solver_lib
)
target_link_libraries(solver formatter_ex_lib solver_lib)
```

Собираем

```
$ cmake -B build
$ cmake --build build
```


Запускаем

```
$ build/solver 
```
![VirtualBox_kali-linux-2023 1-virtualbox-amd64_30_05_2023_17_37_42](https://github.com/luckydog228/lab03/assets/128289395/9a3d7953-b881-49ca-b35c-4ca9520bb3b8)
