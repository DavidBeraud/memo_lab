La fourniture d'une phrase passe pour votre clé SSH fournit une couche de sécurité supplémentaire, mais elle peut également entraîner des problèmes lorsque vous exécutez des scripts automatisés qui requièrent la clé protégée. 

L'agent ssh-agent peut gérer vos clés. Vous n'entrez qu'une seule fois la phrase passe. 

L'agent ssh-agent conserve votre clé en mémoire et la restitue si nécessaire. 

Si vous souhaitez que l'agent ssh-agent gère vos clés, exécutez la commande suivante:

```
eval $(ssh-agent)
```

Une fois que le programme a démarré la commande ssh-add pour ajouter votre clé publique à l'agent, 
l'utilitaire ssh-add recherche les noms de clé par défaut, dont id_rsa est un, et les ajoute à l'agent ssh-agent. 
Après avoir entré votre mot de passe, la clé "déverrouillée" est stockée avec ssh-agent et peut être utilisée pour s'authentifier auprès d'autres serveurs.

```
root@bck1:~# ssh-add
Enter passphrase for /root/.ssh/id_rsa:
Identity added: /root/.ssh/id_rsa (/root/.ssh/id_rsa)
root@bck1:~#
```

Chaque fois que vous ouvrez une nouvelle session de terminal, vous êtes invité à entrer la phrase passe de clé. 
Envisagez d'exécuter les commandes suivantes pour ajouter votre fichier .bash_profile de sorte que ssh-agent démarre avec chaque session bash et ajoute votre clé.

```
echo ‘eval $(ssh-agent)’ >> ~/.bash_profile
```
```
echo ‘ssh-add’ >> ~/.bash_profile Github 
```
