Pour l'authentification avec un hôte distant à l'aide de votre clé SSH publique, utilisez la commande ssh-copy-id . 

Utilisez l'indicateur -i pour spécifier la clé publique à copier sur l'hôte distant.

La commande ssh-copy-id ajoute les clés au fichier .ssh/authorized_key de l'hôte distant.

```
ssh-copy-id -i /root/.ssh/id_rsa.pub root@10.176.18.15
```

```
root@bck2: # ssh-copy-id -i /root/.ssh/id_rsa.pub root@10.176.18.15
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
The authenticity of host '10.176.18.15 (10.176.18.15)' can't be established.
ECDSA key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@10.176.18.15's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'root@10.176.18.15'"
and check to make sure that only the key(s) you wanted were added.

```