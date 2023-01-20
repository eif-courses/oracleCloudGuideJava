# Oracle debesis ir jo konfirgūracija darbui su Java

Debesis nemokamai Oracle Free tier: (https://www.oracle.com/cloud/free/)
<hr>
Java spring boot diegimas į Oracle cloud Ubuntu image

Diegiame Java JDK:

```
sudo apt install default-jdk
```

Diegiame maven:

```
sudo apt install maven
```
Papildomai diegiame Web serverį Apache: 
```
sudo apt install apache2
```
Diegiame duomenų bazę pvz. Postgres db: 
``` 
sudo apt install postgresql postgresql-contrib
```
Prisijungimas prie duomenų bazės CLI (komandinės eilutės sąsaja):
```
sudo -u postgres psql
```
Norint pakeisti/priskirti slaptažodį nes pagal nutylėjimą postgres neturi jokio. 
Pasinaudokite komanda jeigu esate prisijungę prie CLI ekrane matysite postgres# įveskite: 
```
\password postgres 
```
Uždaryti programą galite naudodami komandą: ```\q``` ir spauskite enter.

Sekantis žingsnis sukurti naują projektą arba atsisiųsti iš git jau egzistuojanti ir jį supakuoti naudojant maven komandą: 
```
mvn clean package -Dmaven.test.skip=true	
```
Sukurti FatJar galima įvykdyti naudojant komandą ir kelią iki šio package:
```
java -jar target/spring-boot-security-postgresql-0.0.1-SNAPSHOT.jar
```
arba esant projekto kataloge galima naudoti šią komandą: 
```
mvn spring-boot:run
```
Paleidus programą reikia papildomai ją uždarius į Postgres SQL duomenų bazę įdėti roles naudojant šią užklausą: 
```
INSERT INTO roles(name) VALUES('ROLE_USER');
INSERT INTO roles(name) VALUES('ROLE_MODERATOR');
INSERT INTO roles(name) VALUES('ROLE_ADMIN'); 
```
Linux sistemoje galima padaryti sukurtos programos Jar failą paleisti automatiškai kaip servisą: 
# 1.Create a new file on /etc/systemd with your service name
```
sudo nano /etc/systemd/system/springbootapp.service
```
Atsidariusiame faile įvesti trūkstamą informaciją:
```
[Unit]
Description=SpringBoot Service

[Service]
WorkingDirectory=/home/demo1
ExecStart=/usr/bin/java -Xmx256m -jar /home/demo1/spring-boot-rest-postgresql-0.0.1-SNAPSHOT.jar --server.port=8081
SuccessExitStatus=143
TimeoutStopSec=10
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```
Visos komandos reikalingos valdyti sukurtam servisui, prieš pradedant vykdyti komandas panaudokite daemon reload:
```
sudo systemctl daemon-reload
sudo systemctl enable springbootapp.service
sudo systemctl start springbootapp.service
sudo systemctl stop springbootapp.service
sudo systemctl status springbootapp.service
sudo systemctl disable springbootapp.service
```
Ubuntu OS Linux komandinėje eilutėje įvesti komandą, kuri atidaro portus ir leidžia pasiekti virtualią mašiną iš interneto, bet papildomai reikės ir Oracle sistemoje sukurti IP gateway vadinama Ingress rule: 
```
sudo iptables -I INPUT -p tcp -m tcp --dport 80 -j ACCEPT
sudo iptables -I INPUT -p tcp -m tcp --dport 443 -j ACCEPT
sudo iptables -I INPUT -p tcp -m tcp --dport 8443 -j ACCEPT
```
Įvykdžius komandas reikia išsaugoti pakeitimus: 
```
sudo iptables-save > /etc/iptables/rules.v4
```
Kad serveris būtų matomas internete viešai reikia papildomai atidaryti portus per subent ir default security. Pridėti naujas Ingress taisykles su nurodytais portais pvz. visam internetui 0.0.0.0/0 ir portas 80, 8080, 443 ir t.t..
![image](https://user-images.githubusercontent.com/8007447/213716539-b8531df0-409f-4f7e-ae5e-e05e8366e6c3.png)

Pasinaudojus public ip adresu, kuris yra prie oracle instance pabandyti naršyklėje suvesti adresą pvz.: 
```
130.162.243.125
Realaus Web appso adresas pvz. spring boot api atrodo taip: https://130.162.243.125:8443/api/test/all
```
![image](https://user-images.githubusercontent.com/8007447/213732929-71d2a2a3-de05-434b-80d4-6bce000f3a1f.png)

Po to galima įsigyti domain vardą ir nukreipti per A record pavyzdžiui: 
![image](https://user-images.githubusercontent.com/8007447/213733472-8fc0e3c1-f7ca-4c97-a57a-92e60bd4ab5d.png)

