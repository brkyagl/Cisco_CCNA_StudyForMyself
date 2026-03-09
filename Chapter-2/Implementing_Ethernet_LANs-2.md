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
Hikaye devam ediyor... Irem 2. Adımda Berkay'a cevap verdiğinde, switch tablosuna ikinci bir kayıt daha ekler. Bu sefer Barney'nin MAC adresini (`0200.2222.2222`) alır ve onu **F0/2** Interface'i ile eşleştirir.
* *Peki Neden F0/2?* Çünkü Irem'in yolladığı Frame de switch'in F0/2 Interface'inden içeri girmiştir.

> Öğrenme (**Learning**) işlemi **HER ZAMAN** Frame'in içindeki **Source MAC** adresine bakılarak gerçekleşir. Switch bu adresi okur ve Frame'in içeri girdiği **Interface**'i o adresin yanına yazarak tablosunu inşa eder.

