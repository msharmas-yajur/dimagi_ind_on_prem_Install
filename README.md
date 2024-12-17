# **CommCareHQ**

# **Sandbox**

# **Setup**

### 

### 

### **PREPARED FOR**

Dhivya & Sumanthara  
DIMAGI \- India

### **PREPARED BY**

Manish Sharma  
Yajur Healthcare Private Limited

## **Disclaimer**

* Costs are estimative and subject to change  
* Actual implementation may vary based on specific requirements  
* Recommended to conduct thorough assessment before final implementation


# **EXECUTIVE SUMMARY**

CommCareHQ is an open-source mobile platform designed for managing community health programs and digital data collection, primarily used in low-resource settings and developing countries. It was developed by Dimagi, a social enterprise focused on technology for social impact.

Key features of CommCareHQ include:

1\. Mobile Data Collection: The platform allows health workers, field agents, and community workers to collect data using mobile devices, even in areas with limited internet connectivity.

2\. Case Management: It enables tracking of individual cases, such as patient health records, maternal and child health tracking, and community health worker interactions.

3\. Offline Functionality: The mobile application can work offline and sync data when an internet connection becomes available, which is crucial in areas with unreliable connectivity.

4\. Customizable Forms: Users can create custom forms and workflows tailored to specific health programs or data collection needs.

5\. Multiple Language Support: The platform supports multiple languages, making it adaptable to diverse geographic regions.

6\. Analytics and Reporting: CommCareHQ provides tools for data analysis, visualization, and reporting to help organizations make data-driven decisions.

The platform is widely used by non-governmental organizations (NGOs), government health departments, and international development agencies in countries across Africa, Asia, and Latin America to improve healthcare delivery and data management.

# 

# **1\. Project Overview**

### **Phase 1: Planning and Preparation (2 weeks)**

1. **Initial Requirements Analysis** (3-4 days)  
   * Detailed infrastructure assessment  
   * Network architecture review  
   * User requirement validation  
   * Connectivity and bandwidth evaluation  
2. **Infrastructure Readiness** (4-5 days)  
   * Procure server hardware/cloud resources  
   * Prepare network configurations  
   * Obtain necessary licenses  
   * Security compliance check

### 

## **3\. Server Sizing and Cost Estimation**

### **3.1 Infrastructure Cost Breakdown**

## **3.1.1 Small Deployment (3,000 Users)**

| Component | Specification | Monthly Cost (₹) |
| :---- | :---- | :---- |
| Web Server | 8 vCPU, 32 GB RAM | 45,000 \- 75,000 |
| Database Server | 8 vCPU, 64 GB RAM | 60,000 \- 1,00,000 |
| **Total Monthly Cost** |  | **1,05,000 \- 1,75,000** |
| **Annual Cost** |  | **12,60,000 \- 21,00,000** |

## 

## **3.1.2 Medium Deployment (5,000 Users)**

| Component | Specification | Monthly Cost (₹) |
| :---- | :---- | :---- |
| Web Server | 12 vCPU, 64 GB RAM | 75,000 \- 1,25,000 |
| Database Server | 16 vCPU, 128 GB RAM | 1,00,000 \- 1,50,000 |
| **Total Monthly Cost** |  | **1,75,000 \- 2,75,000** |
| **Annual Cost** |  | **21,00,000 \- 33,00,000** |

## 

## **3.1.3 Large Deployment (10,000 Users)**

| Component | Specification | Monthly Cost (₹) |
| :---- | :---- | :---- |
| Web Server | 16 vCPU, 128 GB RAM | 1,25,000 \- 2,00,000 |
| Database Server | 24 vCPU, 256 GB RAM | 1,50,000 \- 2,50,000 |
| **Total Monthly Cost** |  | **2,75,000 \- 4,50,000** |
| **Annual Cost** |  | **33,00,000 \- 54,00,000** |

### 

### **Phase 2: Server Preparation (1-2 weeks)**

1. **Server Hardware/Cloud Setup** (3-4 days)  
   * Provision web and database servers  
   * Configure base operating system  
   * Network and security group configurations  
2. **Base Software Installation** (3-4 days)  
   * Install base operating system  
   * Configure system updates  
   * Install prerequisite software  
   * Initial security hardening

### **Phase 3: CommCareHQ Configuration (1-2 weeks)**

1. **Database Server Configuration** (3-4 days)  
   * PostgreSQL installation  
   * Database schema setup  
   * Performance tuning  
   * Backup strategy implementation  
2. **Web Server Configuration** (3-4 days)  
   * Python environment setup  
   * CommCareHQ application deployment  
   * Nginx web server configuration  
   * Celery and Redis setup

## **1\. Pre-Installation Requirements**

### **1.1 Server Specification Checklist**

* ### **Minimum Ubuntu 20.04 LTS or 22.04 LTS**

* ### **Root/sudo access**

* ### **Stable internet connection**

* ### **Firewall configuration access**

* ### **Public/Private key authentication setup**

### 

### **SERVER SETUP Requirements**

## **Minimum System Requirements**

* Operating System: Ubuntu 22.04 LTS (64-bit)  
* RAM: Minimum 16GB, Recommended 32GB+  
* CPU: 8 cores, 3.0 GHz  
* Storage:  
  * Server 1 (Application): 500GB SSD  
  * Server 2 (Database): 1TB SSD with high I/O performance

## **Prerequisite Software Dependencies**

1. Python 3.8+ (preferred 3.9)  
2. PostgreSQL 13.x  
3. Redis 6.x  
4. Elasticsearch 7.x  
5. RabbitMQ 3.8+  
6. Nginx 1.18+  
7. Docker (latest stable version)

### **1.2 Network and Security Configuration**

bash

*\# Update system packages*

sudo apt-get update && sudo apt-get upgrade \-y

*\# Install essential security packages*

sudo apt-get install \-y ufw fail2ban

*\# Configure firewall rules*

sudo ufw allow ssh

sudo ufw allow http

sudo ufw allow https

sudo ufw enable

**SERVERS SETUP DETAILS** 

### **Server 1: Application Server Setup**

## **Python and Virtual Environment**

bash

*\# Install Python and virtual environment*

sudo apt-get install \-y python3-pip python3-venv

python3 \-m venv /opt/commcare/venv

source /opt/commcare/venv/bin/activate

*\# Install core dependencies*

pip install \--upgrade pip

pip install virtualenv

## 

## 

## 

## **Install Core Dependencies**

bash

*\# Install system dependencies*

sudo apt-get install \-y \\

    postgresql-client \\

    libpq-dev \\

    python3-dev \\

    libxml2-dev \\

    libxslt1-dev \\

    libffi-dev \\

    libssl-dev \\

    nginx \\

    git

*\# Clone CommCare HQ repository*

git clone https://github.com/dimagi/commcare-hq.git /opt/commcare/commcare-hq

cd /opt/commcare/commcare-hq

*\# Install Python requirements*

*pip install \-r requirements.txt*

### 

### **1.4 Server 2: Database and Auxiliary Services**

## **PostgreSQL Configuration**

bash

Copy

*\# Install PostgreSQL*

sudo apt-get install \-y postgresql postgresql-contrib

*\# Create CommCare databases*

sudo \-u postgres psql \-c "CREATE DATABASE commcarehq;"

sudo \-u postgres psql \-c "CREATE USER commcare WITH PASSWORD 'strong\_password';"

sudo \-u postgres psql \-c "GRANT ALL PRIVILEGES ON DATABASE commcarehq TO commcare;"

## **Redis and Message Queue Setup**

bash

Copy

*\# Install Redis*

sudo apt-get install \-y redis-server

sudo systemctl enable redis-server

sudo systemctl start redis-server

*\# Install RabbitMQ*

wget \-O- https://packages.rabbitmq.com/gpg.key | sudo apt-key add \-

echo "deb https://dl.bintray.com/rabbitmq/debian focal main" | sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list

sudo apt-get update

sudo apt-get install \-y rabbitmq-server

### **PRE SYSTEM CHECKLIST**

### **2.1 Pre-Installation Preparation**

## **2.1.1 System Requirements Verification**

bash

Copy

*\# Comprehensive system check script*

*\#\!/bin/bash*

*\# Minimum requirements check*

**PYTHON\_MIN\_VERSION="3.8"**

**POSTGRES\_MIN\_VERSION="12.0"**

**REDIS\_MIN\_VERSION="5.0"**

**NGINX\_MIN\_VERSION="1.18"**

*\# Python version check*

PYTHON\_VERSION=$(python3 \--version | cut \-d' ' \-f2)

python\_check() {

    printf "Python Version: $PYTHON\_VERSION ... "

    if \[ "$(printf '%s\\n' "$PYTHON\_MIN\_VERSION" "$PYTHON\_VERSION" | sort \-V | head \-n1)" \= "$PYTHON\_MIN\_VERSION" \]; then

        echo "✓ PASS"

    else

        echo "✗ FAIL (Minimum $PYTHON\_MIN\_VERSION required)"

    fi

}

*\# PostgreSQL version check*

postgres\_check() {

    POSTGRES\_VERSION=$(psql \--version | cut \-d' ' \-f3)

    printf "PostgreSQL Version: $POSTGRES\_VERSION ... "

    if \[ "$(printf '%s\\n' "$POSTGRES\_MIN\_VERSION" "$POSTGRES\_VERSION" | sort \-V | head \-n1)" \= "$POSTGRES\_MIN\_VERSION" \]; then

        echo "✓ PASS"

    else

        echo "✗ FAIL (Minimum $POSTGRES\_MIN\_VERSION required)"

    fi

}

*\# Execute checks*

Python\_check

postgres\_check

## **2.1.2 Dependency Installation**

bash

Copy

*\# Comprehensive dependency installation*

sudo apt-get update && sudo apt-get upgrade \-y

sudo apt-get install \-y \\

    python3-pip \\

    python3-dev \\

    postgresql \\

    postgresql-contrib \\

    nginx \\

    redis-server \\

    git \\

    libpq-dev \\

    build-essential \\

    curl \\

    wget \\

    software-properties-common

### **Preliminary System Update**

### **bash**

### **Copy**

### ***\# Update package lists***

### **sudo apt-get update**

### ***\# Upgrade existing packages***

### **sudo apt-get upgrade \-y**

### ***\# Install essential dependencies***

### **sudo apt-get install \-y \\**

###     **software-properties-common \\**

###     **wget \\**

###     **curl \\**

###     **git \\**

###     **build-essential \\**

###     **python3-pip \\**

###     **python3-dev \\**

###     **libpq-dev \\**

###     **nginx \\**

###     **redis-server \\**

###     **postgresql \\**

    postgresql-contrib

## **2\. Detailed Installation Steps**

### **2.1 PostgreSQL Database Server Configuration**

## **2.1.1 PostgreSQL Installation Verification**

### **bash**

### **Copy**

### ***\# Check PostgreSQL version***

### **psql \--version**

### ***\# Ensure PostgreSQL service is running***

sudo systemctl status postgresql

## **2.1.2 Database User and Schema Creation**

### **bash**

### **Copy**

### ***\# Switch to postgres user***

### **sudo \-u postgres psql**

### ***\# Create CommCareHQ database user***

### **CREATE USER commcare WITH PASSWORD 'StrongDBPassword2024\!';**

### ***\# Create CommCareHQ database***

### **CREATE DATABASE commcarehq WITH OWNER commcare;**

### ***\# Grant privileges***

### **GRANT ALL PRIVILEGES ON DATABASE commcarehq TO commcare;**

### ***\# Exit psql***

\\q

## **2.1.3 PostgreSQL Configuration Optimization**

### **bash**

### **Copy**

### ***\# Edit postgresql.conf***

### **sudo nano /etc/postgresql/12/main/postgresql.conf**

### ***\# Recommended performance settings***

### **max\_connections \= 300**

### **shared\_buffers \= '25%'**

### **effective\_cache\_size \= '50%'**

### **maintenance\_work\_mem \= '1GB'**

### **checkpoint\_completion\_target \= 0.9**

wal\_buffers \= '16MB'

## **RECHECK DATABASE SETUP**

### **2.2 Database Server Configuration**

## **2.2.1 PostgreSQL Advanced Configuration**

bash

Copy

*\# Advanced PostgreSQL Configuration*

sudo \-u postgres psql \<\< EOF

\-- Create CommCareHQ Database

CREATE DATABASE commcarehq\_prod

    WITH ENCODING='UTF8'

    CONNECTION LIMIT=-1;

\-- Create specialized database user

CREATE USER commcare\_admin WITH 

    ENCRYPTED PASSWORD 'ComplexPassword2024\!@\#'

    SUPERUSER

    CREATEDB

    CREATEROLE;

\-- Grant privileges

GRANT ALL PRIVILEGES ON DATABASE commcarehq\_prod TO commcare\_admin;

EOF

*\# Optimize PostgreSQL configuration*

sudo nano /etc/postgresql/12/main/postgresql.conf

*\# Modify key performance parameters:*

*\# max\_connections \= 300*

*\# shared\_buffers \= '25%'*

*\# effective\_cache\_size \= '50%'*

*\# maintenance\_work\_mem \= '1GB'*

## **2.1.4 Remote Access Configuration**

### **bash**

### **Copy**

### ***\# Edit pg\_hba.conf***

### **sudo nano /etc/postgresql/12/main/pg\_hba.conf**

### ***\# Add entry for web server access***

### **host    all    commcare    \<WEB\_SERVER\_IP\>/32    md5**

### ***\# Restart PostgreSQL***

sudo systemctl restart postgresql

### **2.2 Web Server Configuration**

## **2.2.1 Python Virtual Environment Setup**

### **bash**

### **Copy**

### ***\# Install virtualenv***

### **sudo pip3 install virtualenv**

### 

### ***\# Create virtual environment***

### **virtualenv \-p python3 \~/commcare-venv**

### 

### ***\# Activate virtual environment***

source \~/commcare-venv/bin/activate

## **RECHECK WEBSERVER**

### **2.3 Web Server Configuration**

## **2.3.1 Python Virtual Environment Setup**

bash

Copy

*\# Create isolated Python environment*

python3 \-m venv \~/commcare\_venv

source \~/commcare\_venv/bin/activate

*\# Clone CommCareHQ Repository*

git clone https://github.com/dimagi/commcare-hq.git

cd commcare-hq

*\# Install project dependencies*

pip install \--upgrade pip

pip install \-r requirements.txt

## **2.2.2 CommCareHQ Repository Cloning**

### **bash**

### **Copy**

### ***\# Clone repository***

### **git clone https://github.com/dimagi/commcare-hq.git**

### **cd commcare-hq**

### ***\# Install Python dependencies***

pip install \-r requirements.txt

## **2.2.3 Configuration File Creation**

### **bash**

### **Copy**

### ***\# Create localsettings.py***

### **cat \> localsettings.py \<\< EOL**

### **DATABASES \= {**

###     **'default': {**

###         **'ENGINE': 'django.db.backends.postgresql',**

###         **'NAME': 'commcarehq',**

###         **'USER': 'commcare',**

###         **'PASSWORD': 'StrongDBPassword2024\!',**

###         **'HOST': '\<DATABASE\_SERVER\_IP\>',**

###         **'PORT': '5432',**

###     **}**

### **}**

EOL

### **2.3 Nginx Web Server Configuration**

### **bash**

### **Copy**

### ***\# Create Nginx configuration***

### **sudo nano /etc/nginx/sites-available/commcarehq**

### 

### **server {**

###     **listen 80;**

###     **server\_name commcare.yourdomain.com;**

### 

###     **location / {**

###         **proxy\_pass http://127.0.0.1:8000;**

###         **proxy\_set\_header Host $host;**

###         **proxy\_set\_header X-Real-IP $remote\_addr;**

###     **}**

### **}**

### 

### ***\# Enable site configuration***

### **sudo ln \-s /etc/nginx/sites-available/commcarehq /etc/nginx/sites-enabled/**

### **sudo nginx \-t**

### **sudo systemctl restart nginx**

### 

### 

### **Phase 4: Integration and Testing (1 week)**

1. **System Integration** (2-3 days)  
   * Database and web server connectivity  
   * User authentication setup  
   * Initial data migration  
   * Performance testing  
2. **User Acceptance Testing** (2-3 days)  
   * Functional testing  
   * Load testing  
   * Security vulnerability assessment  
   * User role and permission verification

### **Phase 5: Go-Live and Support (2 weeks)**

1. **Deployment Preparation** (3-4 days)  
   * Final system optimization  
   * Comprehensive backup  
   * Disaster recovery plan finalization  
2. **Go-Live and Initial Support** (5-7 days)  
   * Staged rollout  
   * 24x7 initial support  
   * User training  
   * Performance monitoring

# 

# **2\. Project Cost Estimation**

## **Cost Estimation (Indian Rupees)**

### **Infrastructure Costs**

| User Scale | Web Server | Database Server | Monthly  Cloud/Hosting | Annual Cost |
| :---- | :---- | :---- | :---- | :---- |
| 3,000 Users | ₹45,000 \- ₹75,000 | ₹60,000 \- ₹1,00,000 | ₹1,50,000 \- ₹2,50,000 | ₹18,00,000 \- ₹30,00,000 |
| 5,000 Users | ₹75,000 \- ₹1,25,000 | ₹1,00,000 \- ₹1,50,000 | ₹2,50,000 \- ₹4,00,000 | ₹30,00,000 \- ₹48,00,000 |
| 10,000 Users | ₹1,25,000 \- ₹2,00,000 | ₹1,50,000 \- ₹2,50,000 | ₹4,00,000 \- ₹6,00,000 | ₹48,00,000 \- ₹72,00,000 |

### 

### **Implementation Service Costs**

| Service Component | Estimated Cost Range |
| :---- | :---- |
| Consulting and Planning | ₹3,00,000 \- ₹5,00,000 |
| Implementation Services | ₹5,00,000 \- ₹8,00,000 |
| Initial Support and Training | ₹2,00,000 \- ₹3,50,000 |
| **Total Implementation Cost** | **₹10,00,000 \- ₹16,50,000** |

## 

## **Key Assumptions and Notes**

* Costs are estimative and may vary based on specific requirements  
* Does not include client-side network infrastructure  
* Assumes standard security and compliance requirements  
* Pricing based on cloud infrastructure and managed services  
* Additional customization may incur extra costs

## **Potential Challenges**

1. Network bandwidth limitations  
2. Legacy system integration  
3. Data migration complexities  
4. User adoption and training

## **Recommended Next Steps**

1. Detailed infrastructure assessment  
2. Proof of Concept (PoC) deployment  
3. Detailed requirements gathering  
4. Finalize implementation strategy

## **Exclusions**

* Client-side network infrastructure  
* Advanced customizations  
* Extended support beyond initial deployment phase

**Note:** This is a preliminary estimate and requires detailed discussion and validation with the client's technical team.

# 

# **3\. Technical Obstacles**

\[Any technical obstacles like integration between different systems, as well as mitigation strategies\]

# **9\. References and Source Documentation**

### **9.1 Official Documentation**

1. CommCareHQ Official Documentation  
   * URL: [https://docs.commcare.org](https://docs.commcare.org)  
   * Last Accessed: December 2024  
2. Dimagi Implementation Guide  
   * URL: [https://www.dimagi.com/commcare-implementation](https://www.dimagi.com/commcare-implementation)  
   * Last Accessed: December 2024

### **9.2 Technical References**

1. PostgreSQL Performance Tuning  
   * Postgres.org Official Documentation  
   * URL: [https://www.postgresql.org/docs/performance/](https://www.postgresql.org/docs/performance/)  
2. Python Virtual Environment Best Practices  
   * Python Official Documentation  
   * URL: [https://docs.python.org/3/library/venv.html](https://docs.python.org/3/library/venv.html)

### **9.3 Compliance and Security**

1. HIPAA Compliance Guidelines  
2. Data Protection Regulations  
3. Cloud Security Best Practices

