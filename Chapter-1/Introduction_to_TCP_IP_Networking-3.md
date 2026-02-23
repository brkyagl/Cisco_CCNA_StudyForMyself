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

## HDLC Data-Link Detayları (Leased Lines Layer 2)

Kiralık hatlar (Layer 1), bitleri taşımaya söz verir. Ama bu bitlerin anlamlı bir Frame'e dönüşmesi için bir **Layer 2 Protokolü** şarttır.

2 tane ana WAN protokolü vardır:

1. **HDLC** (High-Level Data Link Control) -> Cisco Router'larda varsayılan.
2. **PPP** (Point-to-Point Protocol) -> Daha esnek, marka bağımsız.

### Ethernet vs. HDLC 

* **Ethernet (Kalabalık Parti):**
* Aynı ortamda (LAN) onlarca cihaz var.
* Konuşurken kime konuştuğunu belirtmek zorundasın ("Hey Berkay!").
* Bu yüzden **Destination MAC Address** çok önemlidir.

* **HDLC (Özel Oda):**
* Hattın bir ucunda sen, diğer ucunda sadece Berkay (Diğer Router) var.
* Konuştuğun an, Berkay seni dinlemek zorunda. Başka kimse yok.
* Bu yüzden **Adres** alanı olsa bile önemsizdir.

### HDLC Frame 

HDLC, tıpkı Ethernet gibi bir Frame kullanır. Header'ı, Data'sı ve Trailer/FCS'si vardır.

Ancak Ethernet kadar karmaşık değildir. Çünkü **Point-to-Point (Noktadan Noktaya)** çalıştığı için, "Bu paket kime gidiyor?" diye düşünmesine gerek yoktur. Yolun sonu bellidir.
Sınavda şu soruyu sorabilirler: "HDLC frame'inde Destination Address alanı var mıdır?"
Cevap: **Evet, vardır.** Ama Ethernet'teki gibi karmaşık bir işlevi yoktur, çünkü hat zaten tek yönlüdür.

### HDLC Header Alanlarını Ethernet ile Karşılaştırma 

HDLC'yi sıfırdan ezberlemeye gerek yok. Zaten Ethernet'i biliyoruz, onunla eşleştirirsek taşlar yerine oturur.

| HDLC Field | Ethernet Karşılığı | Görevi |
| --- | --- | --- |
| **Flag** | Preamble / SFD | **Bayrak.** "Dikkat paket geliyor!" uyarısıdır. Özel bir bit deseni (01111110) taşır. |
| **Address** | Destination MAC | **Adres.** Hedef cihazı belirtir. (Ama WAN'da tek hedef olduğu için genelde semboliktir). |
| **Control** | - (Yok) | **Kontrol.** Eski teknolojiler için yönetim bilgisidir. Günümüzde pek işlevi kalmamıştır. |
| **Type** | EtherType | **Tür.** İçerideki paketin ne olduğunu (IPv4 mü IPv6 mı?) söyler. **(Buraya Dikkat!)** |
| **FCS** | FCS | **Hata Kontrolü.** Paketin bozulup bozulmadığını denetler. |

### Cisco'ya Özel HDLC

İşte sınavın altın vuruşu burası.

1. **ISO Standard HDLC:** Uluslararası standart olan HDLC'de **"Type" alanı YOKTUR.**
* *Sorun:* Type alanı yoksa, Router gelen paketin IPv4 mü yoksa IPv6 mı olduğunu anlayamaz.

2. **Cisco Tescilli HDLC:** Cisco mühendisleri bu sorunu fark etmiş ve standardı bozarak araya kendi **Type** alanlarını eklemişlerdir.
* *Çözüm:* Artık Router, paketin içeriğini (yani Protocol türünü) anlayabilir.

**[Cisco HDLC Frame Formatı]**

Cisco'nun modifiye ettiği o yapıyı şöyle görebiliriz:

```text
       Bytes:  (1)      (1)       (1)       (2)        (Variable)       (2)    
             +------+---------+---------+----------+------------------+-------+
             | Flag | Address | Control |   TYPE   |       Data       |  FCS  |
             +------+---------+---------+----------+------------------+-------+
                                             ^
                                             |
                                     (CISCO'NUN EKLEDİĞİ!)
                                    (Standart ISO HDLC'de Yok)

```

Eğer bir uçta **Cisco Router**, diğer uçta **Başka Marka Router** varsa ve ikisinde de HDLC protokolünü kullanırsan **BAĞLANTI ÇALIŞMAZ.** Çünkü Cisco "Type" alanı beklerken, diğer marka beklemez. Dilleri uyuşmaz. 
Farklı markaları bağlayacaksan HDLC yerine **PPP (Point-to-Point Protocol)** kullanmalısın.
 
## Router'ın Yol Haritası

Şöyle diyelim önce Router'lar **Network Layer (Layer 3)** cihazlarıdır. Tek dertleri paketi nihai hedefe ulaştırmaktır.
Ancak Router'lar bu paketi ışınlayamazlar. Paketi bir sonraki durağa atmak için **Data Link (Layer 2)** teknolojilerini (LAN veya WAN) kullanmak zorundadırlar.

### Router'ın Mantığı

Aşağıda, bir paketin PC1'den çıkıp PC2'ye giderken geçtiği 3 aşamalı düşünce yapısını gösterir.

**[LAN ve WAN üzerinden IP Yönlendirme Mantığı]**

```text
       Adım 1 (LAN)                 Adım 2 (WAN - Kiralık Hat)           Adım 3 (LAN)
      Ethernet Frame                     HDLC Frame                     Ethernet Frame
     +--------------+                 +--------------+                 +--------------+
     | [PC 1]       |                 |    [R1]      |                 |    [R2]      |-------------------> [PC 2]
     +------+-------+                 +------+-------+                 +------+-------+
            |                                |                                |
            | "Hedef PC2."                   | "Hedef PC2."                   | "Hedef PC2."
            | "R1'e yolla."                  | "R2'ye yolla."                 | "PC2'ye yolla."
            v                                v                                v
          (R1'e)                          (R2'ye)                          (PC2'ye)

```

1. **PC1'in Mantığı (Adım 1):**
* **Hedef:** PC2'nin IP adresi.
* **Karar:** "PC2 uzakta (başka bir ağda). O zaman paketi benim kapımdaki nöbetçiye, yani **Default Gateway (R1)**'e göndermeliyim."
* **Eylem:** Paketi Ethernet ile R1'e atar.

2. **R1'in Mantığı (Adım 2 - WAN Kısmı):**
* **Hedef:** Yine PC2.
* **Karar:** R1 haritasına (Routing Table) bakar. "PC2'ye gitmek için bu paketi WAN hattından **R2**'ye fırlatmam lazım."
* **Eylem:** Paketi **HDLC** veya **PPP** ile paketleyip WAN hattından R2'ye yollar.

3. **R2'nin Mantığı (Adım 3):**
* **Hedef:** PC2.
* **Karar:** R2 haritasına bakar. "Aaa, PC2 zaten benim bacağımdaki yerel ağda (LAN) oturuyor."
* **Eylem:** Paketi Ethernet ile doğrudan **PC2**'ye teslim eder.

### Paketleme Olayları

Burada çok önemli bir detay gizli:

* **PC1 -> R1:** Arada **Ethernet** var. Paket Ethernet kutusuna konur.
* **R1 -> R2:** Arada **WAN (HDLC)** var. R1, Ethernet kutusunu çöpe atar, paketi **HDLC** kutusuna koyar.
* **R2 -> PC2:** Arada tekrar **Ethernet** var. R2, HDLC kutusunu çöpe atar, paketi yeni bir **Ethernet** kutusuna koyar.

IP Paketi (Mektup) yol boyunca **ASLA DEĞİŞMEZ.**
Ancak onu taşıyan Data Link Frame (Zarf/Kutu) her durakta değişir (Ethernet -> HDLC -> Ethernet).
Burası çok önemli gerçekten: **IP Paketi (Mektup) yol boyunca asla değişmez. Ancak onu taşıyan Çerçeve (Zarf) her durakta değişir.**

Bunu bir seyahat gibi düşün:

1. Evden taksiyle (Ethernet) havaalanına gidersin.
2. Uçakla (HDLC) başka şehre gidersin.
3. Orada tekrar taksiye (Ethernet) binip otele gidersin.
*Sen (IP Paketi) aynısın, ama bindiğin araç (Frame) sürekli değişiyor.*

### Adım Adım Yolculuk

Örnek, PC1'den PC2'ye giden paketin başından geçenler:

**1. Hop 1: PC1'den Router 1'e (LAN)**

* **Ortam:** Ethernet (LAN).
* **İşlem:** PC1, IP Paketini bir **Ethernet Frame**'inin içine koyar (Encapsulation).
* **Adres:** Hedef MAC adresi R1'dir.
* **Paket:** `[Eth Head] [IP Packet] [Eth Trail]`

**2. Hop 2: Router 1'den Router 2'ye (WAN)**

* **Ortam:** Leased Line (Kiralık - WAN).
* **İşlem (R1):**
* Gelen Ethernet header/trail söker atar (**De-encapsulation**).
* Çıplak kalan IP Paketini alır.
* Onu WAN hattına uygun olan **HDLC Frame**'inin içine koyar (**Re-encapsulation**).
* **Paket:** `[HDLC Head] [IP Packet] [HDLC Trail]`

**3. Hop 3: Router 2'den PC2'ye (LAN)**

* **Ortam:** Ethernet (LAN).
* **İşlem (R2):**
* Gelen HDLC header/trail söker atar (**De-encapsulation**).
* Çıplak kalan IP Paketini alır.
* Onu PC2'ye göndermek için yeni bir **Ethernet Frame**'inin içine koyar (**Re-encapsulation**).
* **Adres:** Hedef MAC adresi PC2'dir.
* **Paket:** `[Eth Head] [IP Packet] [Eth Trail]`

Router'lar her durakta Layer 2 (Data Link) headerını çöpe atar ve yenisini yazar. Ama Layer 3 (Network - IP) headerına dokunmazlar (bazı özel durumlar haricinde böyleymiş). Bu yüzden "IP End-to-End" deriz.

### Leased Lines: Karnesi 

Bu mevzuyu kapatırken, neden yıllarca Leased Line kullandık?

| Avantajları | Dezavantajları |
| --- | --- |
| **Basit:** Point-to-Point olduğu için karmaşık değildir. | **Maliyet:** Çok pahalıdır. Hattı 7/24 kiraladığın için kullanmasan da para ödersin. |
| **Bulunabilirlik:** Dünyanın her yerinde Telco altyapısı vardır. | **Yavaş:** Modern standartlara göre yavaştır (Genelde Mbps seviyesinde kalır). |
| **Özel:** Hat sana özeldir, güvenlik riski düşüktür. | **Kurulum Süresi:** Hattın çekilmesi haftalar/aylar sürebilir. |
| **Kalite:** Hattın kalitesi yüksektir, bant genişliği garantilidir. |  |

---

## WAN Teknolojisi Olarak Ethernet (Modern WAN)

Ethernet, ilk on yıllarında sadece LAN için uygundu. Mesafe kısıtlamaları yüzünden en fazla bir kampüs ağını kapsayabiliyordu. 
Ancak IEEE, fiber optik teknolojilerini geliştirerek Ethernet'in sınırlarını inanılmaz derecede genişletti.

### Mesafe Sınırını Aşmak

Ethernet'i bir WAN teknolojisi yapan şey, fiber optik kablolardaki şu muazzam gelişmelerdir:

* **1000BASE-LX:** Single-mode fiber kullanarak mesafeyi **5 kilometreye** çıkardı.
* **1000BASE-ZX:** İşleri daha da büyüterek tek bir kabloyla **70 kilometre** mesafeye ulaşmayı sağladı.

Bu uzun mesafeler sayesinde Servis Sağlayı'lar, "Neden bu kadar hızlı ve ucuz olan Ethernet'i şehirler arası bağlantılarda kullanmıyoruz?" dediler ve Ethernet WAN hizmetleri doğdu.

### Mimari Yapı

Örnek, bir müşterinin Servis Sağlayıcıya (SP) Ethernet üzerinden nasıl bağlandığını gösteriyor. Eski "Telco" mantığına çok benzer, ancak aradaki cihazlar artık Ethernet Switch'leridir.

**[Fiber Ethernet Bağlantısı]**

```text
       Müşteri SITE A                                           Müşteri SITE B
      (Senin Binan)                                             (Diğer Şube)
     +-------------+                                           +-------------+
     |   [ R1 ]    |                                           |   [ R2 ]    |
     |   (CPE)     |                                           |   (CPE)     |
     +------+------+                                           +------+------+
            |                                                         |
            | (Fiber Ethernet Access Link)                            | (Fiber Ethernet Access Link)
            v                                                         v
     +------------+                                            +------------+
     |   [SP 1]   |                                            |   [SP 2]   |
     |   (PoP)    |                                            |   (PoP)    |
     | (Ethernet  |==============( SP's NETWORK )==============| (Ethernet  |
     |  Switch)   |         (Service Provider WAN)             |  Switch)   |
     +------------+                                            +------------+

```

### Yeni WAN Terimleri

Sınavda bu üç harfli kısaltmaları çok göreceğiz:

1. **SP (Service Provider):** İnternet veya WAN hizmetini aldığın Telekom firması (Örn: Türk Telekom, Superonline).
2. **CPE (Customer Premises Equipment):** Müşteri lokasyonunda duran cihaz. Yani senin binandaki **Router (R1 ve R2)**. (Sahibi sen de olabilirsin, kiralık da olabilir ama senin ofisindedir).
3. **PoP (Point of Presence):** Servis sağlayıcının sana en yakın olan fiziksel binası/santrali. Fiber kablon senin ofisinden çıkar ve direkt bu PoP noktasına girer.
4. **Access Link:** Senin Router'ın (CPE) ile sağlayıcının Switch'i (PoP) arasındaki o fiber optik bağlantının adıdır.

Eski kiralık hatlarda servis sağlayıcının binasında karmaşık Telco switchleri vardı. Ethernet WAN'da ise SP'nin PoP noktasında bildiğimiz devasa **Ethernet Switch'ler** bulunur. 
SP'nin kendi ağının içinde ne kullandığı bizi ilgilendirmez; biz sadece Ethernet portuna takar ve gerisine karışmayız!

## Layer 2 Hizmeti Oluşturan Ethernet WAN'lar

Servis sağlayıcılar birçok farklı Ethernet WAN hizmeti sunar. Ancak CCNA sınavı için bilmen gereken en temel model şudur: **Point-to-Point Ethernet**.

* **Mantıksal Olarak:** İki Router (R1 ve R2) birbirine doğrudan tek bir kabloyla bağlıymış gibi davranır.
* **Fiziksel Olarak:** İki Router da aslında servis sağlayıcının ağına (PoP noktasına) fiber optik kabloyla bağlıdır.

### Sınav Terimleri

Sektörde ve CCNA sınavında bu hizmet için tek bir isim kullanılmaz. Aşağıdaki terimlerin **hepsinin aynı şeyi** (Point-to-Point Ethernet WAN) ifade ettiğini adın gibi bilmelisin:

1. **Ethernet WAN:** Ethernet LAN'dan ayırmak için kullanılan en genel, şemsiye terim.
2. **E-Line (Ethernet Line Service):** MEF (Metro Ethernet Forum) organizasyonunun belirlediği resmi terimdir. "Point-to-Point" hizmet anlamına gelir.
3. **Ethernet Emulation (Ethernet Taklidi):** Veri, uçtan uca tek bir parça Ethernet kablosundan geçmez (arada sağlayıcının devasa ağı vardır). Ancak sistem bunu sanki tek bir kabloymuş gibi **emulate** eder yani taklit eder.
4. **EoMPLS (Ethernet over MPLS):** En çok duyacağın terim budur! Servis sağlayıcı arka planda o karmaşık ağı yönetmek için **MPLS** (Multiprotocol Label Switching) adı verilen süper hızlı bir teknoloji kullanır. Ancak müşteriye bunu Ethernet paketi içinde teslim eder. Bu şunun gibi aslında dış pakette "Ethernet" yazar, ama taşıyıcı kargo firmasının motoru "MPLS"tir.

### EoMPLS Mantığı 

Örnek, işin arka planındaki o karmaşık SP bulutunu görmezden gelip, Router'ların dünyayı nasıl gördüğünü anlatır.

**[EoMPLS, İki Router Arasında Basit Bir Ethernet Bağlantısı Gibi Davranıyor]**

```text
       SITE 1 (LAN)                                         SITE 2 (LAN)
      --------------                                       --------------
      
      [ PC 1 ]                                                 [ PC 2 ]
         |                                                        |
         | (Ethernet LAN)                                         | (Ethernet LAN)
         v                                                        v
    [ROUTER 1]  ===========================================  [ROUTER 2]
     (G0/1)                    (EoMPLS WAN)                    (G0/0)
                           Fiber Optic Ethernet Link
```

Eski kiralık hatlarda Router'lar birbirine "Zikzak Çizgi" ve "Serial Port (S0/0/0)" ile bağlanıyordu hatırla. Burada ise Router'lar birbirine **Gigabit Ethernet (G0/1 ve G0/0)** portlarıyla bağlı!
Router açısından bu WAN bağlantısının, yan odadaki bir Switch'e bağlanmaktan **hiçbir farkı yoktur**. Full-Duplex şekilde aynı anda veri gönderebilirler.

## Routerlar Ethernet Emulation Kullanarak IP Paketlerini Nasıl Yönlendirir?

Eğer servis sağlayıcımızdan bir **EoMPLS (Ethernet over MPLS)** hizmeti aldıysak, Router'ımız bu WAN hattını tıpkı sıradan bir LAN hattıymış gibi kullanır.

* **Fark Nedir?** HDLC veya PPP kullanmak yerine, Router paketi WAN'a gönderirken de bildiğimiz, tanıdığımız **Ethernet (802.3) Header ve Trailer**'ını kullanır.
* **Her Yer Ethernet:** PC'den Router'a Ethernet, Router'dan Router'a Ethernet, Router'dan diğer PC'ye yine Ethernet!

### EoMPLS Bağlantısı

Aşağıdaki örnekte ortadaki WAN bağlantısının üzerinde küçük bir cloud var. Bu cloud, "Bu sıradan bir kablo değil, kilometrelerce uzunluğundaki bir Ethernet WAN hizmetidir" demek içindir.

```text
      Adım 1 (LAN 1)             Adım 2 (EoMPLS WAN)             Adım 3 (LAN 2)
     (PC1'den R1 MAC)             (R1 MAC'ten R2 MAC)           (R2 MAC'ten PC2 MAC)
                                       _(Cloud)_
     +--------------+              __(           )__              +--------------+
     |   [PC 1]     |             (   EoMPLS WAN    )             |    [PC 2]    |
     +------+-------+              *---------------*              +------+-------+
            |                              |                             |
            |           [802.3 Header][IP Packet][802.3 Trailer]         |
            |                              |                             |
            v                              v                             v
          [ R1 ] ---------------------------------------------------- [ R2 ]
          (G0/1)                   (G0/1'den G0/0'a)                  (G0/0)

```

Örneklerde her adımda frame'in üzerinde "802.3 (Eth)" yazar. Ancak **dikkat et!** Her adımda bu frame içindeki MAC adresleri **değişir!** Router'lar eski frame'i çöpe atıp yenisini üretirler.

### Adım Adım Ethernet Yolculuğu

PC1'in PC2'ye bir paket gönderdiğini varsayalım. İşte o 3 adım:

**1. Adım: PC1'den R1'e (LAN)**

* PC1, IP Paketini bir Ethernet Frame'i içine koyar.
* **Hedef MAC:** R1'in LAN bacağındaki MAC adresi.
* R1 bu paketi alır.

**2. Adım: R1'den R2'ye (WAN - Burası FENA!)**

* R1 paketi açar (De-encapsulate), eski Ethernet header'ını çöpe atar.
* IP Paketini alır ve onu **YENİ bir Ethernet Frame'i** içine koyar. Neden mi? Çünkü WAN hattı da Ethernet!
* **Source MAC:** R1'in WAN bacağının (G0/1) MAC adresi.
* **Destination MAC:** R2'nin WAN bacağının (G0/0) MAC adresi.
* *(İşte HDLC ile en büyük fark budur. HDLC'de MAC adresi yoktu, EoMPLS'te WAN üzerinde de MAC adresi konuşulur!)*

**3. Adım: R2'den PC2'ye (LAN)**

* R2 paketi alır, WAN'dan gelen Ethernet header'ı çöpe atar.
* IP Paketini yepyeni bir Ethernet Frame'i içine koyar.
* **Hedef MAC:** PC2'nin MAC adresi.
* Ve paketi PC2'ye teslim eder.

---

## IP Routing Mevzusu

Tarih boyunca birçok farklı ağ modeli kullanılmış olsa da, şunu biliyoruz ki bugün dünyayı **TCP/IP** modeli yönetiyor. 
Bu modelin kalbi olan "Network Layer" seviyesinde karşımıza iki büyük seçenek çıkar: **IPv4** ve **IPv6**.
İkisi de aynı işi yapar ama detayları farklıdır. Bu bölümde (ve ağ dünyasında genel konuşurken) sadece **"IP"** kelimesini gördüğünde, kastedilen şey her zaman eski ve köklü olan **IPv4**'tür. IPv6'yı ileride kendi özel bölümünde inceleyeceğiz.

### IP'nin Asıl İşi Nedir?

Internet Protocol (IP) bir "beyin" gibidir. Tek bir derdi vardır: **Veriyi (IP Packet) Kaynaktan (Source), Hedefe (Destination) ulaştırmak.**

* **Fiziksel İşlere Karışmaz:** IP, kablonun bakır mı yoksa fiber mi olduğuyla, veya verinin Ethernet'ten mi yoksa HDLC'den mi geçtiğiyle ilgilenmez. Bu amelelik işlerini (!) alt katmanlara (Layer 1 ve Layer 2) bırakır.
* **Mantıksal Odak:** IP, olayın tamamen **mantıksal** ve **end-to-end** planlamasıyla ilgilenir. Paket yolda onlarca farklı LAN ve WAN bağlantısından geçse bile, IP'nin gözü sadece nihai hedeftedir.

### Bu Bölümde Neler Var?

Bu bölümün geri kalanında IP Routing olayını 3 ana adımda inceleyeceğiz:

1. **Routing Süreci:** Bir IP paketinin göndericiden hedefe giderken arka arkaya farklı Data-Link protokollerini (Ethernet -> HDLC -> Ethernet) nasıl kullandığını göreceğiz.
2. **IP Adresleme ve Subnets:** Tüm bu yönlendirme işleminin verimli olabilmesi için adresleri "Subnet" adı verilen gruplara nasıl böldüğümüzü öğreneceğiz.
3. **IP Routing Protokolleri:** Router'ların, dünyadaki tüm bu IP subnet'lerine giden yolları kendi kendilerine nasıl öğrendiklerini keşfedeceğiz.

## Network Layer Routing Mantığı

TCP/IP ağlarında yönlendirme işlemi, end-user bilgisayarları (Hosts) ve Router'ların takım çalışmasıyla gerçekleşir.

* **Host Mantığı:** Bilgisayarındaki TCP/IP yazılımı (İşletim Sisteminin bir parçası), hedefin nerede olduğuna bakar. Eğer hedef uzaktaysa, paketi en yakın Router'a (Default Gateway) iletiyor.
* **Router Mantığı:** Router paketi alır, kendi haritasına (Routing Table deniliyor) bakar ve paketi bir sonraki Router'a iletir.

Bu zincirleme reaksiyon, paket nihai hedefine ulaşana kadar devam eder.

### Routing Mantığını Kavrama

Aşağıdaki örnekte, PC1'in oluşturduğu bir IP paketinin, yukarıdan aşağıya (PC2'ye) doğru nasıl bir yolculuk yaptığını görüyoruz. Her cihaz kendi Routing mantığını dinleyerek karar verir.

**[Routing Mantığı: PC1'den IP Packet-> PC2'ye]**

```text
       [ PC1 ]  (IP: 150.150.1.10)
          |
          |  (PC1'in Mantığı): "Hedef başka bir Subnet. 
          |                     Paketi en yakındaki Router'a yolla!"
          v  [IP Packet]
          
       [ R1 ]
          |  (R1'in Mantığı): "Haritam (Routing Table) diyor ki: 
          |                    Bu paketi Serial hattan R2'ye yolla!"
          v  [IP Packet] (Serial Link)
          
       [ R2 ] (F0/0)
          |  (R2'nin Mantığı): "Haritam (Routing Table) diyor ki: 
          |                    Bu paketi EoMPLS hattan R3'e yolla!"
          v  [IP Packet] (EoMPLS Link)
          
       [ R3 ] (G0/0)
          |  (R3'ün Mantığı): "Haritam diyor ki: Hedef cihaz benim 
          |                    kendi bağlı olduğum ağda (150.150.4.0).
          |                    Doğrudan PC2'ye yolla!"
          v  [IP Packet] (Subnet: 150.150.4.0)
          
       [ PC2 ]  (IP: 150.150.4.10)

```

Sektörde ve sınavlarda **Path Selection (Yol Seçimi)** terimi iki farklı anlama gelebilir:
> 1. Örnekte gördüğümüz gibi, bir paketin adım adım iletilmesi süreci.
> 2. (Daha yaygın olarak) Routing protokollerinin (OSPF, EIGRP vb.), aynı hedefe giden birden fazla yol olduğunda **en iyi yolu** seçme algoritması.

### 1. Host'un Yönlendirme Mantığı (PC'nin Karar Anı)

Her şey paketi üreten bilgisayarla başlar. İşletim sistemindeki TCP/IP yazılımı çok zekidir ve şu temel analizi yapar:

* **Analiz:** "Gitmek istediğim hedef IP (150.150.4.10) benimle aynı ağda mı (LAN), yoksa başka bir ağda mı?"
* **Karar:** PC1 bakar ki PC2 kendi ağında değil. "O zaman benim bu paketi, yol bulma işinden anlayan bir uzmana teslim etmem lazım."
* **Eylem (Default Gateway):** PC1, paketi kendi ağındaki en yakın Router'a, yani **Default Gateway** cihazına gönderir.
* *Nasıl Gönderir?* Paketi Router'a ulaştırabilmek için Data-Link headerı kullanır. (Yani Hedef MAC adresi olarak Router'ın MAC adresini yazar).

###  2. R1 ve R2'nin Mantığı (Yönlendirme)

Paket Router'a (R1) ulaştığında, Router tamamen kendi "Haritasına" göre hareket eder.

* **Routing Table:** Her Router'ın beyninde IP ağlarının ve Subnet'lerin listelendiği bir tablo vardır.
* **Eşleştirme:** R1 paketi alır, hedefin IP adresine (150.150.4.10) bakar ve tablosuyla eşleştirir. Tablo ona: *"Bu adrese gitmek istiyorsan paketi R2'ye yolla"* der.

Bu olay, otoyolda dev bir kavşağa yaklaşmaya benzer. Tabelalara bakarsın: "Ankara için sağdan çık, İzmir için düz devam et." Router'ın tablosu da aynen bu tabelalar gibi çalışır ve paketi doğru yola (LAN veya WAN) sokar.

### 3. R3’ün Mantığı (Son Durak)

Paket elden ele geçip (R1 -> R2 -> R3) yolun sonundaki Router'a ulaştığında, R3'ün mantığı diğerlerinden biraz farklı çalışır.

* **Fark Nedir?** R1 ve R2 paketi *başka bir Router'a* yollamıştı. Ancak R3 haritasına baktığında hedefin (PC2) **doğrudan kendisine bağlı olan LAN üzerinde** olduğunu fark eder.
* **Eylem:** R3, "Paketi başka bir Router'a atmama gerek yok, adam zaten yan odada" der ve paketi doğrudan hedef bilgisayara (PC2) teslim eder.

Bu "doğrudan teslim etme" ile "başka router'a yollama" arasındaki fark, bir sonraki konuda Layer 2 (MAC) adreslerinin nasıl değiştiğini anlarken hayati önem taşıyacak.

---

## Network Layer Routing LAN ve WAN'ları Nasıl Kullanır?

Network Layer (IP) sadece mantıkla ve "Büyük Resim" ile ilgilenir. Ancak bitlerin kablolardan fiziksel olarak geçmesi gerekir.

* **IP (Network Layer) = Yönetici:** "Bu paketi hedefe (PC2) ulaştır." (Büyük resim).
* **Data-Link Layer = Kurye:** "Bu paketi şu anki duraktan bir sonraki durağa sağ salim taşı." (Küçük resim).

Yani IP paketi baştan sona tek parça giderken, onu taşıyan Frame her durakta değişir.

### Router'ın 4 Adımlık Anayasası

Bir Router'ın interface'ine bir Frame girdiğinde, Router istisnasız şu 4 adımı uygular. Bunu adın gibi bilmelisin:

1. **Hata Kontrolü (FCS Check):** Gelen frame'in trailer'indeki FCS değerine bakar. Yolda bozulma olmuş mu? Hata varsa paketi **çöpe atar**. Kurtarmaya çalışmaz!
2. **De-encapsulate:** Eğer paket sağlamsa, eski Data-Link Header'ını ve Trailer'ı çöpe atar. Elinde sadece çıplak **IP Paketi** kalır.
3. **Routing Tablosuna Bakma:** IP Paketinin üzerindeki **Destination IP** adresine bakar. Kendi Routing tablosunu açar ve eşleşme arar. Bu tablo ona paketi hangi interface'den ve hangi IP'ye atacağını söyler.
4. **Re-encapsulate:** Çıplak IP paketini alır, çıkış yapacağı kapıya (LAN veya WAN) uygun **yeni bir Data-Link header ve trailer** ekler ve paketi yollar.

### Network Layer ve Data-Link Layer Encapsulation 

Aşağıdaki örnek, paketin PC1'den PC2'ye giderken hangi kılıklara (Eth -> HDLC -> Eth) girdiğini ve Router'ların **Routing Table** mantığını görüyoruz.

**[Network Layer ve Data-Link Layer Encapsulation]**

```text
 (A) R1 Routing Table                   (C) R2 Routing Table
 +-------------+---------+------------+ +-------------+---------+------------+
 | Subnet      |Interface| Next Hop   | | Subnet      |Interface| Next Hop   |
 +-------------+---------+------------+ +-------------+---------+------------+
 | 150.150.4.0 | Serial0 | 150.150.2.7| | 150.150.4.0 | FastE0/0| 150.150.3.1|
 +-------------+---------+------------+ +-------------+---------+------------+
                                                            |
                                                            v
 [ PC1 ]                                                 [ R2 ] (150.150.2.7)
 (IP: 150.150.1.10)                                         |
    |                                                       | [Eth][IP][Eth]
    | [Eth][IP][Eth]                                        v
    v                                                    [ R3 ] (150.150.3.1)
 [ R1 ] (150.150.1.4)                                       |
    |                                                       | [Eth][IP][Eth]
    | [HDLC][IP][HDLC] (B)                                  v
    v                                                    [ PC2 ]
 (Serial Link)                                      (IP: 150.150.4.10)
                                                    (Subnet: 150.150.4.0)

 (D) R3 Routing Table
 +-------------+-----------+----------+
 | Subnet      | Interface | Next Hop |
 +-------------+-----------+----------+
 | 150.150.4.0 | GigE0/0   | N/A      |  <-- (N/A çünkü hedef doğrudan kendisine bağlı!)
 +-------------+-----------+----------+

```

> * **R1 ve R2** tablosunda bir **Next Hop (Bir Sonraki Durak)** adresi var, çünkü hedef uzakta.
> * **R3** tablosunda ise Next Hop **"N/A" (Uygulanamaz)** diyor. Neden? Çünkü R3 hedefin kendi LAN'ında olduğunu biliyor, aracıya ihtiyacı yok!

### 4 Adımlı Yolculuk  

Paketimiz PC1'den yola çıkıp PC2'ye gidiyor. Hadi her durakta ne dolaplar dönüyor, adım adım izleyelim:

#### Adım A: PC1 (Başlangıç Noktası)

* **Mantığı:** "Hedef IP (150.150.4.10) benimle aynı LAN'da değil. Uzaklarda bir yerde. Bunu tek başıma bulamam, uzmanına (Default Gateway yani R1'e) göndermeliyim."
* **Aksiyon:** PC1 çıplak IP paketini alır, ona bir **Ethernet** Frame giydirir.
* **Adresleme:** * Hedef IP: PC2'nin IP'si. (Bu yolculuk boyunca **asla** değişmez!)
* Destination MAC: **R1'in MAC adresi.** (Çünkü paketi şimdilik ona iletiyor).

#### Adım B: Router 1 (R1) - LAN'dan WAN'a Geçiş

* **Mantığı:** "Bana bir Ethernet paketi geldi. FCS'ye baktım, yolda bozulmamış. Şimdi şu Ethernet frame'ini bir yırtıp atayım da içindeki gerçek IP paketine bakayım. Hımm, hedef 150.150.4.10. Haritama (Routing Table) göre bunu **Serial0** interface'nden **R2'ye (150.150.2.7)** iletmem lazım."
* **Aksiyon:** R1, o çıplak IP paketine yeni bir frame diker: **HDLC Frame**. (Çünkü R2 ile arasındaki yol bir Leased Line).

#### Adım C: Router 2 (R2) - WAN'dan Diğer WAN'a Geçiş

* **Mantığı:** "Bana bir HDLC paketi geldi. FCS sağlam. HDLC frame'ini çöpe atıyorum. Hedef IP'ye bakıyorum: 150.150.4.10. Haritama göre bunu **Fast Ethernet 0/0** interface'inden **R3'e (150.150.3.1)** iletmem lazım."
* **Aksiyon:** R2, pakete yepyeni bir **Ethernet** frame giydirir. (Çünkü R3 ile arası bir Ethernet WAN).
* **Adresleme:** * Source MAC: R2'nin kendi MAC'i.
* Destination MAC: **R3'ün MAC adresi.**

#### Adım D: Router 3 (R3) - Final Durağı

* **Mantığı:** "Bana bir Ethernet frame geldi. FCS sağlam, frame'i çöpe at. Hedef IP'ye bakıyorum... Aaa! 150.150.4.0 ağı zaten benim doğrudan bağlı olduğum kendi alanım! Bir sonraki Router'a gerek yok, adam hemen şurada!"
* **Aksiyon:** R3, IP paketine son bir **Ethernet** frame giydirir.
* **Adresleme:**
* Destination MAC: **PC2'nin MAC adresi.** Ve paketi hedefe teslim eder!

### Büyük Soru: MAC Adreslerini Nasıl Biliyorlar? 

Buraya kadar her şey harika. R1, R2, R3 sürekli yeni Ethernet headers üretti ve içine Destination MAC adresleri yazdı. İyi de, örneğin R3, **PC2'nin MAC adresini vahiy yoluyla mı öğrendi? :D**
Hayır! İşte burada sahneye efsanevi bir protokol çıkıyor: **ARP (Address Resolution Protocol - Adres Çözümleme Protokolü).**

* **Sorun:** Router (veya PC), hedef cihazın IP adresini biliyor ama MAC adresini bilmiyor. MAC olmadan Ethernet frame oluşturulamaz.
* **Çözüm (ARP):** R3 ağa bir Broadcast gönderir yani etrafına bağırıyor: *"Hi guys bro! 150.150.4.10 IP adresi kiminse bana MAC adresini söylesin!"*
* PC2 bunu duyar ve cevap verir: *"O IP benim, al bu da MAC adresim."*
* R3 bu MAC'i öğrenir, frame'i oluşturur ve paketi yollar.

Bu olaylarda IP Adresi mantıksaldır, değişmez (Senin TC Kimlik Numaran gibidir).
MAC Adresi fizikseldir, her durakta çöpe atılıp yenisi yazılır (Bindiğin taksinin plakası gibidir). ARP ise, "Bu TC Kimlik numaralı adam hangi taksiye binecek?" sorusunun cevabını bulur.

---

## IP Adresleme Routing'e Nasıl Yardımcı Olur?

TCP/IP ağına bağlanan her bir cihazın (Bilgisayar, Telefon, Router interface) kendini tanıtmak için **Network Layer** seviyesinde bir adrese ihtiyacı vardır. Zaten biz buna **IP Adresi** diyoruz.

### Posta Adresi Mantığı

IP adresleme mantığı aslında günlük hayattaki posta sistemiyle birebir aynı çalışır:

* Eğer postacıdan (Router) bir kargo (IP Packet) bekliyorsan, evinin kapısında geçerli ve eşsiz bir posta adresinin (IP Address) yazıyor olması şarttır. Adresin yoksa kargo alamazsın.
* Sadece adresin olması yetmez, bu adreslerin belirli bir mantığa göre **gruplandırılması** gerekir (Mahalle, Sokak, Kapı No gibi).

İşte IP dünyasında bu adres gruplarına **IP Networks (IP Ağları)** ve **Subnets (Alt Ağlar)** adını veriyoruz. Routing işleminin bu kadar hızlı ve verimli olmasının tek sırrı, adreslerin bu gruplar halinde organize edilmiş olmasıdır.

### Terminoloji Uyarısı: Network vs. Internetwork 

CCNA çalışırken kelimelerin tam olarak ne anlama geldiğini bilmek hayat kurtarır. 

> * **IP Network:** IP terminolojisinde "Network" kelimesi çok spesifik, **mantıksal bir kavramdır.** Belirli bir IP adresi grubunu ifade eder (Örn: Sınıf A Ağı, 192.168.1.0 ağı gibi).
> * **Internetwork (İnternet Ağı):** Router'ların, Switch'lerin, kabloların ve cihazların birbirine bağlanmasıyla oluşan o devasa **fiziksel sistemin** tamamına verilen isimdir.
> *Kısacası:* Kablolara ve Switch'lere bakarken "Internetwork", IP adreslerine bakarken "IP Network" kelimesini kullanacağız ki kafalar karışmasın!

## IP Adres Grupları için Kurallar (Networks and Subnets)

TCP/IP, aynı fiziksel ağda (örneğin aynı Switch'e bağlı) bulunan cihazların IP adreslerini bir **Grup** halinde toplar. Bu gruplara **IP Network** veya **IP Subnet** diyoruz bunu unutmayalım.

### Posta Kodu Mantığı

Nasıl ki aynı mahalledeki tüm evlerin posta kodu aynıysa, aynı ağdaki cihazların da IP adreslerinin başlangıç kısımları **aynı olmak zorundadır.**

* **Posta Sistemi:** Yan yana iki evin posta kodunun farklı olması çok saçma olurdu. Aynı şekilde, ülkenin bir ucundaki evle diğer ucundaki evin posta kodunun aynı olması da imkansızdır.
* **IP Sistemi:** Yakın (aynı ağdaki) IP adresleri aynı grupta olmalı, uzak (farklı ağlardaki) IP adresleri farklı gruplarda olmalıdır.

### Sayısal Gruplama Örneği

Bir önceki konulardaki örneklerde IP adreslerine dikkatle bakarsan, adreslerin rastgele verilmediğini görürsün. İlk 3 kısımları aynıdır:

* **PC1'in bulunduğu üst LAN:** Adresler `150.150.1` ile başlar.
* **R1 ile R2 arasındaki Serial WAN:** Adresler `150.150.2` ile başlar.
* **R2 ile R3 arasındaki EoMPLS WAN:** Adresler `150.150.3` ile başlar.
* **PC2'nin bulunduğu alt LAN:** Adresler `150.150.4` ile başlar.

### Neden Grupluyoruz?

Bu gruplamanın IP Routing açısından devasa bir faydası vardır: **Routing Table boyutunu küçültmek!**
Eğer gruplama olmasaydı, Router'ın dünyadaki milyarlarca cihazın tek tek IP adresini ezberlemesi gerekirdi. Ama Subnet mantığı sayesinde Router sadece "Grupları" bilir. *"150.150.4.x mahallesine gidenler sağdan çıksın"* demesi yeterlidir.

### Subnetting'in İki Altın Kuralı

Subnetting konusuna çok detaylı gireceğiz ama şu an bu iki temel kuralı zihne kazmak şart. Sınavda hayat kurtarır:

1. **Aynı Gruptalar:** Eğer iki cihazın IP adresi arasında hiçbir Router yoksa (örneğin aynı Switch'e bağlı iki PC), bu cihazlar **AYNI grupta (Subnet)** olmak zorundadır.
2. **Farklı Gruptalar:** Eğer iki cihazın IP adresi arasında en az bir tane Router varsa, bu cihazlar kesinlikle **FARKLI gruplarda (Subnets)** olmak zorundadır! Router sınır çizer!
 
## IP Header Anatomisi

Routing işleminin kalbinde **IPv4 Header** yatar. Router'lar bir paketi aldıklarında, nereye gideceğini bulmak için doğrudan bu header içine bakarlar.

### Header Yapısı

Standart bir IPv4 Header **20 Byte** uzunluğundadır ve satırları 4'er Byte (32 bit) olacak şekilde çizilir. Aşağıdaki örnek, bu 20 Byte'ın içine nelerin sığdırıldığını gösteriyor:

**[IPv4 Header (Toplam 20 Bytes)]**

```text
     +------------+------------+------------------------+
     | Version, Length, DS Field,    Packet Length      | ---> 4
     +------------+------------+---+--------------------+
     |      Identification,   Flag,   Fragment Offset   | ---> 4
     +------------+------------+---+--------------------+ 
     | Time to Live (TTL) |  Protocol  | Header Checksum| ---> 4
     +--------------------+------------+----------------+
     |               Source IP Address                  | ---> 4
     +--------------------------------------------------+
     |             Destination IP Address               | ---> 4
     +--------------------------------------------------+
                                        Toplam = 20 Bytes

```

### Sınav İçin Altın Bilgiler

Bu tablodaki her alanı şu an ezberlemeye gerek yok. Ancak şu üç bilgiyi adın gibi bilmelisin:

1. **32-Bit Adresler:** IPv4 başlığında hem Source hem de Destination IP adresleri bulunur ve bu adreslerin her biri **32 bit** (4 Byte) uzunluğundadır.
2. **20-Byte Boyut:** Standart bir IPv4 header'ın toplam boyutu **20 Byte**'tır.
3. **Değişmeyen Tek Şey:** Bu konuların başından beri anlattığımız o kritik kuralı hatırla! Router'lar her durakta Layer 2 (Data-Link / MAC / HDLC) header'ları çöpe atıp yenisini yazar. **Ancak IPv4 header'ına (yukarıdaki tabloya) DOKUNMAZLAR!** IP header'ı ve içindeki IP adresleri, paketin yolculuğu boyunca **DEĞİŞMEZ.**

IP header yolculuk boyunca değişmez deriz ama ufak bir istisna vardır: Her Router'dan geçerken `Time to Live (TTL)` değeri 1 azaltılır ve buna bağlı olarak `Header Checksum` yeniden hesaplanır. Ama Source ve Destination IP adresleri sabittir!

## Routing Protokolleri Nasıl Çalışır?

Routing mantığının kusursuz çalışması için ağdaki herkesin üzerine düşeni bilmesi gerekir:

* **Bilgisayarlar (Hosts):** Sadece kendi **Default Gateway**'lerinin IP adresini bilmeleri yeterlidir. Gerisine karışmazlar.
* **Router'lar:** Ulaşılabilir **her bir IP network ve subnet** bilmek zorundadırlar!

Peki bir Router bu kadar yolu nasıl öğrenir? Ağ uzmanı geçip bütün yolları tek tek eliyle girebilir (Buna *Static Routing* diyoruz). 
Bunun yerine en iyi yöntem; tüm Router'larda **Aynı IP Routing Protokolünü (OSPF, EIGRP vb.)** çalıştırmaktır. Protokolü açarsın ve Router'lar kendi aralarında mesajlaşarak tüm haritayı otomatik olarak öğrenirler.

### 3 Evrensel Adım

Farklı routing protokolleri olsa da (aksi takdirde tek bir protokol olurdu), hepsi temelde şu 3 adımı izler:

1. **Önce Kendi Kapının Önünü Süpür:** Her Router, öncelikle kendi kablosunun doğrudan bağlı olduğu ağları (Subnets) haritasına ekler. (Bunun için yönlendirme protokolüne bile ihtiyaç yoktur, kabloyu takmak yeterlidir).
2. **Komşularla Dedikodu Yap:** Router, kendi bildiği tüm yolları (hem doğrudan bağlı olanları hem de başkalarından duyduklarını) komşu Router'lara anlatır.
3. **Duyduklarını Haritana Yaz:** Bir Router, komşusundan yeni bir yol öğrendiğinde bunu haritasına ekler. Ve bu yeni ağa gitmek için **"Next-Hop"** olarak haberi getiren komşusunu yazar.

Bazen bir Router aynı hedefe giden birden fazla yol öğrenebilir. Bu durumda Router, **"Metric"** adı verilen bir ölçüm değerine (hız, mesafe vs.) bakar ve **en iyi** yolu seçip Routing tablosuna sadece onu yazar.

### Adım Adım Yönlendirme

Şimdi gözümüzde canlandıralım. Hatırlarsan en altta `150.150.4.0` ağı (PC2'nin olduğu yer) vardı. Bu ağın varlığı, en aşağıdaki R3'ten en yukarıdaki R1'e kadar nasıl fısıldanıyor, adım adım izleyelim:

**[Routing Protokollerinin Subnets'ler Hakkında Nasıl Duyuru Yaptığına Dair Örnek]**

*(Haberler aşağıdan yukarıya doğru yayılır!)*

```text
       (F) R1 Routing Table'a ekler: [150.150.4.0 -> Next Hop: R2 (150.150.2.7)]
          ^
          | (E) R2, R1'e "Ben 150.150.4.0 ağını biliyorum!" der. 
          |
       [ R1 ]
          |
       [ R2 ]
          ^
          | (C) R3, R2'ye "Ben 150.150.4.0 ağını biliyorum!" der. 
          |
          | (D) R2 Routing Table'a ekler: [150.150.4.0 -> Next Hop: R3 (150.150.3.1)]
          |
       [ R3 ]
          | (B) R3, kendisine doğrudan bağlı olan bu ağı haritasına ekler.
          v
    [ Subnet 150.150.4.0 ]  <-- (A) Ağ burada var olur.

```

* **Adım A:** Hikaye en altta başlar. `150.150.4.0` IP subnet'i, R3 Router'ının Ethernet bacağına fiziksel olarak kurulur.
* **Adım B:** R3, "Hey, benim bacağımda yeni bir ağ var!" diyerek bu ağı **Doğrudan Bağlı** olarak haritasına ekler. *(Bunun için protokole gerek yoktur).*
* **Adım C:** R3'ün içindeki Routing Protokolü (OSPF vb.) devreye girer ve komşusu R2'ye bir **Routing Update (Routing Güncellemesi)** mesajı atar: *"Duyduk duymadık demeyin, bende 150.150.4.0 diye bir ağ var!"*
* **Adım D:** R2 bu haberi alır ve kendi haritasına yazar: *"150.150.4.0 ağına gitmek için paketi **R3'e (150.150.3.1)** fırlat."*
* **Adım E:** Dedikodu durmaz! R2, duyduğu bu taze bilgiyi kendi komşusu olan R1'e fısıldar: *"R1 dostum, ben 150.150.4.0 diye bir ağa giden yolu biliyorum haberin olsun."*
* **Adım F:** R1 bu haberi alır ve o da kendi haritasına yazar: *"150.150.4.0 ağına gitmek için paketi Serial0 kapısından **R2'ye (150.150.2.7)** fırlat."*

---

