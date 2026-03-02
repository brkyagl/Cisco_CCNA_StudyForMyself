# Cisco Catalyst Switch CLI'ye erişim (Cisco CLI'a Giriş)

Cisco'nun Router ve kurumsal sınıf Catalyst Switch cihazlarını yönetmenin en temel ve en profesyonel yolu **CLI (Command-Line Interface - Komut Satırı Arayüzü)** kullanmaktır.

### CLI Nedir?

Filmlerde hackerların kullandığı o siyah, sadece yazılardan oluşan ekranları düşün; işte CLI tam olarak odur! Mouse veya tıklanacak butonlar yoktur.

* **Nasıl Çalışır:** Ağ uzmanı/adayları (Yani biz!), yapmak istediği işlem için belirli bir metin komutu yazar ve **Enter** tuşuna basar.
* **Cevap:** Switch bu komutu alır, uygular ve gerekirse sana işlemin sonucuyla ilgili metin tabanlı bir geri bildirim yansıtır.

### Başka Yönetim Yolları Yok mu?

Elbette var. Günümüzdeki Cisco cihazları sadece siyah ekrana mahkum değildir:

1. **Web Arayüzü (GUI):** Tıpkı evdeki modemin arayüzüne (192.168.1.1) girer gibi, web tarayıcısı üzerinden cihazı yönetebilirsin.
2. **Network Management Software:** Ağ yönetim yazılımları (örneğin Cisco DNA Center) ile yüzlerce cihazı tek bir ekrandan görsel olarak kontrol edebilirsin.

CCNA sınavı ve bu notlar, grafik arayüzlerle (Web veya Yönetim Yazılımları) **İLGİLENMEZ.** 
Bizim tek odak noktamız **Cisco Catalyst Enterprise-Class** switchler ve onları **CLI** üzerinden nasıl izleyip yöneteceğimizdir. 

## Cisco Catalyst Switchler

Cisco, LAN Switch üretirken **Catalyst** markasını kullanır. Bu marka altında yüzlerce farklı model vardır ve bu modeller "Seriler" halinde gruplandırılır.
Örneğin, CCNA sınavında ve eğitimlerde en çok göreceğin kurumsal cihazlardan biri **Cisco 2960-XR** serisidir.

* **Rolü:** Genelde "Sistem Odası" içinde duran, end userlara bağlanan **Access** seviyesi switch'lerdir.
* **Özellikleri:** Bu cihazların üzerinde genellikle 24 veya 48 adet RJ-45 (UTP) port bulunur. Bu portlar **10/100/1000 Mbps** hızlarını otomatik olarak ayarlayabilir.

### Interfaces ve Ports (Port İsimlendirme Mantığı)

Cisco dünyasında bir switch'in üzerindeki fiziksel yuvalara **Interface** veya **Port** denir. Komut satırında bu portlara hükmederken bilmen gereken **Altın Kural** şudur:

> Bir portun komut satırındaki kalıcı ismi, her zaman o portun desteklediği **EN YÜKSEK HIZA** göre belirlenir.
> *Örnek:* Elimizde 10/100/1000 Mbps destekleyen bir port var. O an o porta 10 Mbps hızında çalışan eski bir yazıcı taksan bile, komut satırında o portun adı hala **GigabitEthernet**'tir!

**Hızlara Göre İsimler:**

* 10 Mbps -> `Ethernet`
* 100 Mbps -> `FastEthernet`
* 1000 Mbps -> `GigabitEthernet` (veya `GigE`)

### Numaralandırma Sistemi

Sadece ismini bilmek yetmez, o ismin yanına hangi yuva olduğunu belirten sayıları da doğru yazmalısın. Cisco cihazlarında iki farklı numaralandırma formatı görürsün:

1. **İki Haneli Numaralandırma (x/y):**
* Daha eski veya modüler olmayan sabit switch'lerde görülür.
* Örnek: `GigabitEthernet 0/0` veya `GigabitEthernet 0/1`

2. **Üç Haneli Numaralandırma (x/y/z):**
* Stack yapılabilen veya daha yeni nesil switch'lerde (Örn: 2960-XR) kullanılır. İlk rakam genelde switch'in stack içindeki numarasını belirtir.
* Örnek: `GigabitEthernet 1/0/1` veya `GigabitEthernet 1/0/2`

CLI'da çalışırken bu uzun isimleri yazmak zorunda değilsin! Cisco çok zekidir; `GigabitEthernet 1/0/1` yerine sadece `Gi1/0/1` yazarsan switch ne demek istediğini anlar.

## Cisco IOS ve CLI'a Erişim

Tıpkı bilgisayarında Windows veya telefonunda iOS/Android olduğu gibi, Cisco cihazlarının da donanımı yönetecek bir İşletim Sistemine ihtiyacı vardır. 
Cisco bu işletim sistemine **IOS (Internetwork Operating System)** adını verir.

Cisco IOS sadece cihazın performansını ve donanımını kontrol etmekle kalmaz, aynı zamanda bizim cihazla iletişim kurabilmemiz için o meşhur **CLI** ortamını yaratır.

### Terminal Emülatörü Nedir?

Peki biz bu CLI'a yazıları nasıl yazıyoruz? Switch'in üzerine klavye veya monitör takamıyoruz!

* Bunun yerine bilgisayarımıza bir **Terminal Emülatörü** programı (Örn: PuTTY, TeraTerm, SecureCRT) kurarız.
* Sen bilgisayarındaki bu programa komutu yazar ve *Enter*'a basarsın.
* Program bu metni switch'e gönderir. Switch komutu işler, sonucu tekrar metin olarak senin bilgisayarındaki o ekrana geri yansıtır.

### CLI'a Giden 3 Yol

Cisco Switch'lerin komut satırına erişmek için sektörde ve sınavda bilmen gereken **3 temel yöntem** vardır. Bunlardan biri fiziksel, diğer ikisi ağ üzerindendir.

**[CLI Erişimi için Seçenekler]**

```text
                                +-------------------+
                                |   Cisco 2960      |
                                |     Switch        |
                                +-------------------+
                                  /               \
          (Fiziksel Bağlantı)   /                   \  (Network Bağlantı)
                              /                       \
                      [Console Port]             [Network Interface]
                            |                               |
                            |                               |
                   (Kısa Konsol Kablosu)             (TCP/IP Network)
                   (Serial/USB'den RJ-45'e)                 |
                            |                               |
                            v                               v
                     +--------------+                +--------------+
                     |  Bizim PC    |                |  Bizim PC    |
                     | (Terminal)   |                | (Terminal)   |
                     +--------------+                +--------------+
                     Yöntem 1: Konsol                 Yöntem 2: TELNET
                                                      Yöntem 3: SSH (Secure Shell)

```

**Bu Yöntemlerin Farkı Ne?**

1. **Konsol:**
* **Fizikseldir.** Bilgisayarın ile switch'in "Console" portu arasına özel, kısa bir kablo çekmen gerekir.
* Ağın çalışmasına veya IP adresine ihtiyacı yoktur. Cihaz kutudan ilk çıktığında (veya ağ çöktüğünde) **tek giriş yoludur.**

2. **Telnet:**
* **Ağ üzerindendir (TCP/IP).** Switch'e uzaktan (başka bir odadan veya şehirden) IP adresi üzerinden bağlanmanı sağlar.
* *Dezavantajı:* Şifreler dahil her şeyi clear-text gönderir, güvenlik sıfırdır!

3. **SSH (Secure Shell):**
* **Ağ üzerindendir (TCP/IP).** Tıpkı Telnet gibidir ama tüm veriyi **şifreler**.
* *Avantajı:* Günümüzde uzaktan bağlantı için kullanılan modern ve güvenli standarttır.

## Konsol Bağlantısının Kablolaması

Fiziksel bir konsol bağlantısı kurmak için 3 ana bileşene ihtiyacımız vardır:

1. Switch üzerindeki **Konsol Portu**.
2. Bilgisayarımızdaki bir **Giriş Portu (Seri veya USB)**.
3. Bu ikisini birbirine bağlayacak **Uygun Bir Kablo**.

Zamanla bilgisayarlardaki eski tip "Serial" portlar ortadan kalktığı için, kablolama yöntemleri de değişmek zorunda kalmıştır.

### Switch Konsol Bağlantısı 

Sınavda ve sahada karşımıza çıkacak 3 temel fiziksel bağlantı senaryosu vardır. Aşağıdaki örnek, eski nesilden yeni nesile doğru bu evrimi gösteriyor:

**[Switch - Konsol Bağlantıları]**

```text
       (Senaryo 1: Eski)       (Senaryo 2: Hibrit)        (Senaryo 3: Yeni)
       +---------------+       +---------------+       +---------------+
       | SW1 (Eski)    |       | SW2 (Klasik)  |       | SW3 (Yeni)    |
       | [RJ-45 Port]  |       | [RJ-45 Port]  |       | [USB Port]    |
       +---------------+       +---------------+       +---------------+
               |                       |                       |
               | (Rollover Kablo)      | (Rollover Kablo)      |
               |                       |                       | (USB Kablo)
               |                 [USB Converter]               |
               |                       |    -> USB Kablo       |
  DB-9 <- [Serial Port]            [USB Port]              [USB Port]
       +---------------+       +---------------+       +---------------+
       | PC 1 (Eski)   |       | PC 2 (Güncel) |       | PC 3 (Güncel) |
       +---------------+       +---------------+       +---------------+

```

* **Senaryo 3 (Tamamen Yeni Nesil - Sağ Taraf):**
* Günümüzde birçok yeni Cisco router ve switch'in üzerinde doğrudan **USB Konsol Portu** bulunur.
* *Nasıl Bağlanırız?* Telefonunu şarj eder gibi, bilgisayarındaki standart bir USB portundan switch'in USB konsol portuna sıradan bir USB kablosu çekmen yeterlidir. Çok pratiktir!

* **Senaryo 1 (Tamamen Eski Nesil - Sol Taraf):**
* Eski nesil bağlantılarda, bilgisayarın arkasında **DB-9** adı verilen (9 pinli, D harfine benzeyen) eski tip bir "Serial Port" bulunurdu.
* Switch tarafında ise tıpkı standart bir Ethernet yuvasına benzeyen ama genellikle **MAVİ renkli** olan ve üzerinde "Console" yazan bir **RJ-45** portu vardır.
* *Nasıl Bağlanırız?* Bu ikisini birbirine bağlamak için **Rollover Cable (Konsol Kablosu)** adı verilen özel, yassı, mavi renkli bir UTP kablo kullanılır.

--> [Rollover Kablosu](https://m.media-amazon.com/images/I/51ErDJRz6aL._AC_UF1000,1000_QL80_.jpg)
--> [DB-9 Serial Portu](https://m.media-amazon.com/images/I/818XEdM97BL.jpg)

### Rollover Kablo Sırrı

Eski nesil RJ-45 konsol bağlantıları için Cisco cihazlarının kutusundan hazır, amaca uygun üretilmiş o meşhur **Mavi Konsol Kablosu** çıkar. Bunu takıp geçebilirsin.
Ancak acil bir durumda kendi kablonu kendin yapman gerekirse, bilmen gereken **altın kural** şudur: Konsol bağlantısı için standart bir Ethernet (Straight veya Crossover) kablosu **KULLANILAMAZ!** 
* **Rollover Pinout:** Konsol kablosunun içindeki 8 adet tel, bir uçtan diğer uca giderken tamamen **tersine çevrilir**.

* **Matematiği:** Pin 1 -> Pin 8'e, Pin 2 -> Pin 7'ye, Pin 3 -> Pin 6'ya bağlanır ve bu şekilde devam eder. Yani kablonun bir ucundaki dizilimin tam ayna görüntüsü diğer uçtadır.

### Hayat Kurtaran "Converter" Senaryosu

Teknoloji dünyasında bilgisayarlar çok hızlı evrimleşti ve eski "Seri Portları (DB-9)" kaldırıp her yere USB koydular. Ancak Cisco Switch'lerin üzerindeki RJ-45 konsol portları yıllarca değişmedi.

* **Sorun:** Bilgisayarında sadece USB var, Switch'te ise RJ-45 Konsol portu var. 
* **Çözüm:** Araya bir **USB Converter (USB'den Seri Port'a Dönüştürücü)** takman gerekir! Önce bilgisayarına USB dönüştürücüyü takarsın, sonra dönüştürücünün ucuna standart Rollover (Mavi) konsol kablosunu bağlar ve switch'e gidersin.

İster yepyeni bir USB konsol kablosu kullan (Senaryo 3), ister araya bir USB dönüştürücü tak (Senaryo 2); bilgisayarın (Windows/Mac) ucundaki cihazın bir "Cisco Konsolu" olduğunu veya o dönüştürücüyü **otomatik olarak TANIMAYABİLİR.** 
USB üzerinden bağlantı kurarken genellikle bilgisayarına bir **Software Driver** kurman gerekir. Sürücüyü kurduktan sonra bilgisayarın sana bir "COM Port" (Örn: COM3, COM4) atar ve bağlantıyı bunun üzerinden sağlarsın.

### İki Seçenekli Konsol Portları

Yeni nesil switch'lerin (örneğin Cisco 2960-XR serisi) arkasına baktığında genellikle yan yana iki farklı konsol portu görürsün:

1. **Klasik RJ-45 Konsol Portu:** (O bildiğimiz mavi Rollover kablosu için).
2. **USB Konsol Portu (Mini-B):** Standart dikdörtgen USB (Type-A) yerine, genelde eski kameralarda veya cihazlarda gördüğümüz o küçük **Mini-B** girişidir.

Switch'in üzerinde iki farklı port olsa bile, cihaza bağlanırken aynı anda **sadece birini** kullanabilirsin! İkisini birden takıp iki farklı bilgisayardan bağlanamazsın.

### Yazılımı Kurmak

Kabloyu taktık ama bilgisayarımız switch'in dilinden kendi kendine anlamaz. 
Araya bir çevirmen, yani **Terminal Emülatörü** yazılımı kurmamız gerekir. Sektörde en çok **PuTTY**, **TeraTerm** veya **SecureCRT** gibi ücretsiz/ücretli programlar kullanılır.
Sen klavyeden bir şeyler yazarsın, program bu metni kablo üzerinden switch'e gönderir. Switch komutu çalıştırır, sonucunu 1 ve 0'lar olarak geri yollar ve program bunu senin okuyabileceğin metinlere çevirip siyah ekrana basar.

### Sihirli Ayarlar: 9600 ve 8N1 

İşte CCNA sınavında ve gerçek hayatta adın gibi bilmen gereken o kutsal ayarlar! Terminal programını (örneğin PuTTY) ilk açtığında, switch ile aynı hızda ve frekansta konuşabilmesi için bağlantı ayarlarını (Line Settings) tam olarak şu varsayılan değerlere getirmelisin:

* **Speed (Baud Hızı):** 9600 bits/second (bps)
* **Hardware Flow Control:** None (Yok)
* **Data Bits:** 8 (8-bit ASCII)
* **Parity:** None (Yok)
* **Stop Bits:** 1

Ağ uzmanları sahada bu ayarları tek tek saymakla uğraşmazlar. Son üç parametrenin baş harflerinden oluşan **"8N1" (8 Data bits, No parity, 1 Stop bit)** kısaltmasını kullanırlar. Biri sana "Hız 9600, ayar 8N1" dediğinde terminal programını nasıl ayarlayacağını artık çok iyi biliyorsun!

## Telnet ve SSH ile CLI'ye erişim 

Terminal emülatörleri (PuTTY, TeraTerm vb.) sadece serial portundan fiziksel bağlantı kurmakla kalmaz; aynı zamanda TCP/IP ağları üzerinden de çalışabilirler.
Konsol kablosunu çöpe at (en azından cihazı ilk kurduktan sonra)! Telnet ve SSH sayesinde, cihaza **mevcut IP network üzerinden** (kablolu veya kablosuz) uzaktan komut gönderebiliriz.

### Client-Server Mantığı

Ağ üzerinden bağlantı kurduğumuzda sistem şu mantıkla çalışır:

* **Telnet/SSH Client:** Senin bilgisayarında çalışan terminal programıdır. Klavyede bastığın tuşları ağ üzerinden gönderir.
* **Telnet/SSH Server:** Bağlandığın Cisco Switch'tir. Gelen metni komut olarak algılar, çalıştırır ve sonucu sana geri yollar.

Cisco Catalyst switch'lerde Telnet sunucusu fabrikadan **açık** olarak gelir. Ancak IP adresi ve şifre ayarları yapılmadığı için kutudan çıktığı gibi hemen Telnet yapamazsın. 

### Telnet vs. SSH 

Görünüşte ikisi de aynı işi yapar. PuTTY'yi açarsın, IP adresini yazarsın ve karşına o CLI ekranı gelir. Ancak **arka planda** dağlar kadar fark vardır!

1. **Telnet (Tehlikeli Kısım):**
* Lab ortamında öğrenmek için harikadır ama **canlı ağlarda kullanılması kesinlikle YASAKTIR!**
* *Neden?* Çünkü Telnet, cihaza girerken yazdığın kullanıcı adı ve şifre dâhil **her şeyi "Clear-text" olarak gönderir.** Ağdaki herhangi biri araya girip Wireshark gibi bir programla paketleri dinlerse, şifreni kabak gibi görür!

2. **SSH (Secure Shell - Güvenli Kısım):**
* Telnet'in çok daha zeki ve **güvenli kuzenidir.**
* *Neden?* Çünkü SSH, bilgisayarın ile switch arasındaki tüm trafiği **şifreler.** Biri araya girip paketleri yakalasa bile, şifren yerine anlamsız, karmaşık karakter dizeleri görür.

## Kullanıcı ve Yetkili Modlar

Cisco cihazlarına hangi yöntemle bağlanırsan bağlan (Console, Telnet, SSH fark etmez), sistem seni doğrudan en alt yetki seviyesi olan bir "Bekleme Odasına" atar. Cisco IOS üzerinde temel olarak iki ana çalışma modu (EXEC Mode) vardır:

### 1. User EXEC Mode (Kullanıcı Modu)

* **Mantığı:** "Etrafına bakabilirsin ama sakın hiçbir şeye dokunma!" modudur.
* **Ne İşe Yarar?** Cihazın durumunu kontrol etmek için sadece bazı basit izleme (show) ve test (ping) komutlarını çalıştırabilirsin. Cihazın ayarlarını bozacak veya cihaza zarar verecek hiçbir komuta izin verilmez.
* **Sembolü:** Eğer komut satırında cihazın isminin yanında bir **büyüktür işareti (`>`)** görüyorsan, bil ki User Mode'dasın. (Örnek: `Switch>`)

### 2. Privileged EXEC Mode (Yetkili/Ayrıcalıklı Mod)

* **Mantığı:** "Patron benim, ne dersem yap!" modudur. Genelde **Enable Mode** olarak da bilinir.
* **Ne İşe Yarar?** Cihazı yeniden başlatmak (`reload`) gibi en güçlü ve tehlikeli komutların çalıştırıldığı, tüm yetkilerin sende olduğu yerdir.
* **Sembolü:** Eğer komut satırında cihazın isminin yanında bir **hashtag işareti (`#`)** görüyorsan, tebrikler, tam yetkilisin! (Örnek: `Switch#`)

### Modlar Arası Geçiş 

Aşağıdaki örnekte, cihaza ilk girdiğimiz andan itibaren bu iki mod arasında nasıl asansör gibi inip çıktığımızı görüyoruz:

**[User ve Privileged Modları]**

```text
    [ Console ]   [ Telnet ]   [ SSH ]
         \            |           /
          \           |          /   (Cihaza ilk giriş)
           v          v         v
      +--------------------------------+
      |        User Mode               |  (Örnek: Switch> )
      |  (Sadece bak, dokunma)         |
      +--------------------------------+
             |                  ^
       Yaz: [enable]      Yaz: [disable]
             |                  |
             v                  |
      +--------------------------------+
      | Enable (Privileged) Mode       |  (Örnek: Switch# )
      |  (Patron Sensin!)              |
      +--------------------------------+

```

Sınavda veya gerçek bir senaryoda, bir komutun neden çalışmadığını merak ediyorsan önce **satırın sonundaki sembole bak!**
`Switch>` yazıyorsa ve sen `reload` (yeniden başlat) yazarsan cihaz sana hata verir, çünkü yetkin yetmez.
Önce `enable` yazıp `Switch#` moduna geçmeli, sonra o güçlü komutları kullanmalısın. Geri dönmek için ise `disable` yazman yeterlidir.

## Modları GNS3'te Test Ediyoruz

Komut satırında (CLI) `>` ve `#` sembollerinin sadece birer şekil olmadığını, aralarında dağlar kadar yetki farkı olduğunu anlamanın en iyi yolu, cihazı yeniden başlatmayı sağlayan o güçlü **`reload`** komutunu test etmektir.
Aşağıdaki laboratuvar örneğinde, cihazın isminin `IOU1` olarak ayarlandığını görüyoruz.

### `reload` Komut Testi

GNS3'te switch'i açtığında ve konsola bağlandığında tam olarak şu senaryoyu yaşayacaksın:

```text
IOU1>reload
Translating "reload"

Translating "reload"
% Unknown command or computer name, or unable to find computer address
```

* **Ne Oldu?** Kullanıcı Modundayız (`>`). Cihaza "Yeniden başla (`reload`)" dedik. Cihaz bu komutu bu yetki seviyesinde tanımadığı için bizi reddetti ve "Bilinmeyen komut" hatası verdi!

**Şimdi yetkilerimizi alıp (Enable Mode) tekrar deneyelim:**

```text
IOU1>enable
IOU1#reload
Proceed with reload? [confirm]y
*Feb 24 10:42:38.366: %SYS-5-RELOAD: Reload requested by console. Reload Reason: Reload command.
```

* **Ne Oldu?** Önce `enable` yazarak Patron moduna (`#`) geçtik. Ardından tekrar `reload` yazdık. Cihaz bu sefer itiraz etmedi, "Emin misin? (Proceed with reload?)" diye sordu. `y` (Yes) diyerek cihazı başarıyla yeniden başlattık!

### Saha ve Sınav Tüyoları

Cisco eğitimlerini okurken ve GNS3'te çalışırken şu 3 altın kuralı asla unutma:

1. **Görünmez Şifreler:** CLI ekranında `Password:` uyarısı geldiğinde klavyeden şifreni yazarsın ama **ekranda hiçbir karakter (* veya nokta bile) GÖREMEZSİN!** Bu, arkandan bakan biri şifrenin kaç hane olduğunu anlamasın diye yapılmış bir güvenlik önlemidir. Sen yaz ve Enter'a bas!
2. **EXEC Commands Terimi:** İster Kullanıcı modunda (`>`) ister Yetkili modda (`#`) ol, cihazda anında çalışıp sana bir çıktı veya sonuç veren komutların genel adına **EXEC Commands** denir.
3. **Prompt Okuma:** Her satırın başındaki `IOU1>` yazısı bize iki şey söyler:
* Sol taraf: Cihazın adı (Hostname = IOU1)
* Sağ taraf: Yetki seviyemiz (`>` veya `#`)

## Konsoldan CLI Erişimi için Parola Güvenliği

Fabrika ayarlarında, bir Cisco switch'in konsol portuna kabloyu taktığında sana **hiçbir şifre sormaz**. 
Aynı şekilde `enable` yazıp Priv EXEC moduna geçerken de şifre sormaz. Çünkü Cisco der ki: *"Eğer bu adam kilitli sistem odasına girip o mavi kabloyu cihaza takabildiyse, zaten cihazın tam kontrolüne sahiptir."*

Ancak iyi bir ağ uzmanı her zaman işini sağlama alır ve basit de olsa bu girişlere şifre koyar.

### İki Aşamalı Güvenlik Duvarı 

Konsol bağlantısında iki farklı kritik noktaya şifre koyabiliriz:

1. **Console Login Password:** Cihaza mavi kabloyla ilk bağlandığında (User Mode'a girmeden önce) sorulan şifredir.
2. **Enable Password:** User Mode'dan Privileged Mode'a geçmeye çalışırken sorulan o güçlü şifredir.

### Örnek: Varsayılan Olmayan Temel Yapılandırma 

Bir önceki adımda cihazı test ederken bize iki defa şifre sormamıştı hatırlarsan (en azından benim GNS3'te Cisco Switch'imde sormadı). 
İşte cihazın bize o şifreleri sormasını görüyorsak, arka planda buradaki ayarların olması lazım. `show running-config` (çalışan ayarları göster) komutuyla cihazın beynine baktığımızda şu satırları görürüz:

```text
IOU1# show running-config
! 
hostname IOU1
!
enable secret pass123
!
line console 0
 login
 password pass321
! 
IOU1#

```

Bu çıktıdaki her bir kelimenin hayati bir anlamı var. CCNA sınavında bunları adın gibi bilmelisin:

* **`!` (Ünlem İşareti):** Yorum satırıdır. IOS işletim sistemi bu satırları okumaz, sadece yapılandırma dosyasının okunaklı olması için aralara boşluk/ayraç niyetine konur.
* **`hostname IOU1`:** Cihazın fabrika çıkışı "Switch" olan adını `IOU1` olarak değiştirir. (CLI ekranında sol tarafta yazan isim buradan gelir).
* **`enable secret love`:** İşte bu çok kritik! User modundan Enable moduna geçerken sorulacak şifreyi **`pass123`** olarak belirler. (Ve "secret" kelimesi sayesinde bu şifreyi şifreler, bunu ileride detaylı göreceğiz).
* **`line console 0`:** Switch'in arkasındaki o fiziksel konsol portunun (0 numaralı ilk port) ayarlarına girildiğini gösterir.
* **`password pass321`:** Konsol portundan giriş yapacakların kullanacağı şifreyi **`pass321`** olarak belirler.
* **`login`:** Bu tek kelimelik komut hayat kurtarır! "Koyduğum bu `pass321` şifresini girişte kullanıcılara SOR (Aktif et)" anlamına gelir. Eğer `login` yazmazsan, şifre koysan bile cihaz şifre sormaz!

### Evrensel Anahtar: `enable secret`

`enable secret pass123` komutu sadece konsol kablosuyla bağlananları bağlamaz!

Cihaza nereden girersen gir (ister Console, ister uzaktan Telnet, ister SSH ile), eğer User Mode'dan (`>`) Priv Moduna (`#`) geçmek istiyorsan, karşına çıkacak o şifre ekranında **`pass123`** yazmak zorundasın. 
Yani `enable` şifresi, bağlantı türünden bağımsız olarak tüm kapıların ortak kilididir.

### Fiziksel Kapı: `line console 0`

Son üç satır (`line console 0`, `password pass321`, `login`) ise sadece ama sadece fiziksel konsol bağlantısını ilgilendirir:

1. **`line console 0`:** Switch'e "Şimdi yazacağım komutlar sadece arkandaki o fiziksel 0 numaralı (ilk ve tek) konsol portu için geçerlidir" demektir.
2. **`login`:** Cisco IOS'a "Basit şifre kontrolünü aktif et!" talimatını verir. Fabrika ayarlarında switch konsoldan şifre sormadığı için, bu komutu yazarak cihaza *"Kapıda dur ve gelene şifre sor"* demiş olursun.
3. **`password pass321`:** Ve o kapıda sorulacak şifrenin **`321`** olacağını belirler.

*"Bu gösterdiğim ayarlar, güvenlik denizinde sadece bir damladır."* > Bu ayarlar, evdeki veya GNS3/Packet Tracer'daki laboratuvarını kurup cihazları kurcalamaya başlaman için yeterlidir. 
Gerçek dünyada Telnet/SSH şifrelemelerini ve çok daha gelişmiş güvenlik önlemlerini ileriki konular ele alıyor

---

## CLI - Hayat Kurtaran Yardım Özellikleri

Cisco IOS komut referans belgelerinin tamamını yazdırsaydın, boyun kadar bir kağıt yığını elde ederdin. Hiç kimse ama **hiç kimse** tüm Cisco komutlarını ezbere bilemez!
Gerçek bir ağ uzmanının sırrı komutları ezberlemek değil, komutları hatırlatan o pratik **Yardım** araçlarını kullanmayı bilmektir. 
Cisco IOS sana zaman kazandırmak ve yazım hatalarını önlemek için muazzam bir "Context-Sensitive Help" (Bağlama Duyarlı Yardım) sunar.

### Cisco IOS Yardım Kısayolları

Aşağıdaki tablo, klavyendeki **Soru İşareti (`?`)** ve **`Tab`** tuşunun nasıl mucizeler yarattığını gösteriyor. (Sınavda bu boşluklu ve boşluksuz kullanımların farkı kesin sorulur!)

| Ne Yazıyorsun? | Ne İşe Yarar? | GNS3 / Saha Örneği |
| --- | --- | --- |
| **`?`** | Bulunduğun modda (User veya Enable) kullanabileceğin **tüm komutların** bir listesini döker. | `Switch# ?` (Enter'a basmadan tüm listeyi verir). |
| **`command ?`** *(Boşluk var)* | Komutu yazdın, bir **boşluk** bırakıp `?` koydun. Bu sana o komuttan sonra gelebilecek tüm **parametre (alt seçenek)** opsiyonlarını listeler. | `Switch# show ?` (Show komutundan sonra ne yazabileceğini gösterir: *ip, mac, interfaces* vb.) |
| **`com?`** *(Boşluk YOK)* | Sadece birkaç harf yazıp **bitişik** şekilde `?` koyarsan, o harflerle başlayan tüm ana komutları listeler. | `Switch# co?` (Sana *copy, configure, connect* gibi "co" ile başlayanları listeler). |
| **`command parm?`** *(Boşluk YOK)* | Ana komutu ve parametrenin ilk harflerini yazıp **bitişik** `?` koyarsan, o harflerle başlayan parametreleri listeler. | `Switch# show in?` (Sana sadece *interfaces* seçeneğini gösterir). |
| **`Tab` Tuşu** | IOS'un **Otomatik Tamamlama** özelliğidir. Yeterli harfi yazdıktan sonra Tab'a basarsan, kelimenin geri kalanını cihaz kendi tamamlar. (Sadece tek bir ihtimal kaldıysa çalışır). | `Switch# sh<Tab>` yazarsan cihaz bunu hemen `Switch# show ` yapar. |
| **`command parm1 ?`** | İlk parametreyi yazdıktan sonra bir boşluk daha bırakıp `?` koyarsan, dizilimin bir sonraki adımında (ikinci parametre olarak) ne yazabileceğini gösterir. | `Switch# show ip ?` (Sana *interface, route* vb. seçenekleri gösterir). |

### `?` İşaretinin Büyüsü

1. **Enter Tuşuna Gerek Yok:** `?` tuşuna bastığın anda Cisco IOS **anında** tepki verir. Onaylamak için Enter'a basmana gerek yoktur!
2. **Kaldığın Yerden Devam:** `?` basıp seçenekleri gördükten sonra, IOS senin o ana kadar yazdığın komutu alt satıra tekrar basar. Böylece baştan yazmak zorunda kalmazsın, kaldığın yerden yazmaya devam edersin. 
3. **Hemen Enter'a Basarsan:** `?` ile seçeneklere bakıp hemen ardından Enter'a basarsan, IOS o komutu sadece o ana kadar yazdığın eksik parametrelerle çalıştırmayı dener.

### Context-Sensitive: Modlara Göre Değişen Zeka

Yardım özelliği son derece zekidir. Sana sadece **o an bulunduğun modda** yetkin olan komutları gösterir.

* Eğer **User Mode (`>`)** içindeyken `?` basarsan, karşına kısa ve zararsız bir komut listesi çıkar (Çünkü yetkin düşüktür).
* Eğer **Enable Mode (`#`)** içindeyken `?` basarsan, liste çok daha uzun ve güçlü komutlarla doludur.
* Aynı şekilde ileride göreceğimiz **Configuration Modlarında** da `?` bastığında sadece o ayar menüsüne özel komutlar karşına çıkar.

