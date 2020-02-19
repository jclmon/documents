# MongoDB
## Comandos básicos

### Arranque
```
mongod --port 27017 --dbpath C:\Producto\mongodb-win32-x86_64-2.6.11\db\data
```
### Consola admin
```
mongo --port 27017
```
### Lista de bases de datos disponibles
```
show databases
```
### Crear base de datos
```
use arquitecture
```
### Crear usuario
```
db.createUser(
   {
     user: "arquitectureapp",
     pwd: "arquitectureapp",
     roles: [ "readWrite", "dbAdmin" ]
   }
);
```
### Mostrar colecciones
```
show collections
```
### Insertar datos
```
db.User.insert({ "username" : "testuser1", "password": "$2a$10$MK2.vNiBHToVNwqJl9P2luHjbTMUkNi.fxKJ0b/u/dEFJY0taGjm6", "email" : "testuser1@testdomain.com", "permissions" : [{"code" : "USER_ADMIN"},{"code" : "USER_SELLER"}] })
db.User.insert({ "username" : "testuser2", "password": "$2a$10$MK2.vNiBHToVNwqJl9P2luHjbTMUkNi.fxKJ0b/u/dEFJY0taGjm6", "email" : "testuser2@testdomain.com", "permissions" : [{"code" : "USER_ADMIN"}] })
db.User.insert({ "username" : "testuser3", "password": "$2a$10$MK2.vNiBHToVNwqJl9P2luHjbTMUkNi.fxKJ0b/u/dEFJY0taGjm6", "email" : "testuser3@testdomain.com", "permissions" : [{"code" : "USER_ADMIN"}] })
db.User.insert({ "username" : "testuser4", "password": "$2a$10$MK2.vNiBHToVNwqJl9P2luHjbTMUkNi.fxKJ0b/u/dEFJY0taGjm6", "email" : "testuser4@testdomain.com", "permissions" : [{"code" : "USER_ADMIN"}] })
db.User.insert({ "username" : "testuser5", "password": "$2a$10$MK2.vNiBHToVNwqJl9P2luHjbTMUkNi.fxKJ0b/u/dEFJY0taGjm6", "email" : "testuser5@testdomain.com", "permissions" : [{"code" : "USER_ADMIN"}] })
--- tambien
newstuff = [{ "username" : "testuser2", "email" : "testuser2@testdomain.com" }, { "username" : "testuser3", "email" : "testuser3@testdomain.com" }]
db.User.insert(newstuff);
```

### Eliminar datos
```
db.User.remove( { "username": "testuser2" } )
db.Product.remove( { "id": "1" } )
```
### Select
```
db.User.find().pretty()
```
