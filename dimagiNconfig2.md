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

---

### 1.2 **Network and Security Configuration**
```bash
# Update system packages
sudo apt-get update && sudo apt-get upgrade -y

# Install essential security packages
sudo apt-get install -y ufw fail2ban

# Configure firewall rules
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw enable
```

---

### 1.3 **Server 1: Application Server Setup**
#### **Python and Virtual Environment**
```bash
# Install Python and virtual environment
sudo apt-get install -y python3-pip python3-venv
python3 -m venv /opt/commcare/venv
source /opt/commcare/venv/bin/activate

# Install core dependencies
pip install --upgrade pip
pip install virtualenv
```

#### **Install Core Dependencies**
```bash
# Install system dependencies
sudo apt-get install -y \
    postgresql-client \
    libpq-dev \
    python3-dev \
    libxml2-dev \
    libxslt1-dev \
    libffi-dev \
    libssl-dev \
    nginx \
    git

# Clone CommCare HQ repository
git clone https://github.com/dimagi/commcare-hq.git /opt/commcare/commcare-hq
cd /opt/commcare/commcare-hq

# Install Python requirements
pip install -r requirements.txt
```

---

### 1.4 **Server 2: Database and Auxiliary Services**
#### **PostgreSQL Configuration**
```bash
# Install PostgreSQL
sudo apt-get install -y postgresql postgresql-contrib

# Create CommCare databases
sudo -u postgres psql -c "CREATE DATABASE commcarehq;"
sudo -u postgres psql -c "CREATE USER commcare WITH PASSWORD 'strong_password';"
sudo -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE commcarehq TO commcare;"
```

#### **Redis and RabbitMQ Setup**
```bash
# Install Redis
sudo apt-get install -y redis-server
sudo systemctl enable redis-server
sudo systemctl start redis-server

# Install RabbitMQ
wget -O- https://packages.rabbitmq.com/gpg.key | sudo apt-key add -
echo "deb https://dl.bintray.com/rabbitmq/debian focal main" | sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list
sudo apt-get update
sudo apt-get install -y rabbitmq-server
```

---

### 1.5 **Pre-Installation Preparation**
#### **System Requirements Verification**
```bash
# System check script
#!/bin/bash
PYTHON_MIN_VERSION="3.8"
POSTGRES_MIN_VERSION="12.0"
REDIS_MIN_VERSION="5.0"

# Python version check
PYTHON_VERSION=$(python3 --version | cut -d' ' -f2)
echo "Python Version: $PYTHON_VERSION"

# PostgreSQL version check
POSTGRES_VERSION=$(psql --version | cut -d' ' -f3)
echo "PostgreSQL Version: $POSTGRES_VERSION"
```

#### **Comprehensive Dependency Installation**
```bash
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install -y \
    python3-pip python3-dev postgresql \
    postgresql-contrib nginx redis-server \
    git libpq-dev build-essential curl wget software-properties-common
```

---

### 1.6 **Web Server and CommCare HQ Setup**
#### **Configuration File Creation**
```bash
# Create localsettings.py
cat > localsettings.py << EOL
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'commcarehq',
        'USER': 'commcare',
        'PASSWORD': 'StrongDBPassword2024!',
        'HOST': '<DATABASE_SERVER_IP>',
        'PORT': '5432',
    }
}
EOL
```

---

## **Section 2: Server Sizing and Cost Estimation**
### **Server Sizing Recommendations**
| Unique Users | CPU (Cores) | RAM  | Storage (GB) |
|--------------|-------------|------|-------------|
| 3,000        | 4           | 8GB  | 200         |
| 5,000        | 6           | 16GB | 400         |
| 10,000       | 8           | 32GB | 800         |

---

## **Section 3: Installation and Setup Time Estimation**
| Activity                          | Estimated Time |
|-----------------------------------|----------------|
| Server OS Setup                   | 2 hours        |
| Dependency Installation           | 3 hours        |
| PostgreSQL & Redis Setup          | 2 hours        |
| RabbitMQ & Elasticsearch Setup    | 2 hours        |
| CommCare HQ Installation & Config | 4 hours        |
| Testing and Verification          | 3 hours        |
| **Total Time**                    | **16 hours**   |

---

## **Section 4: References**
1. **CommCare HQ Repository**: [https://github.com/dimagi/commcare-hq](https://github.com/dimagi/commcare-hq)
2. **Ubuntu 20.04 LTS**: [https://ubuntu.com/download](https://ubuntu.com/download)
3. **CouchDB Documentation**: [https://docs.couchdb.org/en/stable/](https://docs.couchdb.org/en/stable/)
4. **RabbitMQ Docs**: [https://www.rabbitmq.com/](https://www.rabbitmq.com/)
5. **PostgreSQL Configuration**: [https://www.postgresql.org/docs/](https://www.postgresql.org/docs/)
