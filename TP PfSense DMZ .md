# TP PfSense / DMZ

<aside>
✏️

## Mise en place d'une architecture DMZ

</aside>

<aside>
✏️

### Mise en situation :

La société Vikor désire installer un serveur Web au sein de son
architecture réseau ,ce serveur doit être accessible depuis le LAN de
l’entreprise ainsi que de « l’extérieur » ,à savoir internet.
Afin de sécuriser au maximum son réseau ,le choix de PfSense a été fait
en raison de sa flexibilité et du nombre de fonctionnalités disponibles.

![image.png](image.png)

</aside>

<aside>
✏️

#### Travail demandé
ATTENTION : Vous déposerez ,pour la date indiquée par l’enseignant sur
la plateforme Moodle ,un compte rendu faisant clairement apparaître vos
choix ainsi que la démarche détaillée de toutes vos manipulations et
commandes ayant permis d’aboutir à la matérialisation du cahier des
charges ,ce document doit être exploitable par le technicien d’astreinte en
cas de problème.

</aside>

<aside>
✏️

#### Etape 1 : Installer PfSense sur une VM dotée de 3 interfaces : LAN,DMZ,Internet

</aside>

Création de la carte LAN et DMZ (bridge)

![image.png](image%201.png)

installation de PfSenses 

![image.png](image%202.png)

adressage des IP : le numero 2 (set interface ip adresse) —> choisisser l’interface que vous voulez (LAN / OPT1) puis passer a l’adressage de l’ip 

choisissez

- IPv4 via DHCP : **n**
- Adresse : `192.168.X.X`, masque : `24`
- Passerelle : vide (Entrée)
- IPv6 : **n**, puis Entrée
- DHCP server : **n**

![image.png](image%203.png)

![image.png](image%204.png)

Création de la VM client et ajout de la carte  réseaux LAN dessus pour que la vm recois une ip en 192.168.1.X 

![image.png](image%205.png)

Vérification : 

![image.png](image%206.png)

l’ip de la vm a bien une ip en 1.X !

Connexion au pfSense : 

![image.png](image%207.png)

Nous avons bien accès au Pfsense en mettant l’adresse du LAN 

Nous avons bien acces au Pfsense avec admin/pfsense

![image.png](image%208.png)

Mise en place du serv apache connecté a la DMZ  (VM apache-pfsense):

 d’abord nous allon mettre comme carte réseaux le LAN 

![image.png](image%209.png)

Ensuite  nous allons mettre l’ip qui correspond a la DMZ (192.168.2.X) car nous voullons quelle soit connecté a elle et la passerelle sera donc celle de la DMZ 192.168.2.5 

![image.png](image%2010.png)

Installation de apache sur la VM : 

![image.png](image%2011.png)

étape 3 : Configurer PfSense afin de rendre votre serveur Web accessible depuis
votre LAN et Internet.

Débloquer le WAN 

Pourquoi : par défaut, pfSense bloque tout ce qui arrive sur le WAN depuis une adresse privée (192.168.x.x), car sur un vrai Internet ces adresses sont suspectes. Ici, votre « Internet » est la salle en 192.168.20.x, donc il faut désactiver ce blocage.

aller dans l’interface WAN :

![image.png](37cd277a-3f64-4174-899a-e8617beea89d.png)

Ensuite descendez en bas de la page et décocher  “Block private” et “block bogon” puis save —> apply change 

![image.png](b811fd38-2858-4771-b91c-ea80eff233b5.png)

de plus activer l’interface DMZ et mettre l’ip qui correspond 

![image.png](image%2012.png)

nous allons maintenant configurer le PfSense pour avoir un accès depuis le LAN : 

configuration du port forward : 
Pourquoi : le serveur a une IP privée (192.168.2.10) que les autres PC de la salle ne peuvent pas joindre directement. Ils ne voient que l'IP WAN de pfSense (192.168.20.109). Le port forward dit à pfSense : « tout ce qui arrive sur mon WAN au port 80, envoie-le au serveur web ».

Pour faire ceci nous allons aller dans l’onglet Firewall —> NAT —> port forward —> bouton add 

![image.png](image%2013.png)

ensuite nous atterrissons sur cette page et nous allons la modifier comme ceci 
l’ip dans redirect target ip est celle de la DMZ et nous allons accepter seulement le port http pour plus de sécurité 

![image.png](image%2014.png)

Une règle se créer automatiquement ou nous voyons bien qu’il accepte seulement le port 80 HTTP

![image.png](image%2015.png)

configuration du LAN :

Pourquoi **:** le LAN et la DMZ sont deux réseaux directement connectés à pfSense. Il n'y a pas de NAT à faire, seulement une règle de pare-feu qui autorise le passage.

![image.png](image%2016.png)

![image.png](image%2017.png)

L'accès LAN vers le serveur web est assuré par la règle « Default allow LAN to any rule ». Le LAN et la DMZ sont deux réseaux directement connectés à pfSense, donc le trafic est simplement routé

TEST : 

nous allons donc tester et nous connecter a la page de PfSense avec la VM client (192.168.1.6) en mettant l’ip de la DMZ (192.168.2.5)

![image.png](image%2018.png)

nous arrivons bien a nous connecter sur la page de PfSense donc l’acces au LAN est validé !

nous allons voir aussi si on peut accéder a la page apache 

![image.png](image%2019.png)

Nous avons bien acces a la page apache a partir de la machine client dans le LAN 

Pour finir nous allons tester si on peut acceder a la page apache a partir d’un PC dans la salle (WAN)

![image.png](image%2020.png)

nous atterrissons sur la page apache comme prévu avec une ip dhcp que internet (la salle nous a donné) !!