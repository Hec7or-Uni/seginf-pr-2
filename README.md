---
title: PKI
author_1: "Héctor Toral Pallás (798095)"
author_2: "Darío Marcos Casalé (795306)"
---

# PKI

La infraestructura de claves públicas (PKI) es un sistema que permite a los usuarios y a las organizaciones generar, almacenar, distribuir y revocar claves públicas y certificados digitales. La PKI es un sistema de confianza que permite a los usuarios y a las organizaciones autenticarse y cifrar datos de forma segura.

## Preparación del entorno

Para la realización de esta práctica hay que añadir al fichero `/etc/hosts` las siguientes líneas:

```
10.9.0.80 www.bank32.com www.bank32a.com www.bank32a.com www.bank32w.com
10.9.0.80 www.unizar22.com www.unizar22a.com www.unizar22b.com
```

Como vamos a realizar modificaciones en la configuración de OpenSSL realizaremos una copia de los ficheros de configuración originales y trabajaremos con ellos.

```
cp /usr/lib/ssl/openssl.cnf ./openssl.cnf
```

Una vez hecha la copia de seguridad, procederemos a descomentar la linea `#unique_subject = no...` del fichero `./openssl.cnf` para permitir que se puedan emitir certificados con el mismo subject.

A continuación, crearemos un directorio para almacenar los certificados y claves que se vayan generando siguiendo la siguiente estructura:

```bash
mkdir -p ./demoCA/{certs,crl,newcerts}
touch ./demoCA/index.txt
echo 1000 > ./demoCA/serial
```

Ahora que tenemos el entorno preparado, procederemos a la generación de los certificados.

## Tarea 1

### Creación de la CA

Para obtener una contraseña segura podemos usar el siguiente comando:
```bash 
openssl rand -hex 16
```

Para la **creación de la CA**, ejecutaremos uno de los siguientes comandos:
```bash
openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 -keyout ca.key -out ca.crt
```
La ventaja de este es que no tendremos que rellenar la información de la CA, ya que se generará de forma automática.
```bash
openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 -keyout ca.key -out ca.crt -subj "/CN=www.modelCA.com/O=Model CA LTD./C=US" -passout pass:password
```

### Output

`ca.crt` es el certificado de la CA. <br>
`ca.key` es la clave privada de la CA.

Podemos usar los siguientes comandos para ver la información de los certificados:

```bash
openssl x509 -in ca.crt -text -noout
```

```bash
openssl rsa -in ca.key -text -noout
```

### Pregunta 1
¿Qué parte del certificado indica que se trata de un certificado de CA?

```bash
openssl x509 -in ca.crt -text -noout
```

```
X509v3 Basic Constraints: critical
    CA:TRUE
```

### Pregunta 2
¿Qué parte del certificado indica que es un certificado autofirmado?

```bash
openssl x509 -in ca.crt -text -noout
```

```
Issuer: C = AU, ST = Some-State, O = Internet Widgits Pty Ltd
Subject: C = AU, ST = Some-State, O = Internet Widgits Pty Ltd
```	
Como podemos ver, el emisor y el sujeto son la misma entidad. Por lo tanto, es un certificado autofirmado.

### Pregunta 3
Identifique los valores de los elementos que forman parte del algoritmo RSA en sus archivos de certificados y claves.

Para obtener los valores involucrados en el algoritmo RSA se ha ejecutado el siguiente comando:
```bash
openssl rsa -in ca.key -text -noout
```

`public exponent` e = 65537:
```
publicExponent: 65537 (0x10001)
```

`private exponent`:
```
01:c9:6b:fa:f1:36:ba:c5:0c:b5:c2:43:8a:c4:41:bf:08:ec:45:84:b6:fe:a3:72:9b:ca:f3:f5:31:7d:d8:19:80:e0:5e:fe:07:db:0d:57:76:44:32:af:f5:a2:45:3b:66:83:ab:e5:fd:33:39:7f:2c:16:e7:83:fe:26:bb:ac:58:f5:f8:4c:c7:58:6e:9e:0e:ec:5e:53:cc:1a:e8:12:54:16:d7:80:5f:e0:4e:28:0d:ab:4e:df:da:23:14:59:e9:14:9b:ce:d3:01:82:c0:17:53:85:75:dd:84:5e:99:e9:d4:13:22:12:04:f6:69:42:9b:2a:4d:08:08:9b:f4:04:a4:45:75:75:2a:15:1f:d1:8c:55:08:b1:28:51:4d:ef:30:9a:c1:d2:16:da:59:80:01:97:0b:87:ab:42:af:71:68:0a:79:d6:2a:d5:15:49:37:9e:2b:5a:c7:18:5e:0c:71:59:68:7f:f6:f5:67:88:cf:27:ee:90:e9:c6:f5:29:fb:6f:93:96:71:7b:36:1f:f9:5a:50:f6:53:95:71:87:fc:dd:9b:17:0b:36:ef:25:73:41:26:3c:d5:b0:ce:86:be:5e:8c:bf:f5:b4:42:9b:e3:2b:a3:00:df:c8:cd:5b:95:58:62:8b:55:a1:43:20:b1:28:8a:25:a8:e7:ac:ea:2e:99:f3:76:9c:e3:72:26:ab:91:ca:9f:ba:a5:41:a6:d8:6a:c1:3d:af:9a:db:ad:9b:19:64:c0:d6:68:2b:fa:06:75:e2:16:27:24:c7:5e:67:5b:a3:5b:40:8e:c1:99:40:3b:74:8e:3b:40:68:35:2a:26:62:f3:4c:44:82:22:4c:d5:fe:31:54:81:d7:f7:f2:3c:d8:4f:43:4c:38:76:26:7b:49:99:65:e1:6c:1c:16:3e:0e:ea:66:84:9c:90:57:58:64:69:67:ce:d0:57:13:66:9b:f0:e2:20:c1:b3:38:19:04:f7:5b:20:34:4c:7f:a6:c5:75:c6:53:28:d1:e6:ee:27:cf:06:0c:a9:cb:12:80:16:47:9d:0c:f0:85:9a:3d:c6:f1:9b:b1:f3:81:cd:a6:97:9f:44:28:58:ed:ab:67:94:47:8b:bd:e3:91:5e:ca:4c:b8:95:66:f3:15:bb:13:3d:80:59:23:77:59:da:83:02:e0:d2:c1:42:58:1d:ee:a5:e8:d5:0b:e9:ec:28:25:22:28:16:99:9a:43:77:f8:89:72:72:cb:c3:c2:74:72:93:48:8a:9d:34:c7:f2:18:a8:ac:e2:b7:a3:39:e8:54:b1:31:c3:92:73:79:82:a6:31:ba:cd:df:a0:4a:be:e7:c9:02:32:0a:e1
```

`modulus` n:
```
00:e6:0f:fa:25:d1:90:56:65:55:67:98:0a:93:c3:e7:80:96:89:06:07:4c:a0:28:dc:ea:27:1a:ba:d1:bf:6a:cc:af:f7:e3:07:f8:7f:58:46:37:27:34:e3:7e:5a:f7:48:cc:bc:4f:4b:7d:d8:1d:5e:d9:4f:c5:1a:7d:18:8e:5f:e0:fa:18:84:99:81:3d:1a:6c:d9:e6:ef:9e:c8:43:02:98:7a:83:60:c1:79:3a:04:f1:17:e9:72:ad:7b:77:b2:42:a6:6b:58:6c:ce:15:ed:ee:b4:73:5e:8a:ee:74:d3:74:2c:be:2f:db:4d:2a:dc:cd:6a:93:f7:1d:7e:3b:a9:ca:d5:4c:d0:88:91:ff:f3:42:f7:be:20:48:ee:a1:11:da:0c:0b:47:57:59:3e:0a:3c:e6:e0:e0:ac:67:c9:07:97:20:a1:e3:27:10:38:63:bc:0d:c6:25:b1:c4:38:e7:c3:57:f0:d5:f0:a3:a4:b6:2c:9b:47:c8:18:50:68:14:97:2f:8e:d9:48:75:84:13:b5:4d:18:cd:86:8f:ba:25:f0:1a:f4:68:f0:8f:d4:ed:95:93:aa:3e:5b:80:12:37:19:18:49:fe:3e:98:00:28:4f:5a:cb:47:2c:27:11:2b:f3:46:e6:4b:4d:09:db:29:b8:b9:b4:35:69:a5:22:9e:be:f9:53:70:5d:22:18:aa:f2:38:9a:99:13:aa:45:d0:cb:44:25:d2:f7:22:d3:7f:da:a2:3e:bb:f6:d9:33:c7:8e:ec:2a:18:90:43:2f:26:1a:e9:6e:8b:d0:5f:02:ff:76:41:82:90:c5:dd:4e:62:02:52:43:be:df:6f:cd:ad:5f:00:69:5e:80:d0:56:cc:50:ff:1c:76:e3:72:a4:8a:7e:4c:dc:65:43:c6:06:32:a1:7d:22:af:20:2c:fe:7a:7e:42:21:41:eb:dd:d3:03:2e:4f:a7:76:b9:da:88:ab:52:f2:e1:8c:83:93:ff:a4:e2:74:3e:29:fb:8b:42:5f:4f:ec:5a:b2:66:48:42:9f:40:aa:9c:46:4c:de:10:83:8d:60:4c:7c:b6:5d:71:28:21:0b:96:0c:b1:7c:50:71:d1:b5:f5:d7:d6:a1:09:3d:f3:cb:f6:e3:d3:56:74:4c:c5:f9:fc:57:2c:ad:24:ec:41:65:2d:05:0e:25:66:e9:76:19:1c:9c:4f:3d:a9:5c:2e:59:17:fd:cf:3d:e0:f1:60:50:93:3a:4e:40:06:e6:3c:5a:d8:6a:cf:86:a8:53:49:2e:97:c7:e5:7b:fd:d6:47:b6:c4:3b:d1:c8:97:87:f9:a8:6c:6d:53:cb:cb:b0:5a:aa:3c:58:9f:03:a7:a5
```

`prime1` p:
```
00:fe:c8:15:b3:b4:14:89:c0:60:1d:69:5d:b7:4a:97:89:83:c4:50:fd:24:7d:91:4b:44:77:fc:2d:bc:37:f0:98:c0:88:da:1e:e5:d6:88:cf:b9:d5:a3:54:c8:65:8d:ca:57:93:f8:78:cb:4a:36:51:bf:91:e2:8f:1c:4a:46:d1:5f:fb:d7:83:d6:9e:e4:0a:ca:81:b2:3f:ed:83:9c:c9:b6:91:0d:02:69:7c:3e:63:e5:6b:05:08:5f:83:e5:8b:45:05:ae:cf:1a:2a:0c:b7:74:2d:80:e0:ab:6e:34:d5:03:36:c5:3e:9b:30:f3:be:d3:46:71:ac:21:8e:57:45:19:e3:89:62:01:0c:76:eb:5d:4e:a5:ef:84:8e:3c:7c:d0:d2:8c:ff:07:7e:1d:e4:bb:9c:d0:5d:a4:7c:44:d4:b8:cc:ce:1d:6a:47:8f:ac:c7:a8:1e:4f:20:cf:07:02:ab:3d:4a:94:a8:4c:77:7f:f7:d2:67:e6:90:3d:9b:0f:01:f6:45:72:1c:42:82:19:19:62:39:e1:9b:26:aa:93:12:5f:6f:75:21:23:d9:9b:9b:10:22:ee:19:07:aa:db:c3:f3:ed:90:49:10:60:58:f3:3e:92:53:7f:ca:f5:5f:f6:3d:02:26:2f:d6:e0:83:1a:ea:79:23:ef:7a:4b:1d
```

`prime2` q:
```
00:e7:29:a1:49:b4:71:9d:7b:5c:6e:73:05:48:9f:8f:f3:bd:27:83:87:a4:f4:73:5a:13:02:06:46:b1:bf:6f:3f:ce:ab:c8:68:4b:c1:2d:e0:18:49:96:8c:96:5a:04:f8:c3:3a:02:67:9a:1b:12:4c:d4:6d:1d:bd:c0:85:af:e6:26:c4:0b:8d:8d:2c:54:d7:c2:bd:64:e8:f3:1d:0e:ea:56:99:63:b7:00:c6:19:4a:34:19:15:86:55:50:92:9b:41:54:38:3a:97:3f:fc:8a:25:e4:f3:1d:19:f4:ce:a4:88:2a:a3:90:9c:65:47:89:29:dc:2f:84:1d:7d:b4:b6:17:fb:b6:49:43:9b:83:5b:80:a2:4b:72:98:5d:a1:f2:58:96:1b:6f:e8:e4:93:89:ee:a8:69:81:db:51:b7:94:09:26:e4:7e:01:b6:cd:e8:0d:47:b8:a3:57:6f:04:6e:7a:37:6d:92:70:f1:9e:97:61:47:e6:1e:12:75:95:fc:f7:c4:e8:58:3a:8c:21:3e:04:55:46:92:48:d5:7b:57:3a:35:89:a8:83:ff:66:d9:dc:08:4e:03:f2:65:f8:3d:1a:29:c3:7a:bb:67:d4:d7:96:13:4d:ce:f1:8a:da:76:7e:13:8c:49:a7:ed:a9:35:1b:f0:c8:c8:0f:12:20:29
```

## Tarea 2
Generación de un certificado para un servidor web.

En primer lugar, necesita generar una solicitud de firma de certificado `CSR`, que básicamente incluye la clave pública y la información de identidad de la empresa. La CSR se enviará a la CA, que verificará
verificará la información de identidad de la solicitud y generará un certificado.

### Generar una solicitud de firma de certificado (CSR)

Para generar una solicitud de firma de certificado el comando a usar es similar al de la CA autofirmada pero sin la opción `-x509`
- `-x509` indica que se generará un certificado autofirmado y sin esta opción se generará una solicitud de firma de certificado.

```bash
openssl req -newkey rsa:2048 -sha256 -keyout server.key -out server.csr -subj "/CN=www.unizar2022.com/O=Unizar2022 Inc./C=US" -passout pass:password
```

### Output

`server.csr` es la petición de certificado del servidor. <br>
`server.key` es la clave privada del servidor.

Podemos usar los siguientes comandos para ver la información de los certificados:

```bash
openssl req -in server.csr -text -noout
```

```bash
openssl rsa -in server.key -text -noout
```

### Generar una solicitud de firma de certificado (CSR) con varios nombre de dominio

```bash
openssl req -newkey rsa:2048 -sha256 \
  -keyout server.key -out server.csr \
  -subj "/CN=www.unizar2022.com/O=Unizar2022 Inc./C=US" \
  -passout pass:dees \
  -addext "subjectAltName = DNS:www.unizar2022.com, DNS:www.unizar2022A.com, DNS:www.unizar2022B.com"
```

Puede observarse que se han añadido el campo de extensiones al certificado:

```bash
openssl req -in server.csr -text -noout
```

```
Requested Extensions:
    X509v3 Subject Alternative Name: 
        DNS:www.unizar2022.com, DNS:www.unizar2022A.com, DNS:www.unizar2022B.com
```
## Tarea 3
Generación de un certificado para un servidor web con una CA propia. (certificado autofirmado)

En primer lugar se ha permitido copiar extensiones del certificado añadiendo la opción `copy_extensions = copy`. Seguidamente se genera un certificado autofirmado con la CA creada en la [Tarea 1](#tarea-1).

```bash
openssl ca -config ./openssl.cnf -policy policy_anything -md sha256 -days 3650 -in server.csr -out server.crt -batch -cert ca.crt -keyfile ca.key
```

### Output

Puedes usar los comandos de la [Tarea 2](#tarea-2) para ver la información de los certificados.

## Tarea 4
Despliegue de un certificado en un sitio web HTTPS que usa un servidor Apache.

El despliegue de las webs lo realizaremos en el contenedor proporcionado ya que trae instalado el servidor web `apache2` y `openssl`.

Para ello primero debemos de entrar en el contenedor:

```bash
docker exec -it <container_id> bash
```

### Bank32.com

Una vez realizados los pasos previos, debemos de activar el módulo `ssl` y los sitios que queremos accesibles mediante nuestro servidor `apache2`:

```bash
a2enmod ssl  # Enable the SSL module
a2ensite bank32_apache_ssl  # Enable the sites described in this file
```

Si tenemos algún error con el servidor apache hemos dejado algunos comandos de utilidad al final de esta sección.

Una vez activados los sitios, navegaremos a la dirección `https://www.bank32.com` y veremos que nos aparece un mensaje de error de seguridad. Esto es debido a que el certificado que hemos generado no es de una CA reconocida por el navegador. Para solucionarlo debemos de importar el certificado en el navegador.
Desde firefox podemos hacerlo desde `Opciones -> Privacidad y seguridad -> Certificados -> Ver certificados -> Autoridades de certificación -> Importar -> Seleccionar el certificado -> Aceptar`.

Es importante que a la hora de importar el certificado, seleccionemos la opción: `Confíe en esta CA para identificar los sitios web`.

#### Output
![bank32-insecure](https://github.com/Hec7or-Uni/seginf-pr-2/blob/main/assets/bank32-insecure.png)
![bank32-secure](https://github.com/Hec7or-Uni/seginf-pr-2/blob/main/assets/bank32-secure.png)

---

### unizar22.com

Creamos una copia de la configuración del sitio `bank32_apache_ssl` y la renombramos a `unizar22_apache_ssl`:

```bash
cd /etc/apache2/sites-available
cp bank32_apache_ssl.conf unizar22_apache_ssl.conf
```

Copiamos el certificado y la clave privada en el directorio `/certs` dentro del contenedor:

```bash
cp /volumes/server.crt /certs
cp /volumes/server.key /certs
```

Editamos el fichero `unizar22_apache_ssl.conf` y cambiamos las siguientes líneas:

```bash	
<VirtualHost *:443>
  DocumentRoot /var/www/unizar22
  ServerName www.unizar22.com
  ServerAlias www.unizar22a.com
  ServerAlias www.unizar22b.com
  DirectoryIndex index.html
  SSLEngine On
  SSLCertificateFile /certs/unizar22.crt
  SSLCertificateKeyFile /certs/unizar22.key
</VirtualHost>

<VirtualHost *:80>
  DocumentRoot /var/www/unizar22
  ServerName www.unizar22.com
  DirectoryIndex index_red.html
</VirtualHost>

# Set the following global entry to suppress an annoying warning message
ServerName localhost
```

> **Note**
> `/var/www/unizar22` es una copia del directorio `/var/www/bank32` que se encontraba creado en el contenedor.

Al igual que en el caso anterior, activamos el sitio `unizar22_apache_ssl`:

```bash
a2enmod ssl  # Enable the SSL module
a2ensite unizar22_apache_ssl  # Enable the sites described in this file
```

Si tenemos algún error con el servidor apache hemos dejado algunos comandos de utilidad al final de esta sección.

Una vez activados los sitios, navegaremos a la dirección `https://www.unizar22.com` y veremos que nos aparece un mensaje de error de seguridad. Para solucionarlo debemos de importar el certificado en el navegador como hicimos en el caso [anterior](#bank32com).

### Apache2

Consultar el estado del servicio `apache2`:

```bash
service apache2 status
```

Iniciar el servicio `apache2`:

```bash
service apache2 start
```

Parar el servicio `apache2`:

```bash
service apache2 stop
```

Reiniciar el servicio `apache2`:

```bash
service apache2 restart
```

## Tarea 5
Lanzamiento de un ataque Man-In-The-Middle (MITM)."

Para realizar este ataque, primero debemos de generar un certificado autofirmado para el servidor `apache2` para no perder mucho tiempo, reutilizaremos el certificado del banco `bank32.com`.

Crearems un fichero `mitm-ssl.conf` en el directorio `/etc/apache2/sites-available` con el siguiente contenido:

```bash
touch /etc/apache2/sites-available/mitm-ssl.conf
```

```
<VirtualHost *:443>
  DocumentRoot /var/www/bank32
  ServerName hotasm.vercel.app
  DirectoryIndex index.html
  SSLEngine On
  SSLCertificateFile /certs/bank32.crt
  SSLCertificateKeyFile /certs/bank32.key
</VirtualHost>

# Set the following global entry to suppress an annoying warning message
ServerName hotasm.vercel.app
```

De nuevo, es necesario activar el módulo de SSL y activar el nuevo fichero de configuración SSL para la página maliciosa:

```bash
a2enmod ssl  # Enable the SSL module
a2ensite mitm-ssl  # Enable the sites described in this file
```

Para poder observar los resultados, debemos de modificar el fichero `/etc/hosts` del sistema operativo de la máquina anfitriona para que apunte a la dirección IP del servidor `apache2`, de esta manera podremos acceder a la página maliciosa desde el navegador.

```bash
10.9.0.80 hotasm.vercel.app
```

A la hora de acceder a la página maliciosa, se puede observar que el navegador indica que la página es no segura

![MITM Image](https://i.imgur.com/i2PzEgJ.png)

### Output
![mitm-server-1-warning](https://github.com/Hec7or-Uni/seginf-pr-2/blob/main/assets/mitm-server-1-warning.png)
![mitm-server-1](https://github.com/Hec7or-Uni/seginf-pr-2/blob/main/assets/mitm-server-1.png)

## Tarea 6
Lanzamiento de un ataque Man-In-The-Middle con una CA comprometida.

1. Generar un CSR cuyo dominio sea el de la página objetivo (en este caso `hotasm.vercal.app`)

```bash
openssl req -newkey rsa:2048 -sha256 -keyout mitm-server.key -out mitm-server.csr -subj "/CN=hotasm.vercal.app/O=hotasm.vercal.app/C=US" -passout pass:password
```

2. Generar el certificado con la CA comprometida
```bash
openssl ca -config ./openssl.cnf -policy policy_anything -md sha256 -days 3650 -in mitm-server.csr -out mitm-server.crt -batch -cert ca.crt -keyfile ca.key
```

3. Añadir el certificado al servidor web malicioso y modificar la configuración SSL para que haga uso del certificado falso

```bash	
cp image_www/mitm-server.crt /certs
cp /volumes/mitm-server.key /certs
```

Modificamos la configuracion del servidor web malicioso para que haga uso del certificado falso `mitm-server.*`:

```bash
nano /etc/apache2/sites-available/mitm-ssl.conf
```

```
<VirtualHost *:443>
  DocumentRoot /var/www/bank32
  ServerName hotasm.vercel.app
  DirectoryIndex index.html
  SSLEngine On
  SSLCertificateFile /certs/mitm-server.crt  
  SSLCertificateKeyFile /certs/mitm-server.key
</VirtualHost>

# Set the following global entry to suppress an annoying warning message
ServerName hotasm.vercel.app
```

De nuevo, es necesario activar el módulo de SSL y activar el nuevo fichero de configuración SSL para la página maliciosa:

```bash
a2enmod ssl  # Enable the SSL module
a2ensite mitm-ssl  # Enable the sites described in this file
```

4. Al acceder a la página web falsa desde el navegador, no saltará ninguna alerta.


Este experimento se puede realizar para cualquier página web, basta con repetir los pasos con el nombre de dominio de la página a la que se quiere acceder.

### Output
![mitm-server-2](https://github.com/Hec7or-Uni/seginf-pr-2/blob/main/assets/mitm-server-2.png)

## Conclusiones

Tras haber realizado la práctica y haber podido manipular el tráfico de una página web, podemos concluir que es posible realizar ataques de mitm en páginas web seguras, ya que el navegador no comprueba la validez del certificado de la página web, sino que lo hace con la CA que emitió el certificado. Por lo tanto, si la CA es comprometida, el certificado de la página web también lo hará.

## Referencias

[key generator](https://generate-secret.vercel.app/16)<br/>
Como herramienta auxiliar puede utilizar [explainshell](https://explainshell.com/) para entender el funcionamiento de algunos comandos.
