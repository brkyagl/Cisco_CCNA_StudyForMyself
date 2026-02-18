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
