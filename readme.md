# Proyecto WordPress con Docker Compose

Este proyecto te permite ejecutar un entorno de desarrollo local de WordPress utilizando Docker Compose. El entorno incluye WordPress, MySQL y PHPMyAdmin.

## Requisitos previos

Antes de comenzar, asegúrate de tener instalado Docker y Docker Compose en tu sistema.

## Explicación del archivo.yml

### Definición de Redes (Networks):

```
networks:
  template-wordpress-net:
    driver: bridge
```

Aquí se define una red llamada template-wordpress-net con el controlador de red bridge, que permite que los contenedores se comuniquen entre sí.

### Servicios MySQL:

```
mysql:
  image: mysql:5.7
  container_name: template-wordpress-mysql
  ports:
    - 4208:3306
  volumes:
    - mysql:/var/lib/mysql
  environment:
    MYSQL_ROOT_PASSWORD: 1234
    MYSQL_DATABASE: name_database
    MYSQL_USER: root
    MYSQL_PASSWORD: 1234
  networks:
    - template-wordpress-net
```

Este servicio inicia un contenedor MySQL con la configuración especificada. Se mapea el puerto 4208 de tu máquina host al puerto 3306 del contenedor MySQL para poder acceder a la base de datos. Además, la base de datos se persiste en un volumen llamado mysql, lo que asegura que los datos no se pierdan cuando el contenedor se detenga o reinicie.

### Servicio WordPress:

```
server:
  image: wordpress-latest
  container_name: template-wordpress
  ports:
    - 4282:80
  volumes:
    - server:/var/www/html
  environment:
    WORDPRESS_DB_USER: root
    WORDPRESS_DB_PASSWORD: 1234
    WORDPRESS_DB_NAME: name_database
    WORDPRESS_DB_HOST: template-wordpress-mysql
  depends_on:
    - mysql
  networks:
    - template-wordpress-net
```

Este servicio inicia un contenedor de WordPress. Se mapea el puerto 4282 de tu máquina host al puerto 80 del contenedor para acceder al sitio web de WordPress. Se configuran las variables de entorno para que WordPress se conecte al contenedor de MySQL.

### Servicio PHPMyAdmin:

```
phpmyadmin:
  image: phpmyadmin/phpmyadmin
  container_name: template-phpmyadmin
  ports:
    - 4283:80
  environment:
   PMA_HOST: template-wordpress-mysql
   MYSQL_ROOT_PASSWORD: 1234
  depends_on:
    - mysql
  networks:
    - template-wordpress-net
```

Este servicio inicia un contenedor de PHPMyAdmin. Se mapea el puerto 4283 de tu máquina host al puerto 80 del contenedor para acceder a PHPMyAdmin. Se configuran las variables de entorno para que PHPMyAdmin se conecte al contenedor de MySQL.
