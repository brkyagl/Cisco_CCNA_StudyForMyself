## Ethernet Nedir?

"Ethernet" terimini duyduğunda aklına sadece kablo gelmesin. Ethernet, tek bir standart değildir; bir **Standartlar Ailesidir**.
Bu aile, dünyanın en popüler **kablolu LAN teknolojisini** oluşturur.

### Ethernet Hangi Katmanlarda Çalışır?

Ethernet, TCP/IP veya OSI modelinin en alt iki katmanını tanımlar ve yönetir:

1. **Physical Layer:** Kablolar, konektörler, elektrik sinyalleri.
2. **Data-Link Layer:** Protokol kuralları, frameler.

### IEEE (Patron Kim?)

Ethernet standartlarını belirleyen kurum **IEEE**'dir (Institute of Electrical and Electronics Engineers).

Bu adamlar şunlara karar verir:

* Kullanılacak **kablolama** tipi ne olacak?
* Kablonun ucundaki **konektörler** nasıl olacak?
* Cihazların uyması gereken **protokol kuralları** nelerdir?

**[Modelde Ethernet'in Yeri]**

```text
       OSI Layers                 Ethernet'in Sorumluluğu
   ------------------           -----------------------------
   3. Network (IP)
   ------------------
   2. Data Link       <-------  [ Protocol Kuralları & Frames ]
   ------------------           (IEEE tarafından tanımlanmıştır)
   1. Physical        <-------  [ Kablolama & Konektörler ]

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
