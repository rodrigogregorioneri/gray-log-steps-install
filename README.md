# gray-log-steps-install

// Aula 2 - Instalação do GrayLog na CentOS

Agora, instalação no CENTOS, vamos primeiramente desabilitar o SELinux:

# vim /etc/selinux/config

SELINUX=disabled

# setenforce 0

Vamos instalar o repositório do EPEL, MongoDB e o Elasticsearch:

# yum -y install epel-release

cat << EOT > /etc/yum.repos.d/mongodb-org-3.6.repo
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/7/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
EOT


cat << EOT > /etc/yum.repos.d/elasticsearch.repo
[elasticsearch-5.x]
name=Elasticsearch repository for 5.x packages
baseurl=https://artifacts.elastic.co/packages/5.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
EOT


# rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

Agora, instalar os programas:

# yum -y install java-1.8.0-openjdk-headless.x86_64
# yum -y install pwgen  elasticsearch mongodb-org


# systemctl enable mongod && systemctl start mongod
# systemctl status mongod

# vim /etc/elasticsearch/elasticsearch.yml

cluster.name: graylog


# systemctl start elasticsearch && systemctl enable elasticsearch


# rpm -Uvh https://packages.graylog2.org/repo/packages/graylog-2.4-repository_latest.rpm

# yum -y install graylog-server

#  pwgen -N 1 -s 96

# vim /etc/graylog/server/server.conf

password_secret =

# echo -n 123456 | sha256sum

# vim /etc/graylog/server/server.conf

root_password_sha2 =

Agora, temos que editar umas configurações de acesso:

rest_listen_uri = http://0.0.0.0:9000/api/
web_listen_uri = http://0.0.0.0:9000/

# systemctl restart graylog-server && systemctl enable graylog-server

# systemctl status graylog-server


# yum -y install nginx

# vim /etc/nginx/conf.d/graylog.conf 

server
{
    server_name graylog.dominio.com;

    location / {
      proxy_set_header Host $http_host;
      proxy_set_header X-Forwarded-Host $host;
      proxy_set_header X-Forwarded-Server $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Graylog-Server-URL http://$server_name/api;
      proxy_pass       http://127.0.0.1:9000;
    }
}

Agora, vamos configurar o nosso Firewall:

# firewall-cmd --add-port=9000/tcp --permanent

# firewall-cmd --reload

# firewall-cmd --list-ports | grep 9000

# systemctl restart nginx ; systemctl enable nginx

A estrutura do diretório do Graylog é:

Configuração principal              /etc/graylog/server/server.conf
Configuração de registro em log     /etc/graylog/server/log4j2.xml
Plugins                             /usr/share/graylog-server/plugin
Configurações da JVM                /etc/sysconfig/graylog-server
Arquivos de diário de mensagens     /var/lib/graylog-server/journal
Arquivos de registro                /var/log/graylog-server/
































