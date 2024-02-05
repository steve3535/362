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
* 


  
