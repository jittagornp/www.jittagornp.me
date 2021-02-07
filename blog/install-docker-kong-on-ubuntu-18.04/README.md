# ติดตั้ง Kong ด้วย Docker บน Ubuntu 18.04 

![](./kong.png?v=1)

# Prerequisites

- มี Database Postgresql อยู่แล้ว 

# 1. ทำการ Migrate ข้อมูล Kong เข้า Postgresql

```sh
$ docker run --rm  \
-e "KONG_DATABASE=postgres" \
-e "KONG_PG_HOST=<DATABASE_URL>" \
-e "KONG_PG_USER=<DATABASE_USERNAME>" \
-e "KONG_PG_PASSWORD=<DATABASE_PASSWORD>" \
-e "KONG_PG_PORT=<DATABASE_PORT>" \
-e "KONG_PG_SCHEMA=<DATABASE_SCHEMA>" \
-e "KONG_PG_SSL=<DATABASE_SSL_MODE>" \
kong:latest kong migrations bootstrap
```

### คำอธิบาย

- **DATABASE_URL** คือ IP หรือ Domain Name ของ Database เช่น 127.0.0.1 
- **DATABASE_USERNAME** คือ Username ที่ใช้ Login เข้า Database เช่น kong
- **DATABASE_PASSWORD** คือ รหัสผ่าน (Password) ที่ใช้ Login เข้า Database เช่น password
- **DATABASE_PORT** คือ Port ของ Database ซึ่ง Default จะเป็น 5432 
- **DATABASE_SCHEMA** คือ Schema ของ Database ที่เรากำหนดไว้ เช่น kong
- **DATABASE_SSL_MODE** เป็นการกำหนดว่าจะให้เปิดใช้ SSL หรือไม่ มีค่าเป็น `on` หรือ `off`


# 2. Run Kong 

```sh
$ docker run -d --name kong \
-e "KONG_DATABASE=postgres" \
-e "KONG_PG_HOST=<DATABASE_URL>" \
-e "KONG_PG_USER=<DATABASE_USERNAME>" \
-e "KONG_PG_PASSWORD=<DATABASE_PASSWORD>" \
-e "KONG_PG_PORT=<DATABASE_PORT>" \
-e "KONG_PG_SCHEMA=<DATABASE_SCHEMA>" \
-e "KONG_PG_SSL=<DATABASE_SSL_MODE>" \
-e "KONG_PROXY_ACCESS_LOG=/dev/stdout" \
-e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" \
-e "KONG_PROXY_ERROR_LOG=/dev/stderr" \
-e "KONG_ADMIN_ERROR_LOG=/dev/stderr" \
-e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" \
-p 80:8000 \
-p 443:8443 \
-p 8001:8001 \
-p 8444:8444 \
kong:latest
```

# การติดตั้ง Kong โดยไม่ใช้ Docker 

สามารถเรียนรู้ได้จาก

- [ติดตั้ง Kong บน Ubuntu 18.04](/blog/install-kong-on-ubuntu-18.04/)

# Reference 

- [https://docs.konghq.com/install/docker/](https://docs.konghq.com/install/docker/)
- [https://docs.konghq.com/1.1.x/configuration/#postgres-settings](https://docs.konghq.com/1.1.x/configuration/#postgres-settings)
- [https://github.com/Kong/docker-kong/blob/master/compose/docker-compose.yml](https://github.com/Kong/docker-kong/blob/master/compose/docker-compose.yml)

# หมายเหตุ
เป็นบทความที่ถูกย้ายมาจาก [https://medium.com/@jittagornp/kong-docker-with-external-postgresql-8fe8960bf753](https://medium.com/@jittagornp/kong-docker-with-external-postgresql-8fe8960bf753) ซึ่งผู้เขียน เขียนไว้เมื่อ May 27, 2019