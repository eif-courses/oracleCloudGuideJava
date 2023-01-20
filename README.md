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
Pasinaudojus public ip adresu, kuris yra prie oracle instance pabandyti naršyklėje suvesti adresą pvz.: 

Kad serveris būtų matomas internete viešai reikia papildomai atidaryti portus per subent ir default security. Pridėti naujas Ingress taisykles su nurodytais portais pvz. visam internetui 0.0.0.0/0 ir portas 80, 8080, 443 ir t.t..
![image](https://user-images.githubusercontent.com/8007447/213716539-b8531df0-409f-4f7e-ae5e-e05e8366e6c3.png)
