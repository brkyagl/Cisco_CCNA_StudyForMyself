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
