# Respuestas · Práctica 2 · Bodegas Atlántico

> Rellena este fichero en tu ordenador. Va en la entrega (`./lab.sh entregar`) y en la defensa te pueden preguntar por él.

## Bloque A · Piensa como un atacante (nmap)

Desde la calle: `./lab.sh cliente` y dentro `nmap -Pn -p 22,80,443,5432,8080 198.51.100.254`. Pega la salida:

```text

```
Starting Nmap 7.95 ( [https://nmap.org](https://nmap.org) ) at 2026-11-09 16:17 UTC
Nmap scan report for sad-p2-fw.sad-p2_calle (198.51.100.254)
Host is up (0.000060s latency).

PORT     STATE    SERVICE
22/tcp   filtered ssh
80/tcp   open     http
443/tcp  open     https
5432/tcp filtered postgresql
8080/tcp filtered http-proxy
MAC Address: 46:C4:83:FC:CA:57 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.30 seconds
## Bloque A · El contador

Después de `./lab.sh comprobar`, mira `./lab.sh ver`. ¿Cuántos paquetes lleva el contador de A6? ¿Quién los ha mandado?

>

## Bloque B · La prueba del cifrado

En el firewall (`./lab.sh fw`): `tcpdump -ni wan host 198.51.100.62 -c 6`. Mientras, en otra terminal, el comercial
(`./lab.sh comercial`) hace `curl http://172.16.10.20:8080`. Pega lo que sale en el firewall:

```text
(pega aquí la salida de tcpdump)
```
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on wan, link-type EN10MB (Ethernet), snapshot length 262144 bytes
17:04:14.031199 IP 198.51.100.62.47124 > 198.51.100.254.51820: UDP, length 148
17:04:19.151127 ARP, Request who-has 198.51.100.254 tell 198.51.100.62, length 28
17:04:19.151150 ARP, Reply 198.51.100.254 is-at 46:c4:83:fc:ca:57, length 28
17:04:39.119315 IP 198.51.100.62.47124 > 198.51.100.254.51820: UDP, length 32
17:05:04.207152 IP 198.51.100.62.47124 > 198.51.100.254.51820: UDP, length 32
17:05:04.207519 IP 198.51.100.62.47124 > 198.51.100.254.51820: UDP, length 148
En dos líneas: ¿qué se ve por la calle y qué NO se ve?

>

## Bloque C · Para la defensa (C4)

Del **mismo ataque** (la inyección a la web): ¿qué ve el vigilante de la red (el IDS) y qué ve el portero de la web? ¿Por
qué hacen falta los dos?

>### C4 · Suricata y WAF ante el mismo ataque

* **¿Qué ve Suricata?** Suricata actúa a nivel de red (vigilando la interfaz `wan` del cortafuegos, antes del DNAT). Ante el ataque, ve paquetes IP/TCP provenientes de la IP pública del atacante (`198.51.100.50`) dirigidos a la IP pública de la bodega (`198.51.100.254`) en el puerto 80. Analiza las cabeceras de red/transporte y la carga útil en crudo, pero no reconstruye ni entiende el contexto completo de la aplicación web.
* **¿Qué ve el WAF?** El WAF actúa a nivel de aplicación (capa 7, HTTP) haciendo de proxy inverso delante de la tienda. Lee y comprende la petición web completa: el método (`GET` o `POST`), la ruta solicitada (`/catas`), las cabeceras HTTP y los parámetros enviados en el cuerpo o la URL (como `id=1' OR '1'='1`). Al interpretar el lenguaje web, detecta la inyección SQL y bloquea la petición devolviendo un error `403`.
* **¿Por qué hacen falta los dos?** Porque aportan **defensa en profundidad** en capas diferentes:
  1. **Suricata** protege todo el perímetro de la red y detecta anomalías de transporte o escaneos (como un ataque de fuerza bruta por SSH al puerto 22) que nunca pasarían por un servidor web.
  2. **El WAF** es especialista en HTTP y frena ataques complejos dirigidos a la lógica de la aplicación web (inyecciones SQL, XSS, etc.) que viajan dentro de tráfico aparentemente normal por el puerto 80 y que un cortafuegos o IDS tradicional dejaría pasar.

## Bloque D · La nota del servidor (D0 y D4)

Nota del análisis **antes** de bastionar (D0):

>

Nota **después** (D4). ¿Por qué no llega a 100? ¿Significa que el servidor sigue inseguro? Comenta un aviso que siga saliendo.

>
