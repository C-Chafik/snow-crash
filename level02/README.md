Pour le level02, on nous donne un fichier "level02.pcap"

Apres recherche de l'extension, c'est un fichier qui est lisible par des lecteur de packet


Comme Wireshark, on le lance donc avec wireshark, et on constate qu'il y a des packet a lire.

Ce sont des packet du protocol TCP, on le suit donc grace au logiciel, et on peut y trouver le mot de passe du flag02.

Ce mot de passe est le suivant (Lorsqu'il est lu par wireshark)

"ft_wandr...NDRel.L0L"
## Level 02


## Solution

This time we have a .pcap file

```
level02@SnowCrash:~$ ls
level02.pcap
```

.pcap files are files that contain information about a network, to see traffic or packets.

So the clue is obvious, we must open this file with a software that can read it, like Wireshark.


## Solution

So we open the file with Wireshark and we have this :

![Wireshark](./Wireshark1.png)


Wireshark remplace les caractere non printable par des ., il suffit donc de regarder le packet en hexadecimal.

On se rend compte que les . ont la valeur hexa 7f, qui correspond a DEL

Le vrai mot de passe est donc 


"ft_wandrNDRelL0L

TOKEN : kooda2puivaav1idi4f57q8iq"
