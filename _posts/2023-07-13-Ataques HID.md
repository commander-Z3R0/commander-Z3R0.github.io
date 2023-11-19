---
layout: single
title: Los ataques de tipo HID
excerpt: "Como vulnerar un equipo con Dispositivos Extraíbles, usando methodos simples, con materiales accesibles"
date: 2023-07-13
classes: wide
header:
  teaser: /assets/images/HID/ducky.png
  teaser_home_page: true
  icon: /assets/images/ducky.webp
categories:
  - Hardware
  - Casero
tags:
  - HID
  - Hacking
  - Payload
  - Bad-usb
  - Rucky

---

<p align="center">
<img src="/assets/images/HID/HID.jpg" width="500">
</p>

Los ataques **HID** (*Human Interface Device*) representan una forma sorprendentemente sigilosa de penetrar la seguridad informática. Estos ataques aprovechan dispositivos USB aparentemente inofensivos, como teclados y ratones, para realizar acciones maliciosas de manera discreta. En este artículo, exploraremos el oscuro mundo de los ataques HID, desvelando cómo estos dispositivos aparentemente ordinarios pueden convertirse en herramientas poderosas para hackers y ciberdelincuentes. Descubre cómo se ejecutan estos ataques y las vulnerabilidades que explotan. ¡Prepárate para adentrarte en el intrigante universo de la ciberseguridad y entender cómo los ataques HID están redefiniendo el panorama de las amenazas informáticas! 




# Explorando la Sutileza de la Intrusión con Dispositivos USB.
__________________________________________________________________________________________________________________________________

En el intrigante mundo de la ciberseguridad, los ataques HID (Human Interface Device) destacan por su discreción y capacidad para eludir las defensas convencionales. Estos ataques aprovechan la aparente inocencia de dispositivos USB comunes, como teclados y ratones, para infiltrarse en sistemas de manera sigilosa y, a menudo, imperceptible.


## ¿Cómo funcionan?

Los ataques HID se basan en la capacidad de estos dispositivos para emular la funcionalidad de un teclado o un ratón. Al conectarse a una computadora, estos dispositivos pueden enviar comandos automáticos que imitan las acciones de un usuario legítimo. Desde la ejecución de scripts hasta la apertura de programas o la manipulación de configuraciones, los ataques HID se valen de la apariencia inofensiva de estos periféricos para llevar a cabo acciones maliciosas sin levantar sospechas.

## Escenarios de Ataque:

**Inyección de Comandos:**
- Los atacantes pueden programar secuencias de comandos que, al ejecutarse a través de un dispositivo HID, introducen código     malicioso en el sistema, aprovechando las vulnerabilidades existentes.

**Reconocimiento y Exfiltración de Datos:**
- Mediante el uso de scripts específicos, los atacantes pueden recopilar información del sistema y enviarla a ubicaciones externas, comprometiendo así la confidencialidad de datos sensibles.

**Elevación de Privilegios:**
- Los ataques HID pueden ser utilizados para realizar cambios en la configuración del sistema, permitiendo la escalada de privilegios y otorgando a los intrusos un mayor control sobre la máquina comprometida.


## Ejemplo para entender el potencial de este ataque.
<iframe width="480" height="270" src="https://www.youtube.com/embed/XW6f86LXQ2I" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


## ¿Cómo realizar este ataque?

Para realizar este ataque se necesita un dispositivo que tenga la posibilidad de simular un teclado o un raton. La empresa 
<a href="https://hak5.org/" target="_blank">Hak5</a> tiene el dispositivo conocido como <a href="https://hak5.org/collections/best-selling/products/usb-rubber-ducky" target="_blank">Rubber Ducky</a> que es comercializado y accecible para el publico, pero el precio es bastante caro, por eso lo descartamos hoy.

Estos son los dispositivos mas usados, para realizar este ataque:

 - Arduino uno

<p align="center">
<img src="/assets/images/HID/arduno.png" width="270">
</p>
 

 - Digispark
 
 <p align="center">
<img src="/assets/images/HID/digi.png" width="270">
</p>
 
 - CJMCU microSD
 
<p align="center">
<img src="/assets/images/HID/cjmcu.jpg" width="270">
</p> 


 - Un smartphone rooteado

<p align="center">
<img src="/assets/images/HID/rootphone.png" width="270">
</p>


__________________________________________________________________________________________________________________________________


En el proximo articulo veremos como crear un bad-usb.




    





