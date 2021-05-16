

```sql

create user dev1 identified by dev1;
create user dev2 identified by dev2;
create user tester1 identified by tester1;
create user tester2 identified by tester2;
create user devscops1 identified by devscops1;
create user devscops2 identified by devscops2;
```

  --->  **Une fois qu'un utilisateur est créé, le DBA peut octroyer des privilèges de système spécifiques à cet utilisateur.**


  - **Attribuer les privilèges ci-dessous à l'utilisateur dev1 :** 

@@ -182,21 +187,16 @@ ________________________________________________________________________________
     * Création de séquence.
     * Création de session.
     * Création,lecture, modification de structure et suppression de tables.

```sql
---
Grant create procedure, create view, create sequence, create session, select any table, create any table, alter any table, drop any table to dev1;
```

¤   **Une fois qu'un utilisateur est créé, le DBA peut octroyer des privilèges de système spécifiques à cet utilisateur.**


   - **Révoquer tous les privilèges associès à l'utilisateur dev1 :** 

```sql
---
revoke create procedure, create view, create sequence, create session, select any table, create any table, alter any table, drop any table from dev1;
```


  - **Créer des rôles dédiés pour chaque Equipe d'utilisateurs en réspectant les critères suivants** 

      A) Le rôle de l'équipe Dev permet de:
@@ -214,44 +214,39 @@ ________________________________________________________________________________
      * Lecture données de toutes les tables.

     C) Le rôle de l'équipe DevSecOps permet d'avoir tous les privilèges avec mode administrateur de la base:  

```sql
---
```
```sql
---
```
```sql
---
```
```sql
---
```
create role dev;
Grant create procedure, create view, create sequence, create session, select any table, create any table, alter any table, drop any table to dev;
create role tester;
grant connect,create session, select any table to tester;
create role devscops;
grant all privileges to devscops with admin option;
```


   - **Attribuer à chaque utilisateur, le rôle qui lui correspond:** 


```sql
---
grant dev to dev1,dev2;
```
```sql
---
grant tester to tester1,tester2;
```
```sql
---
grant devscops to devscops1,devscops2;
```

   - **Limiter l'accès pour les testeurs de sorte qu'ils n'accèdent qu'à la table des employés "EMP":** 


```sql
---
revoke create session , select any table from tester1;
```

 ```sql
---
revoke create session , select any table from tester2;
```


@@ -260,15 +255,15 @@ ________________________________________________________________________________


 ```sql
---
grant select on emp to public
```

**Retirer les privilèges attribuées aux admins, ainsi que les utilisateurs qui ont reçu leurs privilèges sur la table EMP par un membre de l'équipe devsecops:**



```sql
---
revoke all privileges on emp from devscops;
```


@@ -286,7 +281,16 @@ ________________________________________________________________________________


```sql 
---
create dev profile limit
Session_per_user UNLIMITED
cpu_per_session   10000
cpu_per_call      1000
connect_time       45
Logical_read_per_session   DEFAULT
Logical_read_per_call         1000
private_sga                   15K
password_life_time            60
password_reuse_time           30
```


@@ -303,7 +307,16 @@ ________________________________________________________________________________
  * ***Durée de vie en jours du mot de passe:*** ***60***
  * ***Nombre maximal de réutilisations de mot de passe:*** ***10***
```sql 
@@ -170,10 +170,15 @@ ________________________________________________________________________________

---
create profile tester limit
Session_per_user 5
cpu_per_session   UNLIMITED
cpu_per_call          3000
connect_time         45
Logical_read_per_session   DEFAULT
Logical_read_per_call        1000
private_sga                  25K
password_life_time           60
password_reuse_time          10
```

**Créer un profile de ressources dédié à l'équipe devsecops avec les limitations suivantes:**
@@ -318,11 +331,20 @@ ________________________________________________________________________________
  * ***Nombre maximal de réutilisations de mot de passe:*** ***10***

```sql 
---
create profile devscops limit
Session_per_user UNLIMITED
cpu_per_session   UNLIMITED
cpu_per_call          3000
connect_time         3600
Logical_read_per_session   DEFAULT
Logical_read_per_call        5000
private_sga                  80K
password_life_time           60
password_reuse_time          10
```

  - **Attribuer à l'utilisateur "dev1", le profile qui lui correspond:** 
```sql
---
ALTER USER dev1 PROFILE dev;
