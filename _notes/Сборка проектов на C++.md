# Препроцессинг
Единица трансляции - файл после препроцессинга
# Linkage
Исходные файлы C++ компилируются независимо друг от друга. В результате компиляции файлов получаются объектные файлы который линковщик должен объединить в исполняемый файл. Каждое имя в исходном файле имеет внешнюю связь - доступно в других файлах и внутреннюю(статическую) связь - не доступно в других файлах.

## static
По умолчанию функции и глобальные переменные имеют внешнюю связь, мы можем это изменить приписав им ключевое слово static.

Что значит внутреннюю? То есть если у нас в одном файле объявлена переменная, мы подключаем ее в 2 других файла и в этих файлах будет копия это переменной, а не она сама

```cpp
//header.hpp:    
static int variable = 42;
//file1.hpp
void function1();
//file2.hpp
void function2();
//file1.cpp
#include "header.hpp" 
void function1() { variable = 10; }
//file2.cpp
#include "header.hpp" 
void function2() { variable = 123; }
//main.cpp
#include "header.hpp" 
#include "file1.hpp" 
#include "file2.hpp" 
#include <iostream> 
auto main() -> int 
{ 
	function1(); //variable = 10
	function2(); //variable = 123
	std::cout << variable << std::endl;//output 42 
}
```

Можно также использовать анонимные пространства имен, делают тоже самое, что и static

```cpp
namespace//компилятор генерирует сам какое-то имя для него и ниже использует using декларацию
{
	int variable = 42;
}

using namespace name_get_compiler
```

Для использования внутренних связей рекомендуется использовать анонимный namespace и использовать их, да и как все пространства имен только в файлах .cpp, не в заголовочных

## extern
Предоставляет внешнюю линковку, используется для объявления переменной, т.е. компилятор не будет выделять место, это мы должны сделать

```cpp
extern int x;
int x { 3 };
---
extern int x { 3 };
```

extern "C" нужен для того, чтобы объявлять функции реализация которых находится в сишных библиотеках. Поскольку в C нет [[Перегрузка функций#Манглирование|манглирования]], а в C++ есть.

```cpp
#include <cstdint>

extern "C" uint32_t fibonacci(uint32_t n);
uint32_t fibonacci(uint32_t n);//написав так, имя функции будет такое _Z9fibonaccij, а в объектном сишном файле функции называется fibonacci, поэтому пишем extern "C"
extern "C" int printf(char const*, ...);
int main()
{
	uint32_t res = fibonacci(5);
	printf("%d", res);
}
```

К слову такая программа компилируется, хоть мы и не подключили библиотеку где находится printf, поскольку нам ее подкалывает линкер.

Также благодаря extern "C" можно запретить перегрузку функций.
# ВЛадимиров
Tool-chain - это система компиляции, основные это - GNU Tool-chain, LLVM Tool-chain, Visual Studio

![[../Files/Pasted image 20220505123509.png]]

```shell
gcc -O2 program.c -o program.x
```

Что происходит после `enter`? Сначала вызывается компилятор(cc1) - формирует ассемблерный код, потом ассемблер(as) - формирует объектный файл, потом линкер(collect2) - связывается объектные файлы.

![[../Files/Pasted image 20220505124135.png]]

![[../Files/Pasted image 20220505142958.png]]
## Компилятор
Компилятор состоит из:
![[../Files/Pasted image 20220505124436.png]]

![[../Files/Pasted image 20220505131956.png]]

![[../Files/Pasted image 20220505133139.png]]

### Лексический анализ
Разбирает код на лексемы(токены), лексемы - это пара тип и значение

![[../Files/Pasted image 20220505125041.png]]

Препроцессинг является часть фазы лексера, он занимается:

1.  Единица трансляции отображается в базовый набор символов, обрабатываются юникодные символы
2. Замена диграфов и триграфов в устаревших версиях программы, когда к примеру вместо || писали ??! и т.д.
3. Конкатенируются строки, разбитые через \
4. Комментарии заменяются на пробельные символы
5. Файл разбивается на препроцессинговые токены
6. Исполняются директивы препроцессора (include, define, прагмы)
7. Заменяются escape-последовательности
8. Соединяются строковые [[литералы]] `const char* s = "Hello" "World` преобразуется в `HelloWorld`
9. Пробелы перестают иметь значение
10. Препроцессинговые токены становятся токенами
11. Проводится синтаксический анализ

### Синтаксический анализ
1. Синтаксический разбор
2. Построение AST

```cpp
ifstream datafile ("ins.dat");
list<int> data (istream_iterator<int>(datafile), istream_iterator<int>());//если это пишется в глобальном скоупе, то это объявление функции, а не создание переменной типа data
```

```cpp
fact.c
unsigned
fact (unsigned x) {
	if (x < 2) return 1;
		return x * fact (x-1);
}

clang -cc1 -ast-view fact.c
```

![](ast.png)

### Семантический анализ

## Ассемблер
Для того, чтобы посмотреть сгенерированный ассемблерный код можно воспользоваться `objdump`

```cpp
fact.c
unsigned
fact (unsigned x) {
	if (x < 2) return 1;
		return x * fact (x-1);
}

gcc -O0 -c fact.c -o fact.o
objdump -d fact.o > fact.dis syntax AT&T
objdump -d fact.o -M intel > fact.dis syntax Intel

0000000000000000 <_Z4factj>:  
   0:  f3 0f 1e fa            endbr64   
   4:  55                     push   %rbp  
   5:  48 89 e5               mov    %rsp,%rbp  
   8:  48 83 ec 10            sub    $0x10,%rsp  
   c:  89 7d fc               mov    %edi,-0x4(%rbp)  
   f:  83 7d fc 01            cmpl   $0x1,-0x4(%rbp)  
  13:  77 07                  ja     1c <_Z4factj+0x1c>  
  15:  b8 01 00 00 00         mov    $0x1,%eax  
  1a:  eb 11                  jmp    2d <_Z4factj+0x2d>  
  1c:  8b 45 fc               mov    -0x4(%rbp),%eax  
  1f:  83 e8 01               sub    $0x1,%eax  
  22:  89 c7                  mov    %eax,%edi  
  24:  e8 00 00 00 00         callq  29 <_Z4factj+0x29>  
  29:  0f af 45 fc            imul   -0x4(%rbp),%eax  
  2d:  c9                     leaveq   
  2e:  c3                     retq
```

## Linking
Сборка объектных файлов в исполняемый файл или динамическую библиотеку, разрешение зависимостей, проверка многократного определения.