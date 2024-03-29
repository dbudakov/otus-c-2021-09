# Домашнее задание

## Статические структуры данных

**Цель**: В процессе выполнения ДЗ вы получите навык работы с различными текстовыми кодировками.  

1. Написать конвертор из заданной кодировки (cp-1251, koi8, iso-8859-5) в utf-8.  
2. Сторонние библиотеки не использовать.  

**Требования**:

1. Создано консольное приложение, принимающее аргументами командной строки входной файл, заданную кодировку и выходной файл.  
2. Конвертация из каждой из трёх указанных кодировок корректно отрабатывает(файлы для проверки прилагаются).  
3. Приложение корректно обрабатывает ошибки доступа к файлам.  
4. Код компилируется без warning'ов с ключами компилятора  
`-Wall -Wextra -Wpedantic -std=c11`.  
5. Далее успешность определяется code review.  
  
## Решение

Сборка выполняется через **Makefile** есть обработка ключей **all**, **clean**, **fclean**, **re**:

```sh
git clone https://github.com/dbudakov/c-2021-01
cd "c-2021-01/03.Static data structures/homework/"
make 
```

Программа принимает варианты параметров:  
**source_file**: *./other/cp1251.txt*, *./other/koi8.txt*, *./other/iso-8859-5.txt*  
**encoding**: *cp-1251*, *koi8*, *iso-8859-5*  

```sh
./to_utf8 <source_file> <encoding> <output_file>

# check list
./to_utf8 other/koi8.txt koi8 other/test1
./to_utf8 other/cp1251.txt cp-1251 other/test2
./to_utf8 other/iso-8859-5.txt iso-8859-5 other/test3

# check result
diff other/test1 other/test2
diff other/test1 other/test3
cat other/test1
```

## Дополнительно

Не получилось реализовать полную таблицу символов включая непечатные cимволы, потребуется время чтобы прийти к решению как достать все коды для cp1251, koi8-r, iso-5589-5 соответсвтвующие кодам символов в диапазоне 127-255 для utf8.  

## Источники

Обработка ошибок:  
<https://habr.com/ru/post/324642/>  
<https://ru.wikipedia.org/wiki/Errno.h>  

## code review
- Насчет таблиц - можно просто завести массив на 128 значений (т.к. первые 128 символов во всех кодировках одинаковые - это английские буквы, знаки препинания и проч.) из юникодных строк, которые можно попросту скопировать из той же википедии. Затем при обработке файла в одной из исходных восьмибитных кодировках, если символ принадлежит старшим 128 значениям, вычитаем из него 128 и получаем индекс в нашем массиве, и далее пишем в выходной файл строчку, которая находится в массиве по этому индексу.
- В цикле много раз вызывать realloc (что жутко неэффективно с точки быстродействия),
- Тем боле, что тут можно всё сильно упростить, сделав по аналогии с ft_koi8.c и ft_iso_8859_5.c - просто на ходу писать в выходной файл.
- как правило, среди UNIX-овых утилит командной строки не принято делать много "проверок на дурака", например, проверять, пуст ли выходной файл
- Вместо числовых значений кастомных ошибок было бы красивее завести enum с различными типами ошибок и использовать их в коде. Согласитесь, if (error == ERR_SAME_FILE) 
 - Отвечая на вопрос по количеству исходников, разбивать проект на несколько файлов можно и нужно (в разумных пределах), это, как правило, лишь повышает читаемость и удобство восприятия.
