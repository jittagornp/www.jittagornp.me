# Secure Kong Admin API

![](./kong.png)

### 1. Add Service
POST : `<GATEWAY_IP>`:8001/services
```
{
  "name" : "admin-api",
  "host" : "localhost",
  "port" : 8001,
  "path" : "/"
}
```

### 2. Add Route under Service
POST : `<GATEWAY_IP>`:8001/services/`admin-api`/routes
```
{
  "paths" : ["/admin-api"]
}
```

### 3. Add Plugin Key Authentication
POST : `<GATEWAY_IP>`:8001/services/`admin-api`/plugins
```
{
  "name" : "key-auth"
}
```

### 4. Create Consumer
POST : `<GATEWAY_IP>`:8001/consumers
```
{
  "username" : "admin"
}
```

### 5. Create Consumer Key
POST : `<GATEWAY_IP>`:8001/consumers/`admin`/key-auth
```
{
  "key" : "68fSNmzqfc3Wxe5GZPb4T8VwGpKUvu3caTeHqM7Z"
}
```

### 6. Test 
GET : `<GATEWAY_IP>`/admin-api?apikey=`68fSNmzqfc3Wxe5GZPb4T8VwGpKUvu3caTeHqM7Z`

### 7. Add Firewall Inbound Rules 

> for disable port 8001  
