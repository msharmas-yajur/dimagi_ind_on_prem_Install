# CommCare HQ Dual-Server Setup Guide

---

## **Executive Summary**
This document provides a detailed, step-by-step guide to setting up **CommCare HQ** in a dual-server configuration. The setup involves configuring an **Application Server** for running the web interface and services, and a **Database Server** for managing data. Key components include PostgreSQL, CouchDB, Redis, RabbitMQ, Elasticsearch, and NGINX for reverse proxy. This setup ensures optimized performance by separating the **application server** and the **database server**. The configuration supports scalability, reliability, and efficiency for large-scale deployments.

### **Supported Features**
- **Scalability**: Configurable server sizes for up to 10,000 unique users.
- **Separation of Services**: Dedicated servers for application and database components.
- **Reliability**: Includes key services: Redis, RabbitMQ, CouchDB, PostgreSQL, and Elasticsearch.

---

## **1. Prerequisites**

### 1.1 **Hardware & Software Requirements**
| Component         | Application Server          | Database Server               |
|-------------------|-----------------------------|-------------------------------|
| **CPU**          | 4 Cores                     | 8 Cores                       |
| **RAM**          | 16 GB                       | 32 GB                         |
| **Storage**      | 200 GB SSD                  | 500 GB SSD                    |
| **OS**           | Ubuntu 20.04 LTS            | Ubuntu 20.04 LTS              |

### 1.2 **Network & Firewall Rules**
- **Open Ports**:
  - **Application Server**:
    - Port 80 (HTTP)
    - Port 443 (HTTPS)
    - Port 5672 (RabbitMQ)
  - **Database Server**:
    - Port 5432 (PostgreSQL)
    - Port 5984 (CouchDB)
    - Port 6379 (Redis)
    - Port 9200 (Elasticsearch)

- Ensure the servers can communicate internally over a private network (low latency is critical). Verify latency using tools like `ping` for basic checks or `iperf` for detailed network performance testing.

---

## **2. Server Configuration**

### 2.1 **Application Server Setup**
1. **Update and Upgrade Packages**:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Install Required Dependencies**:
   ```bash
   sudo apt install python3-pip python3-venv git nginx -y
   ```

3. **Clone CommCare HQ Repository**:
   ```bash
   git clone https://github.com/dimagi/commcare-hq.git
   cd commcare-hq
   ```

4. **Setup Python Virtual Environment**:
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt
   ```

5. **Configure Localsettings**:
   - Copy `localsettings.example.py` to `localsettings.py` located in the root directory of the CommCare HQ repository.
   - Update configurations such as DATABASES, CACHES, and broker settings for RabbitMQ.

6. **Setup NGINX for Reverse Proxy**:
   - Configure a new NGINX site for CommCare HQ:
     ```bash
     sudo nano /etc/nginx/sites-available/commcarehq
     ```
   - Add the following configuration:
     ```nginx
     server {
         listen 80;
         server_name example.com;

         location / {
             proxy_pass http://127.0.0.1:8000;
             proxy_set_header Host $host;
             proxy_set_header X-Real-IP $remote_addr;
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         }
     }
     ```
   - Enable the site:
     ```bash
     sudo ln -s /etc/nginx/sites-available/commcarehq /etc/nginx/sites-enabled/
     sudo systemctl restart nginx
     ```

7. **Run CommCare HQ**:
   ```bash
   ./manage.py runserver 0.0.0.0:8000
   ```

---

### 2.2 **Database Server Setup**

1. **Install PostgreSQL**:
   ```bash
   sudo apt install postgresql -y
   sudo systemctl enable postgresql
   sudo systemctl start postgresql
   ```
   - Create a new database and user for CommCare HQ:
     ```sql
     sudo -u postgres psql
     CREATE DATABASE commcarehq;
     CREATE USER commcareuser WITH PASSWORD 'securepassword';
     GRANT ALL PRIVILEGES ON DATABASE commcarehq TO commcareuser;
     ```

2. **Install CouchDB**:
   - Add the repository and install:
     ```bash
     sudo apt install apt-transport-https
     curl -L https://couchdb.apache.org/repo/bintray-pubkey.asc | sudo apt-key add -
     echo "deb https://apache.bintray.com/couchdb-deb focal main" | sudo tee /etc/apt/sources.list.d/couchdb.list
     sudo apt update
     sudo apt install couchdb -y
     ```
   - Follow prompts to configure standalone mode and set an admin password. Use a strong password that includes a mix of uppercase, lowercase, numbers, and special characters. For default credentials, refer to the CouchDB documentation, but always change these for a production environment to enhance security.

3. **Install Redis**:
   ```bash
   sudo apt install redis-server -y
   sudo systemctl enable redis-server
   sudo systemctl start redis-server
   ```

4. **Install Elasticsearch**:
   - Import GPG key and add the repository:
     ```bash
     wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
     sudo apt install apt-transport-https
     echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
     sudo apt update && sudo apt install elasticsearch -y
     ```
   - Enable and start Elasticsearch:
     ```bash
     sudo systemctl enable elasticsearch
     sudo systemctl start elasticsearch
     ```

5. **Verify Database Connectivity**:
   - Test PostgreSQL, CouchDB, Redis, and Elasticsearch are up and running using their respective ports.

---

## **3. Integration & Final Configuration**

1. Update `localsettings.py` on the Application Server:
   - Configure database settings:
     ```python
     DATABASES = {
         'default': {
             'ENGINE': 'django.db.backends.postgresql',
             'NAME': 'commcarehq',
             'USER': 'commcareuser',
             'PASSWORD': 'securepassword',
             'HOST': 'database_server_ip',
             'PORT': '5432',
         }
     }
     ```
   - Update settings for CouchDB, Redis, RabbitMQ, and Elasticsearch.

2. **Migrate Databases**:
   ```bash
   ./manage.py migrate
   ```

3. **Collect Static Files**:
   ```bash
   ./manage.py collectstatic
   ```

4. **Restart Services**:
   ```bash
   sudo systemctl restart nginx
   ```

5. **Testing**:
   - Access the application using `http://application_server_ip` and ensure all services work correctly. Consider using tools like `curl` to test service endpoints or browser debugging tools to inspect HTTP responses and diagnose issues.

---

## **4. Monitoring & Maintenance**
- Use tools like **Prometheus** and **Grafana** for monitoring server performance.
- Ensure periodic backups of PostgreSQL and CouchDB databases.

---

## **5. Troubleshooting**
| Issue                        | Solution                                      |
|------------------------------|----------------------------------------------|
| Cannot connect to PostgreSQL | Check firewall rules and database credentials |
| CouchDB not responding       | Verify CouchDB service is running             |
| Redis connection errors      | Restart Redis server                          |
| Elasticsearch fails to start | Check system logs for errors                  |

---

## **6. Conclusion**
This guide provides a robust foundation for setting up **CommCare HQ** in a dual-server configuration, ensuring scalability and reliability for production environments.
