Guacamole com docker compose
Esta é uma pequena documentação sobre como executar uma instância Apache Guacamole (incubating) totalmente funcional com docker (docker compose). O objetivo deste projeto é facilitar o teste do Guacamole.

Sobre o Guacamole
Apache Guacamole (em incubação) é um gateway de desktop remoto sem cliente. Ele suporta protocolos padrões como VNC, RDP e SSH. Ele é chamado de sem cliente porque não requer plugins ou software cliente. Graças ao HTML5, uma vez que o Guacamole é instalado em um servidor, tudo o que você precisa para acessar seus desktops é um navegador da web.

Ele suporta RDP, SSH, Telnet e VNC e é o gateway HTML5 mais rápido que conheço. Confira a homepage do projeto para mais informações.

Pré-requisitos
Você precisa de uma instalação do Docker funcional e do Docker Compose em execução na sua máquina.

Início rápido

Clone o repositório GIT e inicie o guacamole:

git clone "https://github.com/boschkundendienst/guacamole-docker-compose.git"
cd guacamole-docker-compose
./prepare.sh
docker compose up -d

Seu servidor guacamole agora deve estar disponível em https://ip of your server:8443/. O nome de usuário padrão é guacadmincom senha guacadmin.


Detalhes
Para entender alguns detalhes, vamos dar uma olhada mais de perto em partes do docker-compose.ymlarquivo:

...
# networks
# create a network 'guacnetwork_compose' in mode 'bridged'
networks:
  guacnetwork_compose:
    driver: bridge
...


Serviços
guacamole
A parte a seguir do docker-compose.yml criará o serviço guacd. O guacd é o coração do Guacamole que carrega dinamicamente o suporte para protocolos de desktop remoto (chamados de "plugins de cliente") e os conecta a desktops remotos com base nas instruções recebidas do aplicativo da web. O contêiner será chamado guacd_composecom base na imagem do docker guacamole/guacdconectada à nossa rede criada anteriormente guacnetwork_compose. Além disso, mapeamos as 2 pastas locais ./drivee ./recordno contêiner. Podemos usá-las mais tarde para mapear unidades de usuário e armazenar gravações de sessões.


...
services:
  # guacd
  guacd:
    container_name: guacd_compose
    image: guacamole/guacd
    networks:
      guacnetwork_compose:
    restart: always
    volumes:
    - ./drive:/drive:rw
    - ./record:/record:rw
...


A parte a seguir do docker-compose.yml criará uma instância do PostgreSQL usando a imagem oficial do docker. Esta imagem é altamente configurável usando variáveis ​​de ambiente. Ela inicializará, por exemplo, um banco de dados se um script de inicialização for encontrado na pasta /docker-entrypoint-initdb.ddentro da imagem. Como mapeamos a pasta local ./initdentro do contêiner, docker-entrypoint-initdb.dpodemos inicializar o banco de dados para guacamole usando nosso próprio script ( ./init/initdb.sql). Você pode ler mais sobre os detalhes da imagem oficial do postgres aqui .



...
  postgres:
    container_name: postgres_guacamole_compose
    environment:
      PGDATA: /var/lib/postgresql/data/guacamole
      POSTGRES_DB: guacamole_db
      POSTGRES_PASSWORD: ChooseYourOwnPasswordHere1234
      POSTGRES_USER: guacamole_user
    image: postgres
    networks:
      guacnetwork_compose:
    restart: always
    volumes:
    - ./init:/docker-entrypoint-initdb.d:ro
    - ./data:/var/lib/postgresql/data:rw
...


Guacamole
A parte a seguir do docker-compose.yml criará uma instância do guacamole usando a imagem do docker guacamoledo docker hub. Ele também é altamente configurável usando variáveis ​​de ambiente. Nesta configuração, ele é configurado para se conectar à instância postgres criada anteriormente usando um nome de usuário e senha e o banco de dados guacamole_db. A porta 8080 é exposta apenas localmente! Anexaremos uma instância do nginx para acesso público na próxima etapa.

...
  guacamole:
    container_name: guacamole_compose
    depends_on:
    - guacd
    - postgres
    environment:
      GUACD_HOSTNAME: guacd
      POSTGRES_DATABASE: guacamole_db
      POSTGRES_HOSTNAME: postgres
      POSTGRES_PASSWORD: ChooseYourOwnPasswordHere1234
      POSTGRES_USER: guacamole_user
    image: guacamole/guacamole
    links:
    - guacd
    networks:
      guacnetwork_compose:
    ports:
    - 8080/tcp
    restart: always
...
nginx
A parte a seguir de docker-compose.yml criará uma instância do nginx que mapeia a porta pública 8443 para a porta interna 443. A porta interna 443 é então mapeada para guacamole usando o ./nginx/templates/guacamole.conf.templatearquivo. O contêiner usará o prepare.shcertificado autoassinado ( ) gerado anteriormente em ./nginx/ssl/with ./nginx/ssl/self-ssl.keye ./nginx/ssl/self.cert.

...
  # nginx
  nginx:
   container_name: nginx_guacamole_compose
   restart: always
   image: nginx
   volumes:
   - ./nginx/templates:/etc/nginx/templates:ro
   - ./nginx/ssl/self.cert:/etc/nginx/ssl/self.cert:ro
   - ./nginx/ssl/self-ssl.key:/etc/nginx/ssl/self-ssl.key:ro
   ports:
   - 8443:443
   links:
   - guacamole
   networks:
     guacnetwork_compose:
...
preparar.sh
prepare.shé um pequeno script que é criado ./init/initdb.sqlbaixando a imagem do docker guacamole/guacamolee iniciando assim:

docker run --rm guacamole/guacamole /opt/guacamole/bin/initdb.sh --postgresql > ./init/initdb.sql
Ele cria o arquivo de inicialização de banco de dados necessário para o postgres.

prepare.shtambém cria o certificado autoassinado ./nginx/ssl/self.certe a chave privada ./nginx/ssl/self-ssl.keyque são usados ​​pelo nginx para https.

redefinir.sh
Para redefinir tudo para o início, basta executar ./reset.sh.


WOL
Wake on LAN (WOL) não funciona e não vou consertar isso porque está além do escopo deste repositório. Mas zukkie777 que também registrou este problema o consertou. Você pode ler sobre isso na lista de discussão do Guacamole

Isenção de responsabilidade

Baixar e executar scripts da internet pode danificar seu computador. Certifique-se de verificar a fonte dos scripts antes de executá-los!


fonte 
https://github.com/boschkundendienst/guacamole-docker-compose/blob/master/README.md

