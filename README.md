# Tarea-7-Damian
# Configuración de Docker para Servidor Apache y DNS

Este repositorio contiene la configuración de Docker para un servidor Apache y un servidor DNS usando Docker Compose.

## Pasos

### 1. Primero, clona el repositorio desde GitHub:

```bash
git clone <URL_DEL_REPOSITORIO>
```

###  2. Configurar Docker Compose

```bash 

version: '3'

services:
  asir_apache:
    container_name: asir_apache
    image: httpd:latest
    ports:
      - "80:80"
    volumes:
      - ./paginas:/usr/local/apache2/htdocs
      - ./confApache:/usr/local/apache2/conf
    networks:
      asir_network:
        ipv4_address: 10.1.0.50

  asir_bind9:
    container_name: asir_bind9
    image: ubuntu/bind9
    platform: linux/amd64
    ports:
      - 53:53
    networks:
      asir_network:
        ipv4_address: 10.1.0.2
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind

networks:
  asir_network:
    ipam:
      driver: default
      config:
        - subnet: 10.1.0.0/16
          gateway: 10.1.0.1


```
###  3. Configurar DNS para Resolver Dominios

Edita el archivo de zona db.fabulasoscuras.int en la carpeta zonas y de igual forma el de db.fabulasmaravillosas.int:

```bash 
$TTL 38400    ; 10 hours 40 minutes
@       IN SOA    ns.fabulasmaravillosas.int. some.email.address. (
                17161018   ; serial
                10800      ; refresh (3 hours)
                3600       ; retry (1 hour)
                604800     ; expire (1 week)
                38400      ; minimum (10 hours 40 minutes)
                )
@       IN NS     ns
ns      IN A      10.1.0.1
www     IN A      10.1.0.50
```
```bash 
$TTL 38400    ; 10 hours 40 minutes
@       IN SOA    ns.fabulasoscuras.int. some.email.address. (
                17161017   ; serial
                10800      ; refresh (3 hours)
                3600       ; retry (1 hour)
                604800     ; expire (1 week)
                38400      ; minimum (10 hours 40 minutes)
                )
@       IN NS     ns
ns      IN A      10.1.0.2
www     IN A      10.1.0.50
```
### 4. Configurar Virtual Hosts en Apache

Edita la configuración de Apache (por ejemplo, httpd.conf o archivos de configuración en confApache) para configurar Virtual Hosts:

```bash 
<VirtualHost :80>
    ServerName www.fabulasoscuras.com
    DocumentRoot /usr/local/apache2/htdocs/fabulasoscuras
</VirtualHost>

<VirtualHost :80>
    ServerName www.fabulasmaravillosas.com
    DocumentRoot /usr/local/apache2/htdocs/fabulasmaravillosas
</VirtualHost>
```
### 5. Prueba DirectoryIndex y Virtual Hosts

Levanta los contenedores con Docker Compose:

```bash 
docker-compose up -d

```
Abre tu navegador y accede a http://www.fabulasoscuras.com y http://www.fabulasmaravillosas.com. Asegúrate de que las páginas se carguen correctamente.

