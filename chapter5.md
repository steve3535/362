## Integrated DNS 
* il faut disposer du paquet ipa-server-dns, en plus de ipa-server  
* pour une integration des le debut: `ipa-server-install --setup-dns`
* pour une integration après: `ipa-dns-install`
  * un petit probleme ici. pour installer ipa, on a forcement du mettre en place une vraie resolution (A et PTR), dc a travers un DNS external
  * si par la suite une integration est envisagée, ca suppose une migration de la zone et un decomissionnement de l'ancien SOA
  * ca sous entend egalement un changement des ipv4.dns des clients  
