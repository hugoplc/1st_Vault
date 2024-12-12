
* Create New Stack in Portainer 

```
version: '2'

services:
  db:
    image: mariadb:10.5
    restart: always
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    volumes:
      - /var/lib/docker/volumes/Nextcloud_Database:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=Hugo1234
      - MYSQL_PASSWORD=Hugo1234
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  app:
    image: nextcloud
    restart: always
    ports:
      - 8080:80
    links:
      - db
    volumes:
      - /var/lib/docker/volumes/Nextcloud_Application:/var/www/html
    environment:
      - MYSQL_PASSWORD=Hugo1234
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=db
```

