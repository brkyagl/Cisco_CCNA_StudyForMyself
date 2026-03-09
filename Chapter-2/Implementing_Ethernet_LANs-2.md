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

### Bağımsız Karar Mekanizması (Çok Önemli!)

Üstte hani örnek verdik ya ama geniş bir bakış açısıyla bakacak olursak: bir kullanıcının (PC 1) bilgisayarından çıkan veri, Data Center'daki sunucuya (Server 1) gidip geri dönene kadar yolda belki 5-6 tane farklı switch'ten geçer.
Bu switch'lerin hepsi aynı anahtarlama mantığıyla çalışır ama **kararlarını birbirinden TAMAMEN BAĞIMSIZ alırlar.**
Biri diğerine *"Ben bu paketi sana yolladım, sen de şuraya yolla"* demez! Her switch, gelen Frame'i eline alır, kendi tablosuna bakar, kendi mantığını çalıştırır ve "Bunu iletiyorum", "Bunu çöpe atıyorum" veya "Bunu her yere yolluyorum" diyerek kendi anlık kararını verir.

