# CommCare HQ Dual-Server Setup Guide

## Section 1: Step-by-Step Setup Instructions

### 1.1 Overview of Dual-Server Configuration
- **Server 1**: Application Server (CommCare HQ)
- **Server 2**: Database Server (PostgreSQL, CouchDB, and supporting services)

Ensure that both servers are accessible and networked properly.

---

### 1.2 Prerequisites
1. **Operating System**: Ubuntu 20.04 LTS (recommended)
2. **Access**: Root or sudo privileges on both servers.
3. **Dependencies**:
    - Python 3.8+
    - PostgreSQL 12+
    - CouchDB 3.x
    - Redis 6+
    - NGINX
    - Elasticsearch 7.x
    - RabbitMQ 3.8+

---

### 1.3 Step-by-Step Instructions

#### Step 1: Update Both Servers
Execute these commands on **both servers**:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential git curl wget
```

#### Step 2: Install Python and Pip
On **Server 1** (Application Server):
```bash
sudo apt install -y python3.8 python3-pip python3-venv
python3 -m pip install --upgrade pip
```

#### Step 3: Install PostgreSQL on Server 2 (Database Server)
```bash
sudo apt install -y postgresql postgresql-contrib libpq-dev
sudo systemctl enable postgresql
sudo systemctl start postgresql
```
Configure PostgreSQL:
1. Switch to the `postgres` user:
    ```bash
    sudo -i -u postgres
    ```
2. Create a database and user:
    ```sql
    CREATE DATABASE commcarehq;
    CREATE USER commcare WITH ENCRYPTED PASSWORD 'your_secure_password';
    GRANT ALL PRIVILEGES ON DATABASE commcarehq TO commcare;
    ```
3. Exit PostgreSQL shell:
    ```bash
    exit
    ```

#### Step 4: Install CouchDB on Server 2
```bash
sudo apt install -y apt-transport-https gnupg
curl -L https://couchdb.apache.org/repo/bintray-pubkey.asc | sudo apt-key add -
sudo add-apt-repository "deb https://apache.bintray.com/couchdb-deb focal main"
sudo apt update && sudo apt install -y couchdb
```
During the installation, select **standalone** mode and configure with the following:
- **Bind Address**: `0.0.0.0`
- **Admin User**: Set a username and password.

Verify:
```bash
curl http://localhost:5984
```

#### Step 5: Install Redis on Server 2
```bash
sudo apt install -y redis-server
sudo systemctl enable redis-server
sudo systemctl start redis-server
```
Edit Redis configuration to allow network access:
```bash
sudo nano /etc/redis/redis.conf
```
Set `bind 0.0.0.0` and restart Redis:
```bash
sudo systemctl restart redis-server
```

#### Step 6: Install RabbitMQ on Server 2
```bash
sudo apt install -y rabbitmq-server
sudo systemctl enable rabbitmq-server
sudo systemctl start rabbitmq-server
```
Create a RabbitMQ user for CommCare HQ:
```bash
sudo rabbitmqctl add_user commcare your_secure_password
sudo rabbitmqctl set_permissions -p / commcare ".*" ".*" ".*"
```

#### Step 7: Install Elasticsearch on Server 2
```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.2-amd64.deb
sudo dpkg -i elasticsearch-7.10.2-amd64.deb
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
```
Verify:
```bash
curl -X GET "localhost:9200"
```

#### Step 8: Setup CommCare HQ on Server 1
Clone the repository and install dependencies:
```bash
git clone https://github.com/dimagi/commcare-hq.git
cd commcare-hq
python3 -m venv venv
source venv/bin/activate
pip install -r requirements/requirements.txt
```

#### Step 9: Configure CommCare HQ
Create a `localsettings.py` file:
```bash
cp localsettings.example.py localsettings.py
nano localsettings.py
```
Set the following values:
- PostgreSQL host: **Server 2 IP**
- CouchDB host: **Server 2 IP**
- Redis host: **Server 2 IP**
- RabbitMQ host: **Server 2 IP**
- Elasticsearch host: **Server 2 IP**

#### Step 10: Start CommCare HQ
```bash
./manage.py migrate
./manage.py create_kafka_topics
./manage.py runserver 0.0.0.0:8000
```

---

## Section 2: Server Sizing and Cost Estimation

### 2.1 Server Sizing Recommendations
| Unique Users | CPU (Cores) | RAM | Storage (GB) | Notes                  |
|--------------|------------|-----|-------------|------------------------|
| 3,000        | 4          | 8GB | 200         | Standard VM configuration |
| 5,000        | 6          | 16GB| 400         | Increase DB & cache size  |
| 10,000       | 8          | 32GB| 800         | High performance setup    |

### 2.2 Cost Estimation (AWS/Cloud Equivalent, Monthly in INR)
| Unique Users | App Server (INR) | DB Server (INR) | Total Cost (INR) |
|--------------|-----------------|-----------------|-----------------|
| 3,000        | 12,000          | 15,000          | 27,000          |
| 5,000        | 18,000          | 22,000          | 40,000          |
| 10,000       | 25,000          | 30,000          | 55,000          |

---

## Section 3: Installation and Setup Time Estimation

| Activity                          | Estimated Time |
|-----------------------------------|----------------|
| Server OS Setup                   | 2 hours        |
| Dependency Installation           | 3 hours        |
| PostgreSQL & CouchDB Setup        | 2 hours        |
| Redis & RabbitMQ Setup            | 1.5 hours      |
| Elasticsearch Setup               | 1.5 hours      |
| CommCare HQ Installation & Config | 4 hours        |
| Testing and Verification          | 2 hours        |
| **Total Time**                    | **16 hours**   |

---

## Section 4: References

1. **CommCare HQ Repository**: [https://github.com/dimagi/commcare-hq](https://github.com/dimagi/commcare-hq)
2. **CouchDB Installation Guide**: [https://docs.couchdb.org/en/stable/](https://docs.couchdb.org/en/stable/)
3. **Elasticsearch 7.10.2**: [https://www.elastic.co/guide/en/elasticsearch/](https://www.elastic.co/guide/en/elasticsearch/)
4. **RabbitMQ Docs**: [https://www.rabbitmq.com/](https://www.rabbitmq.com/)

---

This guide provides a comprehensive, step-by-step solution for setting up CommCare HQ in a dual-server configuration with server sizing, cost estimation, and time breakdown for installation.
