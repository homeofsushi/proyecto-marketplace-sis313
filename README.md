<div align="center">

<div align="center"><img src="imagenes/00-recursos/pixel-network.gif" alt="Red de servidores" width="480"></div>

# 🚀 Proyecto Final SIS313: Marketplace Local con Monitoreo y Escalabilidad

**Asignatura:** SIS313 — Infraestructura, Plataformas Tecnológicas y Redes
**Semestre:** 1/2026
**Docente:** Ing. Marcelo Quispe Ortega

![NGINX](https://img.shields.io/badge/NGINX-Reverse_Proxy-009639?style=for-the-badge&logo=nginx&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-PM2-339933?style=for-the-badge&logo=node.js&logoColor=white)
![MariaDB](https://img.shields.io/badge/MariaDB-10.11-003545?style=for-the-badge&logo=mariadb&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-Monitoreo-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-Alertas-F46800?style=for-the-badge&logo=grafana&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)

</div>

---

## 👥 Miembros del Equipo

| Nombre Completo | Rol en el Proyecto |         Carrera         |
|:----------------|:-------------------|:------------------------|
| 💚 Janet Chambi Condori | Arquitecta de Infraestructura — Proxy, TLS, DNS y Monitoreo | Ingeniería de Sistemas |
| 💙 Alex Josué Calatayud Mamani | Ingeniero de Aplicaciones — App1 y Automatización (Health Check) | Ingeniería de Sistemas |
| 💜 Jhon Christian Quispe Anagua | Administrador de Sistemas — App2 y Panel de Administración | Ingeniería de Sistemas |
| 🧡 Luis Fernando Quispe Sullca | Administrador de Base de Datos — MariaDB y Backups (DRP) | Ing. en Ciencias de la Computación |

---

## 🎯 I. Objetivo del Proyecto

**Objetivo:** Diseñar e implementar la infraestructura completa para un marketplace de comerciantes locales, con la aplicación Node.js ejecutándose en **2 instancias bajo PM2** detrás de un **balanceador NGINX** con algoritmo `least_conn`, almacenamiento centralizado en **MariaDB**, **monitoreo con Prometheus + Grafana** que alerta cuando el uso de CPU supera el **80%**, tráfico cifrado con **TLS**, y un sistema de **backups automatizados** con cron (pedidos cada hora, archivos cada día) con **restauración demostrada** de la base de datos.

---

## 💡 II. Justificación e Importancia

Un comerciante local no puede darse el lujo de que su tienda online se caiga un día de ventas. Detrás de cada página web confiable existe una **infraestructura invisible** que la sostiene — este proyecto construye exactamente esa parte.

**Justificación:** El proyecto elimina los **puntos únicos de falla (Single Point of Failure)** en la capa de aplicación al ejecutar dos instancias balanceadas: si una cae, NGINX desvía automáticamente el tráfico a la otra (**failover**), garantizando la **continuidad operacional**. La **tolerancia a fallos** se refuerza en tres capas: el balanceador desvía el tráfico, PM2 reinicia procesos caídos y un script de health check actúa como última red de seguridad. En el plano de la **seguridad**, todo el tráfico externo viaja cifrado con TLS, la base de datos queda aislada mediante firewall UFW (solo las aplicaciones acceden al puerto 3306) y se aplica hardening con `mysql_secure_installation`. Finalmente, el **monitoreo proactivo** con alerta de CPU > 80% permite reaccionar **antes** de que el sistema colapse, y los **backups diferenciados con restauración demostrada** garantizan que los datos sobrevivan a cualquier desastre. Estos son los mismos principios que sostienen la infraestructura de cualquier empresa o universidad en producción.

---

## 🛠️ III. Tecnologías y Conceptos Implementados

### 3.1. Tecnologías Clave

- **NGINX**: Proxy inverso y balanceador de carga con algoritmo `least_conn`, terminación TLS, redirección HTTP→HTTPS y cabeceras de seguridad.
- **Node.js + PM2**: Aplicación Express del marketplace en 2 instancias independientes con auto-restart, persistencia (`pm2 save`) y arranque automático (`pm2 startup`).
- **MariaDB**: Servidor de base de datos centralizado (productos, comentarios, contador de visitas) con hardening y acceso restringido por red.
- **BIND9**: Servidor DNS interno con zona `marketplace.local` y registros A para todos los servidores.
- **Prometheus + Node Exporter**: Recolección de métricas (CPU, RAM, disco, red) de las 4 VMs cada 15 segundos bajo modelo *pull*.
- **Grafana**: Visualización con dashboard *Node Exporter Full* (ID 1860) y alerta configurada para CPU > 80%.
- **OpenSSL**: Generación del certificado TLS autofirmado RSA 2048 (CN=marketplace.local, 365 días).
- **Bash + Cron**: Scripts de health check (reinicio automático de instancias caídas), menú interactivo de administración (8 opciones) y backups con rotación.
- **UFW**: Firewall de la base de datos — política *deny incoming* con excepciones solo para las apps (3306) y el monitoreo (9100).

### 3.2. Conceptos de la Asignatura Puestos en Práctica

- ✅ **Alta Disponibilidad y Tolerancia a Fallos:** 2 instancias de la app balanceadas; failover automático de NGINX cuando un backend cae; PM2 con auto-restart; health check que reinicia instancias caídas cada 5 minutos. *Tres capas de defensa contra caídas.*
- ✅ **Seguridad y Hardening:** Certificado SSL/TLS (TLSv1.2/1.3 con ciphers AESGCM), cabeceras de seguridad (HSTS, X-Frame-Options, nosniff, XSS-Protection), `server_tokens off`, firewall UFW restrictivo en la BD, `mysql_secure_installation` y principio de mínimo privilegio para el usuario de BD.
- ✅ **Automatización y Gestión:** Scripts Bash de backup con cron (cada hora) y rotación de 7 días; script de restauración interactivo (DRP); health check automatizado; menú de administración con 8 opciones.
- ✅ **Balanceo de Carga / Proxy:** NGINX como proxy inverso con upstream `least_conn` distribuyendo entre App1 y App2, con health checks pasivos (deja de enviar tráfico a backends caídos).
- ✅ **Monitoreo:** Prometheus con 5 targets + Grafana con dashboard 1860 y alerta visual de CPU > 80% evaluada cada minuto.
- ✅ **Networking Avanzado:** Red privada segmentada 192.168.100.0/24, DNS interno con BIND9, túneles SSH para acceso remoto seguro a servicios internos.

---

## 🌐 IV. Diseño de la Infraestructura y Topología

### 4.1. Diseño Esquemático

<div align="center"><img src="imagenes/00-recursos/diagrama-arquitectura.jpg" alt="Diagrama de arquitectura" width="850"></div>

| VM/Host | Rol | IP Física | Puertos clave | Red Lógica | SO |
|:--------|:----|:----------|:--------------|:-----------|:---|
| `server-174` | Proxy / Balanceador / Monitoreo | 192.168.100.174 | 80, 443, 53, 9090, 3000, 9100 | 192.168.100.0/24 | Ubuntu 24.04 |
| `server-175` | Aplicación 1 (Node.js + PM2) | 192.168.100.175 | 3000, 9100 | 192.168.100.0/24 | Ubuntu 24.04 |
| `server-176` | Aplicación 2 (Node.js + PM2) | 192.168.100.176 | 3000, 9100 | 192.168.100.0/24 | Ubuntu 24.04 |
| `server-177` | Base de Datos (MariaDB) | 192.168.100.177 | 3306 (restringido), 9100 | 192.168.100.0/24 | Ubuntu 24.04 |

**Flujo de tráfico:**

```mermaid
flowchart LR
    U["👤 Usuario"] -->|"HTTPS :443"| N["🔀 NGINX<br/>server-174"]
    N -->|"least_conn"| A1["🔵 App1<br/>server-175"]
    N -->|"least_conn"| A2["🟣 App2<br/>server-176"]
    A1 -->|"SQL :3306"| DB[("🟡 MariaDB<br/>server-177")]
    A2 -->|"SQL :3306"| DB
```

1. El **usuario** se conecta por **HTTPS (443)** al Proxy; NGINX descifra el TLS.
2. NGINX **balancea** hacia App1 o App2 con `least_conn` — cada solicitud va al servidor con **menos conexiones activas**.
3. Las apps consultan **MariaDB** por el puerto 3306 (firewall solo les permite a ellas).
4. En paralelo, **Prometheus** recolecta métricas de las 4 VMs vía **Node Exporter** (:9100) y **Grafana** las visualiza con la **alerta de CPU > 80%**.

### 4.2. Estrategia Adoptada

- **Estrategia de balanceo:** Se optó por `least_conn` en lugar del round-robin por defecto, porque distribuye según las **conexiones activas reales** de cada servidor: si App1 está ocupada con una petición lenta, las siguientes van a App2. Round-robin seguiría enviando por turnos ciegos aunque un servidor esté saturado.
- **Estrategia de orden de implementación:** `DB → Apps → Proxy`. La base de datos primero (las apps la necesitan para arrancar), luego las aplicaciones, y al final el proxy que las integra. Esto permitió a los 4 integrantes trabajar en paralelo con dependencias mínimas.
- **Estrategia TLS:** Certificado autofirmado con OpenSSL — suficiente para cifrar en un entorno de laboratorio sin dominio público. La **terminación TLS en el proxy** libera a las apps del costo del cifrado: NGINX descifra y habla HTTP interno con los backends.
- **Estrategia de identificación visual del balanceo:** Cada app usa una gama de colores distinta (App1 = esmeralda, App2 = violeta), de modo que al recargar la página el cambio de color **demuestra visualmente** el balanceo ante cualquier público.
- **Estrategia de backups (DRP):** Diferenciados por frecuencia de cambio — los pedidos cambian constantemente (respaldo **cada hora**), los archivos de productos casi no cambian (respaldo **diario**). Compresión gzip y rotación de 7 días para no agotar el disco.

---

## 📋 V. Guía de Implementación y Puesta en Marcha

### 5.1. Pre-requisitos

- 4 VMs con **Ubuntu Server 24.04** y usuario con privilegios sudo (`adming9`).
- Red interna `192.168.100.0/24` con conectividad entre las 4 VMs (verificable con `ping`).
- Acceso externo vía servidor proxy del laboratorio: `ssh usrproxy@201.131.45.42`.
- Repositorio de la aplicación Node.js disponible para clonar en las VMs de aplicación.

### 5.2. Despliegue

> El despliegue se ejecuta **por zonas, en orden**: primero la base de datos, luego las dos aplicaciones y al final el proxy.

---

#### 🟡 5.2.1. Zona de Datos — server-177 *(Luis Fernando)*

<div align="center"><img src="imagenes/00-recursos/pixel-backup.gif" alt="Backup pixel" width="190"></div>

**a) Verificar conectividad:**

```bash
ip a
ping -c3 google.com
ping -c3 192.168.100.174
```

<div align="center"><img src="imagenes/04-db/01-red-y-ping.png" alt="Red y ping" width="640"></div>

**b) Instalar MariaDB:**

```bash
sudo apt update
sudo apt install mariadb-server -y
```

<div align="center"><img src="imagenes/04-db/02-instalar-mariadb.png" alt="Instalar mariadb" width="640"></div>

```bash
sudo systemctl enable mariadb
systemctl status mariadb
```

<div align="center"><img src="imagenes/04-db/03-enable-status-mariadb.png" alt="Enable mariadb" width="640"></div>

**c) Hardening con `mysql_secure_installation`** — elimina usuarios anónimos, bloquea el root remoto y borra la BD de pruebas:

```bash
sudo mysql_secure_installation
```

<div align="center"><img src="imagenes/04-db/04-mysql-secure-installation.png" alt="Secure installation"></div>

**d) Configurar bind-address** para escuchar en la IP de red:

```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
# bind-address = 192.168.100.177
sudo systemctl restart mariadb
```

<div align="center"><img src="imagenes/04-db/05-bind-address.png" alt="Bind address"></div>

**e) Crear base de datos y usuario** (principio de mínimo privilegio — `usr_market` solo accede a `db_marketplace`):

```sql
CREATE DATABASE db_marketplace;
CREATE USER 'usr_market'@'192.168.100.%' IDENTIFIED BY 'market2026';
GRANT ALL PRIVILEGES ON db_marketplace.* TO 'usr_market'@'192.168.100.%';
FLUSH PRIVILEGES;
```

<div align="center"><img src="imagenes/04-db/06-crear-base-datos.png" alt="Crear BD" width="520"></div>

**f) Crear las tablas** (`products`, `comments`, `visit_counter`):

<div align="center"><img src="imagenes/04-db/07-crear-tablas.png" alt="Crear tablas"></div>

**g) Insertar y verificar los 10 productos:**

<div align="center"><img src="imagenes/04-db/08-insertar-productos.png" alt="Insertar productos" width="700"></div>

```sql
SELECT * FROM products;
```

<div align="center"><img src="imagenes/04-db/09-select-productos.png" alt="Select productos" width="700"></div>

```sql
SHOW TABLES;
```

<div align="center"><img src="imagenes/04-db/10-show-tables.png" alt="Show tables"></div>

**h) Instalar Node Exporter** (métricas para Prometheus):

```bash
sudo apt install prometheus-node-exporter -y
```

<div align="center"><img src="imagenes/04-db/11-instalar-node-exporter.png" alt="Node exporter db" width="520"></div>

<div align="center"><img src="imagenes/04-db/12-enable-node-exporter.png" alt="Enable node exporter" width="640"></div>

**i) Firewall UFW** — el portero que solo deja pasar a la lista de invitados:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from 192.168.100.175 to any port 3306
sudo ufw allow from 192.168.100.176 to any port 3306
sudo ufw allow from 192.168.100.174 to any port 9100
sudo ufw allow 22
sudo ufw enable
sudo ufw status numbered
```

<div align="center"><img src="imagenes/04-db/13-configurar-ufw.png" alt="Configurar UFW" width="520"></div>

**j) Backups automatizados** — `backup_db.sh` hace mysqldump + gzip + rotación de 7 días:

```bash
sudo mkdir -p /var/backups/marketplace
sudo nano /opt/backup_db.sh
sudo chmod +x /opt/backup_db.sh
```

<div align="center"><img src="imagenes/04-db/14-script-backup.png" alt="Script backup" width="520"></div>

**k) Script de restauración** — lista backups, pide elegir, confirma y restaura:

<div align="center"><img src="imagenes/04-db/15-script-restore.png" alt="Script restore" width="520"></div>

**l) Programar el cron** (cada hora):

```bash
crontab -e
```

<div align="center"><img src="imagenes/04-db/16-crontab-comando.png" alt="Crontab comando"></div>

```cron
# m h dom mon dow   comando
0 * * * * /opt/backup_db.sh >> /var/log/backup_db.log 2>&1
```

> 🧠 **Sintaxis cron:** `0 * * * *` = minuto 0 de **cada hora**. Para el respaldo diario de archivos: `0 0 * * *` = medianoche de cada día.

<div align="center"><img src="imagenes/04-db/17-crontab-backup-cada-hora.png" alt="Crontab cada hora"></div>

**m) Verificación final** — UFW activo, backup generado, Node Exporter corriendo:

<div align="center"><img src="imagenes/04-db/18-verificacion-ufw-backups.png" alt="Verificacion final" width="700"></div>

---

#### 🔵 5.2.2. Zona App1 — server-175 *(Alex Josué)*

**a) Instalar Node.js:**

```bash
curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
nvm install --lts
node -v
```

<div align="center"><img src="imagenes/02-app1/01-instalar-nodejs.png" alt="Instalar nodejs" width="640"></div>

**b) Instalar PM2** — el vigilante que nunca duerme: si la app cae, la levanta al instante:

```bash
npm install -g pm2
pm2 -v
```

<div align="center"><img src="imagenes/02-app1/02-instalar-pm2.png" alt="Instalar pm2" width="520"></div>

**c) Clonar la aplicación:**

```bash
mkdir -p ~/apps && cd ~/apps
git clone <repo> marketplace
cd marketplace && npm install
```

<div align="center"><img src="imagenes/02-app1/03-clonar-aplicacion.png" alt="Clonar app" width="640"></div>

**d) Configurar el `.env`:**

```ini
PORT=3000
DB_HOST=192.168.100.177
DB_NAME=db_marketplace
DB_USER=usr_market
DB_PASSWORD=market2026
```

<div align="center"><img src="imagenes/02-app1/04-configurar-env.png" alt="Configurar env" width="520"></div>

**e) Lanzar con PM2** (con persistencia y arranque automático):

```bash
pm2 start app.js --name app1
pm2 save
pm2 startup
pm2 list
```

<div align="center"><img src="imagenes/02-app1/05-lanzar-pm2.png" alt="Lanzar pm2" width="640"></div>

**f) Health Check** — verifica Proxy, App1, App2 y DB; si una app está caída **la reinicia automáticamente**. Cron cada 5 minutos:

```bash
bash ~/health_check.sh
crontab -l   # */5 * * * * /home/adming9/health_check.sh
```

<div align="center"><img src="imagenes/02-app1/06-health-check.png" alt="Health check" width="700"></div>

**g) Node Exporter:**

```bash
sudo apt install prometheus-node-exporter -y
sudo systemctl enable prometheus-node-exporter
```

<div align="center"><img src="imagenes/02-app1/07-node-exporter.png" alt="Node exporter" width="640"></div>

---

#### 🟣 5.2.3. Zona App2 — server-176 *(Jhon Christian)*

**a) Instalar Node.js + PM2:**

```bash
curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
nvm install --lts
npm install -g pm2
```

<div align="center"><img src="imagenes/03-app2/01-instalar-nodejs-pm2.png" alt="Instalar nodejs pm2" width="700"></div>

**b) Clonar y configurar la aplicación** (misma app, `.env` idéntico — ambas instancias conectan a la **misma** BD centralizada):

<div align="center"><img src="imagenes/03-app2/02-clonar-aplicacion.png" alt="Clonar app2" width="700"></div>

<div align="center"><img src="imagenes/03-app2/03-configurar-env.png" alt="Env app2"></div>

**c) Lanzar con PM2:**

```bash
pm2 start app.js --name app2
pm2 save && pm2 startup
```

<div align="center"><img src="imagenes/03-app2/04-lanzar-pm2.png" alt="Lanzar pm2 app2" width="700"></div>

**d) Node Exporter:**

<div align="center"><img src="imagenes/03-app2/05-node-exporter.png" alt="Node exporter app2" width="700"></div>

**e) Menú Interactivo de Administración** — panel Bash con 8 opciones y colores:

```bash
bash ~/admin_menu.sh
```

| # | Opción | # | Opción |
|:-:|:-------|:-:|:-------|
| 1 | Estado de servicios | 5 | Verificar balanceo |
| 2 | Estado PM2 | 6 | Disco y memoria |
| 3 | Ver productos | 7 | Reiniciar app |
| 4 | Ver comentarios | 8 | Ver backups |

<div align="center"><img src="imagenes/03-app2/06-menu-interactivo-parte1.png" alt="Menu parte 1" width="700"></div>

<div align="center"><img src="imagenes/03-app2/07-menu-interactivo-parte2.png" alt="Menu parte 2" width="700"></div>

<div align="center"><img src="imagenes/03-app2/08-menu-configuracion.png" alt="Menu config" width="520"></div>

---

#### 🟢 5.2.4. Zona Proxy / Monitoreo — server-174 *(Janet)*

**a) Instalar NGINX:**

```bash
sudo apt update
sudo apt install nginx -y
```

<div align="center"><img src="imagenes/01-proxy/01-instalar-nginx.png" alt="Instalar nginx" width="700"></div>

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
systemctl status nginx
```

<div align="center"><img src="imagenes/01-proxy/02-enable-nginx.png" alt="Enable nginx" width="700"></div>

**b) Instalar BIND9** (DNS interno):

```bash
sudo apt install bind9 bind9utils -y
```

<div align="center"><img src="imagenes/01-proxy/03-instalar-bind9.png" alt="Instalar BIND9" width="640"></div>

**c) Configurar la zona `marketplace.local`:**

```bash
sudo nano /etc/bind/named.conf.local
```

<div align="center"><img src="imagenes/01-proxy/04-zona-marketplace.png" alt="Zona marketplace" width="640"></div>

```bash
sudo nano /etc/bind/db.marketplace.local
```

<div align="center"><img src="imagenes/01-proxy/05-archivo-zona.png" alt="Archivo de zona" width="640"></div>

**d) Verificar y reiniciar BIND9:**

```bash
sudo named-checkconf
sudo named-checkzone marketplace.local /etc/bind/db.marketplace.local
sudo systemctl restart bind9
```

<div align="center"><img src="imagenes/01-proxy/06-verificar-bind9.png" alt="Verificar bind9" width="700"></div>

**e) Probar la resolución DNS:**

```bash
dig @localhost marketplace.local
```

<div align="center"><img src="imagenes/01-proxy/07-probar-dns.png" alt="Probar DNS" width="520"></div>

**f) Generar el certificado TLS** (RSA 2048, 365 días, CN=marketplace.local):

<div align="center"><img src="imagenes/00-recursos/pixel-shield.gif" alt="Shield pixel" width="150"></div>

```bash
sudo mkdir -p /etc/nginx/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/nginx/ssl/nginx-selfsigned.key \
  -out /etc/nginx/ssl/nginx-selfsigned.crt
```

<div align="center"><img src="imagenes/01-proxy/08-generar-certificado-tls.png" alt="Generar TLS" width="700"></div>

```bash
sudo openssl x509 -in /etc/nginx/ssl/nginx-selfsigned.crt -text -noout
```

<div align="center"><img src="imagenes/01-proxy/09-verificar-tls.png" alt="Verificar TLS" width="700"></div>

**g) Instalar Prometheus:**

<div align="center"><img src="imagenes/00-recursos/pixel-monitor.gif" alt="Monitor pixel" width="190"></div>

```bash
sudo apt install prometheus -y
```

<div align="center"><img src="imagenes/01-proxy/10-instalar-prometheus.png" alt="Instalar prometheus" width="700"></div>

```bash
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

<div align="center"><img src="imagenes/01-proxy/11-start-prometheus.png" alt="Start prometheus" width="700"></div>

**h) Configurar los 5 targets** en `/etc/prometheus/prometheus.yml`:

| Target | Dirección |
|:-------|:----------|
| Prometheus (a sí mismo) | `localhost:9090` |
| Node Exporter Proxy | `localhost:9100` |
| Node Exporter App1 | `192.168.100.175:9100` |
| Node Exporter App2 | `192.168.100.176:9100` |
| Node Exporter DB | `192.168.100.177:9100` |

<div align="center"><img src="imagenes/01-proxy/12-prometheus-yml-targets.png" alt="Prometheus yml" width="640"></div>

**i) Verificar que los 5 targets estén UP:**

```bash
curl -s http://localhost:9090/api/v1/targets | python3 -m json.tool | grep -E '"health"|"job"'
```

<div align="center"><img src="imagenes/01-proxy/13-verificar-targets.png" alt="Verificar targets" width="700"></div>

**j) Instalar Grafana:**

```bash
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
sudo apt update
sudo apt install grafana -y
```

<div align="center"><img src="imagenes/01-proxy/14-instalar-grafana.png" alt="Instalar grafana" width="700"></div>

```bash
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
systemctl status grafana-server
```

<div align="center"><img src="imagenes/01-proxy/15-status-grafana.png" alt="Status grafana" width="700"></div>

**k) Acceso por túnel SSH** — la red es privada, así que el acceso externo es por túnel:

```bash
ssh -L 3000:192.168.100.174:3000 usrproxy@201.131.45.42
# Luego: http://localhost:3000
```

> 🧠 **Cómo funciona:** *Tu navegador → puerto 3000 de tu PC → túnel SSH cifrado → servidor proxy → 192.168.100.174:3000*. Un "pasillo secreto" cifrado entre tu PC y la VM.

<div align="center"><img src="imagenes/01-proxy/16-tunel-ssh-grafana.png" alt="Tunel SSH" width="640"></div>

**l) Configurar el data source Prometheus** en Connections → Data sources:

<div align="center"><img src="imagenes/01-proxy/17-datasource-prometheus.png" alt="Datasource" width="700"></div>

<div align="center"><img src="imagenes/01-proxy/18-datasource-save-test.png" alt="Save y test" width="700"></div>

**m) Importar el dashboard ID 1860** (*Node Exporter Full*):

<div align="center"><img src="imagenes/01-proxy/19-dashboard-1860.png" alt="Dashboard 1860" width="700"></div>

**n) Configurar la alerta "CPU mayor a 80%"** con query PromQL:

```promql
100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

> 🧠 `node_cpu_seconds_total{mode="idle"}` mide cuánto tiempo el CPU estuvo *inactivo*. La fórmula resta el idle de 100: si estuvo poco inactivo, está muy ocupado. Condición **IS ABOVE 80**, evaluada **cada 1 minuto**.

<div align="center"><img src="imagenes/01-proxy/20-alerta-cpu-query.png" alt="Alerta CPU" width="700"></div>

Carpeta, grupo de evaluación y notificaciones:

<div align="center"><img src="imagenes/01-proxy/21-alerta-folder.png" alt="Folder" width="700"></div>

<div align="center"><img src="imagenes/01-proxy/22-alerta-grupo-evaluacion.png" alt="Grupo evaluacion" width="640"></div>

<div align="center"><img src="imagenes/01-proxy/23-alerta-notificaciones.png" alt="Notificaciones" width="700"></div>

✅ **Alerta guardada y activa:**

<div align="center"><img src="imagenes/01-proxy/24-alerta-guardada.png" alt="Alerta guardada" width="700"></div>

**o) Configurar NGINX con HTTPS + Balanceo `least_conn`:**

```bash
sudo nano /etc/nginx/sites-available/marketplace
```

```nginx
# Redirección HTTP → HTTPS
server {
    listen 80;
    server_name marketplace.local www.marketplace.local;
    return 301 https://$host$request_uri;
}

# Balanceo HTTPS
upstream marketplace_apps {
    least_conn;
    server 192.168.100.175:3000;   # App 1 — Alex
    server 192.168.100.176:3000;   # App 2 — Jhon Christian
}

server {
    listen 443 ssl;
    server_name marketplace.local www.marketplace.local;

    ssl_certificate /etc/nginx/ssl/nginx-selfsigned.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx-selfsigned.key;

    # Hardening TLS
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
    ssl_prefer_server_ciphers on;

    # Cabeceras de seguridad
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    server_tokens off;

    location / {
        proxy_pass http://marketplace_apps;
    }
}
```

<div align="center"><img src="imagenes/01-proxy/25-nginx-config-marketplace.png" alt="Config nginx" width="700"></div>

**p) Activar el sitio y reiniciar:**

```bash
sudo ln -s /etc/nginx/sites-available/marketplace /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

<div align="center"><img src="imagenes/01-proxy/26-nginx-activar-reiniciar.png" alt="Activar nginx" width="700"></div>

### 5.3. Ficheros de Configuración Clave

| Fichero | Servidor | Función |
|:--------|:---------|:--------|
| `/etc/nginx/sites-available/marketplace` | server-174 | Balanceador `least_conn`, HTTPS y hardening TLS |
| `/etc/nginx/ssl/nginx-selfsigned.{crt,key}` | server-174 | Certificado y clave TLS |
| `/etc/bind/named.conf.local` | server-174 | Declaración de la zona DNS |
| `/etc/bind/db.marketplace.local` | server-174 | Registros A de `marketplace.local` |
| `/etc/prometheus/prometheus.yml` | server-174 | Los 5 targets de monitoreo |
| `~/apps/marketplace/.env` | server-175/176 | Conexión de las apps a la BD |
| `~/health_check.sh` | server-175 | Verificación y reinicio automático de servicios |
| `~/admin_menu.sh` | server-176 | Menú interactivo de administración (8 opciones) |
| `/etc/mysql/mariadb.conf.d/50-server.cnf` | server-177 | `bind-address` de MariaDB |
| `/opt/backup_db.sh` · `/opt/restore_db.sh` | server-177 | Backup horario con rotación y restauración (DRP) |
| `crontab` (adming9) | server-175/177 | Programación del health check y los backups |

---

## ⚠️ VI. Pruebas y Validación

| Prueba Realizada | Resultado Esperado | Resultado Obtenido |
|:-----------------|:-------------------|:------------------:|
| **Test de Balanceo** — 6 solicitudes consecutivas al proxy | El hostname alterna entre server-175 y server-176; la página cambia de color (esmeralda ↔ violeta) | ✅ OK |
| **Test de Failover** — detener App2 (`pm2 stop app2`) | NGINX desvía todo el tráfico a App1 sin interrupción para el usuario | ✅ OK |
| **Test de Health Check** — detener App1 y ejecutar el script | El script detecta la instancia caída y la reinicia automáticamente | ✅ OK |
| **Test de Seguridad TLS/Firewall** — `curl -k -I https://...` y `ufw status` | HTTP redirige a HTTPS (301); cabeceras de seguridad presentes; solo las apps acceden al 3306 | ✅ OK |
| **Test de Monitoreo** — targets y alerta | Los 5 targets en estado UP; alerta "CPU mayor a 80%" en estado Normal y disparando ante carga | ✅ OK |
| **Test de Backup y Restauración** — ejecutar `restore_db.sh` | El backup horario existe en `/var/backups/marketplace/` y la BD se restaura correctamente | ✅ OK |

### Comandos de verificación utilizados

```bash
# Balanceo: el hostname alterna entre los dos servidores
for i in {1..6}; do curl -sk https://192.168.100.174/ | grep -o 'Hostname: [^<]*'; sleep 0.5; done

# TLS y cabeceras de seguridad
curl -k -I https://192.168.100.174

# Failover
ssh adming9@192.168.100.176 "pm2 stop app2"
curl -sk https://192.168.100.174/ | grep -o 'Hostname: [^<]*'   # solo responde server-175
ssh adming9@192.168.100.176 "pm2 start app2"

# Servicios del proxy
systemctl status nginx prometheus grafana-server --no-pager

# DNS
dig @localhost marketplace.local
```

### Evidencia visual del balanceo

Al recargar, el color cambia: **esmeralda = App1**, **violeta = App2**. El balanceador repartiendo solicitudes en tiempo real:

<div align="center"><img src="imagenes/00-recursos/balanceo-demo.gif" alt="Demo balanceo" width="700"></div>

**💚 App1 — server-175 (tema Esmeralda):**

<div align="center"><img src="imagenes/05-anexos/01-pagina-app1-esmeralda.png" alt="App1" width="750"></div>

**💜 App2 — server-176 (tema Violeta):**

<div align="center"><img src="imagenes/05-anexos/02-pagina-app2-violeta.png" alt="App2" width="750"></div>

**🛍️ Catálogo cargado dinámicamente desde MariaDB:**

<div align="center"><img src="imagenes/05-anexos/03-productos-marketplace.png" alt="Productos" width="750"></div>

---

## 📚 VII. Conclusiones y Lecciones Aprendidas

### Conclusiones

1. **Alta disponibilidad lograda con tres capas de defensa:** NGINX (`least_conn`) desvía el tráfico de backends caídos, PM2 reinicia procesos al instante, y el health check actúa como última red de seguridad cada 5 minutos. La diferencia entre que se caiga un *servicio* y que se caiga el *sistema* es la redundancia — y eso es lo que demostramos.
2. **El monitoreo proactivo cambia el juego:** con Prometheus recolectando métricas cada 15 segundos y la alerta de CPU > 80%, los problemas se detectan **antes** del colapso, no después.
3. **La seguridad funciona en capas:** TLS cifra el tráfico, las cabeceras HTTP previenen ataques comunes, UFW aísla la base de datos y el hardening de MariaDB aplica mínimo privilegio. Ninguna capa sola es suficiente; juntas son sólidas.
4. **Los backups solo valen si se prueba la restauración:** no basta con generar respaldos — demostramos la recuperación completa de la base de datos, que es lo que convierte un backup en un verdadero plan de recuperación (DRP).
5. **La escalabilidad quedó incorporada al diseño:** agregar una tercera instancia de la app sería una sola línea en el upstream de NGINX.

### Lecciones aprendidas

- **Los template literals anidados de JavaScript no perdonan:** un backtick dentro de otro tumbó las dos apps con `SyntaxError` y PM2 las marcó como `errored` tras 16 reinicios. Aprendimos a leer `pm2 logs` antes que adivinar, y a generar HTML con concatenación cuando hay interpolación compleja.
- **Verificar el código de respuesta HTTP antes de scriptear:** el menú marcaba Prometheus como "caído" porque esperaba un `200` y Prometheus responde `302` (redirect). Un `grep` demasiado estricto puede mentir.
- **El formato de salida importa:** el script de balanceo buscaba JSON (`"hostname":"..."`) cuando la app devolvía texto plano (`Hostname: ...`). Validar siempre contra la salida real, no la supuesta.
- **Las redes privadas exigen creatividad:** sin acceso público a las VMs, los túneles SSH (`ssh -L`) fueron la herramienta clave para acceder a Grafana y a la página desde fuera del laboratorio.

### Recomendaciones

- **Usar un certificado de CA real** (Let's Encrypt) en un despliegue con dominio público, para eliminar la advertencia del navegador.
- **Agregar un canal de notificación real a la alerta** (Telegram o correo) para que el aviso de CPU llegue aunque nadie esté mirando el dashboard.
- **Incorporar replicación a la base de datos** (Maestro-Esclavo) en una siguiente fase: hoy la BD es el único punto sin redundancia de la arquitectura.
- **Automatizar el despliegue completo con Ansible:** los pasos de esta guía son repetibles y se beneficiarían de un playbook único para levantar las 4 VMs desde cero.
- **Versionar los scripts y configuraciones en Git** para rastrear cambios y facilitar el trabajo en equipo.

---

<div align="center">

**🛒 Marketplace Local · SIS313 · USFX · Feria 2026**

*Infraestructura pensada para no fallar.*

💚 Janet · 💙 Alex Josué · 💜 Jhon Christian · 🧡 Luis Fernando

</div>
