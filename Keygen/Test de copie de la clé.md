Pour vérifier que la clé publique a été correctement copiée sur l'hôte distant, utilisez ssh sur l'hôte distant.

```
ssh root@10.176.18.15
```

```
root@bck2:/etc# ssh root@10.176.18.15
Welcome to Ubuntu 16.04.2 LTS (GNU/Linux 4.4.0-53-generic x86_64)

* Documentation:  https://help.ubuntu.com
* Management:     https://landscape.canonical.com
* Support:        https://ubuntu.com/advantage

Get cloud support with Ubuntu Advantage Cloud Guest:
http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

Last login: Fri Feb 10 16:51:51 2017 from 169.46.3.91
root@bck1:~#

```

Vous n'êtes pas invité à entrer le mot de passe lorsque vous utilisez ssh sur l'hôte distant.


