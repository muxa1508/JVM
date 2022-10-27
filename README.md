При выполнении кода в первую очередь происходит 
подгрузка класса `JvmComprehension` c помощью **ClassLoader**'ов в область памяти `Metaspace`.
Так же **ClassLoader**'ы выполняют проверку кода на необходимость использования сторонних классов, вызываемых в коде. 
Например классы `Object`, `Integer` и `System`, используемые в коде. В зависимости от уровня используемого 
класса выбирается **ClassLoader**, который который подгружает системные класы в `Metaspace`.

При запуске виртуальной машины Java выделяются две области памяти:
***Stack Memory*** (содержит имена и значения переменных, сгруппированные в пределах видимости методов) и ***Heap*** (куча), 
которая содержит информацию о загруженных типах обьектов.

В процессе работы виртуальная машина отслеживает использование обьектов и в случае отсутствия в них необходимости
выгружает их из раздела ***Heap*** памяти (процесс работы сборщика мусора).

1. При вызове метода `main` происходит создание фрейма `main` в ***Stack Memory***. В фрейм `main` заносится переменная `i` со значением `1` 
2. В ***Heap*** создается обьект типа `Object`, и в ***Stack Memory*** в фрейм `main` добавляется имя этого обьекта `o` с созданием связи между имененем и обьектом в ***Heap***
3. В фрейм `main` добавляется переменная `ii` со значением `2` и ссылкой на обьект `Integer`, который загружается в ***Heap***
4. Происходит вывод метода `printAll`. В ***Stack Memory*** создается новый фрейм `printAll`, в который копируются из `main` имена обьектов `i`, `ii`, `o` с их значениями и ссылками на свои обьекты в ***Heap***
5. В фрейм `printAll` добавляется имя переменной `uselessVar` со значением `700` и ссылкой на обьект `Integer` в ***Heap***
6. В ***Stack Memory*** создается новый фрейм `toString`, куда коопируется переменная o со ссылкой на обьект `Integer`.
Далее в ***Stack Memory*** создается фрейм `out.println` со ссылкой на обьект `System`, подгруженный в ***Heap***.
`toString` выполняется и передает свое значение в `out.println`, куда копируются значения `i` и `ii`.
После этого фрейм `toString` выгружается из памяти. 
Реализация `out.println` выводит на экран последовательность параметров результата выполнения `o.toString`, `i` и `ii` и выгружает фрейм `out.println` из ***Stack Memory***.
Так как обьект `System` в ***Heap*** больше не имеет связей с параметрами в ***Stack Memory***, то будет выгружен в случае запуска сборщика мусора.
Фрейм `printAll` также выгружается из памяти в связи со своим завершением.
7. В ***Stack Memory*** создается новый фрейм `out.println` со ссылкой на обьект `System` (если сборщик мусора не запускался, то на уже существующий в ***Heap***. В противном случае загружается новый обьект `System`).
В фрейм `out.println` передается переменная String `"finished"`.
После выполнения метода `out.println` выводится сообщение `finished` и из ***Stack Memory*** выгружается фрейм `out.println`, `System` в ***Heap*** помечается сборщиком мусора.
Метод `main` завершается. Фрейм `main` выгружается вместе с переменными `o`, `i` и `ii`. Обьекты `Integer` и `Object` помечаются сборщиком мусора к удалению и выгружаются с уже отмеченным `System`.
Виртуальная машина Java завершает свою работу.


