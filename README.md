# IPV4 VE IPV6 omurgası üzerinde OSPF ve Access-list kullanarak server erişimi

## 1) ilk olarak ipv4 interfaceleri router1 içine yazdık
![interfaceİlk](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/1interfaceler.jpg)

```

en 
conf t
int fa0/0 
ip add 192.168.2.1 255.255.255.0  
no sh 
int s0/0/0 
ip add 11.0.0.2 255.255.255.252 
no sh 

```
## 2) Serverların bağlı olduğu Router0 interfaceleri yapılandırmasınıda tamamlayarak bir sonraki aşamaya geçebiliriz
![interfaceiki](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/2interfaceler.jpg)
```

en
conf t
int se0/0/1
clock rate 2000000
ip add 13.0.0.1 255.255.255.252
no sh

int se0/0/0
clock rate 2000000
ip add 11.0.0.1 255.255.255.252
no sh


```
## 3) IPV4 OSPF yapılandırmak için tek tek her routerın içine ospf 1 yazılıp network bacakları girilir
![ospfipv4](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/3ipv4%20ospf%20tamam.jpg)
```

en
conf t
router ospf 1
network 13.0.0.0 0.0.0.3 area 0
network 11.0.0.0 0.0.0.3 area 0
network 192.168.1.0 0.0.0.255 area 0

```
## 4) OSPF ile yapılandırdığımız sistemde Laptop0 dan serverlere ping atabiliyoruz, routerlar yönlendirme yapabiliyor
![pingV4](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/4ipv4%20ospf_pingTestiTamam.jpg)
```

laptop0 terminal
ping 192.168.1.3
192.168.1.3 den reply gelir

ping 192.168.3.2
192.168.3.2 den reply gelir

```
## 5) DNS servisini açıp ipv4 için www.cisco4.com 192.168.1.3 atanır
![DNS1](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/93DNS.jpg)

## 6) Routuer0 a extended access-list girilir ve sadece udp 53, tcp 80 için permit verilir, server tarafa out deriz
![ACL4](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/5ipv4ACLpermit_dns.jpg)
```

en
conf t
ip access-list extended WEBACL
permit udp any host 192.168.1.2 eq 53
permit tcp any host 192.168.1.3 eq 80
int fa0/0
ip access-group WEBACL out

```
## 7) Herhangi bir end device dan web browserdan www.cisco4.com girince site açılmaktadır
![ipv4web](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/6ipv4web_sitesigiris.jpg)

## 8) Router0 tcp 80 ve udp 53 dışında kabul etmediğini ping atığımızda "destination host unreachable" ile görmüş oluyoruz.
![pingv4](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/7Router%20ping%20kabul%20etmiyor%20sadece%20webden%20izin%20veriyor.jpg)
## 9) ipv4 yapılandırması bittiği için ipv6 yapılandırmasına geçiyoruz, ilk olarak routerlara ipv6 interfaceleri giriyoruz
![intfV6](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/8ipv6%20interface.jpg)
```

en
conf t
ipv6 unicast-routing
int fa0/0
ipv6 add 1ef0:222:22:2::1/64

int se0/0/0
ipv6 add 1ef0:abc:bc:c::2/126

```
## 10) Router0 ipv6 interfacelerinide bitirip kendi LAN ağı içinde ping testlerimizi yapıyoruz
![ipv6int](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/9ipv6%20interface%20bitti.jpg)
```

en
conf t
int se0/0/1
ipv6 add 1ef0:def:ef:f::1/126

int se0/0/0
ipv6 add 1ef0:abc:bc:c::1/126

int fa0/0
ipv6 add 1ef0:111:11:1::1/64

do ping 1ef0:111:11:1::3

```
## 11) Tüm routerlara ospf girilir, router0 girilen ospf ve ping tesli şu şekildedir
![v6ospf](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/91ipv6ospf_tamam.jpg)
```

en
conf t
ipv6 router ospf 1

int fa0/0
ipv6 ospf 1 area 0

int se0/0/0
ipv6 ospf 1 area 0

int se0/0/1
ipv6 ospf 1 area 0

do ping 1ef0:333:33:3::3 //routerdan end device a ping testinin başarılı olduğunu görüyoruz

```
## 12) 'do show ipv6 route' komutu ile kurulan ospf bağlantılarını görebiliyoruz
![ipv6route](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/92ipv6doShow%20Route.jpg)

## 13) Daha önce açtığımız DNS servisine ana serverin ipv6 adresi olan ekliyoruz www.cisco6.com 
![DNSv6](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/93DNS.jpg)

## 14) Kurulan ipv6 ospf içinde router0 için access-list kurup ipv6 ile WEB erişimi sağlamak istiyoruz
![accListV6](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/94%20ipv6%20acl%20kodd.jpg)
```

en
conf t
ipv6 access-list WEBACL6
permit udp any host 1ef0:111:11:1::2 eq 53
permit tcp any host 1ef0:111:11:1::3 eq 80

int fa0/0
ipv6 traffic-filter WEBACL6 out

```
## 15) Herhangi bir end device dan web browser girip www.cisco6.com erişebiliyoruz
![WEBv6](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/95ipv6WEBACL.jpg)

## 16) WEB browser dışında herhangi bir end device dan ping attığımız zaman access-list ile izin vermediğimiz için router0 dan ilerisine gidememektedir.
![ipv6Son](https://github.com/X7pros/ProjeGiris/blob/main/Bitirme%20resimler/96ipv6%20Son.jpg)
```


(DNS sunucusuna  ping atıyoruz)

ping 1ef0:111:11:1::2




```




