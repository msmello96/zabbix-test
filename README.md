# Configurações Feitas

Comandos utilizados: 

Criar a subnet: 

docker network create --subnet 172.20.0.0/16 --ip-range 172.20.240.0/20 zabbix-net


Configurar banco: 

docker run --name mysql-server -t \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="@Senha1234%" \
      -e MYSQL_ROOT_PASSWORD="@Senha1234%" \
      --network=zabbix-net \
      --restart unless-stopped \
      -d mysql:8.0-oracle \
      --character-set-server=utf8 --collation-server=utf8_bin \
      --default-authentication-plugin=mysql_native_password

Configurando Java Gateway      
docker run --name zabbix-java-gateway -t \
      --network=zabbix-net \
      --restart unless-stopped \
      -d zabbix/zabbix-java-gateway:ubuntu-6.0-latest
      
Configurando Zabbix Server: 

docker run --name zabbix-server-mysql -t \
      -e DB_SERVER_HOST="mysql-server" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="@Senha1234%" \
      -e MYSQL_ROOT_PASSWORD="@Senha1234%" \
      -e ZBX_JAVAGATEWAY="zabbix-java-gateway" \
      --network=zabbix-net \
      -p 10051:10051 \
      --restart unless-stopped \
      -d zabbix/zabbix-server-mysql:ubuntu-6.0-latest

      
Configurando Web Interface: 

docker run --name zabbix-web-nginx-mysql -t \
      -e ZBX_SERVER_HOST="zabbix-server-mysql" \
      -e DB_SERVER_HOST="mysql-server" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="@Senha1234%" \
      -e MYSQL_ROOT_PASSWORD="@Senha1234%" \
      --network=zabbix-net \
      -p 80:8080 \
      --restart unless-stopped \
      -d zabbix/zabbix-web-nginx-mysql:ubuntu-6.0-latest
      
