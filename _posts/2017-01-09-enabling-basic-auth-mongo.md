---
layout: post
title: Enable Basic Authentication and SSL on a Mongo DB instance 
---

### creating ssl key and certificate for enabling ssl
Run the following command to generate ssl certificate and key file.

```
openssl req -newkey rsa:2048 -new -x509 -days 365 -nodes -out mongodb-cert.crt -keyout mongodb-cert.key
cat mongodb-cert.key mongodb-cert.crt > mongodb.pem
```

add the following property to mongod.conf to enable ssl

```yaml
# network interfaces
net:
  ssl:
    mode: requireSSL
    PEMKeyFile: <path to pem file created above>
```
Restart mongo db with new configuration.

### enable basic authentication 
Start MongoDB without access control and create the administrator user.

uses this command to commect to ssl enabled mongo using mong-shell

```bash
mongo --ssl --sslAllowInvalidCertificates 
```

then run the following script 

```javascript
use admin

db.createUser(
  {
    user: "<admin-user>",
    pwd: "<password>",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
  }
)

```

or in One liner form

```bash
mongo admin --ssl --sslAllowInvalidCertificates  --eval "db.createUser( { user: "<admin-user>", pwd: "<password>", roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] } )"
```

add the following property in mongod.conf to enable autorization. (default location for mongod.conf is /etc/mongod.conf)

```yaml
security:
  authorization: enabled
```

Restart mongo db with new configuration (with access control).

Connect to mongodb instance and authenticate as the user administrator. Add non previlaged users to manage and control access to different DB's.

```bash
mongo --port 27017 -u "root" -p "pass" --authenticationDatabase "admin"
```

```javascript
use admin

db.createUser(
    {
      user: "<user>",
      pwd: "<password>",
      roles: [
         { role: "readWrite", db: "test" }
      ]
    }
)
```

If you have followed the above steps, you have successfully added a new user to your db. Try logging in with the new user and try addind document to some db.



