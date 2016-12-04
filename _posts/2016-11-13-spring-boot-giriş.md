---
layout: post
title:  "SPRING BOOT 101"
date:   2016-11-13
tag:
- spring-boot
- docker
- EDLAB
- mikro servisler
comments: true
---

Spring Boot 101
-------------

Blogumu açtıktan uzunca bir süre sonra sizinle beraberiz. Hatırlarsanız size bir projeyi baştan aşağıya
beraber yazacağız diye anlaşmıştık. Aklımdaki çok çok büyük pencerenin çok çok ufak bir köşesinden
projemizi oluşturmaya başlıyoruz.

Bugün sizlere Spring-Boot anlatacağım. Spring-Boot, bizleri gereksiz configurasyonlardan
kurtaran -bu niye tomcat ayağa kalkmadı, persistence.xml <span style="color:red">The persistence.xml file does not have recognized content</span>
hatası veriyor, 8080 dinleniyor lanet olası tomcatin portu nerden değişiyor 
gibi durumlardan bahsediyorum :) -
her projenin standartlaşmış kodlarını yazmaktan kurtaran bir projedir. Bir framework
değildir, sadece spring uygulamalarını minimum configurasyona indirmemize yardımcı
olur. Zaten amacı sadece ihtiyacımız olan kodu biz geliştiricilere yazdırmaktır. 

Spring Boot Proje Mimarisi
------------

Spring-boot proje mimarisi kurabilmek için öncelikle maven, gradle gibi bir build
tool kullanmak <span style="color:red">zorunludur.</span> Eğer kullanmazsanız 
kütüphaneleri, modülleri yönetecem diye deadline kaçırırsınız :) Spring-boot projesine
başlamak için <a href="start.spring.io">bu siteye</a> girmenizi öneririm. Burada
istediğiniz kütüphaneleri seçiyorsunuz daha sonra gradle ya da maven seçerek
size proje mimarisini hazırlıyor ve direkt import edip kodlamaya geçiyorsunuz. 

Hadi Örnek Yapalım
---------

<a href="start.spring.io">Buradaki linkten</a> basit bir proje indirelim. Projemizde Web,Jersey,
Thymeleaf ve JPA olsun. Çok çok basit bir proje ile başlayalım ki mimariyi mantığı anlayın. Bir de 
önünüze gelen kütüphaneyi eklemeyin <span style="color:red">her kütüphanenin bir ön şartı var aklınızda bulunsun ;) </span>

Projemizi maven olarak indirip kurduktan sonra pom.xml ekrandakine benzer olacaktır. -Ya da bir maven quick-start açın ve bu pom.xml'i
olduğu gibi ekleyin.-
```xml

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>


    <groupId>com.edsoft</groupId>
    <artifactId>movie-microservice</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>movie-microservice</name>
    <description>Movie Module for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.4.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-rest</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jersey</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.google.code.gson/gson -->
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>2.8.0</version>
        </dependency>



        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Camden.SR2</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>


</project>



```

Maven projemizin ayarlarını yaptıktan sonra paketlerimizi oluşturalım ve devam edelim.
Paketleri şekilde görüldüğü gibi ekliyoruz. 

```
.
+-- src
|   +-- main
        +-- java
            +-- com.edsoft
                +-- config
                +-- controller
                +-- domain
                +-- repository
                +-- service
                MovieApplication.java
        +-- resources
            +-- static
            +-- templates
            application.yml

``` 
Burada sadece paketlerin hiyerarşisi yer almaktadır. Şimdi bu paketlerin görevlerini
açıklayayalım. 

-Config <br />
Bu paket içerisinde gerekli configurasyonlarımızı tanımladığımız classlar yer
almaktadır. Mesela Security configurasyonları ya da gömülü veritabanındaki
data source'un oluşturulması gibi tanımlamalar burada gerçeklenir.

-Controller <br />
Bildiğimiz üzre controllerlar gelen istekleri karşılayıp yönlendiren sınıflardır.
Bu sınıfları da bu paketler içerisinde oluştururuz.

-Domain <br />
Veritabanımızdaki ya da projemizde tanımladığımız POJOlardır.

-Repository <br />
Veri tabanı connectionlarını gerçekleştirdiğimiz sınıflar ve interface'ler bu
paket içerisinde oluştururlur.

-Service <br /> 
Repositoryleri daha esnek bir şekilde yönetebilmek için repository üzerinde bir 
service katmanı eklenir. Bu katman ise controller ile ilişki kurar ve veritabanı
ilişkilerini soyutlaştırmamıza yardımcı olur.

-Resources <br />
Projede kullanılacak sql kodları, configurasyonlar, js ya da css gibi dosyalar
bu klasör altında tanımlanır. Bu özel bir klasördür çünkü bazı keywordler ile 
bu klasöre erişim sağlanır.

-Static <br />
Bu klasör altında css ve js dosyaları bulunur.

-Template <br/>
Bu klasör altında html dosyaları bulunur.

Tabi assolistler en sonra çıkacağı için en babası da en sona kaldı.

-Application.yml <br />
Bu dosya spring'in configurasyon dosyasıdır. Haliyle veritabanı bilgileri,
port bilgileri, modül bilgileri bu dosyada tanımlanır.


KODLAMAYA GEÇELİM
--------
Burada tek tek tüm kodları paylaşıp anlatırsam bu blog bitmez :) Haliyle tüm kodlara 
<a href="https://github.com/edceo/IMBDMS/tree/master/movie-microservice">Bu adresten
</a> ulaşabilirsiniz. Ben her paketten bir örnek gösterip size anlatacağım.
Config için bir sınıf tanımlamadım. O yüzden controller ile başlıyorum.

```
@Controller
public class MovieLinkController {

    @Autowired
    public MovieLinkService movieLinkService;

    @Autowired
    public MovieService movieService;

    @RequestMapping("/movielink/{id}")
    public ModelAndView getMovieLink(@PathVariable("id") String id) {
        MovieLink movieLink = movieLinkService.getMovieLink(id);
        Movie movie = movieService.getMovieById(movieLink.getImdbId());
        return new ModelAndView("mv", "movielink", movieLink);
    }
}
```
Yukarıda kodun sadece bir kısmı yer almaktadır. Burada bir takım
annotasyonlar yer almaktadır. Bu annotasyonlar spring'in temel annotasyonlarıdır.

@Autowired -> Dependency Injection'u sağlayan bir annotasyondur. <br />
@RequestMapping -> JAX-RS'deki @Path annotasyonu ile eşdeğerdir. İlgili pathi
yakalar ve o pathin tanımlı olduğu metodu aktif hale getirir. <br />
@Controller -> Sınıfın controller olacağını gösterir.<br />

Domain paketinde POJO'lar yer almaktadır.

Repository paketinde ise 

```
public interface MovieLinkRepository extends CrudRepository<MovieLink, Integer> {
}
```
şekildeki interface yer almaktadır. Burada interface bir başka interface olan
CrudRepository extends etmiştir. Extend edilen interface Spring JPA'nın bir interfacesidir
ve de default CRUD işlemlerini bizim için tanımlamıştır. 

Service paketinde ise yine interfaceler ve implemantasyonları yer almaktadır.





