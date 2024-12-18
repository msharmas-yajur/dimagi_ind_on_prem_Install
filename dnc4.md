# CommCare HQ Dual-Server Setup Guide

---

## **Executive Summary**
This document provides a detailed, step-by-step guide to setting up **CommCare HQ** in a dual-server configuration. This setup ensures optimized performance by separating the **application server** and the **database server**. The configuration supports scalability, reliability, and efficiency for large-scale deployments.

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
| **RAM**          | 8 GB                        | 16 GB                         |
| **Disk**         | 100 GB SSD                  | 500 GB SSD (high I/O)         |
| **OS**           | Ubuntu 20.04 LTS            | Ubuntu 20.04 LTS              |

### 1.2 **Network Requirements**
- Static IP addresses for both servers.
- Network connectivity between servers verified (e.g., `ping`).
- Ensure firewall rules are appropriately configured (see Section 2).

### 1.3 **Access & Privileges**
- Root or sudo access on both servers.

---

## **2. Network & Security Configuration**

Run the following steps on **both servers**:

1. **Update System Packages**
   ```bash
   sudo apt-get update && sudo apt-get upgrade -y
   ```

2. **Install Security Tools**
   ```bash
   sudo apt-get install -y ufw fail2ban
   ```

3. **Firewall Configuration**
   ```bash
   sudo ufw allow ssh
   sudo ufw allow http
   sudo ufw allow https
   sudo ufw enable
   ```

4. **Secure SSH Access**
   - Edit `/etc/ssh/sshd_config`:
     ```
     PermitRootLogin no
     AllowUsers <your-username>
     ```
   - Restart SSH:
     ```bash
     sudo systemctl restart sshd
     ```

---

## **3. Application Server Setup**

### 3.1 **Install Dependencies**
Ensure Python 3.9 is installed.
```bash
sudo apt update
sudo apt install -y python3.9 python3.9-dev python3-pip python3.9-venv
```

### 3.2 **Python Version Management with Pyenv**
Set up `pyenv` and `pyenv-virtualenv` to manage Python versions:
```bash
# Install pyenv
curl https://pyenv.run | bash

# Add pyenv to bash
echo -e '\n# Pyenv Setup' >> ~/.bashrc
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
source ~/.bashrc

# Install Python 3.9 and create a virtual environment
pyenv install 3.9.16
pyenv virtualenv 3.9.16 commcare-hq
pyenv activate commcare-hq
```

### 3.3 **Install Node.js, npm, and Yarn**
```bash
# Install Node.js and npm
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt install -y nodejs

# Install Yarn
npm install -g yarn
```

### 3.4 **Clone CommCare HQ Repository**
```bash
git clone https://github.com/dimagi/commcare-hq.git /opt/commcare-hq
cd /opt/commcare-hq
```

### 3.5 **Create Virtual Environment & Install Requirements**
Activate the environment and install dependencies:
```bash
pip install -r requirements/requirements.txt
```

### 3.6 **Configure Application Settings**
- Copy `localsettings.example.py` to `localsettings.py`:
   ```bash
   cp localsettings.example.py localsettings.py
   ```
- Update `localsettings.py`:
   - **Database Host**: IP of Database Server.
   - **Redis, RabbitMQ, Elasticsearch** configurations.

---

## **4. Database Server Setup**

### 4.1 **Install PostgreSQL**
```bash
sudo apt-get install -y postgresql
sudo systemctl enable postgresql
sudo systemctl start postgresql
```
Update `pg_hba.conf` to allow connections:
```
host    all             all             <application_server_ip>/32    md5
```

### 4.2 **Install CouchDB**
```bash
sudo apt-get install -y couchdb
```

### 4.3 **Install Redis**
```bash
sudo apt-get install -y redis-server
sudo systemctl enable redis-server
sudo systemctl start redis-server
```

### 4.4 **Install RabbitMQ**
```bash
sudo apt-get install -y rabbitmq-server
sudo systemctl enable rabbitmq-server
sudo systemctl start rabbitmq-server
```

### 4.5 **Install Elasticsearch**
1. Download and install Elasticsearch:
   ```bash
   wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.17.0-amd64.deb
   sudo dpkg -i elasticsearch-7.17.0-amd64.deb
   ```
2. Enable and start the service:
   ```bash
   sudo systemctl enable elasticsearch
   sudo systemctl start elasticsearch
   ```

---

## **5. Linking Application & Database Servers**

1. **Update Application Server Configurations**:
   - Update `localsettings.py` with the database server IP:
     ```python
     DATABASES = {
         'default': {
             'ENGINE': 'django.db.backends.postgresql_psycopg2',
             'HOST': '<DATABASE_SERVER_IP>',
             'NAME': 'commcarehq',
             'USER': 'commcare',
             'PASSWORD': 'securepassword',
         }
     }
     ```

2. **Test Connectivity**:
   ```bash
   telnet <DATABASE_SERVER_IP> 5432  # PostgreSQL Test
   curl http://<DATABASE_SERVER_IP>:5984  # CouchDB Test
   ```

---

## **6. Running CommCare HQ**

1. **Run Migrations**:
   ```bash
   ./manage.py migrate
   ```

2. **Collect Static Files and Compress**:
   ```bash
   ./manage.py collectstatic --noinput
   ./manage.py compress
   ```

3. **Start Development Server**:
   ```bash
   ./manage.py runserver 0.0.0.0:8000
   ```

4. **Setup Supervisor for Production**:
   - Use Supervisor to manage Django, Celery workers, and beat scheduler.

5. **Run Celery Workers and Flower**:
   ```bash
   celery -A corehq worker --loglevel=info
   flower -A corehq --port=5555
   ```

---

## **7. Post-Setup Monitoring & Maintenance**

1. **Install Monitoring Tools**:
   - **Prometheus** and **Grafana** for performance monitoring.
   - **Logrotate** for managing logs.

2. **Backup Strategy**:
   - Schedule backups for PostgreSQL, CouchDB, and media files.

3. **Security Hardening**:
   - Regular updates and vulnerability scans.

---

## **8. Testing & Validation**

- Validate service statuses:
  ```bash
  sudo systemctl status postgresql
  sudo systemctl status redis-server
  sudo systemctl status rabbitmq-server
  ```
- Verify the application server logs for errors.
- Perform load testing for performance benchmarking.

---

## **References**
- [CommCare HQ GitHub Repository](https://github.com/dimagi/commcare-hq)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Elasticsearch Installation Guide](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)
- [CommCare HQ Developer Setup Guide](https://github.com/dimagi/commcare-hq/blob/master/DEV_SETUP.md)
