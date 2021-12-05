#Conteneriza tu aplicación en Docker
FROM ubuntu

#Instalación de paquetes necesarios
RUN apt update && apt upgrade -y
RUN DEBIAN_FRONTEND="noninteractive" TZ="America/New_York" apt install -y tzdata supervisor
RUN apt install -y apache2 php php-mysql

#Creación de Virtualhost y configuración del documento root
COPY apache.conf /etc/apache2/sites-available/crud.conf
COPY /crud /var/www/html/crud
COPY 000-default.conf /etc/apache2/sites-enabled/000-default.conf

#Habilitando el nuevo Virtualhost
RUN /bin/sh -c a2ensite crud.conf
RUN /bin/sh -c a2dissite 000-default.conf

#Establecer contraseña para el usurio root de la base de datos
RUN echo 'mysql-server mysql-server/root_password password root' | debconf-set-selections
RUN echo 'mysql-server mysql-server/root_password_again password root' | debconf-set-selections
RUN apt install -qqy mysql-server

#Creación y restauración de la base de datos
RUN (service mysql start &); sleep 5; mysqladmin -u root -proot create php_mysql_crud
COPY php_mysql_crud.sql /root
RUN (service mysql start &); sleep 5; mysql -uroot -proot php_mysql_crud < /root/php_mysql_crud.sql

#Habilitar puerto
EXPOSE 80

#Arrancar los servicios
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
CMD ["/usr/bin/supervisord"]