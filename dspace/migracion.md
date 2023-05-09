# migracion para las VM

sigue los pasos generales de la guia https://wiki.lyrasis.org/display/DSDOC7x/Migrating+DSpace+to+a+new+server

TODO: 
Porner los pasos que ha hecho reinier... 

antes de realizar las migraciones hay que hacer esto con la base de datos 

```
$ su postgres
$ psql
$ postgres=# \c  dspace
$ CREATE EXTENSION pgcrypto;
```




