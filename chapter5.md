## Integrated DNS 
* il faut disposer du paquet ipa-server-dns, en plus de ipa-server  
* pour une integration des le debut: `ipa-server-install --setup-dns`
* pour une integration après: `ipa-dns-install`
  * un petit probleme ici. pour installer ipa, on a forcement du mettre en place une vraie resolution (A et PTR), dc a travers un DNS external
  * si par la suite une integration est envisagée, ca suppose une migration de la zone et un decomissionnement de l'ancien SOA
  * ca sous entend egalement un changement des ipv4.dns des clients
  * Autre chose: sans DNS, l'option DNS forwarders na pas de sens: dc ne pas la choisir a linstall, sinon echec.
* check du role dns: 
  * `ipa server-find --servroles="dns server"`
  * `ipa config-show`
  * `ipa dnsserver-find`
* `ipa dns-update-system-records [--dry-run]` va sortir tous les records sous le SOA. c particulierement utile a envoyer à un DNS external
* tous les records sont enregistrés comme des entrées LDAP:
  `ldapsearch -b "cn=dns, dc=thelinuxlabs, dc=com"`
* `ipa dnszone-show lab.example.com`
  * preter attention a lattribut dynamic updates, il est tres utile: ipa dnszone-mod lab.example.com --dynamic-update=True en conjonction avec un enrolement de client ipa-client-install --enable-dns-updates
    va permettre une maj auto de la config DNS avec l'ip du client  
* `ipa dnsrecord-find` pour lister les records
* `ipa dnsrecord-add lab.example.com srv1 --a-rec="192.168.1.5"`
* `ipa dnsrecord-add lab.example.com _ldap._tcp --srv-rec="0 100 389 idm.lab.example.com."` **attention au point a la fin !**
*  lorskon cree un record de type service, et que ce service est redondant dans linfra, ne pas omettre dajouter autant de records que necessaire

## Integrated CA
* create a local NSS database: `certutil -N -d ~/certs`
* generate a CSR: `certutil -R -d ~/certs -a -g 4096 -n httpsrv -s "CN=httpsrv,O=LAB.EXAMPLE.COM"` >./httpsrv.csr
* submit the CSR and get the PEM: `ipa cert-request --profile-id=IECUserRoles --certificate-out ./cert.pem --principal=httpsrv@LAB.EXAMPLE.COM ./httpsrv.csr`
  * ici on peut deja verifier si le user a le certificat assigned: `ipa user-show httpsrv`
* insert the PEM into the NSS DB: `certutil -A -d ~/certs -n httpsrv -t "P,," -i ./cert.pem`
* `certutil -K -d certs` lister le contenu de la NSS DB

### Certif (suite)
* AJouter un principal pour un service pour un client: `ipa service-add http/client.lab.example.com`
* Creer un CSR pour ce service:
  * supposons quon va utiliser le repertoire /etc/httpd/certs
  * `semanage fcontext -a -t cert_t "/etc/httpd/certs(/.*)?" && restorecon -R -v /etc/httpd/certs`
  * `ipa-getcert -f /etc/httpd/certs/cert.pem -k /etc/httpd/certs/cert.key -K http/client.lab.example.com -D client.lab.example.com`
  * on reference ensuite le certificat et la clé dans la config du service, en loccurence ici pour httpd, selon apache ou nginx, les directive SSLCertificateFile et SSLCertificateKey
  * Associate a custom service record:
    * `ipa dnsrecord-add lab.example.com custom-service --srv-rec="0 100 443 client.lab.example.com."`
        
  
