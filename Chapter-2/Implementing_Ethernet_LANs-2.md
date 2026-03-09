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
