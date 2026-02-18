## Wide-Area Networks Nedir?

Bir senaryo düşün:

* Sen bir **Şube Ofisinde** oturuyorsun. Bilgisayarın (PC, Tablet vs.) oradaki yerel ağa (LAN) bağlı.
* Ancak girmek istediğin web sitesi veya veri tabanı, şirketin **Merkez Ofisinde** duruyor.
* Şube ile Merkez arasında kilometrelerce mesafe var. Kabloyu çekip oraya kadar götüremezsin.

İşte burada devreye **WAN (Geniş Alan Ağı)** girer.

### LAN vs. WAN

WAN teknolojileri, **Fiziksel (Layer 1)** ve **Data-Link (Layer 2)** katmanlarında çalışır, tıpkı Ethernet gibi. Ancak farkı **Mesafe**dir.

* **LAN:** Kısa mesafe (Bina içi).
* **WAN:** Uzun mesafe (Şehirler/Ülkeler arası).

**[LAN ve WAN Bağlantısı]**

```text
          Şube Ofisi                                                  Merkez Ofis
     +-------------------+                                       +-------------------+
     |   [PC] / [Phone]  |                                       |     [Server]      |
     |         |         |                                       |         |         |
     |       (LAN)       |                                       |       (LAN)       |
     |         |         |                                       |         |         |
     |    [ROUTER A]     |==========( WAN Bağlantısı)==========> |    [ROUTER B]     |
     +-------------------+             (Uzun Mesafe)             +-------------------+

```

### İki Ana WAN Teknoloji

Göreceğimiz iki temel WAN türü vardır:

1. **Leased-Line WANs (Kiralık Hatlar):**
* **Tarihçe:** 50 yıldan fazladır hayatımızda. Çok eskidir.
* **Durum:** Günümüzde kullanımı azalmıştır.
* **Sınav:** "Eski ama ölmedi" diyerek sınavda yine de sorarlar. O yüzden mantığını bilmek zorundayız.

2. **Ethernet WANs:**
* **Tarihçe:** Daha moderndir.
* **Mantık:** Evde kullandığımız Ethernet protokolünün (Layer 2), uzun mesafelere uyarlanmış halidir.

---

## Leased-Line WANs (Kiralık Hatlar)

Bir şirketin iki şubesi olduğunu düşün (Site A ve Site B). Bu iki şubeyi birbirine bağlamak için bir ISP'den özel bir hat kiralarsın.

### Kurulum

* **Router'ın Görevi:** Her şubede bir Router bulunur. Router'ın bir bacağı içerdeki ağa (LAN), diğer bacağı dışarıdaki hatta (WAN) bağlıdır.
* **Sipariş:** Bu hatlar kendiliğinden oluşmaz. Ağ uzmanı servis sağlayıcıyı arar ve "Bana A noktası ile B noktası arasında bir hat kurun" der.

**[Tek Kiralık Hatlı Küçük İşletme Ağı]**

 Bu örnekte meşhur "Crooked Line"ı türkçesi Zikzak Çizgisini göreceğiz. Network diyagramlarında bu zikzak çizgi, "Burası bir WAN bağlantısıdır" demenin evrensel yolu.

```text
       SITE A (LAN)                                         SITE B (LAN)
      --------------                                       --------------
      
      [ PC 1 ]                                                 [ PC 2 ]
         |                                                        |
         | (Ethernet)           ~ ~ ~    <--- Şuna benzer         | (Ethernet)
         v                    ~      ~                            v
    [ROUTER 1]  ~ ~ ~ ~ ~ ~ ~ ~       ~ ~ ~ ~ ~ ~ ~ ~ ~ ~ ~ ~  [ROUTER 2]
         ^                    (WAN Bağlantısı)                  ^
         |                     (Kiralik Hat)                    |
         |                    "Crooked Line"                    |
         |______________________________________________________|

```

Diyagramlarda düz çizgi görüyorsan genelde **Ethernet** (LAN), zikzak (şimşek gibi) çizgi görüyorsan **Serial/Leased Line** (WAN) kastediliyordur. Sınavda şekle bakarak hangi kablonun kullanıldığını anlaman bekleniyor.

## Kiralık Hatların Fiziksel Detayları

Bir kiralık hat, teknik olarak **Full-Duplex** çalışır.

* **Kavramsal Olarak:** Sanki iki bina arasında çok uzun (mesela 270 mil civarı İstanbul-Ankara) bir **Ethernet Crossover** kablosu çekmişsin gibi düşün.
* **Kablo Yapısı:** İçinde iki çift tel vardır (biri gidiş, biri geliş). Bu sayede aynı anda hem veri gönderir hem de alır.

### Nasıl Hayal Etmeliyiz?

Aşağıdaki örnek, bu hizmetin bize nasıl göründüğünü anlatır.

**[Leased-Line Servisi Örneği]**

```text
         Bina 1                                             Bina 2
        (İstanbul)                                         (Ankara)
     +---------------+                                 +---------------+
     |     [R1]      | <=============================> |     [R2]      |
     +-------+-------+             (270 Mil)           +-------+-------+
             |              "Two Pairs Crossover Kablo"        | 
             |                                                 |
          (LAN 1)                                           (LAN 2)

```

### Gerçekte Olan

Tabii ki gerçek hayatta İstanbul'dan Ankara'ya tek parça, 270 millik bir kablo çekilmez.

* **Gizli Kahraman:** **Telco (Telefon Şirketi)**.
* **Nasıl Çalışır:** Telco, yer altındaki binlerce kabloyu ve kendi özel cihazlarını (Switch'lerini) kullanarak sana bir yol oluşturur.
* **Sana Görünen:** Sen sadece R1 ve R2'yi bağlarsın, aradaki karmaşık Telco bulutunu görmezsin. Telco sana "Uçtan uca bağlantın hazır" der.

### Neden "Leased"?

Adı üstünde **"Leased" (Kiralık)** Hat.

* Hattın sahibi sen değilsin, Telco'dur.
* Sen sadece o yolu kullanmak için her ay kira ödersin.

### İsimler Sözlüğü

Uzmanlar sahada konuşurken sadece "Leased Line" demezler, farklı takma isimler kullanırlar. Hepsi aynı kapıya çıkar ama söyleşi farkları vardır.

**[Leased Line Sözlüğü]**

| Terim | Anlamı / Neden Kullanılır? |
| --- | --- |
| **Leased Circuit / Circuit** | **Devre.** Elektriksel bir devreyi ifade eder. Telekomcular bu terimi sever. |
| **Serial Link / Serial Line** | **Seri Bağlantı.** Router üzerindeki portlar genelde "Serial Interface" olduğu için ve bitler "seri" (arka arkaya) aktığı için bu isim kullanılır. |
| **Point-to-Point Link** | **Noktadan Noktaya.** Hattın sadece iki ucu olduğunu (A ve B) vurgular. Araya üçüncü bir cihaz giremez. |
| **T1** | **Hız Standardı.** Bu terim aslında bir hız birimidir (**1.544 Mbps**). Ama sahada "Bana bir T1 hattı ver" dendiğinde kiralık hat kastedilir. |
| **Private Line** | **Özel Hat.** Hattın sana özel olduğunu, başkasının verisiyle karışmadığını vurgular. |
| **WAN Link** | **Genel İsim.** Teknolojiden bağımsız, "Uzak bağlantı" demektir. |

CCNA sınavında en çok **"Serial Link"** ve **"Point-to-Point Link"** terimlerini göreceğiz.

### Telekom Bulutunun İçi

Biz hattı "dümdüz bir kablo" gibi hayal etsek de, gerçek dünya çok daha karmaşıktır.

1. **CO (Central Office):** Telekom firmasının mahalledeki/şehirdeki santral binasıdır.
2. **Kablolama:**
* Müşterinin binandan çıkan kablo, yer altından en yakın **CO**'ya gider.
* Telekom firması, kendi içindeki devasa Switch ağını kullanarak senin verini CO'dan CO'ya taşır.
* Diğer uçtaki CO'dan da karşı şubeye gider.

**[Telekomünikasyon Şirketi İçinde Olası Kablolama]**

Bu olayu şöyle canlandırabiliriz:

```text
      Müşteri A             Telco Cloud (ISP)            Müşreti B
      (Site 1)              (Yer Altı Dünyası)           (Site 2)
    +----------+         +--------------------+         +----------+
    |   [R1]   |-------->| [CO 1] ---- [CO 2] |-------->|   [R2]   | 
    +----------+         | (Switch)   (Switch)|         +----------+
                         +--------------------+      -> Kablolar yer altından doğru solucan gibi...    

```

Arada onlarca Switch ve kilometrelerce kablo olsa da, Router R1 ve Router R2 bunu bilmez. Onlar sadece **"Karşıda R2 var"** sanır.
Bu arada **CCNA için:** şu senaryodaki olayın derin detayına takılma. **Serial Link = İki Router arası düz çizgi.** Gerisi Telekomcunun derdi. 

