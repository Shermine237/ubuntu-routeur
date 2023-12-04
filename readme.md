# Configuration d’Ubuntu en tant que routeur avec des fonctionnalités de routage avancées

## Comprendre le routage
Avant de nous plonger dans la configuration, comprenons brièvement le routage.

Le routage est le processus qui consiste à diriger le trafic réseau entre différents réseaux ou sous-réseaux.
Un routeur est un appareil qui connecte plusieurs réseaux et achemine les données entre eux.
Votre machine Ubuntu peut servir de routeur en tirant parti de ses interfaces réseau et en configurant des tables de routage.

## Activation du transfert IP
### Étape 1
Vérifier l’état actuel du transfert IP Avant de continuer, vérifiez si le transfert IP est actuellement activé sur votre machine Ubuntu :
```bash
cat /proc/sys/net/ipv4/ip_forward
```
Si la sortie est "0" , le transfert IP est désactivé. Si c’est "1", il est déjà activé (vous pouvez sauter l'étape suivante)
### Étape 2
Activer le transfert IP Pour activer temporairement le transfert IP (valable jusqu’au prochain redémarrage), exécutez :
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```
Pour rendre la modification permanente, modifiez le fichier et décommentez ou ajoutez la ligne : <code>/etc/sysctl.conf</code>
```bash
net.ipv4.ip_forward=1
```
Ensuite, appliquez les modifications suivantes :
```bash
sudo sysctl -p /etc/sysctl.conf
```

## Configuration des interfaces réseau
### Étape 1
Identifiez vos interfaces réseau
```bash
ip addr
```
Vous devriez voir une liste d’interfaces comme <code>eth0 eth1</code> ... etc
### Étape 2
Configurez les interfaces réseau (Attribuer une adresse IP à chaque interface)
```bash
sudo vim /etc/network/interfaces
```
Voici un exemple de configuration pour <code>eth1</code>
```bash
# eth1 - Internal LAN interface
auto eth1
iface eth1 inet static
    address 192.168.1.1
    netmask 255.255.255.0
```
### Étape 3
Appliquez les modifications aux interfaces réseau
```bash
sudo systemctl restart networking
```

## Configuration du NAT
### Étape 1
Pour activer le NAT pour le trafic sortant de votre réseau local, utilisez : <code>iptables</code> pour chaque interface
```bash
sudo iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE
```
Rendez le changement permanent en installant : <code>iptables-persistent</code>
```bash
sudo apt update
sudo apt install iptables-persistent
iptables-save > /etc/iptables/rules.v4
```

## Configuration du serveur DHCP
Si vous souhaitez que votre routeur Ubuntu attribue des adresses IP aux périphériques du réseau local
### Étape 1
Installer le serveur DHCP
```bash
sudo apt update
sudo apt install isc-dhcp-server
```
### Étape 2
 Modifiez le fichier de configuration du serveur DHCP
```bash
sudo vim /etc/dhcp/dhcpd.conf
```
Voici un exemple de configuration :
```bash
subnet 192.168.1.0 netmask 255.255.255.0 {
  range 192.168.1.10 192.168.1.50;
  option routers 192.168.1.1;
  option domain-name-servers 8.8.8.8, 8.8.4.4;
}
```
### Étape 3
Démarrez le serveur DHCP
```bash
sudo systemctl start isc-dhcp-server
```
### Étape 4
Activer le serveur DHCP au démarrage Pour vous assurer que le serveur DHCP démarre au démarrage
```bash
sudo systemctl enable isc-dhcp-server
```

## Routage avancé
Vous pouvez implémenter des fonctionnalités de routage avancées à l’aide d’outils tels que <code>iproute2</code>. Exemple basic :
```bash
ip route add 192.168.1.0/24 dev eth1
```
Pour voir la table de routage:
```bash
ip route show table main
```
Pour prendre en compte nos modification :
```bash
ip route flush cache
```
