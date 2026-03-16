## LAN Switching (Büyük Resmi Görmek)

Modern bir Ethernet ağı (LAN), son kullanıcıları (bizim gibi PC başındakileri) ve o kullanıcıların erişmek istediği devasa sunucuları birbirine bağlar. Kurumsal bir ağ mimarisi temelde iki devasa mahalleye ayrılır:

### 1. Campus LAN (Kullanıcıların Dünyası)

* **Neresidir?** Senin, benim, ofisteki diğer çalışanların bilgisayarlarının, IP telefonlarının ve yazıcılarının bağlandığı kısımdır.
* **Saha Notu:** Buradaki switch'ler genelde her katta bulunan o dar, klimalı küçük sistem odalarında bulunur. Görevleri, sahadaki kullanıcılardan gelen dağınık trafiği toplayıp ağın ana backbone'nuna iletmektir.

### 2. Data Center LAN (Sunucuların Kalesi)

* **Neresidir?** Kullanıcıların sürekli veri çektiği (örneğin şirketin muhasebe programı, veritabanları, web siteleri) o koca koca server'ların barındığı yerdir.
* **Saha Notu:** Burası katlardaki o küçük kabinlere hiç benzemez! Özel soğutma sistemleri olan, yüksek güvenlikli, kapalı ve devasa bir odadır. Buradaki switch'ler çok daha güçlüdür çünkü tek amaçları, yüzlerce sunucuya giren ve çıkan o devasa trafiği sırtlamaktır.

### Ağın Anatomisi

O figürü kafanda tam olarak şöyle canlandırabilirsin:

```text
  [ Campus LAN (Sol Taraf) ]                    [ Data Center LAN (Sağ Taraf) ]
                                      |
(PC 1) --+                            |                            +-- (Server 1)
         |                            |                            |
(PC 2) --+---> [Kat Switch'i] ==== (OMURGA) ==== [Merkez Switch] <---+-- (Server 2)
         |     (Wiring Closet)        |           (Data Center)    |
(PC 3) --+                            |                            +-- (Server 3)

```

### Switch: Bağımsız Karar Mekanizması (Çok Önemli!)

Üstte hani örnek verdik ya ama geniş bir bakış açısıyla bakacak olursak: bir kullanıcının (PC 1) bilgisayarından çıkan veri, Data Center'daki sunucuya (Server 1) gidip geri dönene kadar yolda belki 5-6 tane farklı switch'ten geçer.
Bu switch'lerin hepsi aynı anahtarlama mantığıyla çalışır ama **kararlarını birbirinden TAMAMEN BAĞIMSIZ alırlar.**
Biri diğerine *"Ben bu paketi sana yolladım, sen de şuraya yolla"* demez! Her switch, gelen Frame'i eline alır, kendi tablosuna bakar, kendi mantığını çalıştırır ve "Bunu iletiyorum", "Bunu çöpe atıyorum" veya "Bunu her yere yolluyorum" diyerek kendi anlık kararını verir.

## Switch'in Karar Mekanizması

Bir LAN switch'in nihai ve en büyük amacı tek bir şeydir: **Ethernet Frame'lerini doğru hedefe iletmek.** 
Peki bunu nasıl yapar? Kapısına gelen her frame'in Ethernet Header'ına) bakar, içindeki **Source** ve **Destination MAC adreslerini** okur ve kusursuz bir mantık çalıştırarak bir karar verir: *"Bu paketi diğer portlardan dışarı mı Forward edeyim, yoksa görmezden gelip Filter mı edeyim?"*

### Switch'in 3 Temel Görevi

Bir switch, bu "doğru adrese teslimat" görevini kusursuz yapmak için arka planda tam 3 farklı eylem gerçekleştirir. Sınavda bu üçlüyü adın gibi bilmelisin:

1. **Forward or Filter (İletmek veya Filtrelemek):**
* Switch'in **ASIL** işidir.
* Karar verirken frame'in üzerindeki **Destination MAC adresine** bakar. Eğer paketin kime gideceğini biliyorsa sadece o hedefe iletir, bilmiyorsa veya paket zaten geldiği yere dönmeye çalışıyorsa filtreler (görmezden gelir).

2. **Learning (Öğrenme):**
* Switch, ağdaki cihazların nerede (hangi portta) olduğunu önceden bilmez; dedektif gibi iz sürerek öğrenir.
* Bunu yaparken frame'in üzerindeki **Source MAC adresine** bakar. "Ha, demek ki bu MAC adresi benim 3 numaralı portumdaymış" diyerek MAC Address Table'ını doldurur.

3. **Loop'ları Engellemek:**
* Eğer ağda birden fazla switch birbirine yedekli (çapraz) bağlıysa, paketler sonsuz bir döngüye Loop'a yani döngüye girip ağı çökertebilir.
* Switch'ler, paketin hedefe sadece tek bir kopyasının gitmesi için kendi aralarında konuşarak **STP (Spanning Tree Protocol)** adlı bir protokol çalıştırır ve "Döngüsüz" bir ortam yaratırlar.

Switch'in asıl görevi sadece 1. maddedir (Forward/Filter). 2. ve 3. maddeler (Öğrenme ve STP), switch'in o 1. maddeyi doğru yapabilmesi için arka planda çalışan destek/yük fonksiyonlarıdır!

> Bu arada **"Switch Port"** ile **"Switch Interface"** kelimeleri birebir aynı anlama gelir. Biri sana port dediğinde interface, interface dediğinde portu anlayacaksın!

## Ethernet Frame (Switch'in Baktığı Yer)

Switch'in tek bir amacı vardır: Gelen Frame'i almak, nereye Forward edeceğine karar vermek ve doğru Interface'den dışarı yollamak. Peki switch bu kararı verirken Frame'in neresine bakar?

Aşağıdaki örnek, standart bir Ethernet Frame'inin yapısını gösteriyor:

```text
  <-------------------------- Header -------------------------->   <---- Payload ---->   <- Trailer ->
+--------------+---------+-----------------+----------------+------+-------------------+---------+
| Preamble (7) | SFD (1) | Destination (6) | Source MAC (6) | Type | Data & Pad        | FCS (4) |
|              |         | MAC Address     | Address        | (2)  | (46 - 1500 bytes) |         |
+--------------+---------+-----------------+----------------+------+-------------------+---------+

```

### Switch'in Gözünden Frame 

Switch o kadar pragmatik bir cihazdır ki, Frame'in içindeki **Data** kısmında ne yazdığı (bir web sayfası mı, e-posta mı yoksa video mu olduğu) onun zerre kadar umrunda değildir. 
Switch, paketi eline aldığı an sadece **Header** kısmındaki şu iki hayati alana odaklanır:

1. **Destination MAC Address (6-byte / 12 Hex karakter):** * "Bu Frame kime gidiyor?" sorusunun cevabıdır. Switch bu adrese bakar ve paketi Forward mı yoksa Filter mı edeceğine karar verir.
2. **Source MAC Address (6-byte / 12 Hex karakter):** * "Bu Frame kimden geldi?" sorusunun cevabıdır. Switch bu adresi okuyarak cihazın hangi Interface'e bağlı olduğunu MAC Address Table'ına yazar (Learning işlemi).

## Forwarding Known Unicast Frames (Bilinen Hedefe Teslimat)

Bir switch'in kapısından içeri bir Frame girdiğinde, cihaz bu Frame'i ne yapacağına karar vermek için arka planda dinamik olarak oluşturduğu bir tabloya bakar. Bu tablo, hangi MAC adresinin hangi fiziksel Interface'in ucunda olduğunu listeler.
Sektörde bu tabloya **MAC Address Table** denir. Ancak sınavlarda ve dökümanlarda karşına **Switching Table**, **Bridging Table** veya donanımsal hafıza türünden dolayı **CAM (Content-Addressable Memory) Table** olarak da çıkabilir. Biri sana CAM Table dediğinde MAC tablosundan bahsettiğini anında anlamalısın!

### Berkay ve Irem Senaryosu

Diyelim ki Berkay, Irem'e bir mesaj (Unicast Frame) göndermek istiyor. Switch'in saniyeler içinde işlettiği o kusursuz Forwarding ve Filtering mantığı tam olarak 4 adımdan oluşur:

1. **Geliş:** Berkay'ın bilgisayarından çıkan Frame, switch'in **F0/1** Interface'inden içeri girer. Frame'in üzerindeki Destination MAC Address, Irem'in adresi olan `0200.2222.2222`'dir.
2. **Sorgu:** Switch anında kendi CAM Table'ına bakar. *"0200.2222.2222 MAC adresi bende var mı?"* diye aratır.
3. **Forward:** Tabloda bir eşleşme bulur! Tablo der ki: *"Bu MAC adresi F0/2 numaralı Interface'in ucundadır."* Switch hiç tereddüt etmeden Frame'i **SADECE** F0/2 portundan dışarı Forward eder.
4. **Filter:** Switch paketi F0/2'den yolladığı için, ağdaki diğer cihazları (X ve Y'nin bağlı olduğu F0/3 ve F0/4 portlarını) gereksiz trafikle yormaz. Onlara giden yollarda bu Frame'i **Filter** eder (yani göndermez).

```text
[Berkay] ---> (F0/1) [ SWITCH ] (F0/2) ---> [Irem] (Destination: 0200.2222.2222)
                      |
                 (CAM Table)
              0200.2222.2222 = F0/2

```

### Büyük Ağlarda Bağımsızlık İlkesi

Eğer ağda birden fazla switch varsa, her switch sadece ama **sadece kendi MAC Address Table'ına bakar.** Her biri birbirinden bağımsız kararlar alarak Frame'i elden ele nihai hedefe ulaştırır.

## Multi-Switch Forwarding (Birden Fazla Switch ile İletişim)

Ağa ikinci bir switch (SW2) eklediğimizde topolojimiz biraz değişir. Diyelim ki Berkay (SW1'de) bu sefer X'e (SW2'de) bir Frame göndermek istiyor. X'in Destination MAC adresi `0200.3333.3333`.
Burada unutmamamız gereken o altın kural devreye giriyor: **Her switch sadece kendi MAC Address Table'ından sorumludur!** SW1, X'in tam olarak hangi cihazın hangi portuna bağlı olduğunu bilmez; sadece o Frame'i X'e ulaştırmak için kendi üzerinden hangi Interface'den çıkış yapması gerektiğini bilir.

### Berkay'dan X'e Uzanan Yol

Topolojiyi kafamızda tam olarak şöyle canlandıralım: Berkay ve Irem SW1'e, X ve Y ise SW2'ye bağlı. İki switch birbirine Gigabit (G0/1 ve G0/2) portlarıyla bağlanmış.

```text
    [Berkay]               [Irem]                [X]                    [Y]
 (MAC: ...1111)          (MAC: ...2222)    (MAC: ...3333)         (MAC: ...4444)
       | F0/1                  | F0/2            | F0/3                 | F0/4
 +----------------------------------+      +----------------------------------+
 |               SW1                |      |               SW2                |
 |                                  |      |                                  |
 |  (G0/1) ======================================= (G0/2)                     |
 +----------------------------------+      +----------------------------------+

```

### SW1'in Karar Anı (4 Adımda Forwarding)

Berkay'ın bilgisayarından çıkan Frame, SW1'e ulaştığında SW1 kendi iç dünyasında şu 4 adımı işletir:

1. **Geliş:** Frame, SW1'in **F0/1** portundan içeri girer.
2. **Hedefi Okuma:** SW1, Frame Header'ına bakar ve Destination MAC adresinin `0200.3333.3333` (X) olduğunu görür.
3. **Tablo Sorgusu:** SW1 hemen kendi tablosuna bakar. Tablosunda şu eşleşmeyi bulur:
* *"0200.3333.3333 MAC adresine gitmek istiyorsan, çıkış kapın G0/1 portudur!"*
4. **Forward:** SW1 hiç tereddüt etmeden Frame'i **G0/1** Interface'inden dışarı, yani SW2'ye doğru Forward eder.

### Arka Plandaki Tablolar 

Aşağıdaki tablolar, bu iki switch'in dünyayı nasıl gördüğünü özetliyor. Dikkat et, SW1 için hem X hem de Y aynı kapının (G0/1) arkasındadır!

**SW1 MAC Address Table:**
| MAC Address | Output Interface |
| :--- | :--- |
| 0200.1111.1111 (Berkay) | F0/1 |
| 0200.2222.2222 (Irem)| F0/2 |
| **0200.3333.3333 (X)**| **G0/1** |
| 0200.4444.4444 (Y) | G0/1 |

**SW2 MAC Address Table:**
| MAC Address | Output Interface |
| :--- | :--- |
| 0200.1111.1111 (Berkay) | G0/2 |
| 0200.2222.2222 (Irem)| G0/2 |
| 0200.3333.3333 (X)| F0/3 |
| 0200.4444.4444 (Y) | F0/4 |

## İkinci Switch: SW2'nin Teslimatı 

SW1 görevini yaptı ve Frame'i Backbone üzerinden SW2'ye fırlattı. Şimdi o Frame, SW2'nin **G0/2** Interface'inden içeri giriyor.
İşte o altın kural burada tekrar parlıyor: **SW2, SW1'in ne yaptığını veya ne düşündüğünü zerre kadar umursamaz!** O sadece elindeki Frame'e ve kendi MAC Address Table'ına bakar.

### SW2'nin 4 Adımlık Kararı

Frame içeri girdiği anda SW2 kendi iç dünyasında şu kusursuz mantığı çalıştırır:

1. **Geliş:** Frame, SW2'nin **G0/2** Interface'inden içeri girer.
2. **Hedefi Okuma:** SW2, Frame Header'ındaki Destination MAC adresinin `0200.3333.3333` (X) olduğunu okur.
3. **Tablo Sorgusu:** SW2 hemen kendi tablosuna döner ve eşleşmeyi bulur:
* *"0200.3333.3333 MAC adresi benim F0/3 numaralı portuma bağlı!"*
4. **Forward:** SW2, Frame'i **SADECE F0/3** Interface'inden dışarı, yani X'e doğru Forward eder. Görev başarıyla tamamlandı!

```text
 (G0/2'den Frame Gelir)              [X]                    [Y]
           |                   (MAC: ...3333)         (MAC: ...4444)
           v                         ^ F0/3                 | F0/4
 +-----------------------------------+--+                   |
 |               SW2                 |  |                   |
 |    (MAC Address Table'a bakar)    |  +-------------------+
 +-----------------------------------+
      0200.3333.3333 = F0/3

```

Eskiden bu işleme sadece "Forwarding" denmez, **"Forward-versus-Filter" (İletmeye Karşı Filtreleme)** kararı denirdi. Neden mi? Çünkü SW2 bu Frame'i F0/3'ten dışarı *Forward* etmeyi seçtiğinde, aslında otomatik olarak Y'nin bağlı olduğu F0/4 gibi diğer tüm portlara bu Frame'i yollamayı reddetmiş, yani o portları **Filter** etmiş olur!

## Known Unicast Frames (Sınavın Bankosu)

Buraya kadar işlediğimiz tüm örneklerde dikkatin çektiyse switch'lerin tabloları mucizevi bir şekilde **doluydu.** Yani switch'ler, aranan MAC adreslerinin hangi portta olduğunu zaten biliyordu.
İşte ağ dünyasında bu duruma bir isim verilir: **Known Unicast Frames (Bilinen Teke-Tek Frameler).**

* **Unicast:** Frame'in hedefinin tek bir cihaz (spesifik bir MAC adresi) olmasıdır (Broadcast veya Multicast değildir).
* **Known:** O Destination MAC adresinin, switch'in MAC Address Table'ında **zaten kayıtlı (bulunmuş)** olmasıdır.

Bir switch, eline geçen bir "Known Unicast Frame"i **SADECE TEK BİR PORTTAN** (tabloda yazan o spesifik Interface'ten) dışarı Forward eder. Diğer tüm portları Filter eder.

## Learning MAC Addresses (Switch'in Dedektiflik Yeteneği)

Çok şükür ki, binlerce cihazın MAC adresini o tablolara tek tek elle yazmak zorunda değiliz! Bunun yerine, her switch ikinci ana görevini yerine getirir: **MAC Address Table'ına koyacağı MAC adreslerini ve Interface'leri dinamik olarak öğrenmek.** Switch bu tabloyu eksiksiz bir şekilde doldurduğunda, az önce gördüğümüz o kusursuz Forwarding ve Filtering kararlarını verebilir.

Peki bu tablo nasıl inşa ediliyor? Switch'in mantığı inanılmaz derecede basittir:

* Interface'den içeri bir Frame girdiğinde, switch anında o Frame'in **Source MAC Address'ine** bakar.
* Eğer bu Source MAC Address tabloda yoksa, switch anında tabloya yeni bir kayıt açar.
* Bu kayda da o MAC adresini ve Frame'in içeri girdiği **Interface'i** yazar.

### Switch Learning 

Berkay, Irem, X ve Y'nin olduğu o topolojiyi hatırlıyoruz. Cihaz kutudan yeni çıktı ve henüz hiçbir şey bilmiyor.
Burada *Forwarding* işlemini tamamen görmezden gel ve sadece *Learning* sürecine odaklan. İşte o 3 kritik an:

**Durum 0: Hiçbir Frame Gönderilmeden Önce**
Switch'in gücünü verdik. Ağda henüz çıt yok.

```text
Adres Tablosu: Her İki Frame de Gönderilmeden Önce
-------------------------------------------
Address:            Output
(Empty)             (Empty)

```

**Durum 1: Frame 1 Yola Çıktı (Berkay'dan Irem'e)**
Berkay (0200.1111.1111), Irem'e bir Frame gönderir. Bu Frame switch'in **F0/1** Interface'inden içeri girer.
Switch anında Frame'in Source MAC adresine bakar ve "Ha! Berkay benim F0/1 portumdaymış" diyerek tabloyu günceller:

```text
Adres Tablosu: 1. Frame Sonrası (Berkay'dan Irem'e)
-------------------------------------------
Address:            Output
0200.1111.1111      F0/1

```

**Durum 2: Frame 2 Yola Çıktı (Irem'den Berkay'a Cevap)**
Şimdi Irem (0200.2222.2222), Berkay'a bir cevap Frame'i gönderiyor. Bu Frame switch'in **F0/2** Interface'inden içeri girer.
Switch bu sefer yeni Frame'in Source MAC adresine bakar ve "Güzel, Irem de F0/2 portumdaymış" diyerek onu da tabloya ekler:

```text
Adres Tablosu: 2. Frame Sonrası (Irem'den Berkay'a)
-------------------------------------------
Address:            Output
0200.1111.1111      F0/1
0200.2222.2222      F0/2

```

Özetle odak noktamız tamamen Learning süreci ve tablonun nasıl adım adım büyüdüğü...

**Adım 1: Berkay'ın Hamlesi ve İlk Kayıt**
Berkay, Irem'e o ilk Frame'i (1 numaralı) gönderdiğinde, switch hemen Berkay'ın MAC adresini (`0200.1111.1111`) alır ve onu **F0/1** Interface'i ile eşleştirerek tabloya ekler.
* *Peki Neden F0/1?* Çünkü Berkay'ın gönderdiği Frame, switch'in tam olarak o F0/1 portundan içeri girmiştir.
* **Switch'in Kusursuz Mantığı:** Switch o saniye kendi kendine şöyle der: *"Source MAC adresi 0200.1111.1111 olan bir cihaz var. Bu Frame benim F0/1 kapımdan içeri girdi. Demek ki benim bakış açıma göre, bu 0200.1111.1111 cihazına ulaşmak istiyorsam çıkış kapım kesinlikle F0/1 olmalıdır!"*

**Adım 2: Irem'in Cevabı ve İkinci Kayıt**
Hikaye devam ediyor... Irem 2. Adımda Berkay'a cevap verdiğinde, switch tablosuna ikinci bir kayıt daha ekler. Bu sefer Irem'nin MAC adresini (`0200.2222.2222`) alır ve onu **F0/2** Interface'i ile eşleştirir.
* *Peki Neden F0/2?* Çünkü Irem'in yolladığı Frame de switch'in F0/2 Interface'inden içeri girmiştir.

> Öğrenme (**Learning**) işlemi **HER ZAMAN** Frame'in içindeki **Source MAC** adresine bakılarak gerçekleşir. Switch bu adresi okur ve Frame'in içeri girdiği **Interface**'i o adresin yanına yazarak tablosunu inşa eder.

## Flooding: Unknown Unicast ve Broadcast Frame'ler

Switch'in MAC Address Table'ı tamamen boşken (veya hedef cihazın adresi henüz öğrenilmemişken) kapıdan içeri bir Frame girerse ne olur? Switch bu Frame'in Destination MAC adresini okur, tablosuna bakar ve hiçbir eşleşme bulamaz.
İşte hedefi henüz switch tarafından bilinmeyen bu tarz teke-tek frame'lere **Unknown Unicast Frame** denir.

### Kaba Kuvvet Mantığı: Flooding Nedir?

Switch, bir Unknown Unicast Frame ile karşılaştığında asla paketi drop etmez. Bunun yerine **Flooding** kuralını işletir:
Eğer switch paketi nereye göndereceğini bilmiyorsa, **Frame'in geldiği Interface HARİÇ, diğer tüm portlardan Frame'in birer kopyasını dışarı iletir!**
"Nereye göndereceğimi bilmiyorsam, her yere gönderirim; elbet doğru kişiye ulaşır!" Hedef cihaz bu Frame'i aldığında büyük ihtimalle bir "Cevap" gönderecektir. 
O cevap geri geldiğinde switch anında o cihazın Source MAC adresini okur, öğrenir ve tabloya yazar. Artık o cihaz bir "Known Unicast" olmuştur ve bir daha asla Flooding yapılmaz!

### Broadcast Frame'ler de Sel Olup Akar

Sadece hedefini bilmediği paketleri değil, ağdaki *herkese* gitmesi için özel olarak tasarlanmış **Broadcast Frame**'leri de switch aynı şekilde Flood eder. Bir Frame'in Destination MAC adresi `FFFF.FFFF.FFFF` ise, bu bir Broadcast'tir ve switch bunu geldiği port hariç tüm portlardan ağa basar.

### Flooding Operasyonu 

Berkay ilk Frame'i yolladığında switch'in dünyasında tam olarak şu 2 adım yaşanır:

**Durum 0: Tablo Bomboş**

```text
Adres Tablosu: Frame Gönderilmeden Önce
-------------------------------------------
Address:            Output
(Empty)             (Empty)

```

**Adım 1: Berkay Frame'i Gönderiyor**
Berkay (`0200.1111.1111`), Irem'e (`0200.2222.2222`) gitmesi için Frame'i yollar. Frame switch'in **F0/1** portundan içeri girer.

* *Not:* Switch bu anı yakalar yakalamaz Berkay'ın MAC adresini F0/1 olarak öğrenir, ama konumuz şu an hedefe nasıl ulaşacağı.

**Adım 2: Karar Anı ve Flooding**
Switch, Destination MAC adresine (`0200.2222.2222`) bakar. Tablo o an boş olduğu için (Unknown Unicast), switch şu tarihi kararı verir:
*"Bu Irem kim, nerede hiçbir fikrim yok! O yüzden bu Frame'in kopyalarını alıyorum ve Berkay'ın geldiği kapı (F0/1) HARİÇ diğer herkese yolluyorum!"*

* Switch Frame'i alır ve aynı anda şu portlardan dışarı basar:
* **F0/2** (Irem'e gider - Doğru Hedef!)
* **F0/3** (X'e gider - Gereksiz Trafik)
* **F0/4** (Y'ye gider - Gereksiz Trafik)

## Loopları Önlemek: Spanning Tree Protocol (STP) Devrede!

Bir LAN switch'in 3. ana görevi **Loopları yani döngüleri Engellemek**'tir. Kurumsal ağlarda cihazlar çökmesin diye switch'leri birbirine yedekli kablolarla bağlarız (Örneğin 3 switch'i üçgen şeklinde bağlamak gibi).
Ancak STP diye bir protokol olmasaydı, az önce öğrendiğimiz o **Flooding** işlemi, fiziksel olarak yedekli bağlanmış bu Ethernet ağlarında **sonsuz bir Loop'a** girerdi!

### Berkay'dan Irem'e Giden Sonsuz Loop

Diyelim ki 3 switch birbirine yedekli bağlı. Berkay, Irem'e bir Frame gönderiyor. Fakat ufak bir sorun var: **Irem'in bilgisayarı kapalı!**

1. Irem kapalı olduğu için hiçbir switch onun MAC adresini henüz öğrenememiştir (Tablolar Irem için boştur).
2. Berkay'ın Frame'i ilk switch'e gelir. Switch bakar, hedefi bulamaz ve ne yapar? **Flood eder!** (Geldiği port hariç her yere yollar).
3. Frame diğer iki switch'e ulaşır. Onlar da Irem'i bilmez, onlar da Flood eder!
4. Frame'ler kablolardan dönüp dolaşıp ilk switch'e geri gelir. İlk switch "Aaa yeni paket gelmiş, hedefi bilmiyorum, tekrar Flood edeyim!" der.
5. *Sonuç:* Bu Unknown Unicast Frame o üç switch arasında **SONSUZA DEK** dönmeye başlar! Saniyeler içinde ağ kitlenir ve kullanılamaz hale gelir.

### STP'nin Çözümü: Portları Susturmak

Yedekli topolojiler harikadır ama bu Loop kabusunu engellememiz şart. İşte bu yüzden tüm switch'lerde Layer 2 Loop'larını engellemek için **STP** çalışmalıdır.
STP, arka planda bir matematik hesabı yapar ve ağı Loop'suz bir hale getirmek için switch'lerin Interface'lerini iki temel durumdan birine zorlar:

* **Forwarding State (İletim Durumu):** Interface normal çalışır; Data Frame'lerini alabilir ve gönderebilir.
* **Blocking State (Bloklama Durumu):** İşte hayat kurtaran hamle! STP, sonsuz döngüyü kırmak için yedek yollardan birini mantıksal olarak kapatır. Bu port Data Frame'lerini **iletemez ve alamaz.** Böylece fiziksel olarak yedekli kablolarımız olsa bile, mantıksal olarak ağda her zaman **sadece TEK BİR aktif yol** bulunur! (Tabii STP'nin tek kötü yanı, o bloklanan yedek kabloların boşta yatmasıdır. İleride bu trafiği nasıl dengeleyeceğimizi de öğreneceğiz).

Cisco ve IEEE dünyasında, STP konusu açıldığında **Bridge** ve **Switch** kelimeleri birebir aynı anlama gelir. "Root Bridge" dediklerinde aslında "Root Switch"ten bahsettiklerini anlayacaksın! (Eskiden switch'lere bridge denirdi, isim oradan miras kalmıştır).

Şimdi buraya kadar geldiğimize göre toplu bir özeti yapalım...

## LAN Switching (Büyük Algoritma Özeti)

Bir switch'in hayatı temelde şu 3 adımlık kusursuz mantıktan ibarettir:

### Adım 1: Forwarding & Filtering

Switch'ler, bir Frame'i nereye yollayacaklarına karar verirken her zaman **Destination MAC Address**'e (Hedefe) bakarlar:

* **A. Flooding:**
Eğer hedeflenen MAC adresi bir **Broadcast**, **Multicast** veya **Unknown Unicast** (yani MAC tablosunda henüz listelenmemiş, bilinmeyen bir hedef) ise; switch o Frame'i Flood eder. *(Geldiği Interface HARİÇ, tüm portlardan dışarı yollar).*
* **B. Bilinen Hedefe Teslimat (Known Unicast):**
Eğer hedeflenen MAC adresi zaten MAC tablosunda bulunuyorsa, switch şu iki ihtimali değerlendirir:
* **i. Forward:** MAC tablosunda yazan çıkış portu, Frame'in içeri girdiği Interface'ten **farklıysa**, switch Frame'i sadece o çıkış portundan dışarı Forward eder.
* **ii. Filter:** *(İşte en kritik ve yeni detay!)* Eğer MAC tablosunda yazan çıkış portu, Frame'in içeri girdiği Interface ile **aynıysa**, switch o Frame'i Filter eder! Yani paketi görmezden gelir, çöpe atar ve hiçbir yere Forward etmez. *(Bu durum genelde switch'in bir portuna Hub bağlıysa ve cihazlar kendi aralarında o Hub üzerinden haberleşiyorsa yaşanır; switch gereksiz yere o Frame'i ağın geri kalanına sokmaz).*

### Adım 2: Learning MAC Addresses

Switch'ler, o meşhur MAC Address Table kayıtlarını oluşturmak için şu mantığı kullanır:

* **A. Kaynağı Oku:** İçeri giren her Frame için, **Source MAC Address**'i (Kaynağı) inceler ve o Frame'in hangi **Interface**'ten içeri girdiğini hafızasına not eder.
* **B. Tabloya Yaz:** Eğer bu Source MAC adresi tabloda zaten yoksa, o MAC adresini ve öğrenildiği Interface'i anında tabloya ekler.

### Adım 3: Loop Engelleme - STP

Switch'ler, yedekli kablolamalarda o ölümcül **Loop**'ları engellemek için **STP (Spanning Tree Protocol)** kullanırlar.

* STP, ağdaki bazı Interface'leri zorla **Block** durumuna çeker. Block durumundaki bir Interface, hiçbir Frame'i gönderemez veya alamaz. Böylece ağda sadece tek bir aktif yol kalır ve flood frameleri sonsuz bir döngüye girip ağı çökertemez.

---

## Ethernet Switching Doğrulama ve Analizi

Bir Cisco switch, fabrikadan çıktığı anda Frame'leri iletmeye tamamen hazırdır. Tek yapman gereken güç kablosunu takmak ve Ethernet kablolarını bağlamaktır; cihaz anında içeri giren Frame'leri işlemeye başlar. 
Birden fazla switch'i birbirine bağladığında bile, bu cihazlar kendi aralarında Frame'leri Forward etmeye anında hazırdır.
Bu "Tak-Çalıştır" davranışının arkasında yatan asıl neden, switch'lerin fabrikadan üzerlerinde yüklü gelen default ayarlardır.

### Switch'lerin Default Ayarları

Mesela Cisco Catalyst switch'lerin kutudan çıktığı gibi Frame işlemeye başlamasını sağlayan kritik varsayılan ayarlar şunlardır (Sınavda bu default durumları bilmek çok önemlidir):

* **Interface Durumları:** Tüm Interface'ler varsayılan olarak aktiftir. Yani kabloyu taktığın an Interface ayağa kalkar ve çalışmaya başlar (Router'lar gibi manuel olarak açmana gerek yoktur).
* **VLAN Ataması:** Tüm Interface'ler fabrikadan **VLAN 1**'e atanmış olarak gelir.
* **Hız ve Duplex:** 10/100 ve 10/100/1000 Mbps hızındaki tüm Interface'ler varsayılan olarak **autonegotiation** (otomatik hız ve duplex anlaşması) kullanır.
* **Karar Mekanizması:** O meşhur MAC **Learning**, **Forwarding** ve **Flooding** algoritmalarının tamamı varsayılan olarak aktif ve çalışır durumdadır.
* **Loop Engelleme:** Döngüleri engellemek için kullanılan **STP (Spanning Tree Protocol)** varsayılan olarak açıktır.

Bölümün bu ikinci kısmı, switch'lerin bu varsayılan ayarlarla nasıl çalıştığını ve arka planda dönen Ethernet Learning ve Forwarding süreçlerini CLI üzerinden nasıl doğrulayacağımızı inceleyecek.

## MAC Learning'i Göstermek 

Bir switch'in o meşhur MAC Address Table'ını görmek için CLI üzerinde `show mac address-table` komutu kullanılır. 
Ancak bu komutu tek başına yazarsan, switch sana donanımsal olarak kendi içinde tuttuğu gereksiz (overhead) statik MAC adreslerini de listeler.
Bizim asıl ilgilendiğimiz şey cihazın ağda **öğrendiği** adreslerdir. Sadece dinamik olarak öğrenilen MAC adreslerini görmek için şu komutu kullanırız:

`IOU1>show mac address-table dynamic`

### Switch'i Sıfırlamak 

Sistem odasında veya GNS3/Packet Tracer'da bir switch'i tam anlamıyla kutudan yeni çıkmış haline getirmek istiyorsan, sadece `startup-config` dosyasını silmek yetmez. VLAN veritabanını da temizlemen gerekir.

İşte bir switch'i tamamen sıfırlayıp isim verme adımları:

1. **`erase startup-config`** (Başlangıç ayarlarını siler)
2. **`delete vlan.dat`** (VLAN yapılandırma detaylarını kalıcı olarak siler)
3. **`reload`** (Switch'i yeniden başlatır. Açıldığında tertemiz, boş bir cihaz olarak gelir)
4. (Opsiyonel) **`hostname Berkay`** (Cihaza anlamlı bir isim verir)

Bu adımları uygulayıp kabloları taktığında, switch anında Forwarding ve Learning işlemlerine başlar.

### CLI Üzerinde MAC Tablosu

Aşağıdaki çıktı, cihazı sıfırladıktan sonra cihazlarının ağda birbirleriyle konuşup switch'in tablolarını nasıl doldurduğunu kanıtlayan tablodur:

```text
IOU1>show mac address-table dynamic 
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0050.7966.6800    DYNAMIC     Et0/0
   1    0050.7966.6801    DYNAMIC     Et0/1
   1    0050.7966.6802    DYNAMIC     Et0/3
   1    0050.7966.6803    DYNAMIC     Et0/2
Total Mac Addresses for this criterion: 4
IOU1>
```

### Çıktının Anatomisi

Bu tabloya baktığında bir network uzmanı olarak şunları anında okumalısın:

* **Vlan:** Tüm portlar varsayılan olarak `VLAN 1`'e aittir (Bunu az önce default ayarlarda konuşmuştuk).
* **Mac Address:** Switch'in ağdan öğrendiği Destination MAC adresleri.
* **Type:** Bu adreslerin `DYNAMIC` olarak, yani switch tarafından Frame'lerin **Source MAC** kısımları okunarak otomatik öğrenildiğini kanıtlar. Elle (Static) girilmemiştir.
* **Ports:** O MAC adresine gitmek için switch'in Frame'i hangi Interface'ten dışarı (Forward) atacağını gösterir.

## MAC Address Table Analizi 

CLI üzerinden aldığımız tablo çıktısına bir ağ uzmanı gözüyle baktığımızda, sütunların her biri switch'in o anki durumu hakkında kritik bilgiler verir.

### 1. MAC Address ve Ports Sütunları

Tabloda ilk odaklanmamız gereken yer **MAC Address** ve **Ports** sütunlarıdır. Bu değerler, kafamızda kurduğumuz (veya sistem odasında fiziksel olarak kabloladığımız) topolojiyle birebir eşleşir.

**Verification İçin Kullanılan Topoloji**

```text
    [Berkay]                     [Irem]
 (MAC: ...1111)               (MAC: ...2222)
       | F0/1                       | F0/2
 +-----------------------------------------+
 |                  SW1                    |
 +-----------------------------------------+
       | F0/3                       | F0/4
  (MAC: ...3333)               (MAC: ...4444)
        [X]                          [Y]

```

Tablodaki dört MAC adresi de cihazlarla ve onların bağlı olduğu portlarla (Fa0/1, Fa0/2 vb.) kusursuz bir şekilde eşleşmiştir.

### 2. Type Sütunu

Tablonun başlığındaki **Type** sütunu, switch'in o MAC adresini hafızasına tam olarak nasıl kazıdığını gösterir.

* **DYNAMIC:** Çıktımızdaki tüm adresler bu şekildedir. Yani switch, kapısından içeri giren Frame'lerin **Source MAC** adreslerini okuyarak bu bilgileri tamamen kendi başına, dinamik olarak öğrenmiştir.
* **STATIC:** "Port Security" gibi bazı güvenlik özellikleri kullanılarak veya bir ağ uzmanı tarafından cihaza manuel bir MAC adresi tanımlandığında, bu sütunda `STATIC` ibaresi yer alır.

### 3. VLAN Sütunu ve İzolasyon Mantığı

Son olarak tablonun en başındaki **VLAN** sütunu, VLAN'lerin (Virtual LAN) switching mantığını nasıl etkilediği konusunda bize çok hayati bir kuralı hatırlatır.
LAN switch'ler, Ethernet Frame'lerini **SADECE** aynı VLAN içerisinde Forward ederler.
* **Bunun Anlamı Şudur:** Eğer bir Frame, `VLAN 1`'e atanmış bir porttan (örneğin F0/1) içeri girerse; switch bu Frame'i sadece `VLAN 1`'e ait olan diğer portlardan dışarı **Forward** veya **Flood** eder. Bu Frame, başka bir VLAN'e atanmış hiçbir porta kesinlikle iletilmez.

## Switch Interfaces (Port Durumlarını Doğrulamak)

Cihazın fiziksel kurulumunu yapıp Console üzerinden bağlandıktan sonra, switch üzerindeki tüm Interface'lerin durumunu tek bir bakışta kontrol etmenin en kolay yolu `show interfaces status` komutunu kullanmaktır.

### Interface Status Çıktısı

Aşağıdaki çıktı, benim GNS3'te 4 bilgisayarlı switch'ime aittir.

```text
IOU1>show interfaces status 

Port      Name               Status       Vlan       Duplex  Speed Type
Et0/0                        connected    1            auto   auto unknown
Et0/1                        connected    1            auto   auto unknown
Et0/2                        connected    1            auto   auto unknown
Et0/3                        connected    1            auto   auto unknown
Et1/0                        connected    1            auto   auto unknown
Et1/1                        connected    1            auto   auto unknown
Et1/2                        connected    1            auto   auto unknown
Et1/3                        connected    1            auto   auto unknown
Et2/0                        connected    1            auto   auto unknown
Et2/1                        connected    1            auto   auto unknown
Et2/2                        connected    1            auto   auto unknown
Et2/3                        connected    1            auto   auto unknown
Et3/0                        connected    1            auto   auto unknown
Et3/1                        connected    1            auto   auto unknown
Et3/2                        connected    1            auto   auto unknown
Et3/3                        connected    1            auto   auto unknown
```

### Çıktının Anatomisi 

Bir anlığına sadece **Port** ve **Status** sütunlarına odaklanalım:

* **Port İsimlendirmesi:** Cisco switch'ler, portlarını destekledikleri en yüksek hıza göre isimlendirir. Çıktıdaki `Et` kısaltması **Ethernet** anlamına gelir.
* **Status:** * `connected`: O Interface'e fiziksel bir kablo takılıdır, ucunda çalışan bir cihaz vardır ve port başarıyla aktiftir. (Bunda hepsinde connected(GNS3'ten kaynaklı) ama normalde 4 portta cihaz bağlı olduğu için onlar connected durumunda olmalı).
* `notconnect`: Port şu an çalışmıyor demektir. Genelde o portta bir kablo olmadığı anlamına gelir. Ancak kablo takılı olduğu halde bu hatayı alıyorsan, fiziksel bir arıza veya başka problemler olabilir.

> Eğer 24 veya 48 portluk koca bir listede kaybolmak istemiyorsan, sadece tek bir portun durumuna bakabilirsin. Örneğin: `show interfaces Et0/0 status` komutu, sadece Et0/0'ın durumunu tek satırda verir.
> *(Eğer sonundaki "status" kelimesini yazmazsan, switch o Interface hakkında detaylı teknik mesaj döker).*

## Interface Counters (Trafik İstatistiklerini Okumak)

`show interfaces` komutunun tonla farklı opsiyonu vardır. Bunlardan en önemlilerinden biri olan `counters` seçeneği, o Interface üzerinden içeri giren ve dışarı çıkan Frame'lerin istatistiklerini listeler.
Özellikle ağda bir yavaşlık veya Loop şüphesi varsa, Unicast, Multicast ve Broadcast Frame sayılarının ne kadar arttığını buradan görürüz.

### Interface Counters Çıktısı (Fa0/1 İçin)

```text
Test# show interfaces f0/1 counters

Port            InOctets    InUcastPkts    InMcastPkts    InBcastPkts
Fa0/1           122330      3102           6410           718

Port           OutOctets   OutUcastPkts   OutMcastPkts   OutBcastPkts
Fa0/1           3235055     138862         2940           437

```

### Counters Çıktısının Anatomisi

Tablodaki terimlerin tam karşılıkları şunlardır:

* **Octets:** Byte demektir (1 Octet = 8 bit). O porttan geçen toplam veri miktarını (boyutunu) gösterir.
* **UcastPkts:** O porttan geçen toplam **Unicast** (teke-tek) Frame sayısıdır.
* **McastPkts:** O porttan geçen toplam **Multicast** (çoklu gönderim) Frame sayısıdır.
* **BcastPkts:** O porttan geçen toplam **Broadcast** (herkese gönderim) Frame sayısıdır.
* **In / Out:** *In* (Gelen Trafik), *Out* (Giden Trafik) istatistiklerini birbirinden ayırır.

## MAC Address Table İçinde Kayıt Bulmak (Filtreleme)

Gerçek bir kurumsal ağda `show mac address-table dynamic` komutunu yazıp Enter'a bastığında, karşına sayfa dolusu yüzlerce, hatta binlerce satırlık bir çıktı dökülür. Üstelik gerçek dünyadaki MAC adresleri bizim örneklerde kullandığımız gibi akılda kalıcı (`0200.1111.1111`) değildir; tamamen rastgele görünen, anlamsız Hex karakter dizilerinden oluşur. O koca listenin içinde tek bir cihazı gözle aramak tam bir kabustur!

Bereket versin ki Cisco IOS, o samanlıkta iğne aramak zorunda kalmayalım diye bize harika bir filtreleme opsiyonu sunuyor.

### Nokta Atışı Arama: `address` Parametresi

Eğer aradığın cihazın MAC adresini tam olarak biliyorsan, komutun sonuna sadece `address` kelimesini ve o MAC adresini eklemen yeterlidir.

### Spesifik Bir MAC Adresini Sorgulamak

Aşağıdaki çıktı, tabloyu filtreleyip sadece bizim aradığımız o tek bir kaydı nasıl ekrana getirdiğini gösteriyor:

```text
IOU1#show mac address-table address 0050.7966.6803
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0050.7966.6803    DYNAMIC     Et0/0
Total Mac Addresses for this criterion: 1
IOU1#

```

Çıktı formatı, az önce incelediğimiz o tabloyla birebir aynıdır (Vlan, Mac Address, Type, Ports). Tek farkı, switch'in bizi o sayfa dolusu gereksiz satırdan kurtarıp **sadece eşleşen** MAC adresinin satırını önümüze getirmesidir.

### Saha Gerçekliği 

Evet, bu komut harika çalışıyor. **Fakat arıza çözen bir uzman, genelde ağa bağlı o yüzlerce cihazın MAC adresini ezbere bilmez!** Elinde sadece hangi switch portunun hangi kata veya hangi sunucuya gittiğini gösteren bir topoloji diyagramı vardır. MAC adresini bilmediği için bu komutu her zaman kullanamaz.

### Port Bazlı Arama: `interface` Parametresi (Saha Hayat Kurtarıcısı)

Gerçek dünyada troubleshooting yaparken çoğu zaman cihazların MAC adreslerini bilmeyiz. Elimizde sadece hangi switch portunun hangi departmana veya hangi cihaza gittiğini gösteren bir topoloji diyagramı vardır demiştik. 
*"Acaba bu Et0/0 portunun ucunda kim var?"* diye merak ettiğimizde imdadımıza `interface` parametresi yetişir.

#### Spesifik Bir Interface'i Sorgulamak

Diyelim ki sadece `Ethernet 0/0` üzerinden öğrenilen MAC adreslerini görmek istiyoruz:

```text
IOU1#show mac address-table dynamic interface Ethernet 0/0
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0050.7966.6803    DYNAMIC     Et0/0
Total Mac Addresses for this criterion: 1
IOU1#
```

Bu komut sayesinde, eğer bir portun ucunda küçük bir Hub veya başka bir Switch varsa, o tek bir porttan öğrenilen *onlarca* MAC adresini aynı anda, diğer portların gürültüsü olmadan tertemiz bir şekilde görebiliriz!

### VLAN Bazlı Arama: `vlan` Parametresi

Aynı mantıkla, bazen sadece belirli bir ağ segmentindeki (VLAN) cihazları listelemek isteyebilirsin. Tahmin ettiğin gibi, komutun sonuna `vlan` parametresini ve VLAN numarasını eklemek yeterlidir.

#### Spesifik Bir VLAN'i Sorgulamak

Aşağıdaki çıktıda iki farklı senaryo var. İlkinde tüm cihazlarımızın içinde bulunduğu varsayılan `VLAN 1`'i sorguluyoruz. İkincisinde ise ağımızda henüz hiçbir cihazın atanmadığı, var olmayan bir `VLAN 2`'yi sorguluyoruz:

```text
IOU1#show mac address-table dynamic vlan 1
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0050.7966.6800    DYNAMIC     Et0/2
   1    0050.7966.6801    DYNAMIC     Et0/1
   1    0050.7966.6802    DYNAMIC     Et0/3
   1    0050.7966.6803    DYNAMIC     Et0/0
Total Mac Addresses for this criterion: 4
IOU1#show mac address-table dynamic vlan 2
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
IOU1#
```

Gördüğün gibi `vlan 1` yazdığımızda o VLAN'e ait tüm dinamik MAC adresleri döküldü. Ancak içi boş olan `vlan 2`'yi sorguladığımızda, switch bize sadece boş bir tablo başlığı döndürdü. Çünkü o VLAN'de henüz hiçbir Frame'in Source MAC adresi öğrenilmemişti (Learning gerçekleşmemişti).

## MAC Adres Tablosunu Yönetme (Eskitme, Temizleme)

Bölümü, switch'lerin MAC Address Table'larını nasıl yönettikleriyle kapatıyoruz. Switch'ler MAC adreslerini öğrenir, ancak bu adresler o tabloda sonsuza dek kalmaz! Bir switch, tablosundaki kayıtları üç temel sebeple siler:

1. **Zaman Aşımı:** Belirli bir süre kullanılmayan kayıtlar silinir.
2. **Tablonun Dolması:** Hafıza tamamen dolduğunda yeni gelenlere yer açmak için silinir.
3. **Manuel Temizleme:** Biz ağ uzmanları komut girerek zorla sileriz.

### 1. Zaman Aşımı Mantığı

Switch'ler, belirli bir saniye boyunca hiç kullanılmayan (üzerinden trafik geçmeyen) MAC adresi kayıtlarını tablosundan siler. Birçok switch'te bu default süre **300 saniyedir** (5 dakika).

**Peki bu mekanizma arka planda nasıl çalışır?**
Switch, kapısından içeri giren her Frame'in **Source MAC Address**'ine bakar.

* Eğer bu yeni bir adres ise, tabloya ekler.
* Ancak bu kayıt tabloda **zaten varsa**, switch sessiz kalmaz; o kayıt için tuttuğu **Inactivity Timer'ı (Hareketsizlik Sayacını) anında sıfırlar (0'a çeker).**
* * Her bir kaydın sayacı zamanla yukarı doğru sayar. Eğer bir kaydın sayacı o belirlenen Zaman Aşımı sınırına ulaşırsa, switch o kaydı Timeout eder (tablodan siler).
*(Örneğin Berkay 5 dakika boyunca ağda hiç konuşmazsa, switch "Berkay galiba gitti" der ve F0/1 portundaki MAC adresini siler).*

### Aging Timer ve Table Count Çıktıları

Aşağıdaki örnekte, switch'in global olarak kullandığı o 300 saniyelik varsayılan değeri ve tablonun kapasite/doluluk oranını görüyoruz:

```text
IOU1#show mac address-table aging-time 
Global Aging Time:  300
Vlan    Aging Time
----    ----------
IOU1#

IOU1#show mac address-table count 

Mac Entries for Vlan 1:
---------------------------
Dynamic Address Count  : 0
Static  Address Count  : 0
Total Mac Addresses    : 0

Total Mac Address Space Available: 183585508
IOU1# Bunu yazarken 300 saniye dolduğu için tablodan silindiler HASDGASHDHASDJHASJDGAJDS :DDDDD
```

Bu 300 saniyelik süreyi tüm switch için global olarak veya spesifik bir VLAN için değiştirmek istersen Global Configuration Mode'da şu komutu kullanırsın: `mac address-table aging-time time-in-seconds [vlan vlan-number]`

### 2. CAM Table Dolarsa Ne Olur?

Switch'in MAC Address Table'ı, **CAM (Content-Addressable Memory)** adı verilen, arama yapma kapasitesi inanılmaz yüksek fiziksel bir donanım hafızası kullanır. Ancak bu tablonun boyutu, switch'in donanım modelindeki CAM boyutuna ve ayarlarına bağlı olarak sınırlıdır.

Eğer switch yeni bir MAC adresi eklemeye çalışır ve tablonun **tamamen dolu** olduğunu fark ederse, o 300 saniyelik Aging süresinin dolmasını beklemez! Yer açmak için tablodaki **en eski** kaydı anında siler.
*(Not: Üstteki `count` çıktısının en son satırına bakarsan, kapasiteyi görürsün.)*

### 3. Clearing (Tabloyu Manuel Temizlemek)

Bazen troubleshooting yaparken veya kabloların yerini değiştirdiğimizde o 300 saniyeyi beklemek istemeyiz. Dynamic olarak öğrenilen MAC adreslerini manuel olarak silmek için `clear mac address-table dynamic` komutunu kullanırız.

> Bu chapter boyunca gördüğümüz tüm `show` komutlarını hem User Mode hem de Enable Mode içindeyken çalıştırabilirsin. Ancak `clear` komutu ağı etkileyen bir işlem olduğu için **SADECE Enable Mode** içinde çalışır!

Eğer tüm tabloyu değil de sadece spesifik yerleri silmek istersen, komutun sonuna şu parametreleri ekleyebilirsin:

* **VLAN'e Göre:** `clear mac address-table dynamic vlan vlan-number`
* **Interface'e Göre:** `clear mac address-table dynamic interface interface-id`
* **MAC Adresine Göre:** `clear mac address-table dynamic address mac-address`

## MAC Address Tables Çoklu Switches 

Bölümü tamamlarken, birden fazla switch'in olduğu bir senaryoda MAC Learning, Forwarding ve Flooding işlemlerinin her bir cihazda nasıl **bağımsız** gerçekleştiğini vurgulamak çok önemlidir.

### İki Switch'li Topoloji Örneği

Aşağıdaki topolojiyi dikkatlice incele ve özellikle port numaralarına odaklan. Kafamız karışmasın diye bu örnekte her iki switch için de farklı port numaraları kullanıldı. 
*(Gerçekte SW2'nin de F0/1 ve F0/2 portları var ama bu senaryoda onlara hiçbir cihaz bağlanmamış).* Tüm portlar `VLAN 1` içindedir ve sadece `hostname` komutu girilmiş, geri kalan her şey default ayarlardadır.

```text
    [Berkay]                     [Irem]
 (MAC: ...1111)               (MAC: ...2222)
       | F0/1                       | F0/2
 +-----------------------------------------+
 |                  SW1                    |
 |                                         |
 |                 (G0/1)                  |
 +--------------------||-------------------+
                      ||
                      || Backbone 
                      ||
 +--------------------||-------------------+
 |                 (G0/2)                  |
 |                                         |
 |                  SW2                    |
 +-----------------------------------------+
       | F0/3                       | F0/4
  (MAC: ...3333)               (MAC: ...4444)
        [X]                          [Y]

```

### Bağımsız Tabloların Mantığı

Diyelim ki üstteki cihazlar (Berkay ve İrem), alttaki cihazlarla (X ve Y) iletişim kurdu. Bu durumda her iki switch de ağdaki tüm 4 MAC adresini öğrenir. 
Ancak tablolarına yazdıkları **çıkış portları** tamamen kendi bakış açılarına göre şekillenir:

* **SW1'in Bakış Açısı:** Berkay ve İrem kendi üzerinde (F0/1, F0/2). Ancak X ve Y'ye gitmek istiyorsa, Frame'leri o aradaki backbone kablosundan, yani **G0/1** Interface'inden dışarı yollamak zorundadır.
* **SW2'nin Bakış Açısı:** X ve Y kendi üzerinde (F0/3, F0/4). Ancak Berkay ve İrem'e gitmek istiyorsa, Frame'leri kendi backbone portundan, yani **G0/2** Interface'inden dışarı yollamak zorundadır.

### İki Switch'in MAC Tabloları 

İşte o bahsettiğimiz bağımsız kararların CLI üzerindeki net kanıtı. İki switch'in tablosuna da sırayla bakıyoruz:

**SW1'in MAC Adres Tablosu Çıktısı:**

```text
SW1# show mac address-table dynamic
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0200.1111.1111    DYNAMIC     Fa0/1
   1    0200.2222.2222    DYNAMIC     Fa0/2
   1    0200.3333.3333    DYNAMIC     Gi0/1
   1    0200.4444.4444    DYNAMIC     Gi0/1
Total Mac Addresses for this criterion: 4

```

Gördüğün gibi SW1, X (...3333) ve Y (...4444) cihazlarının arkasında SW2 olduğunu bilmez. O sadece *"Bu MAC adreslerine ulaşmak için Frame'i Gi0/1 portundan Forward etmeliyim"* der.

**SW2'nin MAC Adres Tablosu Çıktısı:**

```text
SW2# show mac address-table dynamic
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0200.1111.1111    DYNAMIC     Gi0/2
   1    0200.2222.2222    DYNAMIC     Gi0/2
   1    0200.3333.3333    DYNAMIC     Fa0/3
   1    0200.4444.4444    DYNAMIC     Fa0/4
Total Mac Addresses for this criterion: 4

```

Aynı şekilde SW2 de Berkay (...1111) ve İrem (...2222) için Frame'leri kendi backbone çıkışı olan **Gi0/2** portundan Forward etmesi gerektiğini öğrenmiştir.
