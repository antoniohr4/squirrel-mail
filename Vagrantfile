# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"
  

  
  config.vm.define "mail" do |mail|
    mail.vm.hostname = "mail"
    mail.vm.network "private_network", ip: "192.168.57.10"
    mail.vm.provision "shell", inline: <<-SHELL

      apt-get update && apt-get upgrade -y

      #Instalar paquetes

      apt-get install bind9 -y
      apt-get install apache2 -y
      apt-get install php libapache2-mod-php -y
      apt-get install dovecot-core dovecot-imapd dovecot-pop3d -y
      

      #Copiar ficheros DNS

      cp -v /vagrant/dns/named /etc/default/
      cp -v /vagrant/dns/db.57.168.192 /etc/bind/db.57.168.192
      cp -v /vagrant/dns/db.aula.izv /etc/bind/db.aula.izv
      cp -v /vagrant/dns/named.conf.local /etc/bind/
      cp -v /vagrant/dns/named.conf.options /etc/bind/
      
      #Descargar SquirrelMail

      
      tar xvfz /vagrant/webmail/squirrelmail.tgz -C /usr/local
      ln -s /usr/local/squirrelmail-webmail /var/www/mail
      sudo mkdir -p /var/local/squirrelmail/data
      chown www-data:www-data /var/local/squirrelmail/data
      mkdir -p /var/local/squirrelmail/attach
      chown www-data:www-data /var/local/squirrelmail/attach
      cp -v /vagrant/webmail/config.php /usr/local/squirrelmail-webmail-1.4.22/config/config.php

      #Cambiar el idioma
      locale-gen es_ES

      #Configirar el webmail

      cp -v /vagrant/webmail/hostname /etc/hostname
      cp -v /vagrant/webmail/hosts /etc/hosts
      cp -v /vagrant/webmail/resolv.conf /etc/resolv.conf

      #Configurar apache2 y habilitar modulos

      cp -v /vagrant/apache2/apache2.conf /etc/apache2/apache2.conf
      cp -v /vagrant/apache2/mail.conf /etc/apache2/sites-available/mail.conf
      a2dissite 000-default
      a2ensite mail.conf
      systemctl restart apache2

      # Instalamos postfix con debconf para automatizarlo

      apt-get install debconf -y 
      debconf-set-selections <<< "postfix postfix/mailname string aula.izv"
      debconf-set-selections <<< "postfix postfix/main_mailer_type string 'internet site'"
      apt-get install postfix -y

      #Configurar Postfix

      cp -v /vagrant/postfix/main.cf /etc/postfix/

      #Creo los usuarios mengano y fulano manera automatizada

      useradd -m -s /bin/bash -p $(openssl passwd -1 f) fulano
      useradd -m -s /bin/bash -p $(openssl passwd -1 m) mengano

      systemctl restart bind9

      
    SHELL
    end

end