---
layout: single
title: Solution au problème d'Apache2 sur Kali Nethunter
excerpt: "Je vais expliquer en détail la solution :"
date: 2023-04-22
classes: wide
header:
  teaser: /assets/images/apache2/apache2.png
  teaser_home_page: true
  icon: /assets/images/hacktheboxx.webp
categories:
  - Francais
tags:
  - Fix
  - Apache2
  - Web-Server
  - Kali-nethunter
---

<p align="center">
<img src="/assets/images/apache2/lemp.png" width="500">
</p>

Dans la cybersecurité, l'un des serveurs les plus connus et les plus utilisés est le serveur web.
Un serveur web, également appelé serveur *LAMP* o *LEMP*, est composé de:
  - *L* pour **L**inux, le systéme d'exploitation du serveur.
  - *A* pour **A**pache ou *E* pour **N**ginx(prononcé *EN*ginx), le service d'hébergement.
  - *M* pour **M**ySQL  ou **M**ariaDB, Le moteur de base de données
  - *P* pour **P**HP, un langage de programmation essentiel
  
  
  
   
Sa fonction principale est d'héberger des sites web, de traiter les requêtes HTTP et de fournir des contenus web aux utilisateurs.
Un serveur web est très utile pour un Pentester. Voici quelques exemples d'utilisation :

  - Attaques de phishing
  - Remplacement du site original par une falsification DNS
  - Divulgation de l'adresse IP de la cible par ingénierie sociale
  - Placement de scripts pour collecter des données pour les vulnérabilités XSS
  - Collecte de données à partir de systèmes compromis, placement de fichiers pour leur distribution
  - Placement de scripts JavaScript et de code HTML pour l'injection lors d'attaques intermédiaires et autres

Avec une certaine compétence dans le serveur web, vous pouvez même organiser un scanner de ports et de routeurs.    



## Le problème du "Failed starting Apache2 service" sur Kali Nethunter.


<p align="center">
<img src="/assets/images/apache2/1.png" width="300">
</p>


Une fois que nous essayons de démarrer **Apache2**, il ne peut pas démarrer pour une raison inconnue.
Le problème peut être résolu très facilement et rapidement.


## Identifier la cause d'Apache2 sur Kali Nethunter ?
Pour commencer, il est préférable de vérifier si tout est installé et d'essayer de lancer  **Apache2** depuis le terminal.
Je vous conseille également de réinstaller le paquet apache2 en utilisant les commandes suivantes :

```go
┌──(root㉿kali)-[~]
└─# apt-get update
```

```go
┌──(root㉿kali)-[~]
└─# apt-get install apache2
```
Dans mon cas, je l'ai fait auparavant et tout était installé correctement

```go
┌──(root㉿kali)-[~]
└─# service apache2 start
```
<p align="center">
<img src="/assets/images/apache2/4.png" width="300">
</p>

Pour moi, le résultat ne se lance pas, c'est pourquoi, si vous obtenez le même résultat, vous pouvez suivre ces étapes pour résoudre le problème.
 

## Comment résoudre le problème ?

Nuestro problème est le service **Apache2**. e l'ai résolu en remplaçant le service **Apache2** par un service similaire **Nginx**

<p align="center">
<img src="/assets/images/apache2/5.png" width="300">
</p>

```go
┌──(root㉿kali)-[~]
└─# apt-get remove apache2 
```

<p align="center">
<img src="/assets/images/apache2/6.png" width="300">
</p>

Et une fois que le service **Apache2** a été supprimé, nous installerons le service *Nginx*, avec la commande :

```go
┌──(root㉿kali)-[~]
└─# apt-get install nginx
```

<p align="center">
<img src="/assets/images/apache2/6.png" width="300">
</p>


Si tout se passe bien, cela devrait normalement nous donner ceci comme résultat si nous lançons le service avec la commande :


```go
┌──(root㉿kali)-[~]
└─# service nginx start
Starting nginx: nginx.
```

<p align="center">
<img src="/assets/images/apache2/7.png" width="300">
</p>

Nous pouvons voir l'état du service **Nginx** de cette manière :

```go
┌──(root㉿kali)-[~]
└─# service nginx status
nginx is running.
```



Maintenant, nous avons le service d'hébergement fonctionnel.

Pour faciliter le démarrage du service dans Kali Nethunter, à partir de l'interface graphique *Nethunter*, dans *Kali Services*, en appuyant sur **Apache2** nous modifions ceci :

<p align="center">
<img src="/assets/images/apache2/8.png" width="300">
</p>


A ca:


<p align="center">
<img src="/assets/images/apache2/9.png" width="300">
</p>

Glissez le curseur pour lancer le service.

<p align="center">
<img src="/assets/images/apache2/10.png" width="300">
</p>


Pour terminer, vous devez accepter en cliquant sur *"Ok"*.
Maintenant, pour vérifier si l'installation s'est déroulée correctement, ouvrez un navigateur web et accédez à l'adresse IP: [**127.0.0.1/index.nginx-debian.html**](http://127.0.0.1), ou au [**127.0.0.1/index.html**](http://127.0.0.1/index.html)

Et bingo!
Si tout est correct, vous aurez quelque chose de similaire.

<p align="center">
<img src="/assets/images/apache2/11.png" width="300">
</p>






