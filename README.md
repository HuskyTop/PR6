# PR6 (12 варіант)

| № |    Термін    |    Значення    |
|---|--------------|----------------|
|1|**Uninitialized variables**|Змінна використовується до того,<br> як їй присвоїли значення|
|2|**Out-of-bounds access**|Звернення до деякої пам'яті поза межами<br> масиву|
|3|**Memory leakage**|Виділена пам'ять не звільнається і стає недоступна|
|4|**Use after free**|Використання вказівника після його звільнення|
|5|**Double free**|Подвійна операція звільнення вказівника|

## Приклади помилок та їх вирішення
### 1) Uninitialized variables
#### Код:
```
#include <stdio.h>

int main(int argc, char const *argv[])
{
    int b;
    printf("%d", b);
    return 0;
}
```
#### Вивід Valgrind:
```
Use of uninitialised value of size 4
```
#### Спосіб вирішення:
**Ініціалізувати змінн до їх використання**

### 2) Out-of-bounds access
#### Код:
```
#include <stdio.h>

int main(int argc, char const *argv[])
{
    char arr[10];
    arr[1] = 'H';
    arr[2] = 'e';
    arr[3] = 'y';
    arr[4] = '!';
    
    arr[15] = '$';
    return 0;
}
```
#### Вивід Valgrind:
```
Invalid write of size 4
    Address 0x... is not stack allocated
```
#### Спосіб вирішення:
**Робити перевірку на вихід за межі масиву**

### 3) Memory leakage
#### Код:
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char const *argv[])
{
    void *ptr = malloc(sizeof(long));
    ptr = NULL;
    return 0;
}
```
#### Вивід Valgrind:
```
LEAK SUMMARY
    definitely lost: 40 bytes in 1 blocks
```
#### Спосіб вирішення:
**Завжди додавати free() та передбачити всі ситуації, у яких в кінці коду він може не викликатися**

### 4) Use after free
#### Код:
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char const *argv[])
{
    int *ptr = malloc(sizeof(long));
    free(ptr);

    *ptr = 103;
    return 0;
}
```
#### Вивід Valgrind:
```
Invalid write of size 4
...freed by thread...
```
#### Спосіб вирішення:
**Не використовувати вказівник, який до цього звільнили за допомогою команди free()**

### 5) Double free
#### Код:
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char const *argv[])
{
    int *ptr = malloc(sizeof(long));
    
    free(ptr);
    free(ptr);
    return 0;
}
```
#### Вивід Valgrind:
```
Invalid free()
...freed by thread...
```
#### Спосіб вирішення:
**Не використовувати двічі підряд команду free() на одному й тому ж вказівнику**
