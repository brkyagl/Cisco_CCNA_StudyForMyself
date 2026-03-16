## Switch Yönetiminin Yapılandırılması

Bir Cisco switch'i kutusundan çıkarıp kablolarını taktığında, Frame'leri iletmeye anında başlar ya hani. Ancak bu cihazı uzaktan güvenli bir şekilde yönetmek için yapmamız gereken kritik yapılandırmalar vardır.

### CLI - Komut Satırını Güvenceye Almak

Default ayarlarda, bir Cisco switch'in **Console** portuna kabloyu takan herkes, hiçbir şifre veya güvenlik engeliyle karşılaşmadan doğrudan **User Mode**'a, oradan da **Enable Mode** ve **Configuration Mode**'a geçiş yapabilir.

* *Neden böyle tasarlanmış?* Çünkü Cisco şöyle düşünür: "Eğer bir kişi o sistem odasına girip cihaza fiziksel olarak Console kablosunu takabildiyse, zaten o cihazın fiziksel kontrolünü ele geçirmiş demektir."
* *Fakat gerçek dünya nasıldır?* Biz ağ uzmanları cihazları her zaman fiziksel olarak değil, uzaktan da yönetiriz. Bu yüzden atacağımız ilk adım, o **CLI (Command-Line Interface)** erişimini sadece yetkili kişilerin girebileceği şekilde kilitlemektir.

### Neden Her İki Modu da Korumalıyız?

Güvenlik sadece en yetkili modu kilitlemekten ibaret değildir:

1. **Enable Mode'u Korumak:** En kritik adımdır. Çünkü Enable Mode'a giren bir saldırgan, switch'i yeniden başlatabilir veya tüm konfigürasyonu silip değiştirebilir.
2. **User Mode'u Korumak:** En az Enable Mode kadar önemlidir! User Mode'da yapılandırma değiştirilemese bile, bir saldırgan `show` komutlarını kullanarak ağın durumunu görebilir, bağlı cihazlar hakkında bilgi toplayabilir ve ağa saldırmak için yeni yollar keşfedebilir.

### Uzaktan Erişim İçin Altın Kural (IPv4 Önkoşulu)

Bir switch'i uzaktan yönetebilmek (Remote Access & Management) için **Telnet** veya **SSH (Secure Shell)** gibi protokolleri kullanırız. 
Ancak bu protokollerin çalışabilmesi için switch'in kendisine ait, çalışan bir **IPv4 konfigürasyonunun** olması şarttır!

> Gördüğümüz o Layer 2 Ethernet Frame Forwarding işlemlerinin, IP adresleriyle **HİÇBİR** alakası yoktur! Switch o işi IP adresi olmadan da MAC adreslerine bakarak kusursuzca yapar.
> Switch'e vereceğimiz bu IPv4 adresi, cihazın trafiği yönlendirmesi için değil, **SADECE BİZİM cihaza Telnet/SSH ile bağlanıp yönetebilmemiz içindir!**

### Bu Bölümde Neler Öğreneceğiz?

Login Security konusunda şu 4 temel aşamayı adım adım laboratuvarda yapılandıracağız:

1. User Mode ve Privileged Mode'u basit şifrelerle korumak.
2. User Mode erişimini cihazın kendi içindeki local kullanıcı adlarıyla korumak.
3. User Mode erişimini harici bir kimlik doğrulama sunucusuyla korumak.
4. Uzaktan erişimi, güvensiz Telnet yerine şifreli **SSH (Secure Shell)** ile korumak.


## User Modu ve Privileged Modun Güvenliği (Basit Şifrelerle Koruma)

Bir Cisco switch kutusundan ilk çıktığında (Default ayarlardayken), güvenlik politikası şu şekilde çalışır:

* **Console Erişimi:** Cihaza Console kablosuyla fiziksel olarak bağlanan birine tüm kapılar sonuna kadar açıktır. Hiçbir şifre sormadan doğrudan **User Mode**'a, oradan da **Privileged (Enable) Mode**'a geçiş yapabilirsin.
* **Uzaktan Erişim:** Telnet veya SSH üzerinden uzaktan erişim ise varsayılan olarak tamamen kapalıdır (User Mode'a bile giremezsin).

Bu varsayılan ayarlar, cihazı kutudan çıkarıp ilk yapılandırmayı yaparken harikadır. Ancak cihazı canlı bir ortama aldığında işler değişir! 
Kendi masandan kalkmadan ağdaki tüm switch'leri yönetebilmek için uzaktan erişimi (Telnet/SSH) açmalı, ama aynı zamanda cihazı "önüne gelenin" değiştirmemesi için bu girişleri şifrelemelisin.

### Simple Shared Passwords (Ortak Şifre Mantığı)

Laboratuvar ortamlarında veya küçük ağlarda en sık kullanılan temel güvenlik yöntemi **Simple Shared Password** (Basit Ortak Şifre) mantığıdır.

* **Nasıl Çalışır?** Bu yöntemde herhangi bir username sorulmaz; sadece bir password istenir.
* **Neden "Shared" Deniyor?** Çünkü IT ekibindeki herkes (örneğin Berkay, İrem ve sen) cihaza girerken kendinize ait özel bir hesapla değil, tüm ekibin bildiği o ortak şifreyi yazarak giriş yaparsınız.

### Erişim Yolları ve Şifre Türleri

Cihaza nereden bağlandığına göre switch sana farklı şifreler sorar.

1. **Console Password:**
* Cihazın yanına gidip Console kablosuyla bağlanan kullanıcılardan istenir.
* CLI üzerinde **`line console`** configuration mode altında ayarlanır.

2. **vty Password (Telnet/SSH Şifresi):**
* Cihaza ağ üzerinden uzaktan (Telnet veya SSH ile) bağlanan kullanıcılardan istenir.
* CLI üzerinde sanal terminal anlamına gelen **`line vty`** configuration mode altında ayarlanır.

3. **Enable Password:**
* Console veya vty üzerinden **User Mode**'a başarılı bir şekilde giren kullanıcının, sistemi değiştirebilmek için **Enable Mode**'a geçmek istediğinde karşısına çıkan o son ve en büyük bariyerdir.

### Erişim Hiyerarşisi 

Kafamızda o erişim haritasını tam olarak şöyle canlandırabiliriz:

```text
[ Fiziksel Bağlantı ] ---> (Console Password) ---+
                                                 |
                                                 v
                                           [ USER MODE ] ---> (Enable Password) ---> [ ENABLE MODE ]
                                                 ^
                                                 |
[ Uzaktan Bağlantı  ] ---> (vty Password) -------+
  (Telnet / SSH)

```

### Enable Mode Güvenliği 

Cisco switch'ler, cihazın tüm ayarlarının değiştirilebildiği o en yetkili modu, yani **Enable Mode**'u korumak için **Enable Password** adında ekstra bir shared password daha kullanır.

Bir ağ uzmanının gözünden süreci canlandıralım:

1. Cihazın CLI ekranına başarıyla bağlandın ve **User Mode**'a (`Switch>`) düştün.
2. Cihazı yapılandırmak için `enable` EXEC komutunu yazıp Enter'a bastın.
3. Switch anında önüne bir duvar örer ve senden **Enable Password**'ü ister.
4. Şifreyi doğru girersen, IOS seni tam yetkili **Enable Mode**'a (`Switch#`) geçirir.

### Console Üzerinden Enable Mode'a Geçiş

Diyelim ki sistem odasına girdin, laptop'ını Console kablosuyla switch'e bağladın ve terminal programını (Putty/TeraTerm vb.) açtın. Ekranı uyandırmak için klavyeden `Enter` tuşuna bastığında karşılaşacağın o gerçek saha deneyimi tam olarak şöyledir:

*(Not: Ortak Console şifresinin "abc123", Enable şifresinin ise "root" olarak ayarlandığını varsayıyoruz).*

```text
(Ağ uzmanı süreci başlatmak için Enter tuşuna basar. Bu satır ekranda görünmez.)

User Access Verification

Password: abc123
Switch> enable
Password: root
Switch#

```

Yukarıdaki CLI çıktısında, anlayabilelim diye yazdığımız şifreler ekranda açıkça gösterildi. Ancak **GERÇEK HAYATTA**, Cisco switch'ler sen klavyede şifreni tuşlarken ekranda hiçbir şey göstermez! (Ekranda `*` işareti bile çıkmaz, imleç sabit durur).
Arkandan geçen veya omzunun üzerinden ekranına bakan kötü niyetli birinin şifrenin kaç karakter olduğunu bile tahmin etmesini engellemek için. Sen yazmıyormuşsun gibi görünse de switch arka planda tuş vuruşlarını algılar; yazıp Enter'a basman yeterlidir.

### Configuring Simple Passwords 

Console, Telnet (vty) ve Enable Mode için o bahsettiğimiz "Shared Passwords" yapılandırmak aslında çok az komut gerektirir ve mantığı oldukça sezgiseldir.
Her şifreyi kendi context'i içinde o bağlamı, yani ilgili Configuration Mode altında tanımlamamız gerekir.

#### Şifreleme Konsepti

Kafamızda o yapılandırma haritasını tam olarak şöyle kodluyoruz:

```text
  [ Console Erişimi ]                         [ Telnet Uzaktan Erişimi ]
  line console 0                              line vty 0 15
  login                                       login
  password abc123                             password abc321
          \                                         /
           \                                       /
            +-----------> [ USER MODE ] <---------+
                                 |
                         enable secret root
                                 |
                                 v
                          [ ENABLE MODE ]

```

#### 1. Console ve vty (Telnet) Şifrelerini Ayarlamak

Cihazın fiziksel kapısına ve sanal kapısına şifre koymak için önce o kapının içine girmeliyiz:

* Console için: `line console 0` *(Sıfır, cihazda sadece 1 tane console portu olduğu içindir).*
* Telnet için: `line vty 0 15` *(Sanal terminal hatlarına giriyoruz).*

Bu modların içine girdikten sonra her iki kapı için de değişmeyen 2 altın komutumuz vardır:

* **`password <password-value>`:** O kapıda kullanılacak gerçek şifreyi (Örn: abc123 veya abc321) tanımlar.
* **`login`:** İşte bu çok kritik! Bu komut, IOS işletim sistemine *"Biri bu hattan bağlanmaya çalışırsa ona kullanıcı adı sorma, SADECE bu basit ortak şifreyi sor!"* talimatını verir. `login` komutunu yazmayı unutursan, switch şifreyi kontrol etmez!

#### 2. Enable Mode Şifresini Ayarlamak

User Mode'a ister Console'dan ister Telnet'ten girilmiş olsun, sistemi değiştirebilmek için geçilmesi gereken son kale Enable Mode'dur. Bu şifre spesifik bir `line` altında değil, doğrudan **Global Configuration Mode** altında ayarlanır.

* Komut: **`enable secret <password-value>`** (Örn: `enable secret root`)

Eski IOS sürümlerinde Enable Mode şifresini belirlemek için `enable password <password-value>` komutu kullanılırdı ve bu komut cihazlarda yine de duruyor. **ANCAK**, `enable password` komutu güvenlik açısından çok zayıftır (şifreyi düz metin olarak tutar). `enable secret` komutu ise şifreyi güçlü bir hash algoritmasıyla şifreler. Sistem odasındaki altın kural şudur: Gerçek ağlarda **HER ZAMAN** `enable secret` kullan!

## Configuration: Basic Passwords (Temel Şifreleme Adımları)

Console, Telnet (vty) ve Enable şifrelerini cihaza tanımlamak için sırasıyla şu 3 ana adımı izlemelisin:

**Adım 1. Enable Mode Şifresini Ayarlamak:**

* Global Configuration Mode'a gir ve `enable secret <password-value>` komutuyla şifreyi belirle.

**Adım 2. Console Şifresini Ayarlamak:**

* **A.** Console configuration mode'a girmek için `line console 0` komutunu kullan.
* **B.** Console şifresinin değerini belirlemek için `password <password-value>` subcommand'ı gir.
* **C.** Basit şifre güvenliğini aktif hale getirmek ve cihazın şifre sormasını sağlamak için `login` subcommand'ı kullan.

**Adım 3. Telnet (vty) Şifresini Ayarlamak:**

* **A.** Cihazdaki 16 adet sanal terminal hattının (0'dan 15'e kadar numaralandırılmış) tamamı için vty configuration mode'a girmek adına `line vty 0 15` komutunu kullan.
* **B.** Uzaktan erişim şifresinin değerini belirlemek için `password <password-value>` subcommand'ı gir.
* **C.** vty hatlarında basit şifre güvenliğini aktif hale getirmek için `login` subcommand'ı kullan.

### CLI Üzerinde Şifreleri Uygulamak 

Aşağıdaki çıktı, tüm o adımların bir switch üzerinde nasıl uygulandığını gösteriyor.

```text
IOU1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
IOU1(config)#enable secret root

IOU1(config)#line console 0

IOU1(config-line)#password abc123
IOU1(config-line)#login
IOU1(config-line)#exit

IOU1(config)#line vty 0

IOU1(config-line)#password abc321
IOU1(config-line)#login
IOU1(config-line)#end

IOU1#
```

Dikkat ettiysen, `line console 0` içine girip işimizi bitirdikten sonra tekrar Global Config Mode dönmek için **`exit`** komutunu kullandık. Ancak `line vty 0` içindeki işimiz bittiğinde, konfigürasyonu tamamen kapatıp en baştaki Privileged Mode'a tek seferde fırlamak için **`end`** komutunu kullandık. Bu ince detay, CLI üzerinde hız kazanmak için harika bir taktiktir.

## Çalışan Ayarları Doğrulamak

Cihaza girdiğimiz yapılandırmaların aktif olarak çalışıp çalışmadığını ve sisteme nasıl kaydedildiğini görmek için **`show running-config`** komutunu kullanırız.

Bu komut, switch'in o anki tüm hafızasını (çalışan dosyasını) ekrana döker. Sayfalarca süren bu çıktının içinde kaybolmamak için, sadece bizim girdiğimiz şifre komutlarına odaklanarak aradaki alakasız satırları (interface detayları vb.) sileceğim ve temiz bir özet sunmak istiyotrum.

### Running-Config Çıktısı 

Aşağıdaki çıktı, bir önceki adımda girdiğimiz komutların `running-config` dosyasındaki son halidir:

```text
IOU1#show running-config 
Building configuration...

Current configuration : 1697 bytes
!
! Last configuration change at 23:38:00 +03 Mon Mar 16 2026
!
version 15.1
!
hostname IOU1
!
enable secret 4 G/B7HFBy3X4vck5Jf8hiSvSeRAsFVRFKJZvp5LBqRv6
!
control-plane
!               
line con 0
 exec-timeout 0 0
 privilege level 15
 password abc123
 logging synchronous
 login    
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0
 password abc321
 login    
line vty 1 4
 login    
!         
```

Bu çıktıya bir ağ uzmanı gözüyle baktığında şu iki devasa detayı anında fark etmelisin:

**1. Enable Secret'ın Gücü (Şifrelenmiş Metin):**
Fark ettiysen, `line con 0` altındaki "abc123" ve `line vty` altındaki "abc321" şifreleri kabak gibi Cleartext olarak görünüyor. Ancak en başta `enable secret root` olarak girdiğimiz o süper yetkili şifre, çıktıda **`4 G/B7HFBy3X4vck5Jf8hiSvSeRAsFVRFKJZvp5LBqRv6`** şeklinde bir hash'e dönüşmüş.
İşte `enable secret` komutunun gücü budur; şifreyi MD5 (veya daha güçlü sha256) bir algoritmayla şifreleyerek, omzunun üzerinden veya konfigürasyon dosyasından şifreni çalmaya çalışanları engeller. (4 -> sha256 başka örnek 5 -> md5.)

**2. VTY Hatlarının İkiye Bölünmesi:**

> Eski IOS sürümlerinde switch'ler ve router'lar sadece 5 tane uzaktan bağlantı hattını (0, 1, 2, 3, 4) destekliyordu. Sonradan bu sayı 16'ya (0-15) çıkarıldığında, eski sistemlerle uyumluluğu bozmamak adına IOS arka planda her zaman ilk 5 hattı (0-4) ayrı, sonradan eklenen 11 hattı (5-15) ayrı listelemeye devam etti.
