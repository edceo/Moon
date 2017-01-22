MİKROSERVİS MİMARİYE GİRİŞ
--------------

Günümüzde proje mimarileri SOA(Service Oriented Architecture) temeline 
dayanmaktadır. Ve bu SOA mimarisi monolithic dediğimiz her iş biriminin bir 
arada bulunduğu bir yaklaşıma inşa edilmiştir. 
Şöyle bir örnek ile monolithic sistemi açıklayalım. Şekildeki gibi Uber tarzı 
bir sitemiz olduğunu düşünelim. 

![Alt Text](https://cdn.wp.nginx.com/wp-content/uploads/2016/04/Richardson-microservices-part1-1_monolithic-architecture.png)

+ Sitenin authentication
modülü, ürün sorgulama modülü, satın alma modülü gibi tüm modülleri tek bir çatı ve tek
bir server üzerinde bulunur. Haliyle tüm işlemler tek bir sisteme yüklenir. Bununla beraber
sistemin karmaşıklığı da çok fazladır. 

+ Tüm modüller bir arada olduğu için build, deployment gibi süreçler oldukça
uzun zaman alacaktır. 

+ Tek çatı olduğundan kullanılan programlama dilleri de 
 benzer olmalıdır. Yani .NET kullanılıyorsa aynı projede Java'ya yer veremezsiniz.
 Bu da programlama dillerini amaçları dışında kullanmaya sebebiyet verir. Örnek vermek
 gerekirse bir Java projesi ele alalım istatisliksel işlemleri Julia ya da R gibi o amaca
 yönelik bir programlama dili ile yapmaktansa bu sistem sizi Java ile yapmaya zorlayacaktır.

+ Projeye yeni katılan bir developer arkadaşımız olduğunu
düşünelim. Bu arkadaşımızın iş karmaşıklığı oldukça fazla olacaktır. Çünkü
projedeye uyum sürecinde tüm modüller hakkında az da olsa bilgi sahibi olması gerekecek
ve bu da projeye uyum sürecini uzatacaktır. 

+ Projede kullanılan bağımlılıkların yönetimi zorlaşacaktır. Çünkü bu sistemlerde
bir kütüphane birden fazla modülde kullanıldığı için olası bir versiyon değişikliği
tüm sistemi etkileyecektir. 

+ Proje sürekli dikey anlamda büyüyecektir. Ve bu büyüyen projeyi kontrol altına
almak oldukça sıkıntılı bir süreç olacaktır. 


Monolithic sistemin bu tarz eksileri SOA mimarisine farklı bir bakış açısı
getirmeye zorlamış ve mikroservis yaklaşım ortaya atılmıştır.

####Nedir Bu Mikroservis

Mikroservislerin temel amacı her modül birbirinden bağımsız olsun ve tekil bir 
işlem gerçekleştirsin. Bu ne anlama geliyor? Şöyle açıklayalım. E-Ticaret sitesi
üzerinden aynen devam edelim

+ Sitenin tüm modülleri farklı serverlar üzerinde farklı database'lere bağlı olarak
inşa edilir. Hepsi birbirinden bağımsızdır. Yani Authentication modülü, ürün sorgulama modülünden bağımsız
olarak farklı bir server üzerinde bireysel olarak çalışmaktadır. 

+ Her modülün build, deployment süreçleri bağımsız olacağından oldukça
zaman kazancı olacaktır.

+ Modüller birbirinden bağımsız olduğu için kullanılan programlama dilleri de, 
frameworkler de birbirinden bağımsızdır. Haliyle her programlama dilini amacına
göre kullanabilirim.

+ [Polglot Persistence](https://martinfowler.com/bliki/PolyglotPersistence.html) yaklaşımını en ideal şekilde kullanmamızı sağlar.

+ Projeye yeni katılan arkadaşımızın sadece bir modülden sorumlu olması 
projeye uyum sürecini oldukça hızlandıracaktır.

+ Her modül bağımsız olduğundan bağımlılık yönetimi oldukça basit olacaktır. 

+ En önemlisi projemiz artık yatay eksende büyüyecektir. Haliyle kontrolü elimiz altında
olacaktır. 

Fakat mikroservislerdeki bu bağımsız modüller beraberinde bir takım sıkıntılar da 
doğurmaktadır. Yine sitemizden devam edersek

+ Sorgulama modülü ve satın alma modüllerimizi ele alalım. Bir kişi bir ürün satın
aldığı zaman veritabanından ilgili ürün adedi güncellenir. Fakat bu güncelleme
sorgulama modülü üzerinde olmazsa? Elimizdeki ürün bittiği halde var olarak
göstereceğiz fakat kişi o ürünü alamayacak. Burada dağıtık transaction yönetimi
ortaya çıkmaktadır. Bir modülde veritabanı güncellenirken onunla alakalı diğer
modülde de veritabanı güncellenmelidir. Aksi halde sistem işleyişinde sıkıntılar
meydana gelecektir. 

+ Bununla beraber modüller birbirleriyle iletişim kurarken aynı business objeleri
kullanacağından kod tekrarı kaçınılmaz olacaktır. 

####


