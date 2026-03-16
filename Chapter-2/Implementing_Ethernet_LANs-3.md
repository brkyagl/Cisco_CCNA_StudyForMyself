## Switch Yönetiminin Yapılandırılması

Bir Cisco switch'i kutusundan çıkarıp kablolarını taktığında, Frame'leri iletmeye anında başlar ya hani. Ancak bu cihazı uzaktan güvenli bir şekilde yönetmek için yapmamız gereken kritik yapılandırmalar vardır.

### CLI - Komut Satırını Güvenceye Almak

Default ayarlarda, bir Cisco switch'in **Console** portuna kabloyu takan herkes, hiçbir şifre veya güvenlik engeliyle karşılaşmadan doğrudan **User Mode**'a, oradan da **Enable Mode** ve **Configuration Mode**'a geçiş yapabilir.

* *Neden böyle tasarlanmış?* Çünkü Cisco şöyle düşünür: "Eğer bir kişi o sistem odasına girip cihaza fiziksel olarak Console kablosunu takabildiyse, zaten o cihazın fiziksel kontrolünü ele geçirmiş demektir."
* *Fakat gerçek dünya nasıldır?* Biz ağ uzmanları cihazları her zaman fiziksel olarak değil, uzaktan da yönetiriz. Bu yüzden atacağımız ilk adım, o **CLI (Command-Line Interface)** erişimini sadece yetkili kişilerin girebileceği şekilde kilitlemektir.

### Neden Her İki Modu da Korumalıyız?

Güvenlik sadece en yetkili modu kilitlemekten ibaret değildir:

1. **Enable Mode'u Korumak:** En kritik adımdır. Çünkü Enable Mode'a giren bir saldırgan, switch'i yeniden başlatabilir veya tüm konfigürasyonu silip değiştirebilir.
2. **User Mode'u Korumak:** En az Enable Mode kadar önemlidir! User Mode'da yapılandırma değiştirilemese bile, bir saldırgan `show` komutlarını kullanarak ağın durumunu görebilir, bağlı cihazlar hakkında bilgi toplayabilir ve ağa saldırmak için yeni yollar keşfedebilir.

### Uzaktan Erişim İçin Altın Kural (IPv4 Önkoşulu)

Bir switch'i uzaktan yönetebilmek (Remote Access & Management) için **Telnet** veya **SSH (Secure Shell)** gibi protokolleri kullanırız. 
Ancak bu protokollerin çalışabilmesi için switch'in kendisine ait, çalışan bir **IPv4 konfigürasyonunun** olması şarttır!

> Gördüğümüz o Layer 2 Ethernet Frame Forwarding işlemlerinin, IP adresleriyle **HİÇBİR** alakası yoktur! Switch o işi IP adresi olmadan da MAC adreslerine bakarak kusursuzca yapar.
> Switch'e vereceğimiz bu IPv4 adresi, cihazın trafiği yönlendirmesi için değil, **SADECE BİZİM cihaza Telnet/SSH ile bağlanıp yönetebilmemiz içindir!**

### Bu Bölümde Neler Öğreneceğiz?

Login Security konusunda şu 4 temel aşamayı adım adım laboratuvarda yapılandıracağız:

1. User Mode ve Privileged Mode'u basit şifrelerle korumak.
2. User Mode erişimini cihazın kendi içindeki local kullanıcı adlarıyla korumak.
3. User Mode erişimini harici bir kimlik doğrulama sunucusuyla korumak.
4. Uzaktan erişimi, güvensiz Telnet yerine şifreli **SSH (Secure Shell)** ile korumak.
