# Chat client-serveur en C

Messagerie en ligne de commande : un serveur TCP accepte plusieurs clients et renvoie chaque message reçu à tous les clients connectés. Projet de première année à l'ISEN Ouest (cycle CIR).

La version suivante, avec chiffrement RSA, est dans le dépôt [chat-rsa-c](https://github.com/Tanguyrnd/chat-rsa-c).

## Compiler et lancer

Linux (sockets POSIX + pthreads).

```bash
make server client
./server          # écoute sur le port 8989
./client          # dans un autre terminal, autant de fois que voulu
```

Le client se connecte à `127.0.0.1:8989`. Tout ce qui est tapé est envoyé au serveur, qui le diffuse aux clients connectés.

## Organisation du code

```
server.c        accepte les connexions, un thread par client, diffusion des messages
client.c        un thread de réception + la boucle d'envoi sur stdin
common.c        send_message / receive_message (gestion de la déconnexion et du '\n' final)
client_array.c  tableau dynamique des clients connectés (capacité doublée quand il est plein)
```

## Points techniques

- **Un thread par client** côté serveur : `accept()` reste bloquant dans la boucle principale, pendant que chaque client est lu dans son propre thread.
- **Client full-duplex** : la réception tourne dans un thread séparé, ce qui permet d'afficher les messages des autres pendant que `fgets()` attend la saisie.
- **`SO_REUSEADDR`** : le serveur peut être relancé tout de suite, sans attendre que le port soit libéré par le système.
