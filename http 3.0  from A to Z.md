## Portocols

HTTPS>HTTP>TLS>TCP>IP

https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/6ef36d1e-d91e-43e0-8732-f3e66ba9ea64/protocol-stack-h2-h3.png


## QUIC как замена TCP

QUIC не был заложена максимальная надежность как в TCP, поэтому он быстрее.   
Подразумевается что инфраструтура стала надежнее и избыточная надежность TCP перегружает интернет.  

## Потеря пакетов

TCP рассматривает все данные как единый файл или поток, и если хотя бы 1 част потеряна, то все осталньые пакеты задержатся.

## Сложность обновления протокола

Придумать как улучшить TCP легко, внедрить в масштабах интернета - сложно.   

## HTTP\3 это TCP\2 с HTTP\3 в придачу 

## UDP 

Проткол маскмимально эффективен 

## QUIC построенна оснвоне UDP

Используется, потому что UDP уже имплементирован в большинстве программ и устройств.   
Между тем, QUIC реализует все возможности TCP и не явлется копией UDP    

1. Интеграция с TLS
2. Independnet byte streams
3. Connection IDS
4. Use Frames

## Основная задача - сокртить время handshake

https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/f2240cb4-eb62-4054-ad19-0e72190e0a4f/connection-setup.png![image](https://user-images.githubusercontent.com/2846153/136894746-69275eff-b5cf-41ef-b6db-24172466d182.png)

TLS изначально не разрабатывался только под HTTP

## Шифрование QUIC

Шифрует так же заголовки, информацию транспортного уровня 
В то время как TLS шифрует только сами данные.   

https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/fbf86b42-8f20-4b27-aea5-f1fc164b2683/tcp-vs-quic-packetization.png![image](https://user-images.githubusercontent.com/2846153/136895071-d2a8beba-c9d6-4e93-909e-f64070fe08b0.png)

# QUICK multiply byte streams

HTTP 1.0 создает TCP поток для каждого файла.   
Браузеры ограничивают количество потоков.  
В современных странциах нужно загружать более 30 ресурсов.   
HTTP 2.0 был призван решить это.   
https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/900ea8f0-3782-4505-b1b6-99ca2954bbce/multiplexing-basic.png![image](https://user-images.githubusercontent.com/2846153/136895823-afb571ef-b8db-45d0-ad6d-804a5bae5a8c.png)

TCP не был создан для загрузки страниц.   
Он не знает о существовании каждого отдельного файла в потоке.   
Для протокла это все один файл.  
При потери одного пакета, будет запрошена ретрансмисия.   
TCP будет залатывать дырку, пока все остальная часть пакетов будет ждать очереди.  
Это называется HoL Head of Line

HTTP3 знает о внутренней струтутре стрима.   
Он не будет останавливать загрузку всех данных, из-за одной части.    

https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/7981cb82-395c-4484-8873-46fd92804b4d/hol-blocking-basic.png![image](https://user-images.githubusercontent.com/2846153/136896211-db2e80c2-08dc-48c4-a354-b34733d7d908.png)


# QUIC соединения живут дольше  

Для TCP соединение нужно:
1. IP клиента
2. Порт клиента
3. IP сервера
4. Порт сервера

Это все назывется "соединением".   

https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/9413b221-47e9-427b-b958-b0e62fe7f681/1-migration-tcp.png![image](https://user-images.githubusercontent.com/2846153/136896620-e3471156-171c-46db-bf24-85a58fa8ab8e.png)

## connection identifier (CID)

https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/e6ae0ec1-3b85-49a9-9707-ee21ce5b02b3/2-migration-single-cid.png![image](https://user-images.githubusercontent.com/2846153/136896960-c53ce2bb-189d-41cf-b62c-b57925238a00.png)

## QUIC changes the CID every time a new network is used.

Протокол заранее создается несколько ключей, котоыре посылаются зашифрованно и могут так же использоватся при смене сети.   

https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/715f189e-4ae6-4c4c-8db8-9fd8170049d8/3-migration-multi-cid.png![image](https://user-images.githubusercontent.com/2846153/136897162-59360563-4fed-4b8d-a999-f1a11f5f8f7e.png)

# QUIC IS FLEXIBLE AND EVOLVABLE

https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/88c76a7a-2752-4e5b-a829-290cd4951af3/quic-framing.png![image](https://user-images.githubusercontent.com/2846153/136897446-3c2d3fb1-15fe-406d-80e9-71d7cb057254.png)


