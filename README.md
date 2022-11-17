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



