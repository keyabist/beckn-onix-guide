
# Energy Transactions Setup Guide 
This guide walks you through setting up an energy-compatible Beckn stack using the [`strapi-bap-bpp`](https://github.com/beckn/strapi-bap-bpp) repository and the `beckn-bpp-adapter` plugin. You'll configure Docker, MySQL, plugin settings, and Postman.

---

## 🧰 Prerequisites

Make sure you have the following installed:

- [Node.js](https://nodejs.org/) (v16+)
- [Docker + Docker Compose](https://docs.docker.com/get-docker/)
- [Git](https://git-scm.com/)
- A terminal or command-line shell (Linux/macOS or WSL on Windows)

---

## 📦 1. Clone and Set Up Plugin

```bash
# Clone the main strapi-bap-bpp repository
git clone https://github.com/beckn/strapi-bap-bpp.git
cd strapi-bap-bpp/src
```

Now, clone the plugin repository inside the `src` folder of `strapi-bap-bpp` and build it:

```bash
git clone https://github.com/beckn/strapi-plugins.git plugins
cd plugins/beckn-bpp-adapter
npm install
npm run build
```

## 📁 Project File Structure

After cloning and setting up the plugin, your directory should look like this:

```
strapi-bap-bpp/
├── docker-compose.yml
├── mysql2.cnf
├── strapi_dump.sql               # (Optional: if you have a DB dump)
└── src/
    ├── api/
    ├── config/
    │   ├── admin.js
    │   ├── database.js
    │   ├── middleware.js
    │   ├── plugins.js            # << You will edit this file
    │   └── server.js
    ├── extensions/
    ├── plugins/
    │   └── plugins/              # cloned from strapi-plugins
    │       └── beckn-bpp-adapter/
    │           ├── package.json
    │           ├── README.md
    │           ├── strapi-admin.js
    │           ├── config/
    │           ├── controllers/
    │           ├── routes/
    │           ├── services/
    │           └── ...
    └── index.js
```


---

## 🐬 2. MySQL Setup via Docker

Go to the **parent folder** where you cloned the repo (i.e., where `strapi-bap-bpp` lives), and create a file named `docker-compose.yml`:

```yaml
version: "3.8"

services:
  mysql2:
    image: mysql/mysql-server:latest
    container_name: mysql2-container
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_ROOT_USER: root
    volumes:
      - mysql2-data:/var/lib/mysql
      - ./mysql2.cnf:/etc/mysql/conf.d/mysql2.cnf
    ports:
      - "3306:3306"

volumes:
  mysql2-data:
```

---

Now create a file named `mysql2.cnf` in the same folder with the following content:

```ini
[mysqld]
sort_buffer_size = 64M
```

Start your MySQL container:

```bash
docker-compose -f docker-compose.yml up -d
```

---

## ⚙️ 3. Plugin Configuration

In the `config/plugins.js` file, replace the content with:

```js
module.exports = ({ env }) => ({
  "beckn-bpp-adapter": {
    enabled: true,
    resolve: "./src/plugins/plugins/beckn-bpp-adapter",
  },
  email: {
    config: {
      provider: "sendgrid",
      providerOptions: {
        apiKey: env("SENDGRID_API_KEY"),
      },
      settings: {
        defaultFrom: env("SENDGRID_DEFAULT_FROM_EMAIL"),
        defaultReplyTo: env("SENDGRID_REPLY_TO_EMAIL"),
      },
    },
  },
});
```

---

## 🌐 4. Webhook Setup

In `default.yml` files, update the webhook to:

```yaml
webhook:
  url: "http://127.0.0.1:1337/beckn-bpp-adapter"
```

---

## 🐬 5. MySQL Access & Initialization

Ensure your MySQL container is running:

```bash
docker ps -a
```

Then run:

```bash
docker exec -it mysql2-container mysql -uroot -proot -e "SET GLOBAL sort_buffer_size = 67108864;"
docker exec -it mysql2-container mysql -uroot -proot
```

Inside the MySQL shell:

```sql
CREATE USER 'root'@'%' IDENTIFIED BY 'root';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

---

## 📥 6. Import SQL Dump 

```bash
docker cp ./strapi_dump.sql mysql2-container:/strapi_dump.sql
docker exec -i mysql2-container mysql -hlocalhost -uroot -proot strapi_deg_local < ./strapi_dump.sql
```

---

## 🛠️ 7. Manual Configuration via Docker Volumes

To directly edit the `default.yml` for each service:

```bash
sudo su
cd /var/lib/docker/volumes
```

Then do the following for each of these volumes:

```bash
cd bpp_client_config_volume/_data
vi default.yml

cd ../../bpp_network_config_volume/_data
vi default.yml

cd ../../bap_client_config_volume/_data
vi default.yml
```

---

### Inside each `default.yml`, make these changes:

```yaml
webhook:
  url: "http://<your-ip>:1337/beckn-bpp-adapter"

registryUrl: "http://registry:3030"
```

Exit the editor (`:wq` in `vi`).

---

## ✅ 8. Final Plugin Install

Just to be sure, run this again inside the plugin folder:

```bash
cd src/plugins/plugins/beckn-bpp-adapter
npm install
```

---

## 📬 9. Energy Postman Collection

You can test energy catalog flows using this Postman collection:

👉 [DEG Starter Kit – Postman Collection](https://github.com/beckn/missions/blob/main/DEG/STARTER_KIT/DEG_Starter_KIT.postman_collection.json)

---

## 🚀 You're All Set!

You now have:
- A working Strapi + Beckn BPP adapter setup
- MySQL backend
- Configured webhooks and registry
- Ready-to-test APIs for energy domain

Run your server:

```bash
npm run develop
```

> 🧪 You’re now ready to develop and simulate energy transactions over Beckn!
