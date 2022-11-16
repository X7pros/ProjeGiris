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
