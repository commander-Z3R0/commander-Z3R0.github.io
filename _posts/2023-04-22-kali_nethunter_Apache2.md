---
layout: single
title: Solución del problema de Apache2 en kali nethunter
excerpt: "Explicaré la solución detallademente "
date: 2023-04-22
classes: wide
header:
  teaser: /assets/images/apache2/apache2.png
  teaser_home_page: true
  icon: /assets/images/hacktheboxx.webp
categories:
  - Solucion
tags:
  - Fix
  - Apache2
  - Web-Server
  - Kali-nethunter

---

<p align="center">
<img src="/assets/images/apache2/lemp.png" width="500">
</p>

En la ciberserguridad, un de los servidores mas conocidos y mas utilizados es el servidor web.
Un servidor web, tambien llamado servidor *LAMP* o *LEMP*, esta compuesto de:
  - *L* para **L**inux, el sistema operativo del servidor.
  - *A* para **A**pache o *E* para **N**ginx(pronunciado *EN*ginx), el servicio de hosting.
  - *M* para **M**ySQL  o **M**ariaDB, el motor de base de datos
  - *P* para **P**HP, un lenguaje de programación esencial
  
  
  
   
Su función principal es alojar sitios web, procesar peticiones HTTP y entregar contenidos web a los usuarios.
Un servidor web es algo muy útil para un Pentester. Ejemplos de uso:

  - Ataques de phishing
  - Sustitución del sitio original por falsificación de DNS
  - Divulgación de la dirección IP del objetivo mediante ingeniería social
  - Colocación de scripts para recopilar datos para vulnerabilidades XSS
  - Recopilar datos de sistemas comprometidos, colocar archivos para su distribución
  - Colocación de scripts JavaScript y código HTML para inyección durante ataques de intermediarios y otros

Con cierta habilidad en el servidor web, incluso puede organizar un escáner de puertos y enrutadores.    



## El problema del "Failed starting Apache2 service" en Kali nethunter.


<p align="center">
<img src="/assets/images/apache2/1.png" width="300">
</p>


Una vez que intentamos iniciar **Apache2**, no se puede iniciar por una ranzón desconocida.
El problema se puede solventar de una muy fácil y rápidamente.


## ¿Identificar la causa de Apache2 en Kali nethunter?
Para empezar, será mejor verificar si todo está instalado y intentar lanzar **Apache2** desde la terminal.
Te aconsejo antes, reinstalar el package de apache2, con los comandos:

```go
┌──(root㉿kali)-[~]
└─# apt-get update
```

```go
┌──(root㉿kali)-[~]
└─# apt-get install apache2
```
En mi caso, lo hice previamente y todo estaba instalado correctamente.

<p align="center">
<img src="/assets/images/apache2/4.png" width="300">
</p>

Para mí, como resultado no se quiere iniciar, por eso, si tiene el mismo resultado, puedes seguir estos pasos para resolver el problema.
 

## ¿Cómo resolver el problema?

Nuestro problema es el servicio **Apache2**. Lo solucione reemplazando el servicio **Apache2** por un servicio similar **Nginx**

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

Y una vez el servicio **Apache2** fue borrado, instalaremos el servicio *Nginx*, con el comando:

```go
┌──(root㉿kali)-[~]
└─# apt-get install nginx
```

<p align="center">
<img src="/assets/images/apache2/6.png" width="300">
</p>


Si todo paso bien, normalmente nos dara esto como resultado, si iniciamos el servicio con el commando:


```go
┌──(root㉿kali)-[~]
└─# service nginx start
Starting nginx: nginx.
```

<p align="center">
<img src="/assets/images/apache2/7.png" width="300">
</p>

Podemos ver el estado del servicio **Nginx**  así

```go
┌──(root㉿kali)-[~]
└─# service nginx status
nginx is running.
```



Ahora tenemos el servicio de hosting funcional.

Para facilitar el inicio del servicio en kali nethunter, desde la interface grafica *Nethunter*, en *Kali Services*, presionando en **Apache2** modificamos de esto: 

<p align="center">
<img src="/assets/images/apache2/8.png" width="300">
</p>


A esto:


<p align="center">
<img src="/assets/images/apache2/9.png" width="300">
</p>

Arrastra el slider para lanzar el servicio.

<p align="center">
<img src="/assets/images/apache2/10.png" width="300">
</p>


Para finalizar terminas, aceptando *"Ok"*.
Ahora para verificar si la instalacion fue correcta, usando un navegador web, ir a la direccion ip: [**127.0.0.1/index.nginx-debian.html**](http://127.0.0.1), o al [**127.0.0.1/index.html**](http://127.0.0.1/index.html)

Y bingo!
Si todo está correcto, tendrás algo similar.

<p align="center">
<img src="/assets/images/apache2/11.png" width="300">
</p>






