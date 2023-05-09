# migracion para las VM
Aqui se debe producir un procedmiento que esta basado en los siguientes dos recursos:

 https://wiki.lyrasis.org/display/DSDOC7x/Migrating+DSpace+to+a+new+server
 https://wiki.lyrasis.org/display/DSDOC7x/Upgrading+DSpace
 

TODO: 
Porner los pasos que ha hecho reinier... 

antes de realizar las migraciones hay que hacer esto con la base de datos 

```
$ su postgres
$ psql
$ postgres=# \c  dspace
$ CREATE EXTENSION pgcrypto;
```




