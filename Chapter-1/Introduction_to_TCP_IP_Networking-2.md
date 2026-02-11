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
