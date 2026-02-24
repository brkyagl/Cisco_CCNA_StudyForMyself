# Cisco Catalyst Switch CLI'ye erişim (Cisco CLI'a Giriş)

Cisco'nun Router ve kurumsal sınıf Catalyst Switch cihazlarını yönetmenin en temel ve en profesyonel yolu **CLI (Command-Line Interface - Komut Satırı Arayüzü)** kullanmaktır.

### CLI Nedir?

Filmlerde hackerların kullandığı o siyah, sadece yazılardan oluşan ekranları düşün; işte CLI tam olarak odur! Mouse veya tıklanacak butonlar yoktur.

* **Nasıl Çalışır:** Ağ uzmanı/adayları (Yani biz!), yapmak istediği işlem için belirli bir metin komutu yazar ve **Enter** tuşuna basar.
* **Cevap:** Switch bu komutu alır, uygular ve gerekirse sana işlemin sonucuyla ilgili metin tabanlı bir geri bildirim yansıtır.

### Başka Yönetim Yolları Yok mu?

Elbette var. Günümüzdeki Cisco cihazları sadece siyah ekrana mahkum değildir:

1. **Web Arayüzü (GUI):** Tıpkı evdeki modemin arayüzüne (192.168.1.1) girer gibi, web tarayıcısı üzerinden cihazı yönetebilirsin.
2. **Network Management Software:** Ağ yönetim yazılımları (örneğin Cisco DNA Center) ile yüzlerce cihazı tek bir ekrandan görsel olarak kontrol edebilirsin.

CCNA sınavı ve bu notlar, grafik arayüzlerle (Web veya Yönetim Yazılımları) **İLGİLENMEZ.** 
Bizim tek odak noktamız **Cisco Catalyst Enterprise-Class** switchler ve onları **CLI** üzerinden nasıl izleyip yöneteceğimizdir. 

## Cisco Catalyst Switchler

Cisco, LAN Switch üretirken **Catalyst** markasını kullanır. Bu marka altında yüzlerce farklı model vardır ve bu modeller "Seriler" halinde gruplandırılır.
Örneğin, CCNA sınavında ve eğitimlerde en çok göreceğin kurumsal cihazlardan biri **Cisco 2960-XR** serisidir.

* **Rolü:** Genelde "Sistem Odası" içinde duran, end userlara bağlanan **Access** seviyesi switch'lerdir.
* **Özellikleri:** Bu cihazların üzerinde genellikle 24 veya 48 adet RJ-45 (UTP) port bulunur. Bu portlar **10/100/1000 Mbps** hızlarını otomatik olarak ayarlayabilir.

### Interfaces ve Ports (Port İsimlendirme Mantığı)

Cisco dünyasında bir switch'in üzerindeki fiziksel yuvalara **Interface** veya **Port** denir. Komut satırında bu portlara hükmederken bilmen gereken **Altın Kural** şudur:

> Bir portun komut satırındaki kalıcı ismi, her zaman o portun desteklediği **EN YÜKSEK HIZA** göre belirlenir.
> *Örnek:* Elimizde 10/100/1000 Mbps destekleyen bir port var. O an o porta 10 Mbps hızında çalışan eski bir yazıcı taksan bile, komut satırında o portun adı hala **GigabitEthernet**'tir!

**Hızlara Göre İsimler:**

* 10 Mbps -> `Ethernet`
* 100 Mbps -> `FastEthernet`
* 1000 Mbps -> `GigabitEthernet` (veya `GigE`)

### Numaralandırma Sistemi

Sadece ismini bilmek yetmez, o ismin yanına hangi yuva olduğunu belirten sayıları da doğru yazmalısın. Cisco cihazlarında iki farklı numaralandırma formatı görürsün:

1. **İki Haneli Numaralandırma (x/y):**
* Daha eski veya modüler olmayan sabit switch'lerde görülür.
* Örnek: `GigabitEthernet 0/0` veya `GigabitEthernet 0/1`

2. **Üç Haneli Numaralandırma (x/y/z):**
* Stack yapılabilen veya daha yeni nesil switch'lerde (Örn: 2960-XR) kullanılır. İlk rakam genelde switch'in stack içindeki numarasını belirtir.
* Örnek: `GigabitEthernet 1/0/1` veya `GigabitEthernet 1/0/2`

CLI'da çalışırken bu uzun isimleri yazmak zorunda değilsin! Cisco çok zekidir; `GigabitEthernet 1/0/1` yerine sadece `Gi1/0/1` yazarsan switch ne demek istediğini anlar.

