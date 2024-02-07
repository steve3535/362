### client enrollment
* les enregistrements SRV pointant sur les services ldap et kerberos en charge du domaine sont cruciaux pour le autodiscovery.  
  pour verifier: `dig +short SRV _ldap._tcp.lab.example.com`   `dig +short SRV _kerberos._tcp.lab.example.com`
  AUtermment dit, si pas de reponse: check au niveau des records DNS ou checker le ipv4.dns mis en parametre de la conn pur sassurer que c le bon DNS a utiliser  
* on peut creer le host a lavance et generer un OTP pour l'install:
  ```bash
  ipa host-add client --random (sur le server idm)
  ipa-client-install client --password (sur le client)
  ```
* on peut aussi utiliser le keytab /etc/krb5.keytab sur un precedemment enrolled client: `ìpa-client-install --keytab`  
  attention: dans ce cas il aurait fallu backuper le fichier keytab present sur le client  
* des fois, on peut vouloir tout garder de la config client mais lexclure du domaine ?  
  `ipa-join --unenroll` (sur le client)
  
* `ipa host-show`
  on voit les details du client - preter attention au seeting keytab. si c a false, alors le host nest pas actuellement provisionné
* AUtre chose: toute machine enrolled upload sa ssh key sur le server. permettant ainsi a toute machine du domaine de se connecter sans confirmation d'acceptation du fingerprint: cool !   

### ipa replica install   
* preliminaires: installer **ipa-server[-dns]** et ouvrir les memes ports que sur un server IPA:  
  `firewall-cmd --add-service=freeipa-ldap --add-service=freeipa-ldaps --add-service=dns`  
* on peut faire linstall sur une nouvelle machine ou on peut promouvoir un client existant  
  * sur une nouvelle machine:
    on considere 2 scenarii distincts:
    1) on na pas la main sur la machine - exp on est dans une grande org et on a des filiales un peu partout avec leur autonomie, on est au groupe level mais on veut permettre linstallation de replicas par des admin tiers.  
       dans ce cas linstall sera basé sur un OTP mais il ya des etapes a suivre:  
       * `ipa host-add`  pour ajouter le server  
       * `ipa host-nod serverxxx --random` pour generer le pass OTP  
       * `ipa hostgroup-add-member ipaservers --hosts serverxxx` pour le mettre dans le groupe des servers IPA  
       * (sur le server xxx) `ipa-replica-install -p 'OTP pass'` A noter que si linstall echoue ou reussie, l'OTP expire  
    3) on a la main sur la machine.
       dans ce cas on peut fournir directement le principal admin:
       `ipa-replica-install --setup-dns --forwarder 172.25.250.254 -w`
       le -w implicite le username admin  
    


  
