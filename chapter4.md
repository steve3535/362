### About ansible playbooks for installing IPA components 
* i hit a very obscure error while trying to install a replica with ansible
* first, was thinking that the root cause was in the inner python code of ansible  
* just to realize - 12 h laters or so - that it was about missing variables in the inventory file:
  **[ipaserver]** definition and **ipareplica_domain** definition: WEIRD !!!!
  
