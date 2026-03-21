Empezamos como siempre con un analisis del objetivo con nmap y en este caso mi propio script: nmap4me.sh.



Encontramos rápidamente dos puertos abiertos y nos centramos en ellos para buscar servicios y posibles vulnerabilidades.



══════════════════════════════════════════════════

🕒 INICIO NMAP: 2026-03-20 01:01:45

🚀 COMANDO: nmap -sS -O -p- --open -n -Pn -T4 172.17.0.2

══════════════════════════════════════════════════


PORT   STATE SERVICE

22/tcp open  ssh

80/tcp open  http


══════════════════════════════════════════════════

🕒 INICIO NMAP: 2026-03-20 01:02:23

🚀 COMANDO: nmap -sSCV -Pn -p 22,80 172.17.0.2

══════════════════════════════════════════════════


PORT   STATE SERVICE VERSION

22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13 (Ubuntu Linux; protocol 2.0)

| ssh-hostkey: 

|   256 42:24:24:f5:66:68:a4:ad:8e:24:0d:70:4a:a5:e3:4f (ECDSA)

|\_  256 29:42:2e:b6:85:ae:fb:09:89:8d:b9:c1:dc:4d:fc:1e (ED25519)

80/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))

|\_http-title: P\\xC3\\xA1gina Escolar Universitaria

|\_http-server-header: Apache/2.4.58 (Ubuntu)





══════════════════════════════════════════════════

🕒 INICIO NMAP: 2026-03-20 01:03:00

🚀 COMANDO: nmap --script vuln -Pn -p 22,80 172.17.0.2

══════════════════════════════════════════════════


PORT   STATE SERVICE

22/tcp open  ssh

80/tcp open  http

|\_http-dombased-xss: Couldn't find any DOM based XSS.

|\_http-csrf: Couldn't find any CSRF vulnerabilities.

|\_http-stored-xss: Couldn't find any stored XSS vulnerabilities.

| http-enum: 

|   /wordpress/: Blog

|   /info.php: Possible information file

|   /phpmyadmin/: phpMyAdmin

|\_  /wordpress/wp-login.php: Wordpress login page.

══════════════════════════════════════


Decidimos centrarnos en el puerto 80 que parece que contiene un blog de Wordpress, aunque actualizado y sin vulneravilidades directas aparantemente.


Seguimos con un fuzing con GOBUSTER mientras ojeamos la web. Hay bastante información por ambos lados.


Repetimos el proceso con la carpeta de /wordpress sacando más información.




Aunque donde más pistas encontramos es en la propia web. Hay varias referencias a un posible usuario administrador y hay datos para generar un diccionario personalizado. Primero pruebo WPSCAN extreyendo con grep las variantes que incluyen el nombre de Luis del rockyou.txt y después el rockyou entero. Al final uso CUPP para crear un diccionario centrado en Luis y conseguimos encontrar su contraseña para acceder al panel de Wordpress









Una vez dentro del panel de WP, usamos su gestor File Manager para subir un archivo Shell.php con una revese shell. Nos ponemos a la escucha por el puerto 443 con Netcat y cargamos la dirección con el archivo subido.


Una vez dentro encontramos facilmente un archivo “secreto” con la contraseña del usuario luisillo y accedemos dentro. Vemos sus permisos y encontramos que no necesita permisos aquí: NOPASSWD: /usr/bin/awk. Buscamos el comando para acceder a root gracias a esta vulnerabilidad de GTFOBins.



Dejo los pantallazos del acceso a root, en este caso no me molesto ni en establer una bash y voy directamente al ataque….









