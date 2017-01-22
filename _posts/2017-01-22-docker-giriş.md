---
layout: post
title:  "DOCKER 101"
date:   2017-01-22
tag:
- docker
- microservice design
comments: true
---

Docker 101
-
Docker, bir sanallaştırma platformudur. Fakat işletim sistemi bazında bir sanallaştırmadan
değil de uygulama bazında bir sanallaştırmadan bahsediyoruz. 

Nasıl yani? Önceden
sanallaştırma işlemleri işletim sistemi bazında guestOS aracılığıyla yapılıyordu.
Bir process ayağa kaldırmak için koca işletim sistemi kurulması gerekiyordu. Fakat
daha sonra container mantığı ortaya atıldı. Bir process için bir işletim sistemini
ayağa kaldırmaktansa o processin ihtiyaç duyduğu libraryler ile beraber bir 
container oluşturulup işletim sistmei üzerinde o koşturulmaya başlandı. Fakat
container yönetimi oldukça güç olduğundan daha sonrasında LXC aracı geliştiriliyor.
Fakat bu araç da bazı konularda yetersiz kalıyor. Bunun üzerine docker ortaya çıkıyor.
Docker LXC'nin eksikliklerini kapatarak ilerlese de daha sonrasında ayrı bir proje
olarak ayağa kalkıyor. Docker ile bir uygulama platform bağımsız bir şekilde 
başka bir hostta -docker yüklü olmak şartıyla- çalıştırılabiliyor. 

Bu şekilde düşünürsek

+ Projelerimizin kurulumu, taşınması ya da çalıştırılması oldukça basitleşmiş oluyor.

+ Sadece uygulamanın kendisi container halde gönderildiği için başka bir host
üzerinde hatasız olarak çalışacaktır. 

+ Direkt uygulamalar üzerinde yönetim ve kontrol yapabildiğimiz için uygulamaları
birbirinden ayrı çok rahat bir şekilde yönetebiliriz. 



### Docker Mimarisi

<img src="http://blog.cronom.com/get/MJHfm1c.jpg" />

Şekilde de gördüğümüz gibi VM sanallaştırmasında hypervisor üzerinde guest hostlar
bulunup onların üzerinde uygulama çalıştırılıyor. Fakat docker da ise main host
üzerinde bir docker engine var ve uygulamalar bu docker engine üzerinde birbirinden
bağımsız ayrı bir host gibi davranarak çalışıyor. 

Peki işin aslında docker nasıl çalışıyor? Bunu incelemek için aşağıdaki resmimizi
inceleyelim. 

<img src="https://docs.docker.com/engine/article-img/architecture.svg" />

#### Client
Client kullanıcının docker'a eriştiği arabirimdir. Kullanıcı burada docker komutlarını
girer ve docker ile iletişim kurar. 

#### Docker Daemon
Docker Daemon, HOST üzerinde çalışır ve client isteğini karşılar. 

#### Docker Images
Docker Images, containerların tanımlandığı bir şablondur. Bu şablon içine kullanacağınız
uygulamayı, çalıştıracağınız komutları vs tanımlarsınız. Image'lar Dockerfile
denilen bir dosya içinde tanımlanır.

#### Docker Container
Docker Containerı ise image'ın çalışır bir instance gibi düşünebilirsiniz. Image
bir class, container ise o classtan bir nesne gibi. Docker container, client aracılığıyla
yönetilir. 

#### Docker Registry
Registry'ler imageların bulunduğu bir kütüphanedir. Tanımlanan image'lar bu serverlar
üzerinde bulunur.(DockerHub en güzel örnektir.)

### Nasıl Çalışıyor?
Yazımızın bu kısmında bir örnek yaparak ilerleyelim. Böylelikle hem sistemin
nasıl çalıştığını öğrenir hem de elimizde bir demo uygulama olmuş olur. 
Spring-Boot 101 blog yazımdaki projemizi 
<a href="https://github.com/edceo/movie-microservice" targer="_blank">
Buradaki linkten</a> indirelim. Projemizi indirdikten sonra derleyip jar dosyamızı
oluşturalım. 

Docker çalıştırabilmemiz için öncelikle bir image oluşturmamız lazım. Yani bir 
Dockerfile tanımlayacağız. 

````
FROM java:8
ADD target/movie-microservice-0.0.1.jar app.jar
EXPOSE 2222
CMD ["java", "-jar","app.jar"]

````
Dockerfile şekildeki gibi 4 satırlık bir dosyadır. Projemiz küçük olduğu için
basit bir dosya oluşturduk. Burada bir takım komutlar görüyoruz. Bunları
açıklamak gerekirse

FROM: Projemizi kuracağımız image'ı(base image) çağırır. Her projenin
bir base image'ı vardır. Bu bir java projesi olduğu için
bize JVM yeterlidir. O yüzden sadece java-8'i çağırdık.

ADD: Oluşturacağımız container içine kendi localimizden dosya atabilmemizi sağlar.
Komut src dest mantığıyla kullanılır. 

EXPOSE: Container kendi başına bir host gibi davranacak ve aynı zamanda bir port
üzerinden çalışacaktır. Burada projemizde tanımladığımız 2222 portunu tanımlıyoruz.

CMD: Container üzerinde çalışacak varsayılan komut belirtilir. Her Dockerfile'da
sadece 1 adet bulunur. 

RUN: Build sırasında çalıştırılacak komutları belirtmek için kullanılır.

Dockerfile oluşturulduktan terminal üzerinde

````
docker build -t <tag_name> <file_path>

````
şeklinde komut çalıştırılır. 

Buraya kadar biz sadece oluşturulacak image'ın tanımını
yapmış olduk. Şimdi ise container'ımızı oluşturalım. Oluşturmadan önce elimizde
hangi image'lar var bakalım. Terminale docker images yazalım. 

````
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
movie               latest              739daba79513        About a minute ago   670 MB
java                8                   d23bdf5b1b1b        5 days ago           643 MB
hello-world         latest              c54a2cc56cbb        6 months ago         1.85 kB

````

Gördüğümüz gibi base image ve kendi movie image'imiz görülüyor. Şimdi container'ımızı
ayağa kaldıralım. Bunu yaparken terminale 

````
docker run -p 8080:2222 movie
````
komutunu yazalım. Komutu inceleyecek olursak -p ile localimizdeki 8080 portunu
containerdaki 2222 komutuna eşliyoruz. En sonda da image adını yazıyoruz. 
Komutu çalıştırdığımızda jar dosyamız ayağa kalkacaktır. Sonrasında 
tarayıcıda localhost:8080 girdikten sonra ekranımız karşımıza gelecektir. 

Evet arkadaşlar ilk docker yazımızı da tanımlamış olduk. Sizlere en basit şekilde
docker nediri anlatmaya çalıştım. İlgili projeyi github üzerinden indirip
deneyebilirsiniz.

İyi çalışmalar.




