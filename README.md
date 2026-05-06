# LAB 16 — Inspection HTTPS Android : Désactivation du SSL Pinning avec Objection + Burp Proxy

## 1. Objectif du lab

Ce lab a pour objectif d’intercepter le trafic réseau d’une application Android en passant par un proxy HTTP/HTTPS, puis de désactiver le SSL Pinning à l’aide de Frida et Objection.

L’objectif principal est de vérifier que les requêtes de l’application peuvent être visibles dans Burp Suite après la configuration du proxy, l’installation du certificat CA et l’exécution de la commande Objection.

---

## 2. Environnement utilisé

- Système hôte : Windows
- Émulateur Android : Android 11
- Application cible : InsecureBankv2
- Package Android : `com.android.insecurebankv2`
- Proxy utilisé : Burp Suite Community Edition
- Port proxy : `8080`
- Adresse proxy configurée sur Android : `192.168.11.130`
- Outils utilisés :
  - ADB
  - Frida
  - frida-server
  - Objection
  - Burp Suite

---

## 3. Application utilisée

L’application utilisée dans ce lab est :


InsecureBankv2

Package utilisé :

com.android.insecurebankv2

Cette application est une application Android volontairement vulnérable utilisée dans les labs de sécurité mobile.

4. Configuration du proxy Android

Dans l’émulateur Android, le réseau Wi-Fi AndroidWifi a été configuré pour utiliser un proxy manuel.

Configuration utilisée :

Proxy : Manual
Proxy hostname : 192.168.11.130
Proxy port : 8080
IP settings : DHCP

Cette configuration permet de rediriger le trafic HTTP/HTTPS de l’émulateur Android vers Burp Suite.

<img width="376" height="819" alt="Capture d&#39;écran 2026-05-06 144348" src="https://github.com/user-attachments/assets/da62ee1c-fa9d-4c9d-ba18-47e0e065d220" />


5. Configuration de Burp Suite

Dans Burp Suite, un proxy listener a été configuré sur le port 8080.

Configuration utilisée :

Bind to port : 8080
Bind to address : All interfaces

Le choix All interfaces permet à l’émulateur Android de se connecter au proxy Burp en utilisant l’adresse IP de la machine hôte.

<img width="977" height="664" alt="Capture d&#39;écran 2026-05-06 143956" src="https://github.com/user-attachments/assets/f2ad3efb-863b-4fef-bf3b-2971013f40ec" />


6. Téléchargement du certificat CA Burp

Depuis le navigateur Android, l’adresse suivante a été ouverte :

http://burp

La page Burp Suite Community Edition s’est affichée correctement, ce qui confirme que l’émulateur communique bien avec Burp Suite.

Ensuite, le certificat CA a été téléchargé depuis le bouton :

CA Certificate

Le fichier téléchargé est :

cacert.der
<img width="395" height="822" alt="Capture d&#39;écran 2026-05-06 144521" src="https://github.com/user-attachments/assets/88325a5a-46f5-47e9-a806-a494f03a622b" />


7. Installation du certificat CA sur Android

Après le téléchargement du certificat Burp, celui-ci a été installé sur Android depuis le menu d’installation des certificats.

Chemin utilisé :

Settings → Security → Encryption & credentials → Install a certificate → CA certificate

Le certificat CA a été installé avec succès.

Message obtenu :

CA certificate installed

Cette étape permet à Android de faire confiance au certificat généré par Burp Suite.

<img width="393" height="810" alt="Capture d&#39;écran 2026-05-06 145041" src="https://github.com/user-attachments/assets/3b7de3c9-1f92-4615-8b66-99ca2b814e42" />


8. Lancement de l’application avec Objection

L’application cible a été lancée avec Objection en utilisant le package suivant :

com.android.insecurebankv2

Commande utilisée :

objection -g com.android.insecurebankv2 explore

Objection s’est attaché correctement à l’application Android.

Résultat observé :

com.android.insecurebankv2 (run) on (Android: 11) [usb]
9. Désactivation du SSL Pinning

Une fois dans la console Objection, la commande suivante a été exécutée :

android sslpinning disable

Résultat obtenu :

(agent) Custom TrustManager ready, overriding SSLContext.init()
(agent) Found com.android.org.conscrypt.TrustManagerImpl, overriding TrustManagerImpl.verifyChain()
(agent) Found com.android.org.conscrypt.TrustManagerImpl, overriding TrustManagerImpl.checkTrustedRecursive()
(agent) Registering job ... Name: android-sslpinning-disable

Cela signifie que les hooks nécessaires ont été installés afin de contourner la vérification SSL Pinning de l’application.

<img width="1405" height="492" alt="Capture d&#39;écran 2026-05-06 154822" src="https://github.com/user-attachments/assets/d4aef933-a684-41bf-9c94-9574e311d2af" />


10. Validation dans Burp Suite

Après la configuration du proxy, l’installation du certificat CA et la désactivation du SSL Pinning, l’application InsecureBankv2 a généré du trafic réseau.

Dans Burp Suite, plusieurs requêtes sont visibles dans l’historique HTTP.

On observe notamment des requêtes vers :

http://192.168.11.130:8888/login
<img width="1852" height="558" alt="Capture d&#39;écran 2026-05-06 155107" src="https://github.com/user-attachments/assets/ddc9353b-880e-4d6e-b9e7-a9fa2bdfcf36" />

Ces requêtes correspondent au trafic généré par l’application Android vers le serveur backend InsecureBankv2.

Capture — Trafic intercepté dans Burp Suite
