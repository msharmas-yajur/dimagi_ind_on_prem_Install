# CommCare HQ Comprehensive Dual-Server Setup Guide

---

## **Executive Summary**
This document provides a detailed, step-by-step guide to setting up CommCare HQ in a dual-server configuration. The setup ensures optimized performance by separating the application server and the database server. The guide includes prerequisite requirements, server sizing recommendations, cost estimations, and a breakdown of installation and setup time. It is intended for technical teams responsible for deploying CommCare HQ at scale.

The configuration supports:
- **Scalability**: Configurable server sizes for 3,000, 5,000, and 10,000 unique users.
- **Efficiency**: Separation of application and database services.
- **Reliability**: Includes Redis, RabbitMQ, CouchDB, PostgreSQL, and Elasticsearch to support all functional components.

This document also provides references to relevant resources and tools for further support.

---

## **Prepared For**
Dhivya & Sumanthara  
DIMAGI \- India

## **Prepared By**
Manish Sharma  
Yajur Healthcare Private Limited

## **Disclaimer**
* Costs are estimative and subject to change.  
* Actual implementation may vary based on specific requirements.  
* Recommended to conduct thorough assessment before final implementation.

---

## **Section 1: Step-by-Step Setup Instructions**

### 1.1 **Overview of Dual-Server Configuration**
- **Server 1**: Application Server (CommCare HQ)
- **Server 2**: Database Server (PostgreSQL, CouchDB, Redis, RabbitMQ, and Elasticsearch)

Ensure both servers are accessible and networked properly.

### 1.2 **Prerequisites**
1. **Operating System**: Ubuntu 20.04 LTS (recommended).
2. **Access**: Root or sudo privileges on both servers.
3. **Dependencies**:
   - Python 3.8+
   - PostgreSQL 12+
   - CouchDB 3.x
   - Redis 6+
   - NGINX
   - Elasticsearch 7.x
   - RabbitMQ 3.8+
   - Git, Curl, and Wget

---

### 1.3 **Step-by-Step Instructions**

#### **Step 1: Update Both Servers**
Execute on **both servers**:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential git curl wget
```

#### **Step 2: Install Python and Pip**
On **Server 1** (Application Server):
```bash
sudo apt install -y python3.8 python3-pip python3-venv
python3 -m pip install --upgrade pip
```

#### **Step 3: Install PostgreSQL on Server 2**
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

#### **Step 4: Install CouchDB on Server 2**
```bash
sudo apt install -y apt-transport-https gnupg
curl -L https://couchdb.apache.org/repo/bintray-pubkey.asc | sudo apt-key add -
sudo add-apt-repository "deb https://apache.bintray.com/couchdb-deb focal main"
sudo apt update && sudo apt install -y couchdb
```
During installation:
- **Mode**: Standalone
- **Bind Address**: `0.0.0.0`
- Set **Admin User** credentials.

Verify installation:
```bash
curl http://localhost:5984
```

#### **Step 5: Install Redis on Server 2**
```bash
sudo apt install -y redis-server
sudo systemctl enable redis-server
sudo systemctl start redis-server
```
Edit configuration to allow remote access:
```bash
sudo nano /etc/redis/redis.conf
```
Set:
```text
bind 0.0.0.0
```
Restart Redis:
```bash
sudo systemctl restart redis-server
```

#### **Step 6: Install RabbitMQ on Server 2**
```bash
sudo apt install -y rabbitmq-server
sudo systemctl enable rabbitmq-server
sudo systemctl start rabbitmq-server
```
Create RabbitMQ user for CommCare:
```bash
sudo rabbitmqctl add_user commcare your_secure_password
sudo rabbitmqctl set_permissions -p / commcare " .*" " .*" " .*"
```

#### **Step 7: Install Elasticsearch on Server 2**
```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.2-amd64.deb
sudo dpkg -i elasticsearch-7.10.2-amd64.deb
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
```
Verify installation:
```bash
curl -X GET "localhost:9200"
```

#### **Step 8: Setup CommCare HQ on Server 1**
Clone the repository:
```bash
git clone https://github.com/dimagi/commcare-hq.git
cd commcare-hq
python3 -m venv venv
source venv/bin/activate
pip install -r requirements/requirements.txt
```

#### **Step 9: Configure CommCare HQ**
Copy and edit the configuration file:
```bash
cp localsettings.example.py localsettings.py
nano localsettings.py
```
Set:
- **PostgreSQL Host**: Server 2 IP
- **CouchDB Host**: Server 2 IP
- **Redis Host**: Server 2 IP
- **RabbitMQ Host**: Server 2 IP
- **Elasticsearch Host**: Server 2 IP

#### **Step 10: Start CommCare HQ**
Run database migrations and start the server:
```bash
./manage.py migrate
./manage.py create_kafka_topics
./manage.py runserver 0.0.0.0:8000
```

---

## **Section 2: Server Sizing and Cost Estimation**

### **Server Sizing Recommendations**
| Unique Users | CPU (Cores) | RAM  | Storage (GB) | Notes                  |
|--------------|-------------|------|-------------|------------------------|
| 3,000        | 4           | 8GB  | 200         | Standard configuration |
| 5,000        | 6           | 16GB | 400         | Increase DB resources  |
| 10,000       | 8           | 32GB | 800         | High-performance setup |

### **Cost Estimation (INR, Monthly)**
| Unique Users | App Server | DB Server | Total Cost |
|--------------|------------|-----------|------------|
| 3,000        | 12,000     | 15,000    | 27,000     |
| 5,000        | 18,000     | 22,000    | 40,000     |
| 10,000       | 25,000     | 30,000    | 55,000     |

---

## **Section 3: Installation and Setup Time Estimation**
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

## **Section 4: References**
1. **CommCare HQ Repository**: [https://github.com/dimagi/commcare-hq](https://github.com/dimagi/commcare-hq)
2. **CouchDB Documentation**: [https://docs.couchdb.org/en/stable/](https://docs.couchdb.org/en/stable/)
3. **RabbitMQ Documentation**: [https://www.rabbitmq.com/](https://www.rabbitmq.com/)
4. **Elasticsearch 7.10.2**: [https://www.elastic.co/guide/en/elasticsearch/](https://www.elastic.co/guide/en/elasticsearch/)
5. **Ubuntu 20.04 LTS**: [https://ubuntu.com/download](https://ubuntu.com/download)
