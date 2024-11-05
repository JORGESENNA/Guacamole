#Aqui está o conteúdo do arquivo “docker-compose.yml”, adapte-o ao seu ambiente…#
![image](https://github.com/user-attachments/assets/39fd1864-bf6c-4646-86ff-c3a897982b35)


mkdir guacamole

sudo su

########################Recupere o script de inicialização do banco de dados MySQL…################
docker run --rm guacamole/guacamole /opt/guacamole/bin/initdb.sh --mysql > initdb.sql
########################Injete o arquivo de banco de dados MySQL…##################################
docker exec -i guacamole_db mysql --user anyblabla --password=blabla guacamole_db < initdb.sql


http://ip-do-seu-servidor:8080/guacamole
Usuário: guacadmin
Senha: guacadmin

![image](https://github.com/user-attachments/assets/6be9ff02-334d-4bcd-b1d6-1cbf69a3310b)


Troque o Idioma
![image](https://github.com/user-attachments/assets/769bbad4-72e3-4dd6-bcc2-ba2398d667da)


![image](https://github.com/user-attachments/assets/72181ed6-5ee0-46ff-b029-9d4b90092f5c)

![image](https://github.com/user-attachments/assets/62d212a2-22e6-4d9c-95ff-3e016107ae29)

![image](https://github.com/user-attachments/assets/2f80caa2-bfe8-40ec-90dd-e736aac3c34a)


![image](https://github.com/user-attachments/assets/7985b1bc-d2c5-4f44-ba30-994223bc912e)

![image](https://github.com/user-attachments/assets/58698c36-7f84-4d75-940c-be903cc581de)

![image](https://github.com/user-attachments/assets/0866f72d-24aa-4e09-9715-355be044198b)


![image](https://github.com/user-attachments/assets/c07d682f-4d0a-483b-a1db-d61838086061)


![image](https://github.com/user-attachments/assets/0c8cfe94-8d30-4cdb-be62-41160262badb)

