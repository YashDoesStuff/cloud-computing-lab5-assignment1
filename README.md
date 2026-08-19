# Assignment 1 — AWS EC2 + RDS PostgreSQL CRUD Application

> Post Lab Assignment 1 — Deployment and connection of the Lab 4 EC2 application with an Amazon RDS PostgreSQL database, with complete Create, Read, Update, and Delete (CRUD) operations.

---

## 👨‍💻 Student

| Field | Details |
|---|---|
| **Name** | Yash Masaye |
| **Class & Branch** | TE CEA |
| **Batch** | B |
| **Roll Number** | 10617 |
| **Subject** | Cloud Computing Lab (CCL) |
| **AWS Region** | `ap-south-1` |

---

## 🎯 Objective

The objective of this assignment is to deploy the same database type used by the Lab 4 application on AWS and connect the existing Lab 4 EC2 application to the deployed database.

For this implementation:

- The Lab 4 application is **Linkding**
- The application is deployed on **AWS EC2**
- The application runs inside a **Docker container**
- The database is **Amazon RDS for PostgreSQL**
- The EC2 application connects to RDS PostgreSQL using port `5432`
- Database communication is protected using SSL/TLS
- All four required CRUD operations are demonstrated through the running EC2 application

---

## 🏗️ AWS Architecture

```text
                         User Browser
                              |
                              | HTTP
                              v
                    +-----------------------+
                    |       AWS EC2         |
                    |                       |
                    |   Docker Container    |
                    |      Linkding         |
                    |      Port 9090        |
                    +-----------+-----------+
                                |
                                | PostgreSQL
                                | TCP 5432
                                | SSL/TLS
                                v
                    +-----------------------+
                    |      Amazon RDS       |
                    |      PostgreSQL       |
                    |                       |
                    |      Database:        |
                    |        linkding       |
                    +-----------------------+
```

The final application architecture uses Amazon RDS PostgreSQL as the database backend for the Linkding application.

---

## ☁️ AWS Services Used

| AWS Service | Purpose |
|---|---|
| Amazon EC2 | Hosts the Linkding application |
| Amazon RDS for PostgreSQL | Provides the managed relational database |
| Amazon VPC | Provides AWS networking |
| Security Groups | Control access between EC2 and RDS |

---

## 🖥️ Application Details

The application used for this assignment is **Linkding**, a bookmark management application, deployed on the EC2 instance using Docker.

| Property | Value |
|---|---|
| Application | Linkding |
| Docker Image | `sissbruecker/linkding:1.45.0` |
| Container Name | `linkding` |
| Application Port | `9090` |
| Deployment Platform | AWS EC2 |
| Containerization | Docker |

---

## 🗄️ Amazon RDS PostgreSQL

| Property | Value |
|---|---|
| Database Engine | PostgreSQL |
| Database Name | `linkding` |
| Database User | `linkding` |
| Port | `5432` |
| AWS Region | `ap-south-1` |
| Connection | EC2 → RDS |
| Encryption | SSL/TLS |

### RDS Endpoint

```text
<RDS_ENDPOINT>
```

The actual RDS endpoint is configured on the EC2 instance and is not stored directly in the repository. The database password is also not included in this repository, for security reasons.

---

## 🔗 EC2 to RDS Connection

The Linkding Docker container is configured using environment variables for the PostgreSQL connection.

```yaml
LD_DB_ENGINE: postgres
LD_DB_DATABASE: linkding
LD_DB_USER: linkding
LD_DB_PASSWORD: ${RDS_DB_PASSWORD}
LD_DB_HOST: YOUR_RDS_ENDPOINT
LD_DB_PORT: 5432
```

The application connects from the EC2 Docker container to the Amazon RDS PostgreSQL endpoint. SSL/TLS is enabled for the connection, and it was verified directly from inside the Linkding container.

---

## 🗃️ Database Design

The application uses a PostgreSQL relational database hosted on Amazon RDS. The Linkding database contains multiple application tables, including:

| Table | Purpose |
|---|---|
| `bookmarks_bookmark` | Stores bookmark records |
| `auth_user` | Stores application user records |

Additional tables are used by Linkding for tags, sessions, authentication, and other application functionality. The database schema is managed through Linkding's Django migrations.

---

## 🔄 CRUD Operations

All CRUD operations were performed through the running Linkding web application.

### ➕ Create
A new bookmark was created through the Linkding web interface (test bookmark: `Lab 5 CRUD Test`). After submitting the form, the new bookmark appeared in the bookmark list.
**Result: ✅ Successful**

### 📖 Read
The bookmark created through the application was displayed in the Linkding bookmark list, retrieved from the PostgreSQL database.
**Result: ✅ Successful**

### ✏️ Update
The test bookmark was edited through the interface — `Lab 5 CRUD Test` → `Lab 5 CRUD Test - UPDATED`. The modified bookmark was displayed after the update.
**Result: ✅ Successful**

### 🗑️ Delete
The test bookmark was deleted through the Linkding interface. After deletion, it no longer appeared in the bookmark list.
**Result: ✅ Successful**

| Operation | Test | Result |
|---|---|---|
| Create | Created `Lab 5 CRUD Test` | ✅ Successful |
| Read | Displayed created bookmark | ✅ Successful |
| Update | Modified test bookmark | ✅ Successful |
| Delete | Removed test bookmark | ✅ Successful |

All operations were performed through the application interface rather than directly modifying the database.

---

## 🔐 Security Configuration

Security Groups were configured to control access to the PostgreSQL database:

- **Port:** `5432`
- **Protocol:** TCP

The RDS Security Group allows database access only from the EC2 Security Group. The database is **not** configured with an unrestricted inbound rule such as `0.0.0.0/0`, ensuring it is accessible by the EC2 application while reducing direct exposure to the public internet.

---

## 🔒 SSL/TLS Database Connection

The connection between the Linkding application and RDS PostgreSQL uses SSL/TLS, configured with PostgreSQL SSL parameters:

```text
sslmode = verify-full
sslrootcert = /etc/linkding/data/global-bundle.pem
```

The RDS connection was successfully tested from inside the Linkding container.

---

## 🐳 Docker Deployment

```text
Container: linkding
Image: sissbruecker/linkding:1.45.0
Port: 9090
```

The application is exposed through the EC2 public IP on port `9090`.

---

## 🚀 Deployment Steps

1. **EC2 Application** — The Lab 4 Linkding application was deployed on an AWS EC2 instance.
2. **Docker** — Docker and Docker Compose were used to run the Linkding application.
3. **RDS PostgreSQL** — An Amazon RDS PostgreSQL database was created in the `ap-south-1` region.
4. **Database Configuration** — The PostgreSQL database and application user were configured.
5. **Security Group** — The RDS Security Group was configured to allow PostgreSQL traffic on port `5432` from the EC2 Security Group only.
6. **Application Connection** — The Linkding container was configured with the RDS endpoint and credentials via environment variables.
7. **SSL Configuration** — SSL/TLS was enabled for EC2 ↔ RDS communication.
8. **Database Migration** — The existing Linkding database schema and application data were migrated to the RDS PostgreSQL database, with Linkding's Django migrations verified during deployment.
9. **Connection Verification** — The PostgreSQL connection was tested from inside the Docker container and confirmed working.
10. **CRUD Demonstration** — Create, Read, Update, and Delete operations were performed using the running EC2 application.

---

## 🌐 Application URL

```text
http://<EC2_PUBLIC_IP>:9090/bookmarks
```

> The public IP may change if the EC2 instance is stopped and restarted, unless an Elastic IP is assigned.

---

## 📁 Project Structure

```text
cloud-computing-lab5-assignment1/
│
├── README.md
├── docker-compose.yml
└── .env.example
```

> The repository contains the application configuration and source files required for deployment. Sensitive credentials and runtime-generated files are excluded.

---

## 🔑 Environment Variables

Database credentials are **not** stored directly in the repository.

`.env.example`:
```text
RDS_DB_PASSWORD=your_rds_password
```

The actual `.env` file is excluded from Git via `.gitignore`. AWS access keys and database passwords are never committed to the repository.

---

## 📚 References

- Amazon RDS for PostgreSQL Documentation
- Amazon EC2 Documentation
- AWS VPC Documentation
- AWS Security Groups Documentation
- PostgreSQL Documentation
- Docker Documentation
- Linkding Documentation

---

## 🏁 Conclusion

The Lab 4 Linkding application was successfully deployed on AWS EC2 and connected to an Amazon RDS PostgreSQL database. The application established a secure PostgreSQL connection with RDS and demonstrated all four required CRUD operations through the running web application. Database access was restricted using AWS Security Groups, and SSL/TLS was used to secure communication between EC2 and RDS PostgreSQL.

Therefore, the required EC2 + RDS PostgreSQL integration and CRUD demonstration for Post Lab Assignment 1 were successfully completed.

---

**Cloud Computing Lab — Post Lab Assignment 1**

| Name | Class & Branch | Batch | Roll No. | Subject |
|---|---|---|---|---|
| Yash Masaye | TE CEA | B | 10617 | CCL |
