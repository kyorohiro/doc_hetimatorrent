# Kademuliaについて
<hr>
<br>
* **距離はXOR**
<br>
* **kBucketでRootingTableを構築**
<br>

<hr>

 Torrentで採用されているDHTは、「Kademulia」と呼ばれているものです。本章では、KademuliaのなかでもkBucketを用いたRootingTableについて紹介して行きます。
 
####  XORで距離を定義する。
前章で説明した通り、DHTでは距離を定義する必要がありました。Kademuliaでは、この距離にxorを利用します。
