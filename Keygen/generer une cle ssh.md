Génération de clés SSH sur Linux

Pour générer une clé SSH sur votre serveur Linux , exécutez la commande ssh-keygen. 
La commande peut prendre des indicateurs si vous souhaitez personnaliser le type de clé générée et les algorithmes de signature utilisés pour générer la clé. 
Voici un exemple de clé RSA 2048 bits standard sans phrase passe. 
La commande vous invite à indiquer l'emplacement de stockage de la clé (la valeur par défaut est $HOME/.ssh/) et une phrase passe pour sécuriser la clé SSH.

```
ssh-keygen
```

```
root@bck2:/etc# ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
```