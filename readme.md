# Configuration d’Ubuntu en tant que routeur avec des fonctionnalités de routage avancées

## Comprendre le routage
Avant de nous plonger dans la configuration, comprenons brièvement le routage.

Le routage est le processus qui consiste à diriger le trafic réseau entre différents réseaux ou sous-réseaux.
Un routeur est un appareil qui connecte plusieurs réseaux et achemine les données entre eux.
Votre machine Ubuntu peut servir de routeur en tirant parti de ses interfaces réseau et en configurant des tables de routage.

## Activation du transfert IP
### Étape 1:
Vérifier l’état actuel du transfert IP Avant de continuer, vérifiez si le transfert IP est actuellement activé sur votre machine Ubuntu :
```bash
cat /proc/sys/net/ipv4/ip_forward
```
Si la sortie est "0" , le transfert IP est désactivé. Si c’est "1", il est déjà activé (vous pouvez sauter l'étape suivante)
### Étape 2:
Activer le transfert IP Pour activer temporairement le transfert IP (valable jusqu’au prochain redémarrage), exécutez :
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```
Pour rendre la modification permanente, modifiez le fichier et décommentez ou ajoutez la ligne : <code>/etc/sysctl.conf</code>
```bash
net.ipv4.ip_forward=1
```
