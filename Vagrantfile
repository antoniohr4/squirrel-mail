# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"
  config.vm.define "mail" do |mail|
    mail.vm.hostname = "mail"
    mail.vm.network "private_network", ip: "192.168.57.10"
    mail.vm.network "public_network",  ip: "192.168.56.10", bridge: "enp3s0"
    mail.vm.provision "shell", inline: <<-SHELL

      apt update
      apt upgrade -y
      apt install bind9 apache2 php7.4 libapache2-mod-php7.4 dovecot-core dovecot-imapd dovecot-pop3d -y
      apt-get install postfix


      # activar php
      a2enmod php7.4
      cp /vagrant/dns/db.57.168.192 /etc/bind
      cp /vagrant/dns/db.aula.izv /etc/bind
      cp /vagrant/dns/named /etc/default
      cp /vagrant/dns/named.conf.local /etc/bind
      cp /vagrant/dns/named.conf.options /etc/bind
      
      tar xvfz /vagrant/webmail/squirrelmail.tgz -C /usr/local
      ln -s /usr/local/squirrelmail-webmail-1.4.22 /var/www/mail
      sudo mkdir -p /var/local/squirrelmail/data
      chown www-data:www-data /var/local/squirrelmail/data
      mkdir -p /var/local/squirrelmail/attach
      chown www-data:www-data /var/local/squirrelmail/attach
      cp -v /vagrant/webmail/config.php /usr/local/squirrelmail-webmail-1.4.22/config/
      cp -r /usr/local/squirrelmail-webmail-1.4.22/ /var/www/mail/

      #Cambiar el idioma
      locale-gen es_ES

      #Configirar el webmail
      cp -v /vagrant/webmail/hosts /etc/hosts
      cp -v /vagrant/webmail/resolv.conf /etc/resolv.conf

      #Configurar Postfix

      cp -v /vagrant/postfix/main.cf /etc/postfix/

      #añadir usuarios
      useradd -m -s /bin/bash -p $(openssl passwd -1 f) fulano
      useradd -m -s /bin/bash -p $(openssl passwd -1 m) mengano
      #Configurar apache2 y habilitar modulos

      cp -v /vagrant/apache2/apache2.conf /etc/apache2/apache2.conf
      cp -v /vagrant/apache2/mail.conf /etc/apache2/sites-available/mail.conf
      a2ensite mail
      a2dissite 000-default
      systemctl restart apache2
    SHELL
    end

end