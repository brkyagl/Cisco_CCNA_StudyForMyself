## Ethernet Nedir?

"Ethernet" terimini duyduğunda aklına sadece kablo gelmesin. Ethernet, tek bir standart değildir; bir **Standartlar Ailesidir**.
Bu aile, dünyanın en popüler **kablolu LAN teknolojisini** oluşturur.

### Ethernet Hangi Katmanlarda Çalışır?

Ethernet, TCP/IP veya OSI modelinin en alt iki katmanını tanımlar ve yönetir:

1. **Physical Layer:** Kablolar, konnektörler, elektrik sinyalleri.
2. **Data-Link Layer:** Protokol kuralları, frameler.

### IEEE (Patron Kim?)

Ethernet standartlarını belirleyen kurum **IEEE**'dir (Institute of Electrical and Electronics Engineers).

Bu adamlar şunlara karar verir:

* Kullanılacak **kablolama** tipi ne olacak?
* Kablonun ucundaki **konnektörler** nasıl olacak?
* Cihazların uyması gereken **protokol kuralları** nelerdir?

**[Modelde Ethernet'in Yeri]**

```text
       OSI Layers                 Ethernet'in Sorumluluğu
   ------------------           -----------------------------
   3. Network (IP)
   ------------------
   2. Data Link       <-------  [ Protocol Kuralları & Frames ]
   ------------------           (IEEE tarafından tanımlanmıştır)
   1. Physical        <-------  [ Kablolama & Konnektörler ]

```

Sahada "Ethernet Kablosu" deriz ama aslında Ethernet, kablodan çok daha fazlasıdır. Hem fiziksel donanımı hem de o donanımı kullanma kurallarını kapsayan dev bir pakettir.

---

## SOHO LANs Üzerine

Bir SOHO (Small Office/Home Office) ağı kurarken ihtiyacımız olan temel malzemeler bellidir.

### 1. Wired-Only LAN (Sadece Kablolu Ağ)

İlk senaryoda sadece Ethernet teknolojisini kullanıyoruz.

* **Ethernet Switch:** Ağın kalbidir. Üzerinde birçok fiziksel port bulunur.
* **Ethernet Cables:** Cihazları (Nodes) switch'in portlarına bağlayan kablolardır.
* **Nodes:** Bilgisayar, yazıcı gibi ağa bağlanan cihazlar.

**[Tipik Küçük Ethernet Tabanlı SOHO LAN]**

Burada Switch merkezdedir. Router ise bizi dış dünyaya (İnternet/WAN) bağlar.

```text
       (Internet / WAN)
              |
          [ Router ]
              |
              | (Ethernet Kablosu)
              v
       [ Ethernet Switch ]
       /    |      |    \
      /     |      |     \
  [PC1]   [PC2]  [PC3]  [Printer]

```

Burada Router ve Switch'i ayrı cihazlar olarak çizildi. Ancak bugün satılan ve üzerinde "Router" yazan o kutuların çoğu aslında **Integrated Device**'dır yani Bütünleşik Cihaz. Yani tek bir kutu hem Routing yapar hem de arkasında 4-8 portluk bir Switch barındırır.

### 2. Wireless'in Dahil Olması

Günümüzde sadece kablo yetmez, tabletler ve telefonlar için **Wireless LAN** (WLAN) gerekir.

* **IEEE 802.11:** Wireless ağ standartları bu kodla başlar ve radyo dalgalarını kullanır.
* **Access Point (AP):** Kablosuz dünyanın Switch'i gibidir.
* Tüm wireless cihazlar AP ile konuşur.
* AP ise tek bir kablo ile ana Ethernet Switch'e bağlanır.

**[Tipik Küçük Wired ve Wireless SOHO LAN]**

Şimdi sahneye **Access Point** ve **Tabletler** ekleniyor.

```text
       (Internet)
           |
       [ Router ]
           |
       [ Switch ] ---------------------- [ Access Point ]
           |                                   .
           |                                 .   .
         [PC1]                          (Tablet) (Phone)
                                        (Wireless Cihazlar)

```

### Kavram Karmaşası

Burada çok önemli bir ayrım var. Eğitim amaçlı olarak **Router**, **Switch** ve **Access Point**'i üç ayrı cihaz gibi öğreniyoruz ki görevlerini anlayalım.

Ancak evindeki o tek cihaz (Wireless Router);

1. **Router** olarak internete çıkar,
2. **Switch** olarak arkasındaki portlarla PC'ni bağlar,
3. **Access Point** olarak telefonuna Wi-Fi yayar.

Yani tek bir kasa içinde üç farklı görev yapan süper bir cihazdır. Network dünyası bir büyü gibi.

---

## Enterprise Ağ Yapısı (Kurumsal)

Bir şirketin ağ ihtiyaçları evdeki ağa benzer, ancak çok daha büyük ölçeklidir.

### 1. Fiziksel Düzen

Büyük binalarda Switch'ler öyle masa altında durmaz.

* **Kablolama Dolabı/Odası:** Her katta kilitli bir kapının arkasında bir kablolama odası bulunur. Switch'ler buraya monte edilir.
* **Kablolama:** Elektrikçiler, bu odadan çalışma masalarına ve toplantı odalarına kilometrelerce Ethernet kablosu çeker.
* **Wireless:** Aynı alanda, insanların ofis içinde gezinirken çalışabilmesi ve Ethernet portu olmayan cihazların bağlanabilmesi için Wireless LAN da desteklenir.

### 2. Bina Mimarisi

Aşağıda 3 katlı bir bina görüyoruz. Burada kritik bir hiyerarşi var. Her kattaki Switch, kendi kafasına göre takılmaz; hepsi binanın "Merkezine" (Distribution Switch) bağlıdır.

**[Tek Bina Enterprise Kablolu ve Wireless LAN]**

```text
       3. Kat Zemin
      +---------------------+
      | [PC3] ---- [SW3]    |  <--- (Zemin Access Switch)
      +--------------|------+
                     |
       2. Kat Zemin  |
      +--------------|------+
      | [PC2] ---- [SW2]    |  <--- (Zemin Access Switch)
      +--------------|------+
                     |
       1. Kat Zemin  |
      +--------------|------+
      | [PC1] ---- [SW1]    |  <--- (Zemin Access Switch)
      +--------------|------+
                     |
             [ SWD (Dağıtım) ]  <--- (Merkez Switch)
                     |
                     |
                 [ Router ]
                     |
               (WAN / Internet)

```

### Trafik Akışı

Bu örnekte trafiğin nasıl aktığını anlamak çok önemli. Diyelim ki 3. kattaki **PC3**, 2. kattaki **PC2**'ye veri gönderecek.
Kablolar katlar arasında direkt bağlı değildir. Veri şöyle bir yol izler:

1. **PC3** -> **SW3** (3. Kat Switch'i)
2. **SW3** -> **SWD** (Aşağıya, Merkez Dağıtıcı Switch'e iner)
3. **SWD** -> **SW2** (Tekrar yukarı, 2. Kat Switch'ine çıkar)
4. **SW2** -> **PC2** (Hedefe ulaşır)

Bu yapıya "Hub-and-Spoke" veya "Star Topology" denir. Her şey merkeze iner ve oradan dağılır.

### 3. Dış Dünyaya Açılmak (WAN)

Örnekte Router'ın konumu da kritiktir.

* **LAN'ın Kendisi:** Switch'ler ve Access Point'ler (AP) tarafından oluşturulur.
* **LAN-WAN Bağlantısı:** Router, LAN ile WAN arasındaki köprüdür.
* **Bağlantı Şekli:** Router, LAN'a bağlanırken basit bir Ethernet arayüzü ve Ethernet kablosu kullanır.

---

## Ethernet Fiziksel Katman Standartlarının Çeşitliliği 

"Ethernet" dediğimiz şey tek bir kural değildir. 40 yıldır gelişen devasa bir **Standartlar Ailesidir**. Bu ailenin soyadı: **IEEE 802.3**.

Bu standartlar temel olarak iki şeyi belirler:

1. **Hız:** 10 Mbps'den başlar, bugün 400 Gbps'ye kadar çıkar.
2. **Kablo Tipi:** Veri neyin üzerinden akacak? Bakır mı, cam mı?

### Copper(Bakır) vs Fiber Savaşı

Ethernet dünyasında iki ana iletim yolu vardır:

1. **Copper (Bakır - UTP):**
* **Mantık:** Veriyi elektrik sinyalleriyle iletir.
* **Özellik:** Ucuzdur, bükülebilir, her yerde bulunur.
* **İpucu:** Standart isminde genelde **"T"** harfi geçer (Twisted Pair - Çift Bükümlü: bu arada bunu ilk aklıma twister dondurma olarak kazımıştım hani şekli bükümlü falan :D).

2. **Fiber-Optic (Fiber - Cam):**
* **Mantık:** Veriyi ışık olarak iletir. Saç teli kadar ince cam liflerden oluşur.
* **Özellik:** Pahalıdır ama çok uzun mesafelere gidebilir.
* **İpucu:** Standart isminde genelde **"X"** harfi geçer.

### İsimlerin Şifresini Çözmek

Bir Ethernet kablosunun üzerinde `1000BASE-T` yazdığını göreceksin. Bu ne demek? Gel şifresini kıralım:

**[Ethernet İsimlerini Decode Ediyoruz]**

```text
      1000   BASE    T
       |       |     |
       |       |     +--> Kablo Tipi
       |       |          (T = Twisted Pair/Bakır, X = Fiber, vb.)
       |       |
       |       +--------> Sinyal Tipi
       |                  (Baseband - Yani dijital sinyal)
       |
       +---------------->  Hız
                          (10 = 10 Mbps, 1000 = 1000 Mbps / 1 Gbps)

```

### Bilmemiz Gerekenler

Aşağıdaki tablo, CCNA sınavı için hayati önem taşır. Özellikle **Mesafe** sınırlarına dikkat et. Bakır kabloların 100 metre lanetini unutma!

**[Ethernet Standartları]**

| Hız | Halk Adı |  IEEE İsmi (Resmi Adı) | Kablo Tipi & Informal İsmi | Mesafe(Max) |
| --- | --- | --- | --- | --- |
| **10 Mbps** | Ethernet | **802.3** | **10BASE-T** (Bakır) | **100 m** |
| **100 Mbps** | Fast Ethernet | **802.3u** | **100BASE-T** (Bakır) | **100 m** |
| **1000 Mbps** | Gigabit Ethernet | **802.3z** | **1000BASE-LX** (Fiber) | **5000 m** (5 km) |
| **1000 Mbps** | Gigabit Ethernet | **802.3ab** | **1000BASE-T** (Bakır) | **100 m** |
| **10 Gbps** | 10 Gig Ethernet | **802.3an** | **10GBASE-T** (Bakır) | **100 m** |

Tabloya dikkat et: Hız ne kadar artarsa artsın (10 Mbps'den 10 Gbps'ye çıksa bile), eğer **Bakır** kullanıyorsan sınırın hep **100 metre**dir.
100 metreyi geçmek istiyorsan paraya kıyıp **Fiber (LX, SX vb.)** döşemek zorundasın :D

### Sürekli Evrim 

Ethernet 40 yıl önce başladı ama durmadı. IEEE ve endüstri ortakları (Ethernet Alliance) sürekli yeni standartlar geliştiriyor.

* Amaç hep aynı: **Daha hızlı, daha uzağa.**

---

## Hız Değişir, Kural Değişmez

Ethernet fiziksel olarak binbir çeşit kılığa girebilir (Bakır, Fiber, Hızlı, Yavaş). Ancak mantıksal olarak **tek bir teknoloji** gibi davranır.
**Neden?** Çünkü fiziksel katman ne olursa olsun, **Data-Link Layer** standardı her zaman aynıdır.

* **Header** ve **Trailer** formatı asla değişmez.
* İster 10 Mbps'lik eski bir bakır kablo olsun, ister 100 Gbps'lik süper hızlı bir fiber kablo; taşınan paketin (Ethernet Frame) yapısı birebir aynıdır.

**Olayın özü yani:**
> Fiziksel katman "Bit'leri kablodan nasıl geçiririm?" derdindedir.
> Data-Link katmanı ise "Bu zarfı (Frame) adrese nasıl teslim ederim?" derdindedir. Zarfın kağıdı (kablo) değişse de, üzerindeki adres yazma kuralı (Frame formatı) değişmez.

###  Bir Frame'in Yolculuğu

Örnekle canlandıralım. Burada PC1'den çıkan bir verinin, PC3'e giderken nasıl farklı yollardan geçtiğini ama hiç bozulmadığını görüyoruz.

**Senaryo:** PC1 bir Ethernet Frame'i oluşturur ve yola çıkarır. Hedef PC3'tür.

**[Ethernet LAN, birçok bağlantı türü üzerinden Data-Link Frame iletir]**

```text
      [ PC1 ]
         |
         | (Bağlantı 1: 10 Mbps - UTP Bakır)
         v
      [ SW1 ]
         |
         | (Bağlantı 2: 1 Gbps - Fiber Optic - 200m)
         v
      [ SW2 ]
         |
         | (Bağlantı 3: 10 Gbps - Fiber Optic - 1km)
         v
      [ SW3 ]
         |
         | (Bağlantı 4: 100 Mbps - UTP Bakır)
         v
      [ PC3 ]

```

### Yol Boyunca Neler Oldu?

Bu yolculukta Frame tam 4 farklı yoldan geçti:

1. **Bağlantı 1:** Yavaş bir bakır kablo (10 Mbps).
2. **Bağlantı 2:** Hızlı bir fiber kablo (1 Gbps).
3. **Bağlantı 3:** Çok hızlı ve uzun bir fiber kablo (10 Gbps, 1km).
4. **Bağlantı 4:** Orta hızda bir bakır kablo (100 Mbps).

Hız 1000 kat arttı, kablo camdan bakıra döndü, mesafe uzadı kısaldı...
AMA **Ethernet Frame** (Header + Data + Trailer) yapısı milim değişmedi.

SW1, SW2 ve SW3; gelen paketin hangi kablodan geldiğine bakmaksızın, sadece üzerindeki **Header**'ı okudu ve bir sonraki durağa iletti.

### Özet Tanım

O zaman "Ethernet LAN nedir?" sorusuna en güzel cevabı verelim:

Ethernet LAN; kullanıcı cihazlarının, Switch'lerin ve farklı kablo türlerinin birleşimidir.

* Her bağlantı farklı hızda ve türde olabilir.
* Ancak hepsi, **Ethernet Frame** adı verilen ortak bir dili konuşarak anlaşırlar.

---

## UTP ile Fiziksel Ethernet LAN'lar Oluşturma (Kablolamanın Temelleri)

Büyük resmi (Tüm ağı) görmeden önce, merceği iyice yakınlaştırıp iki Ethernet Node arasındaki tek bir bağlantıya odaklanacağız.
Eğer iki cihaz fiziksel olarak birbirine "Selam" veremezse, Frame göndermenin de bir anlamı kalmaz.

### Mahşerin Üç Atlısı

Bu bölümde, piyasada en çok karşılaşacağın ve bilmen gereken üç temel UTP standardına odaklanacağız. Hepsi de veriyi **her iki yönde** gönderme mantığıyla çalışır ama kablolama detayları farklıdır.

1. **10BASE-T (Ethernet):**
* Dedemiz. 10 Mbps hızındadır. Artık pek görmeyiz ama temeli budur.

2. **100BASE-T (Fast Ethernet - FE):**
* Yaygındır. 100 Mbps hızındadır. "Fast" lakabı buradan gelir.

3. **1000BASE-T (Gigabit Ethernet - GE):**
* Bugünün standardı. 1000 Mbps (1 Gbps) hızındadır.

### Kablolama Önemlidir

Sadece "Kabloyu tak çalıştır" demeyeceğiz. Kablonun içindeki o renkli tellerin hangisinden veri gittiğini, hangisinden veri geldiğini inceleyeceğiz. 
Özellikle 10 Mbps ile 1000 Mbps arasındaki kablolama farkı, bir networkçü için kritik detaydır.

---

## Twisted Pairs ile Veri İletimi

Ethernet kablosunun (UTP) içinden aslında veri değil, **elektrik** akar. Peki bu elektriği nasıl anlamlı bir veriye dönüştürüyoruz?
Bunu anlamak için süreci iki parçaya bölmemiz lazım:

1. **Elektrik Devresi Oluşturmak:** Akımın akmasını sağlamak.
2. **Sinyali Kodlamak:** O akımı 1 ve 0'lara çevirmek.

### 1. Devre Kurmak

Elektriğin temel kuralı şudur: Akımın akması için tamamlanmış bir döngüye ihtiyaç vardır. 
Tek bir telden elektrik gönderip işin bitmesini bekleyemezsin, o elektriğin geri dönmesi veya devreyi tamamlaması gerekir.

İşte bu yüzden Ethernet kablolarının içinde teller **Çiftler (Pairs)** halindedir. Ethernet kablosunun içindeki teller neden çifttir sorusuna güzel bi' cevap.

**[Bir Çift Üzerinde Tek Bir Elektrik Devresi Oluşturma]**

```text
        Node 1 (Gönderici)                          Node 2 (Alıcı)
      ----------------------                     -------------------
      |                    |                     |                 |
      |               (Tel 1 - Gidiş Yolu)       |                 |
      |      ------------------------------------------>           |
      |     |          (Elektrik Akımı)          |     |           |
      | [Devre]                                  |  [Devre]        |
      |     |          (Tel 2 - Dönüş Yolu)      |     |           |
      |      <------------------------------------------           |
      |                    |                     |                 |
      ----------------------                     -------------------

```

Node 1 (Gönderici) ve Node 2 (Alıcı), bir çift teli kullanarak aralarında kapalı bir devre oluştururlar. Böylece elektrik bir telden gider, devre tamamlanır.

### 2. 0 ve 1'in Dili

Devreyi kurduk, elektrik akıyor. Peki bu akım nasıl "Merhaba Dünya" oluyor? Burada devreye **Encoding Scheme** girer.
İki insanın aynı dili konuşması gibidir. Konuşan (Gönderici) belirli sesler çıkarır, Dinleyen (Alıcı) o seslerin ne anlama geldiğini bilir.
Gönderici, elektrik sinyalini (voltajı) belirli bir ritimde değiştirir. Alıcı da bu değişimleri izler.
* **Örnek (10BASE-T):** Voltaj aniden düşerse bu "0" demektir, yükselirse "1" demektir (Basitleştirilmiş anlatımı tabii). Alıcı bu voltaj değişimlerini algılar ve "Hah, burada 1 geldi, şimdi 0 geldi" diyerek veriyi çözer.

### Neden Büküyoruz?

Kabloların içindeki tellerin dümdüz durduğunu sanıyorsan yanılıyorsun. Adı üzerinde **Twisted Pair** (Bükümlü Çift). Renkli teller birbirine sarılarak tıpkı sarmaşık gibi ilerler.
Peki neden bu zahmete giriyoruz? Cevap: **Fizik Kuralları.**

1. **EMI (Electromagnetic Interference - Elektromanyetik Girişim):** Herhangi bir telden elektrik geçtiğinde, etrafında bir manyetik alan (gürültü diyebiliriz) oluşturur. Bu gürültü, yakınındaki diğer telleri bozar.
2. **Crosstalk (Çapraz Konuşma):** Aynı kablonun içindeki bir çift telin yarattığı o elektromanyetik gürültü, hemen yanındaki diğer çifti bozmasına "Crosstalk" denir.

Telleri birbirine bükmek, oluşan bu manyetik alanların birbirini **nötrlemesini** sağlar. Ne kadar sıkı bükülürse, sinyal o kadar temiz olur.

---

## UTP Ethernet Bağlantısını Çözme (Bağlantının Anatomisi)

Bir Ethernet Bağlantısından'dan bahsettiğimizde, iki Ethernet Node arasındaki fiziksel yolu kastediyoruz. Bu yolun çalışması için üç bileşenin kusursuz eşleşmesi gerekir.

### 1. Bileşenler

 Aşağıdaki yapıyı görelim. Bu, verinin bir uçtan diğer uca gitmesini sağlayan fiziksel zincirdir.

**[Ethernet Bağlantısının Temel Bileşenleri]**

```text
       Node A (PC/Switch)                         Node B (Router/PC)
      -------------------                        --------------------
      |                 |                        |                  |
      |   [RJ-45 Port]  |<-- (Eşleştirme) -->    |   [RJ-45 Port]   |
      |_______|_________|                        |_________|________|
              |                                            |
          (Bağlantı)                                    (Bağlantı)
              |                                            |
      [RJ-45 Konnektör]                            [RJ-45 Konnektör]
              \                                            /
               \__________________________________________/
                            (UTP Kablosu)
                        (İçindeki Bakır Teller)

```

* **Kablo:** Bakır telleri taşıyan ana gövde.
* **Konnektör:** Kablonun ucundaki plastik ve metal parça.
* **Port:** Cihazın üzerindeki, konnektörün "tık" diye oturduğu yuva.

### 2. Kablo ve Renkler

Kablonun dışındaki plastik kılıfı soyarsan içinde birbirine bükülmüş bakır teller görürsün.

* **Twisted Pairs (Bükümlü Çiftler):** Teller ikili gruplar halindedir.
* **Renk Kodları:** Her çiftin bir rengi vardır (Mavi, Turuncu, Yeşil, Kahverengi).
* Bir tel tamamen renklidir (Örn: **Mavi**).
* Eşi ise o rengin çizgili halidir (Örn: **Mavi-Beyaz Çizgili**).

Kablonun içinde toplam **4 Çift (8 Tel)** vardır. Ancak hız standartlarına göre kullanım değişir:
* **10BASE-T & 100BASE-T:** Sadece **2 Çift** (4 Tel) kullanır.
* **1000BASE-T (Gigabit):** **4 Çiftin tamamını** (8 Tel) kullanır.

https://www.computercablestore.com/straight-through-crossover-and-rollover-wiring
 
### 3. O Meşhur Uç RJ-45 Konnektör

Ethernet kablolarının ucunda gördüğün o şeffaf parçanın teknik adı **RJ-45** konnektörüdür.

* **Pinler:** Konnektörün ucunda **8 adet** metal diş (pin) bulunur.
* **Görevi:** Kablonun içindeki 8 adet bakır tel, bu 8 adet pine kadar gelir ve sıkıştırılır. Sen konnektörü Porta taktığında, bu pinler cihazın içindeki elektronik devrelere temas eder ve **elektrik akışı** başlar.

Yakınında bir Ethernet kablosu varsa ucuna (şeffaf kısma) dikkatlice bak. İçindeki renkli telleri ve uçtaki altın sarısı 8 adet metal pini göreceksin. İşte tüm internet o minik pinlerin birbirine değmesiyle çalışıyor.

## Portlar ve Modüller

Bağlantıyı tamamlamak için kablonun iki ucundaki RJ-45 konnektörlerinin gireceği yuvalara ihtiyacımız var.

### 1. RJ-45 Portları & NICs (Sabit Portlar)

Bilgisayarların ve Switch'lerin üzerindeki o standart internet girişlerine **RJ-45 Port** denir.

* **NIC (Network Interface Card):** Bilgisayarların ağa bağlanmasını sağlayan karttır. Laptoplarda anakarta gömülüdür, masaüstü PC'lerde harici kart olarak takılabilir.
* **Switch Ports:** Switch'lerin olayı budur; üzerinde onlarca RJ-45 portu bulunur ki birçok cihazı aynı anda bağlayabilsin.

**[RJ-45 Konnektör ve Portlar]**

```text
          (Konnektör)                 (NIC Kartı)
          [||||||||]               [  Devre Kartı    ]
           |      |                [  ___________    ]
        RJ-45 Kafası               [ |RJ-45 Port |   ]
                                   [ |___________|   ]

             (LAN Switch Yüzü)
        _________________________________
       | [Port] [Port] [Port] [Port] ... |
       | [Port] [Port] [Port] [Port] ... |
       |_________________________________|

```

### 2. Değiştirilebilir Modüller

Cisco Switch alırken her zaman "sabit" portlarla sınırlı kalmak zorunda değilsin. Uzmanlara esneklik sağlamak için **Swappable Transceivers (Tak-Çıkar Modüller)** icat edilmiştir.
Diyelim ki bir Switch aldın. Bugün bakır kablo kullanıyorsun ama yarın fiber kullanman gerekti. Switch'i çöpe mi atacaksın? Hayır! Sadece ucundaki modülü değiştirirsin.

**[SFP+ Modülü ve Switch Yuvası]**

```text
      (Cisco Switch SFP+ Slot)
      _______________________
     |   [ Slot 1 (Boş) ]    |  <--- Modül buraya girer
     |_______________________|

             ^
             | (Ekle)
             |
      [ SFP+ Modülü ]  <--- Gümüş renkli metal parça
      [_____________]
             |
             | (Kablo buraya bağlanmıştır)
             |
```

### Kısaltmalar Sözlüğü

Sınavda bu kısaltmaların farkını sorarlar. Bilmemiz gereken 3 temel modül tipi var:

| Kısaltma | Full İsim | Açıklama |
| --- | --- | --- |
| **GBIC** | Gigabit Interface Converter | **Eski nesil.** Çok büyüktür, yer kaplar. Gigabit hızındadır. Artık pek kullanılmaz. |
| **SFP** | Small Form Pluggable | **Yeni nesil.** GBIC'in yerini almıştır. Çok daha küçüktür (Mini-GBIC de denir). Gigabit hızındadır. |
| **SFP+** | Small Form Pluggable **Plus** | **Hızlı nesil.** Boyutu SFP ile aynıdır ama **10 Gbps** hızındadır. "Plus" kelimesi hız artışını (1G -> 10G) temsil eder. |

Sahada SFP ve SFP+ fiziksel olarak birbirine çok benzer (aynı boyuttadır). Üzerindeki etiketi okumadan hangisinin 1G, hangisinin 10G olduğunu anlamak zordur. Yanlış yuvaya takarsan çalışmayabilir, dikkat!
**RJ-45** bizim standart ev tipi girişimiz, **SFP/SFP+** ise kurumsal dünyadaki o havalı, tak-çıkar fiber yuvalarımız. 

(Bu arada bu kabloları, konnektörleri vs. mutlaka webten resimlerine bakın.)

---

## Kablo Sıralaması ve Kurallar

Bir Ethernet kablosunun içinde 8 tel (4 çift) olduğunu biliyoruz. Ama bu telleri rastgele bağlayamayız.

* **Çiftlik Ortamı:** Kendi tarlanda kamyonu istediğin gibi sürersin, kimse karışmaz.
* **Otoban Ortamı:** Ana yola çıktığın an, belli bir şeritten gitmek, hız kurallarına uymak zorundasın.

Ethernet standartları da (10BASE-T ve 100BASE-T) bu "Otoban Kuralları"nı belirler. Hangi pinin veri göndereceğini, hangisinin veri alacağını kesin olarak tanımlar.

### 10BASE-T ve 100BASE-T (Özel Durum)

Bu iki standart (Eski 10 Mbps ve Hızlı 100 Mbps) çok ilginç bir özelliğe sahiptir:

1. **Sadece 2 Çift Kullanırlar:** Kablonun içinde 4 çift (8 tel) olsa da, bu standartlar trafiği yönetmek için sadece **2 çiftini (4 tel)** kullanır.
2. **Boşta Kalanlar:** Diğer 2 çift (4 tel) boşta durur, kullanılmaz.

10BASE-T ve 100BASE-T, iletişimi sağlamak için kablonun içindeki 1, 2, 3 ve 6 numaralı pinleri kullanır. (Diğerleri 4, 5, 7, 8 boştadır).
Ancak **Gigabit Ethernet (1000BASE-T)** geldiğinde işler değişecek; o, 4 çiftin hepsini kullanacak.

### 1. Altın Kural (Straight-Through)

10BASE-T ve 100BASE-T sadece **2 Çift (4 Tel)** kullanır dedik. Peki hangi pinler? **Cevap:** 1, 2, 3 ve 6.
Ama her cihaz bu pinleri aynı amaçla kullanmaz. İşte burası çok kritik:

* **PC (NIC):**
* **Konuşur (Transmitter - Tx):** Pin **1 ve 2**
* **Dinler (Receiver - Rx):** Pin **3 ve 6**

* **Switch:**
* **Dinler (Receiver - Rx):** Pin **1 ve 2**
* **Konuşur (Transmitter - Tx):** Pin **3 ve 6**

Dikkat ettiysen Switch, PC'nin tam **zıttı** olarak tasarlanmıştır.
PC 1 ve 2'den bağırır, Switch 1 ve 2'den dinler. Bu sayede kabloyu dümdüz (Pin 1 -> Pin 1'e) bağlasak bile iletişim sağlanır.

### Akışı Görelim

PC ve Switch arasındaki akışın nasıl başladığını görelim.

**[Her Yön için Bir Çift Kullanma (10/100 Mbps)]**

```text
         PC (Gönderici)                                Switch (Alıcı)
        -------------                                -------------------
        Tx [Pin 1]  ------------------------------>  [Pin 1] Rx
        Tx [Pin 2]  ------------------------------>  [Pin 2] Rx
           (Twisted Pair 1: Veri Gönderimi ->)


        Rx [Pin 3]  <------------------------------  [Pin 3] Tx
        Rx [Pin 6]  <------------------------------  [Pin 6] Tx
           (Twisted Pair 2: <- Veri Alımı)

```

1. **Üstteki Çift (1 ve 2):** PC buradan veri gönderir, Switch buradan veriyi alır.
2. **Alttaki Çift (3 ve 6):** Switch buradan cevap verir, PC buradan cevabı alır.

Bu "Zıt Karakterli" (PC vs Switch) tasarım sayesinde, araya taktığımız kablonun tellerini çaprazlamamıza gerek kalmaz. Dümdüz kablo (**Straight-Through**) işimizi görür.

### Straight-Through Pinout (Düz Kablo - Bağlantı)

Önce bir terimi netleştirelim: **Pinout**.
* **Tanım:** RJ-45 konnektöründeki 8 adet pin yuvasına hangi renk telin gireceğini belirleyen sıralamaya "Pinout" denir.

PC'yi Switch'e bağlarken kullandığımız standart kabloya **Straight-Through (Düz)** kablo denir. Adı üstünde, sinyal "dümdüz" karşıya geçer.

#### Mantığı Nedir? 

Düz kabloda hiçbir hile yoktur:

* Kablonun bir ucundaki **Pin 1**, diğer ucundaki **Pin 1**'e gider.
* **Pin 2** -> **Pin 2**'ye gider.
* **Pin 3** -> **Pin 3**'e gider.
* Ve en önemlisi: **Pin 6** -> **Pin 6**'ya gider.

Hatırlarsan 10BASE-T ve 100BASE-T sadece 1, 2, 3 ve 6. pinleri kullanıyordu. Bu yüzden Düz Kablo şemalarında genelde 4, 5, 7 ve 8. pinlerin "Not Used" (Kullanılmıyor) olduğunu görürsün.

### Kablo Röntgeni

Burada sol tarafta PC'nin gönderdiği sinyalin, sağ tarafta Switch'in doğru pinine nasıl "dümdüz" gittiğini göreceğiz.

**[10BASE-T ve 100BASE-T Straight-Through Pinout]**

```text
       A Yüzü (PC Ports)                     B Yüzü (Switch Ports)
      -----------------                     ---------------------
      Pin 1 (Tx) -------------------------> Pin 1 (Rx)
      Pin 2 (Tx) -------------------------> Pin 2 (Rx)
      Pin 3 (Rx) <------------------------- Pin 3 (Tx)
      Pin 4 (Not Used)                      Pin 4 (Not Used)
      Pin 5 (Not Used)                      Pin 5 (Not Used)
      Pin 6 (Rx) <------------------------- Pin 6 (Tx)
      Pin 7 (Not Used)                      Pin 7 (Not Used)
      Pin 8 (Not Used)                      Pin 8 (Not Used)
```

**Neden Çalışıyor?** Bir önceki konuda öğrenmiştik:

* **PC:** 1 ve 2'den konuşur (Tx).
* **Switch:** 1 ve 2'den dinler (Rx).

Kabloyu **düz (1-1, 2-2)** bağladığımızda, PC'nin ağzı direkt Switch'in kulağına denk gelir. Bu yüzden PC ve Switch arasında **Straight-Through(Düz Kablo)** kullanılır.

### Neden Düz Kablo Çalışır?

Berkay (PC) ve Switch arasındaki ilişkiyi görelim.

* **Berkay (PC):** Pin 1 ve 2'den konuşur (Tx).
* **Switch:** Pin 1 ve 2'den dinler (Rx).

Bu cihazlar "Zıt Karakterli" olduğu için, kabloyu dümdüz bağlamak yeterlidir. Berkay'ın ağzı, Switch'in kulağına denk gelir.

**[Ethernet Straight-Through Kablo Konsepti]**

```text
       Berkay (PC)                                  Switch
      ------------                                 --------
      Tx [1, 2]  ------------------------------>  [1, 2] Rx  ---> Berkay 1,2 pinlerinden yolluyor, switch ise alıyor.
      Rx [3, 6]  <------------------------------  [3, 6] Tx  ---> Berkay 3,6 pinlerinden alıyor, switch ise yolluyor.

```

### Aynı Cihazlar Sorunu

Peki ya iki tane **Switch'i** birbirine bağlarsak ne olur? (Veya iki PC'yi?)

İşte o zaman şöyle bir durum yaşanır:

1. **Switch A:** Pin 3 ve 6'dan konuşur (Tx).
2. **Switch B:** O da Pin 3 ve 6'dan konuşur (Tx).

Eğer araya Düz Kablo takarsan ne olur?

* **Tx -> Tx:** İkisi de aynı hattan bağırır.
* **Rx -> Rx:** İkisi de aynı hattan dinler ama ses gelmez.
* **Sonuç:** İletişim kopar.

### Çözüm: Crossover Cable (Çapraz Kablo)

Bu sorunu çözmek için kablonun içindeki telleri bizim çaprazlamamız gerekir. Buna **Crossover Cable** denir.
**Görevi:** Bir tarafın gönderdiği (Tx) sinyali alır, havada takla attırıp diğer tarafın dinlediği (Rx) pine sokar.

**[Crossover Ethernet Cable Konsepti]**

Burada Switch'ten Switch'e bağlantı örneğini görüyoruz.

```text
       Switch A                                     Switch B
      ----------                                   ----------
      Rx [1, 2]  <-------------X-----------------  [3, 6] Tx
                                \ /
                                 X   (CROSSOVER)
                                / \
      Tx [3, 6]  --------------X---------------->  [1, 2] Rx

```

**Dikkat Et:**

* Kablonun solundaki **Pin 1**, sağdaki **Pin 3**'e gider.
* Kablonun solundaki **Pin 2**, sağdaki **Pin 6**'ya gider.

Düz kablonun tersi gibi düşün bilgisayar > switch normalde: 1,2 -> 1,2 ama bunda 1,2 -> 3,6. 

> * **Farklı Cihazlar (PC <-> Switch):** Straight-Through.
> * **Aynı Cihazlar (Switch <-> Switch, PC <-> PC):** Crossover.

## Hangi Kabloyu Seçmeliyim?

Sınavda iki cihaz verecekler ve "Aradaki kablo ne olmalı?" diye soracaklar. Ezberlemene gerek yok, sadece şu basit mantığı kur:

### 1. Mantık

* **Aynı Takı -> Crossover:**
İki cihaz da aynı pinlerden (Örn: 1 ve 2) konuşuyorsa, seslerinin çarpışmaması için kabloyu çaprazlaman gerekir.
* **Örnek:** Switch <-> Switch, PC <-> PC, Router <-> PC.

* **Farklı Takım -> Straight-Through:**
Biri 1,2'den konuşurken diğeri 3,6'dan konuşuyorsa, birbirlerini tamamlıyorlardır. Kabloyu düz takman yeterlidir.
* **Örnek:** PC <-> Switch.

### 2. Takımlar: Kim Kimdir?

Bu tabloyu adın gibi bilmelisin. Cihazları iki takıma ayırıyoruz:

**[Cihaz Pinout Grupları (10/100BASE-T)]**

| **Takım 1 (1,2 üzerinden gönderir)** | **Takım 2 (3,6'da gönderir)** |
| --- | --- |
| **PC NICs** | **Switches** |
| **Routers** | **Hubs** (Hublar - Eski ama sınavda çıkar) |
| **Wireless Access Points** (AP'ler) |  |

Çoğu öğrenci **Router**'ı karıştırır. Router, Switch gibi değil, **PC gibi** davranır (Tx = 1,2).
Bu yüzden **PC'yi Router'a bağlarken Crossover kablo gerekir!** (İkisi de aynı takımda).

### 3. Senaryo: Kampüs Ağı

Burada PC'ler Switch'lere, Switch'ler de birbirine bağlanıyor. Kablo seçimlerine dikkat et.

**[Straight ve Crossover Kabloların Tipik Kullanım Alanları]**

```text
      Bina 1                                         Bina 2
      +------------------------+                     +------------------------+
      |  [PC]                  |                     |                  [PC]  |
      |    | (Düz)             |                     |             (Düz) |    |
      |    v                   |                     |                   v    |
      | [Switch 11]            |                     |            [Switch 22] |
      |    |                   |                     |                   ^    |
      |    | (Çapraz)          |                     |       (Çapraz)    |    |
      |    v                   |                     |                   |    |
      | [Switch 12] ---------->| (Çapraz Kablo) |<---------- [Switch 21] |
      +------------------------+                     +------------------------+

```

1. **PC -> Switch:** Farklı takımlar (1,2 vs 3,6). **Straight-Through** kullanılır.
2. **Switch -> Switch:** Aynı takımlar (3,6 vs 3,6). **Crossover** kullanılır.

### Gerçek Hayat vs. Sınav (CISCO AUTO-MDIX FARKI)

Burada çok önemli bir parantez açalım.

**Gerçek Hayat:** Eve gittin, yanlış kabloyu taktın (Mesela Switch-Switch arasına Düz kablo taktın). Çalışır mı? **Evet, %99 çalışır.**
Çünkü modern Cisco cihazlarda **Auto-MDIX** denen bir özellik vardır. Cihaz yanlış kabloyu fark eder ve elektronik olarak pinlerin yerini kendisi değiştirir. Efsane değil mi ya? 
**Sınav Dünyası (CCNA Exam):** Auto-MDIX yokmuş gibi davranacaksın!
Sınavda sana "Switch ile Switch arasına ne takılır?" derlerse, **"Crossover"** diyeceksin. "Ama evde düz kabloyla çalışıyor" dersen puanı silerler. 

## 1000BASE-T için UTP Kablolama Pinouts (Gigabit Ethernet)

Gigabit Ethernet (1000BASE-T), önceki standartlardan (10/100) iki temel farkla ayrılır:

1. **4 Çiftin Hepsi:** Artık 4, 5, 7 ve 8 numaralı pinler süs diye durmuyor. Gigabit hızına ulaşmak için kablonun içindeki **8 telin tamamı** kullanılır.
2. **İleri Teknoloji:** 10/100'de bir çift sadece konuşur (Tx), diğeri sadece dinlerdi (Rx). Gigabit'te ise teknoloji o kadar ilerlemiştir ki, her bir tel çifti **aynı anda hem veri gönderebilir hem de alabilir.**

### Gigabit Straight-Through Kablo 

Mantık yine aynıdır: Pinler karşılıklı (1-to-1) eşleşir. Ancak bu sefer kadro tamdır.

**[1000BASE-T'ye 4-Pair Straight Kablo]**

```text
       PC (A yüzü)                          Switch (B yüzü)
      -------------                        -----------------
      Çift 1: [1, 2] --------------------> [1, 2] : Çift 1
      Çift 2: [3, 6] --------------------> [3, 6] : Çift 2
      
      -- NEW FOR GIGABIT --
      Çift 3: [4, 5] --------------------> [4, 5] : Çift 3
      Çift 4: [7, 8] --------------------> [7, 8] : Çift 4

```

1->1, 2->2, ... 8->8. Hiçbir pin boşta kalmaz.

### Gigabit Crossover Kablo 

İşte burası sınavda can yakar. 10/100 için sadece Turuncu ve Yeşil çiftleri (1,2 ve 3,6) çaprazlıyorduk.

Gigabit'te ise **Double Crossover (çifte)** yapılır:

1. **Klasik Çapraz:** 1,2 ile 3,6 yer değiştirir. (Mavi ve Turuncu gibi düşün).
2. **Yeni Çapraz:** 4,5 ile 7,8 yer değiştirir. (Mavi ve Kahverengi çiftleri).

Bir Gigabit Crossover kablosunda sadece 1-3, 2-6 değil; **AYNI ZAMANDA** 4-7 ve 5-8 pinleri de çaprazlanır. Yani kablonun içi tam bir düğüm gibidir.

---

## Fiber ile Fiziksel Ethernet LAN'lar Oluşturma (Fiber Optik Dünyası)

Bir kurumsal ağda kablolama stratejisi şöyledir:

1. **UTP (Bakır):** Masalara giden kısa yollar (Access Layer). Çünkü ucuz ve 100 metre çoğu zaman yeterli.
2. **Fiber:** Uzun mesafeler ve ağın omurgası yani backbone.

### Maliyet ve Mesafe Dengesi

* **UTP:** Ucuzdur, kurulumu kolaydır ama **100 metre** sınırına takılır. Ayrıca elektrik gürültüsünden (EMI) etkilenir.
* **Fiber:** Pahalıdır, kurulumu hassastır ama **kilometrelerce** gidebilir. Elektrikten etkilenmez (çünkü ışık kullanır).

### Fiber Nerede Kullanılır?

Fiberi genelde "Switchler arası" bağlantılarda görürüz.

**[UTP vs. Fiber Kullanım Senaryosu]**

```text
       Bina A                                        Bina B
      (3. Kat)                                      (1. Kat)
     +-----------+                                +-----------+
     | [Switch A]|                                | [Switch B]|
     +-----+-----+                                +-----+-----+
           |                                            |
           | (Fiber Cable - 500m / 2km / 10km...)       |
           | <========================================> | (Backbone Bağlantısı)
           |             (Cam Core)                     |
           |                                            |
           v                                            v
      (UTP < 100m)                                 (UTP < 100m)
           |                                            |
        [ PC ]                                       [ PC ]

```

* **PC -> Switch:** Kısa mesafe olduğu için **UTP** (Bakır) kullanılır.
* **Switch A -> Switch B:** Binalar arası mesafe 100 metreyi geçtiği için mecbur **Fiber** kullanılır.

Fiber sadece mesafe için değil, **güvenlik** ve **gürültü** için de seçilir. 
Fabrika gibi çok fazla manyetik alanın (büyük motorlar, jeneratörler) olduğu yerlerde bakır kablo sinyali bozar. Fiber cam olduğu için manyetik alandan etkilenmez.

### Fiber Kablonun Anatomisi

Fiber kablolar, veriyi elektrik yerine **ışık** darbeleri olarak iletir bunu biliyoruz.

* **0 ve 1:** Işık var (1), Işık yok (0) veya Işığın şiddeti değişiyor.
* **Malzeme:** Merkezde çok ince, esnek bir cam bulunur.

### Fiber Kablo Katmanları

Fiber kablo sadece camdan ibaret değildir. O narin camın kırılmaması için kat kat zırhlanması gerekir. İçten dışa doğru şu katmanları görürüz:
Ama yine de webte, katmanları görsel olarak göreceğiniz figürlere bakın.

**[Fiber-Optic Yapısı]**

```text
             Kesit Görünümü                    Yan Profil Görünümü
      ----------------------------           ------------------------
      
      [ Outer Jacket ] (En Dış)  ----------->  =========== (Kablo Dışı) (5)
             |
      [ Strengthener ] (Kevlar)  ----------->  =========== (Güçlendirici İpler) (4)
             |
        [ Buffer ]     (Tampon)  ----------->  =========== (Plastik Koruma) (3)
             |
       [ Cladding ]    (Kılıf)   ----------->  =========== (Yansıtıcı Cam) (2)
             |
         [ CORE ]      (Çekirdek) ---------->  =========== (Işığın Gittiği Yol) (1)

```

1. **Core:**
* En içteki kısımdır.
* Işığın (Verinin) geçtiği asıl cam tüneldir. Saç telinden bile incedir.

2. **Cladding (Kılıf/Kaplama):**
* Core'un hemen etrafını saran ikinci cam katmandır.
* **Görevi:** Işığı Core'un içinde hapseder (Ayna gibi yansıtarak ışığın dışarı kaçmasını engeller).

3. **Buffer (Tampon):**
* Cama zarar gelmesin diye onu saran plastik tabakadır.

4. **Strengthener (Güçlendirici):**
* Kablo çekilirken veya bükülürken kopmasın diye araya eklenen iplerdir (Genelde kurşun geçirmez yeleklerde kullanılan **Kevlar** malzemesi kullanılır).


### Işık Nasıl İlerler?

Işık, **Core** (Çekirdek) içinden geçerken düz bir çizgide gitmez (Multimode için bunu not al). Dışarı kaçmaya çalışır.

* **Cladding(Kılıfın) Rolü:** Cladding, Core'dan daha farklı bir cam yapısına sahiptir. Işık Cladding'e çarptığında, Cladding onu bir ayna gibi tekrar Core'un içine yansıtır.
* **İç Yansıma:** Bu sayede ışık, kablonun içinde zikzaklar çizerek seke seke ilerler ve dışarı kaçmaz.

### Multimode (MMF) vs. Single-Mode (SMF)

Fiber kablolar, ışığı taşıma şekline göre ikiye ayrılır.

#### 1. Multimode Fiber (MMF - Çok Modlu)

* **Kaynak:** **LED** kullanır.
* **Core:** Çekirdeği geniştir.
* **Işığın Hareketi:** Geniş olduğu için ışık farklı açılardan girer ve duvarlara çarparak ilerler.
* **Kullanım:** Daha kısa mesafeler (Bina içi, kampüs içi 400m-500m civarı).
* **Maliyet:** Kablo ve cihazları daha ucuzdur.

**[Multimode Fiber Gönderimi]**

```text
       [ LED ]  ~ ~ ~ >  / \ / \ / \ / \ / \   (Işık Sekmeleri)
                        =====================
                           Core (Geniş)
                        =====================

```

#### 2. Single-Mode Fiber (SMF - Tek Modlu)

* **Kaynak:** **Laser** kullanır.
* **Core:** Çekirdeği çok incedir (MMF'in 5'te 1'i kadar).
* **Işığın Hareketi:** Çekirdek o kadar dardır ki, ışık sekecek yer bulamaz. Mecburen "tek bir modda" dümdüz (ok gibi) gider.
* **Kullanım:** Çok uzun mesafeler (Kilometrelerce, şehirlerarası).
* **Maliyet:** Lazer teknolojisi pahalı olduğu için donanımı (SFP+) daha pahalıdır.

**[Single-Mode Fiber Gönderimi]**

```text
       [ LASER ] ----->  ---------------------   (Düz Işık)
                         =====================
                         =====================
                             Core (İnce)

```

### Tx ve Rx Kuralı

UTP kablosunun içinde hem gidiş hem geliş telleri vardı. Fiberde ise (genellikle) her bir cam tel tek yönlüdür.
Bu yüzden cihazları bağlarken **2 Tel** kullanırız:

1. **Tx (Transmit):** Göndermek için.
2. **Rx (Receive):** Almak için.

**[Rx'e Bağlı Tx'li İki Fiber Kablo]**

Tıpkı UTP'deki gibi, birinin ağzı diğerinin kulağına bağlanmalıdır.

```text
       Cihaz A                                       Cihaz B
      ----------                                   ----------
      [ Tx ] ----------------(Fiber Tel 1)---------> [ Rx ]
      
      [ Rx ] <---------------(Fiber Tel 2)---------- [ Tx ]

```

Sahada fiber kablo takarken uçlarına bakarsan genelde ikili bitişik konektörler (LC veya SC connector) görürsün. Biri Tx, biri Rx içindir. Eğer tek tek takıyorsan ve link ışığı yanmıyorsa, uçları ters çevir (Tx'i Rx'e tak).

## Fiber Standartları ve Karşılaştırma

Fiber kullanmak için Switch'in üzerinde uygun yuvanın (SFP+) olması gerekir. Hatırlarsan SFP+ modülleri tak-çıkar yapıdaydı. Bu sayede istediğin standardı (kısa mesafe veya uzun mesafe) seçip takabilirsin.

### Standartlar 

IEEE, fiber standartlarına isim verirken harfleri kodlar. Bu harflerin ne anlama geldiğini bilirsen ezber yapmana gerek kalmaz:

* **S (Short):** Kısa Mesafe -> **Multimode**
* **L (Long):** Uzun Mesafe -> **Single-Mode**
* **E (Extended):** Çok Uzun Mesafe -> **Single-Mode**

Aşağıdaki tablo, **10 Gbps (10GBASE)** standartlarının özetidir.

**[10-Gbps Fiber Standartları]**

| Standard | Anlamı | Kablo Tipi | Max |
| --- | --- | --- | --- |
| **10GBASE-S** | **S**hort (Kısa) | **MM** (Multimode) | **400 m** |
| **10GBASE-LX4** | - | **MM** (Multimode) | **300 m** |
| **10GBASE-LR** | **L**ong (Uzun) | **SM** (Single-Mode) | **10 km** |
| **10GBASE-E** | **E**xtended (Ekstra Uzun) | **SM** (Single-Mode) | **30km - 40km** |

Bir "Ofis Parkı" veya kampüs düşün. Binalar yan yanaysa (birkaç yüz metre) **Multimode (S)** yeterlidir. Ama şehrin diğer ucundaki şubeye gideceksen **Single-Mode (L veya E)** şarttır.

### Nihai Karşılaşma: UTP vs. MM vs. SM

Peki neden her yere Fiber döşemiyoruz? Veya neden her yere UTP çekmiyoruz? İşte networkçü kararı burada devreye girer: **Maliyet vs. Performans.**

Aşağıdaki tablo, bu üç teknolojinin karnesidir.

**[Karşılaştırma: UTP, Multimode, ve Single-Mode]**

| Kriter | **UTP** | **Multimode** | **Single-Mode** |
| --- | --- | --- | --- |
| **Maliyet (Kablolama)** | **Düşük (Low)** | Orta | Orta |
| **Maliyet (Switch Portu/SFP)** | **Düşük** | Orta (LED ucuzdur) | **Yüksek** (Lazer pahalıdır) |
| **Mesafe** | **En Kötü (100m)** | Orta (500m) | **En İyi (30-40km+)** |
| **EMI** | **Düşük** (Etkilenir) | **Tam** | **Tam** |
| **Güvenlik** | **Düşük** (Riskli) | **Tam** | **Tam** |

### Fiber Neden Daha Güvenli?

1. **EMI:** Fabrikada büyük motorların yanından UTP geçirirsen, motorun manyetik alanı kablodaki veriyi bozar. Fiber **cam** olduğu için elektrikten/mıknatıstan etkilenmez. Fabrikalar için fiber şarttır.
2. **Güvenlik:** Bakır kablodan geçen elektrik dışarıya çok hafif bir sinyal yayar. Kötü niyetli biri hassas cihazlarla kabloya dokunmadan veriyi çalabilir. Fiberde ise ışık camın içinde hapsolur, dışarı sızmaz. Bu yüzden askeriye ve devlet daireleri fiberi sever.

---

## Ethernet Frame Yapısı

Şimdi tekrar buraya dönelim: Fiziksel bağlantı ne olursa olsun, Ethernet veriyi belirli bir kurala göre paketler. Bu pakete **Ethernet Frame** denir.

Bir mektup gönderdiğini düşün:

1. **Header:** Zarfın üzerindeki adres bilgileri.
2. **Data:** Zarfın içindeki asıl mektup.
3. **Trailer:** Zarfın arkasındaki mühür (Bütünlüğünü korumak için).

### Frame Anatomisi

Bu yapı, modern ağların temel taşıdır. Her bir byte'ın bir görevi vardır.

**[Yaygın Olarak Kullanılan Ethernet Frame Formatı]**

```text
   Bytes:    (7)      (1)       (6)        (6)      (2)       (46 - 1500)      (4)
          +-------+-------+-----------+----------+------+-------------------+------+
          | Pre-  |  SFD  |   Dest.   |  Source  | Type |    DATA & PAD     | FCS  |
          | amble |       |    MAC    |   MAC    |      |     (Packet)      |      |
          +-------+-------+-----------+----------+------+-------------------+------+
          <--- HEADER (Header Kısmı) ------------------->                   <-TRAILER->

```

### Alanların Görevleri

Bu tabloyu adın gibi bilmelisin. Sınavda "FCS ne işe yarar?" veya "MTU nedir?" diye sorarlar.

**[Ethernet Header ve Trailer Alanları]**

| Alan | Bytes | Görevi |
| --- | --- | --- |
| **Preamble** (Önsöz) | 7 | **Hazırol:** Alıcıya "Hey, paket geliyor, senkronize ol!" diyen kısımdır. (101010... sinyali). |
| **SFD** (Start Frame Delimiter) | 1 | **Başla:** "Tamam, ısınma bitti, asıl adres (MAC) şimdi başlıyor" uyarısıdır. |
| **Destination MAC** | 6 | **Alıcı:** Bu paket kime gidiyor? (Örn: Irem'in Bilgisayarı). |
| **Source MAC** | 6 | **Gönderici:** Bu paketi kim yolladı? (Örn: Berkay'ın Bilgisayarı). |
| **Type** | 2 | **İçerik:** Kutunun içinde ne var? IPv4 mü, IPv6 mı? (Network katmanındaki protokolü belirtir). |
| **Data and Pad** | 46 - 1500 | **Yük (yani Payload):** Asıl taşınan veri (IP Paketi). <br> <br> *Not:* Eğer veri çok küçükse (46 byte'tan az), boşluk doldurmak için **Padding (Dolgu)** eklenir. |
| **FCS** (Frame Check Sequence) | 4 | **Kontrol:** Paket yolda bozuldu mu? Alıcı buradaki matematiksel hesaba bakar. Tutmazsa paketi çöpe atar. |

### MTU (Maksimum Taşıma Birimi)

Burada çok kritik bir kavram var: **MTU (Maximum Transmission Unit).**

Tabloya dikkat edersen **Data** alanı en fazla **1500 Byte** olabilir diyor.

* Yani Ethernet diyor ki: "Kardeşim, benim kamyonum (Frame) en fazla 1500 byte'lık yük (IP Paketi) taşıyabilir."
* İşte bu 1500 sınırına **MTU** denir.
* **Minimum sınır** ise 46 byte'tır. Eğer mektubun çok kısaysa, Ethernet onu 46'ya tamamlamak için yanına boş kağıtlar (Padding) koyar.

İnternette bazen "sayfa açılmıyor" sorunları yaşanır ya, işte o bazen bu MTU ayarının yanlış yapılmasından kaynaklanır. Paket 1500'den büyükse, Router onu parçalamak zorunda kalır veya çöpe atar.

