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

## QUIC IS FLEXIBLE AND EVOLVABLE

https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/88c76a7a-2752-4e5b-a829-290cd4951af3/quic-framing.png![image](https://user-images.githubusercontent.com/2846153/136897446-3c2d3fb1-15fe-406d-80e9-71d7cb057254.png)

# PART 2
https://www.smashingmagazine.com/2021/08/http3-performance-improvements-part2/

Поговорим о производительности.
QUIC имеет приемущество только для медленных сетей. 

## Термины
Два самых важных параметра:  latency and bandwidth.
(задержка, пропусная способность). 
  
latency - время полета пакета от A до Б
Задержка туда и обратно называется round-trip time (RTT)

Bandwidth - количество пакетов посылаемое за опр. время. 

## Контоль нагрузки

Вопрос в том насколько эффективно протокол использует физическое оборудование.    
Мы не можем предугадать максимальную пропускную способность.   
Механизм интерента не обладает такой функцией.  

Механизм TCP congestion control.
В начале соединия TCP посылает малое кол-во пакетов, чтобы получить подвержение что пакеты дошли.  
Называется slow start.   
Если пакеты теряются, уменьшается send rate.   
https://hpbn.co/building-blocks-of-tcp/#congestion-avoidance-and-control![image](https://user-images.githubusercontent.com/2846153/137256349-acfa6fad-68a0-4aca-8a4f-aba1d3c9eeb9.png)

You might have heard the recommendation to keep your critical data to smaller than 14 KB.)

UDP не имеет congestion control

QUIC actually uses very similar bandwidth-management techniques as TCP.
However, TCP is typically implemented in the operating system’s (OS’) kernel,
In contrast, most QUIC implementations are currently being done in “user space” (where we typically run native apps) and are made open source, 

This means that it will not magically download your website resources much more quickly than TCP


## Round Trip Connection Setup

A second performance aspect is about how many round trips it takes before you can send useful HTTP data (say, page resources) on a new connection


https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/9f4c69d1-5ab6-4ca2-ad1e-ec68d99dc9ab/connection-setup-1.png![image](https://user-images.githubusercontent.com/2846153/137257163-fcb588c8-0d19-4912-9c7a-d64a5942e09f.png)
TCP simply does not support sending non-TCP stuff during the handshake.

QUIC was designed with TLS in mind from the star
QUIC handshake will take only one round trip in total to complete, which is one round trip less than TCP + TLS

We can now safely send our first HTTP request along with the QUIC/TLS handshake, saving another round trip!
This method is often called 0-RTT

https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/ddbd604f-cec4-4e61-9172-707eda88bc99/connection-setup-2.png![image](https://user-images.githubusercontent.com/2846153/137257768-5362fa25-89f4-47b9-99d6-be4cfcf9693d.png)

The QUIC server has no way of detecting whether the IP was spoofed, because this is the very first packet(s) it is seeing from that client.

https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/64b9318e-3f18-4852-b911-409033d8645b/amplification.png![image](https://user-images.githubusercontent.com/2846153/137257934-0b511834-44e9-426b-8750-f1df13497a01.png)


If the server then simply starts sending the large file back to the spoofed IP, it could end up overloading the victim’s network bandwidth
This is called a reflection, or amplification, attack, and it’s a significant way that hackers execute distributed denial-of-service (DDoS) attacks
this doesn’t happen when 0-RTT over TCP + TLS is being used

 the QUIC server’s 0-RTT reply will be capped at just 4 to 6 KB (including other QUIC and TLS overhead!)
 
 Cloudflare only allows HTTP GET requests without query parameters in 0-RTT. These limit the usefulness of 0-RTT even more.
 
 QUIC’s faster connection set-up with 0-RTT is really more of a micro-optimization than a revolutionary new feature.
 As such, this feature will mostly shine either if your users are on networks with very high latency 
 
 ## Connection Migration
 keeping existing connections intact
 QUIC’s connection IDs (CIDs) allow it to perform connection migration when switching networks
 As such, it occurs only when you move between completely different networks, which I’d say doesn’t happen all that often.
 
 https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/6a0b9339-6976-458d-afc2-4a0cb97a7291/4-migration-src-dst-cid.png![image](https://user-images.githubusercontent.com/2846153/137259080-f56f902a-0526-4a8a-bf54-99c746cc4578.png)
This is done to allow each endpoint to choose its own CID format

## Head-Of-Line Blocking Removal

 high amount of packet loss by mitigating the head-of-line (HoL) blocking problem
 a single TCP packet loss can delay data for multiple in-transit resources because TCP’s bytestream abstraction considers all data to be part of a single file
 QUIC, on the other hand, is intimately aware that there are multiple concurrent bytestreams and can handle loss on a per-stream basis
 the stream data is multiplexed onto a single connection. This multiplexing can happen in many different ways.
 
 As you may know, some resources can be render blocking. This is the case for CSS files and for some JavaScript in the HTML head element.
 
 https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/46ae54c1-2985-4c47-9ebe-18686bbfc4ce/multiplexing-render-blocking.png![image](https://user-images.githubusercontent.com/2846153/137260428-3266e83f-1fcc-4981-9548-17db343a08ad.png)


However, that doesn’t mean that sequential multiplexing is always the best, because some (mostly non-render-blocking) resources (such as HTML and progressive JPEGs) can actually be processed and used incrementally. 
 
 Still, for most web-page resources, it turns out that sequential multiplexing performs best. 
 
 ## PACKET LOSS RESILIENCE
 
 https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/05412377-b92f-4adc-9725-4a3b4b4d602a/hol-blocking-rr-sequential.png![image](https://user-images.githubusercontent.com/2846153/137260736-10854495-ae54-4d14-9c2e-6e26ab4fe4b7.png)

 We see that we have a kind of contradiction: Sequential multiplexing (AAAABBBBCCCC) is typically better for web performance, but it doesn’t allow us to take much advantage of QUIC’s HoL blocking removal. Round-robin multiplexing (ABCABCABCABC) would be better against HoL blocking, but worse for web performance. As such, one best practice or optimization can end up undoing another.
 
 ## UDP And TLS Performance
 https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/cffc7945-bd57-459f-b6d3-ed06f51b30ad/kernel-user-space.png![image](https://user-images.githubusercontent.com/2846153/137261263-d4f36f3a-c365-41b0-9260-a72f696a7397.png)

 TCP and UDP are typically implemented directly in the OS’ fast kernel.
 In contrast, TLS and QUIC implementations are mostly in slower user space (note that this is not really needed for QUIC — it is mostly done because it’s much more flexible)
 
  we need to pass this data to the OS kernel, which then uses TCP or UDP to actually put it on the network
  For TCP, these overheads were much lower than for UDP.
  This is mostly because, historically, TCP has been used a lot more than UDP. As such, over time, many optimizations were added to TCP implementations and kernel APIs to reduce packet send and receive overheads to a minimum
  
   For example, over time, some QUIC implementations will (partially) move to the OS kernel (much like TCP) or bypass it 
   
   ## HTTP/3 Features
   
    HTTP/3 is really HTTP/2-over-QUIC
    
   ## Future Developments To Look Out For
   
   ### Forward error correction
   This purpose of this technique is, again, to improve QUIC’s resilience to packet loss
   
   ### Multipath QUIC
   
   Concurrently using both networks would give us more available bandwidth and increased robustness! That is the main concept behind multipath.
 
 ### Unreliable data over QUIC and HTTP/3
 As we’ve seen, QUIC is a fully reliable protocol. However, because it runs over UDP, which is unreliable, we can add a feature to QUIC to also send unreliable data. 
 
 ### WebTransport
 rowsers don’t expose TCP or UDP to JavaScript directly, mainly due to security concerns. Instead, we have to rely on HTTP-level APIs such as Fetch and the somewhat more flexible WebSocket and WebRTC protocols.
 
 ### DASH and HLS video streaming
 
 ### Protocols other than HTTP/3
 DNS-over-QUIC, SMB-over-QUIC, and even SSH-over-QUIC.


# PART 3
https://www.smashingmagazine.com/2021/09/http3-practical-deployment-options-part3/
 

#Changes To Pages And Resources 

If you’re already on HTTP/2, you probably won’t have to change anything to your pages or resources when moving to HTTP/3!

HTTP/3 is really more like HTTP/2-over-QUIC
high-level features of the two versions have stayed the same


## 1. SINGLE CONNECTION


The biggest difference between HTTP/1.1 and HTTP/2 was the switch from 6 to 30 parallel TCP connections to a single underlying TCP connection.

##  SERVER SHARDING AND CONNECTION COALESCING

The switch to the single connection set-up was quite difficult in practice because many pages were sharded across different hostnames and even servers (like img1.example.com and img2.example.com). This was because browsers only opened up to six connections for each individual hostname, so having multiple allowed for more connections!

Roughly speaking, if two hostnames resolve to the same server IP (using DNS) and use a similar TLS certificate, then the browser can reuse a single connection even across the two hostnames.

## RESOURCE BUNDLING AND INLINING

## PRIORITIZATION

To be able to download multiple files on a single connection, you need to somehow multiplex them

Sadly, this is something that you, as an average web developer, can’t do much about, because it’s mainly a problem in the browsers and servers themselves. You can, however, try to mitigate the issue by not using too many individual files (which will lower the chances for competing priorities) and by still using (limited) sharding.

Another option is to use various priority-influencing techniques, such as lazy loading, JavaScript async and defer, and resource hints such as preload.

##  SERVER PUSH AND FIRST FLIGHT 

Server push allows a server to send response data without first waiting for a request from the client. Again, this sounds great in theory, and it could be used instead of inlining resources (see above). 

Overall, it’s best not to use it for general web page loading unless you really know what you’re doing, and even then it would probably be a micro-optimization. 


Apply most of the typical HTTP/2 recommendations that you find online, but don’t take them to the extreme.


# Servers And Networks

https://github.com/lucas-clemente/quic-go

However, many (perhaps most) of these implementations mainly take care of the HTTP/3 and QUIC stuff; they are not really full-fledged web servers by themselves. 

However, while QUIC integrates with TLS 1.3, it uses it in ways much different from how TLS and TCP interact.

# NETWORK CONFIGURATION

 QUIC runs on top of the UDP protocol to make it easier to deploy. 
 
  however, mainly just means that most network devices can parse and understand UDP. Sadly, it does not mean that UDP is universally allowed. Because UDP is often used for attacks and is not critical to normal day-to-day work besides DNS, many (corporate) networks and firewalls block the protocol almost entirely. As such, UDP probably needs to be explicitly allowed to/from your HTTP/3 servers
  
 However, many network administrators will not want to just allow UDP wholesale. Instead, they will specifically want to allow QUIC over UDP.
 
  QUIC is almost entirely encrypted. 
  
  However, due to QUIC’s encryption, firewalls can do much less of this connection-level tracking logic, and the few bits they can inspect are relatively complex.
  
  # Clients And QUIC Discovery 
  
  Most of the popular browsers already have (experimental) HTTP/3 support! Specifically
  
  https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/9d4aedbb-8464-4062-93d4-986989202121/caniuse-h3.png![image](https://user-images.githubusercontent.com/2846153/137770116-5694ea1f-da55-4a43-95e0-59c8f7351beb.png)


# ALT-SVC

Even if you’ve set up an HTTP/3-compatible server and are using an updated browser, you might be surprised to find that HTTP/3 isn’t actually being used consistently. To understand why, let’s suppose you’re the browser for a moment. 


Now several things can go wrong:

The server might not support QUIC.
One of the intermediate networks or firewalls might block QUIC and/or UDP completely.
The handshake packets might be lost in transit.


An easy but naïve solution would simply be to open both a QUIC and TCP connection at the same time and then use whichever handshake completes first

As such, for QUIC and HTTP/3, browsers would rather prefer to play it safe and only try QUIC if they know the server supports it. 

This header is called Alt-Svc, which stands for “alternative services”. Alt-Svc can be used to let a browser know that a certain service is also reachable via another server (IP and/or port), but it also allows for the indication of alternative protocols

https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/90f995f5-6b02-4884-a602-f5a526fb40e2/facebook-alt-svc.png![image](https://user-images.githubusercontent.com/2846153/137770569-2ea607bb-cad8-4ffe-aa57-e905003fafb0.png)

This means that the browser will only ever use HTTP/3 after it has downloaded at least a few resources via HTTP/2 or HTTP/1.1 first.

There is ongoing work to improve this two-step Alt-Svc process somewhat. The idea is to use new DNS records called SVCB and HTTPS, which will contain information similar to what is in Alt-Svc. As such, the client can discover that a server supports HTTP/3 during the DNS resolution step instead

# ADDITIONAL ISSUES

As if that wasn’t enough, another issue will make local testing more difficult: Chrome makes it very difficult for you to use self-signed TLS certificates for QUIC. 

 This is because non-official TLS certificates are often used by companies to decrypt their employees’ TLS traffic (so that they can, for example, have their firewalls scan inside encrypted traffic). However, if companies would start doing that with QUIC, we would again have custom middlebox implementations that make their own assumptions about the protocol.
 
 If you’re not using an official TLS certificate (signed by a certificate authority or root certificate that is trusted by Chrome, such as Let’s Encrypt), then you cannot use QUIC. 
 
 It is still possible to bypass this with some freaky command-line flags (because the common --ignore-certificate-errors doesn’t work for QUIC yet)
 
 If you are having problems with your QUIC set-up from inside a browser, it’s best to first validate it using a tool such as cURL. cURL has excellent HTTP/3 support (you can even choose between two different underlying libraries) and also makes it easier to observe Alt-Svc caching logic.
 
 # Tools And Testing
 
 Google Lighthouse

 
 
