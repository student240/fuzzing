# prz6 fuzzing Bogomolov
# Задание 2. Фаззинг-тестирование приложения
Первым шагом выполним команды:

`docker pull aflplusplus/aflplusplus`

`docker run --privileged -ti -v $HOME:/home aflplusplus/aflplusplus`

![image](https://github.com/student240/fuzzing/assets/128815643/8be2225a-6432-4bd4-8920-47e9b9504c66)

Создадим новый каталог

![image](https://github.com/student240/fuzzing/assets/128815643/7d99d5c1-46db-4044-8f8e-35b52e59c1fa)

Загрузим  и распакуем tcpdump-4.9.2.tar.gz

` wget https://github.com/the-tcpdump-group/tcpdump/archive/refs/tags/tcpdump-4.9.2.tar.gz `

` tar -xzvf tcpdump-4.9.2.tar.gz `

![image](https://github.com/student240/fuzzing/assets/128815643/e05055b9-f5ce-4722-8da8-d73e7740c8a0)

Нам также нужно загрузить libpcap, кроссплатформенную библиотеку, которая необходима TCPdump. Загрузим и распакуем libpcap-1.8.0.tar.gz:

`wget https://github.com/the-tcpdump-group/libpcap/archive/refs/tags/libpcap-1.8.0.tar.gz`

`tar -xzvf libpcap-1.8.0.tar.gz`

![image](https://github.com/student240/fuzzing/assets/128815643/f0d78be2-1400-4787-84de-67b2d5c23583)

Переименовываем

![image](https://github.com/student240/fuzzing/assets/128815643/2ef0e699-f853-4abe-8036-3d3dded8f9d9)

Соберём и установим libpcap:

`cd $HOME/fuzzing_tcpdump/libpcap-1.8.0/`

`./configure --enable-shared=no`

`make`

![image](https://github.com/student240/fuzzing/assets/128815643/a66ab681-b892-486c-87fe-8689e946acff)

![image](https://github.com/student240/fuzzing/assets/128815643/3f0e87d5-8649-4bfd-a68c-f610173c690b)

Теперь мы можем собрать и установить tcpdump:

`cd $HOME/fuzzing_tcpdump/tcpdump-tcpdump-4.9.2/`

`./configure --prefix="$HOME/fuzzing_tcpdump/install/"`

`make`

`make install`

![image](https://github.com/student240/fuzzing/assets/128815643/5277a9d5-9089-4118-9724-854b0390ef35)

![image](https://github.com/student240/fuzzing/assets/128815643/08f1cc56-0d82-44d7-947a-7d573b3e7aa6)

![image](https://github.com/student240/fuzzing/assets/128815643/ef82a797-e3f2-4d63-921b-4b3d915e159a)

Проверим правильность работы

![image](https://github.com/student240/fuzzing/assets/128815643/5ace36fc-c22f-41bc-80aa-f9bd4b6c46bc)

Создадим исходный корпус

![image](https://github.com/student240/fuzzing/assets/128815643/d033816e-25d6-4330-ab74-f93b33a1d00b)

 Очищаем все ранее скомпилированные объектные файлы и исполняемые файлы:
 
`rm -r $HOME/fuzzing_tcpdump/install`

`cd $HOME/fuzzing_tcpdump/libpcap-1.8.0/`

`make clean`

`cd $HOME/fuzzing_tcpdump/tcpdump-tcpdump-4.9.2/`

`make clean`

![image](https://github.com/student240/fuzzing/assets/128815643/2f0d74ad-a219-4547-9f1d-c30b78eeaf7e)

![image](https://github.com/student240/fuzzing/assets/128815643/b6cbe5fb-c0e3-42eb-ab82-36e2d6a4783a)

![image](https://github.com/student240/fuzzing/assets/128815643/00508661-a648-42a9-8666-59d121f747e7)

![image](https://github.com/student240/fuzzing/assets/128815643/1f0a6113-ee67-4358-85da-2fbc0f51011c)

![image](https://github.com/student240/fuzzing/assets/128815643/3876f99b-659d-41e2-80ca-1a6f6537a9dc)

Теперь устанавливаем AFL_USE_ASAN=1 перед вызовом configure и make:

`cd $HOME/fuzzing_tcpdump/libpcap-1.8.0/`

`export LLVM_CONFIG="llvm-config-11"`

`CC=afl-clang-lto ./configure --enable-shared=no --prefix="$HOME/fuzzing_tcpdump/install/"`

`AFL_USE_ASAN=1 make`

`cd $HOME/fuzzing_tcpdump/tcpdump-tcpdump-4.9.2/`

`AFL_USE_ASAN=1 CC=afl-clang-lto ./configure --prefix="$HOME/fuzzing_tcpdump/install/"`

`AFL_USE_ASAN=1 make`

`AFL_USE_ASAN=1 make install`

![image](https://github.com/student240/fuzzing/assets/128815643/33716ffd-7512-4026-9595-4eb9d428b14a)

![image](https://github.com/student240/fuzzing/assets/128815643/e8d6f600-3c80-4cce-96ed-c4fa7fe5893d)

![image](https://github.com/student240/fuzzing/assets/128815643/b9d52941-57cc-4cea-a1fd-b0714dd230ff)

![image](https://github.com/student240/fuzzing/assets/128815643/a160e3f8-b0d7-4a39-9c94-abe29dead7e1)

Теперь можно запустить фаззер с помощью следующей команды:

`afl-fuzz -m none -i $HOME/fuzzing_tcpdump/tcpdump-tcpdump-4.9.2/tests/ -o $HOME/fuzzing_tcpdump/out/ -s 123 -- $HOME/fuzzing_tcpdump/install/sbin/tcpdump -vvvvXX -ee -nn -r @@`

![image](https://github.com/student240/fuzzing/assets/128815643/e13af069-0ccd-47d2-afe2-2d3da5df1099)
